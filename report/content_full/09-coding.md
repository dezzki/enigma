# 6. Coding

This chapter describes how the design is realized in code. Section 6.1 maps each architectural layer to its technology and rationale. Section 6.2 shows the monorepo layout and workspace responsibilities. Section 6.3 states the coding standards that keep the domain rules enforceable rather than conventional. Section 6.4 presents the key algorithms as representative implementations. Sections 6.5 and 6.6 close with error handling, logging, and testability practices.

## 6.1 Technology Stack

| Layer | Technology | Rationale |
|---|---|---|
| Language | TypeScript with strict compiler options | One typed language across the stack; state machine, gates, and DTOs are compile-time checked. |
| Web framework | Next.js with the T3 stack (React, tRPC, Prisma, Tailwind) | Typed end-to-end procedures, server rendering for the admin dashboard, and a mobile-first member PWA. |
| Mobile client | React Native via Expo, sharing the domain packages | One codebase for iOS and Android reusing the same rules engine. |
| API layer | tRPC procedures with REST and WebSocket bindings at the edge | Type-safe client-server contracts with streaming for countdowns, chat, and verdict events. |
| ORM and migrations | Prisma with versioned migrations | Declarative schema, a generated typed client, and migrations that carry constraints into the database. |
| Database | PostgreSQL | Relational integrity and constraints needed for atomic cutoffs, idempotency, and audit records. |
| Cache and queues | Redis with BullMQ | Durable jobs for AI verification and notification fan-out, plus rate-limit counters and hot-feed caching. |
| Object storage | AWS S3 with presigned URLs | Direct, resumable uploads that keep media off the API tier; private buckets with checksums. |
| AI verification | Multimodal provider API (vision and text) | Proof-type-specific analysis returning verdict, confidence, plain-language explanation, and model version. |
| Authentication | OAuth 2.0 and OpenID Connect with JWT sessions | Delegated identity with short-lived tokens, rotating refresh, and role claims evaluated on every endpoint. |
| Media processing | sharp for decode and re-encode, in-house pHash | Sanitizes uploads, drops metadata, and extracts fingerprints before storage. |
| Observability | Structured JSON logs, OpenTelemetry traces, queue dashboards | Correlates requests, queue depth, AI latency, and audit events during live challenges. |

## 6.2 Repository Structure

The codebase is a pnpm and Turborepo monorepo. Applications compose domain packages; packages never import from applications, and the domain core never imports a database client.

```text
enigma/
├── apps/
│   ├── web/               member-facing Next.js PWA
│   ├── admin/             web admin dashboard: challenges, queues, reports
│   └── api/               tRPC routers, REST and WebSocket edge, BullMQ workers
├── packages/
│   ├── core/              domain: state machine, posting gate, XP, policies
│   ├── verification/      AI pipeline, routing, peer quorum, provider adapters
│   ├── anti-cheat/        pHash, metadata stripping, device checks, rate limits
│   ├── db/                Prisma schema, migrations, generated client, repositories
│   ├── ui/                shared design system components
│   └── testing/           clock fakes, provider stubs, fixture builders
├── infra/                 container images, queue definitions, deployment manifests
├── turbo.json
├── pnpm-workspace.yaml
└── package.json
```

The dependency rule is directional: `apps/*` depend on `packages/*`; `core` depends only on TypeScript utilities; `db` depends on core types; `verification` and `anti-cheat` depend on core and are injected into API workers. This keeps the lifecycle, gate, and XP rules executable without a database, which also makes them directly testable.

## 6.3 Coding Standards and Conventions

- **TypeScript strict mode.** The compiler runs with `strict`, `noUncheckedIndexedAccess`, and `exactOptionalPropertyTypes`; implicit `any` is an error, and lint enforces import boundaries between packages.
- **Domain rules are pure functions.** The transition map, gate, routing, quorum, and XP arithmetic live in `packages/core` as pure functions; application code orchestrates but never re-implements them.
- **Server-authoritative transitions.** `Challenge.state` is written only by the transition service; repositories expose that function rather than a generic update, and a direct state write is treated as a defect in review.
- **Versioned Prisma migrations.** Every schema change ships as a reviewed migration; clients are rebuilt in CI, and runtime schema synchronization is forbidden in every environment.
- **Append-only audit.** Verification records and admin actions are inserted, never updated or deleted; the application role holds no UPDATE or DELETE grant, and overrides are new rows.
- **Time discipline.** Stored timestamps are UTC; domain functions receive a `Clock` and never call `Date.now()`, and deadlines come from server-assigned timestamps.
- **Idempotent write handlers.** Every mutating handler accepts an idempotency key or is naturally idempotent, so the mobile sync queue can retry safely.
- **Typed errors and naming.** Failures are typed errors with stable codes; Prisma models are PascalCase mapped to snake_case tables, TypeScript uses PascalCase types and camelCase values with kebab-case file names, and Prettier formatting is enforced.
- **No unnecessary comments.** Code is self-describing; comments are reserved for invariants code cannot express, such as why the cutoff check repeats inside the transaction.
- **Tests with the code.** Pure functions are tested beside their sources, and every bug fix adds a regression case.

## 6.4 Key Algorithms and Representative Code

The implementations below compile against the package interfaces and encode the rules from Chapter 5; they are representative rather than complete listings.

### 6.4.1 Challenge State Machine Transition Guard

The guard owns the only legal-transition map. Time-based transitions compare the injected server clock inside a transaction, so a scheduler tick that arrives late, or twice, produces the same result.

```typescript
import type { Challenge, ChallengeState, PrismaClient } from '@enigma/db';

const VALID_TRANSITIONS: Record<ChallengeState, readonly ChallengeState[]> = {
  DRAFT: ['ANNOUNCED'],
  ANNOUNCED: ['LIVE'],
  LIVE: ['ENDING'],
  ENDING: ['ARCHIVED'],
  ARCHIVED: [],
};

export class ChallengeTransitionError extends Error {
  readonly code = 'ILLEGAL_CHALLENGE_TRANSITION';

  constructor(readonly from: ChallengeState, readonly to: ChallengeState) {
    super(`Illegal challenge transition ${from} to ${to}`);
  }
}

export function assertTransition(from: ChallengeState, to: ChallengeState): void {
  if (!VALID_TRANSITIONS[from].includes(to)) {
    throw new ChallengeTransitionError(from, to);
  }
}

export function resolveScheduledTransition(
  challenge: Pick<Challenge, 'state' | 'startAt' | 'endAt' | 'graceMinutes'>,
  now: Date,
): ChallengeState | null {
  const time = now.getTime();
  switch (challenge.state) {
    case 'ANNOUNCED':
      return time >= challenge.startAt.getTime() ? 'LIVE' : null;
    case 'LIVE':
      return time >= challenge.endAt.getTime() ? 'ENDING' : null;
    case 'ENDING':
      return time >= challenge.endAt.getTime() + challenge.graceMinutes * 60_000 ? 'ARCHIVED' : null;
    default:
      return null;
  }
}

export async function transitionChallenge(
  db: PrismaClient,
  challengeId: string,
  next: ChallengeState,
  now: Date,
): Promise<Challenge> {
  return db.$transaction(async (tx) => {
    const challenge = await tx.challenge.findUniqueOrThrow({ where: { id: challengeId } });
    assertTransition(challenge.state, next);
    if (next !== 'ANNOUNCED' && resolveScheduledTransition(challenge, now) !== next) {
      throw new ChallengeTransitionError(challenge.state, next);
    }
    return tx.challenge.update({ where: { id: challengeId }, data: { state: next } });
  });
}
```

Only an administrator publish moves a challenge to `ANNOUNCED`; every later transition must equal the state derived from the server clock at commit time.

### 6.4.2 Posting Gate Guard

The gate returns a decision rather than throwing, so callers can render an explanatory message and tests can assert reasons directly. Surfaces that are not in the live-only set remain available around the clock.

```typescript
import type { Challenge, Role } from '@enigma/db';

export type Surface = 'POST' | 'CHECK_IN' | 'VOTE' | 'COMMENT' | 'STORY' | 'NOTE';

export type GateDenial = 'NOT_A_MEMBER' | 'CHALLENGE_NOT_LIVE' | 'DEADLINE_PASSED' | 'NOT_JOINED';

export type GateDecision =
  | { readonly allowed: true }
  | { readonly allowed: false; readonly reason: GateDenial };

export interface GateSubject {
  readonly userId: string;
  readonly communityRole: Role | null;
  readonly challengeStatus: 'ACTIVE' | 'AT_RISK' | 'DROPPED' | 'COMPLETED' | null;
}

const LIVE_ONLY: ReadonlySet<Surface> = new Set(['POST', 'CHECK_IN', 'VOTE']);

export function canPost(
  surface: Surface,
  subject: GateSubject,
  challenge: Pick<Challenge, 'state' | 'endAt'>,
  now: Date,
): GateDecision {
  if (subject.communityRole === null) {
    return { allowed: false, reason: 'NOT_A_MEMBER' };
  }
  if (LIVE_ONLY.has(surface) && challenge.state !== 'LIVE') {
    return { allowed: false, reason: 'CHALLENGE_NOT_LIVE' };
  }
  if (surface === 'CHECK_IN') {
    if (subject.challengeStatus !== 'ACTIVE' && subject.challengeStatus !== 'AT_RISK') {
      return { allowed: false, reason: 'NOT_JOINED' };
    }
    if (now.getTime() > challenge.endAt.getTime()) {
      return { allowed: false, reason: 'DEADLINE_PASSED' };
    }
  }
  return { allowed: true };
}
```

Submission creation calls `canPost` at the edge and repeats the equivalent check inside the transaction from §6.4.8, making the boundary atomic.

### 6.4.3 Verification Routing

Routing converts the model confidence into one of three pipelines. Invalid confidence values fail fast, and a fraud signal overrides the thresholds and routes directly to human review.

```typescript
import type { PrismaClient } from '@enigma/db';

export const AUTO_APPROVE_THRESHOLD = 0.9;
export const PEER_QUORUM_THRESHOLD = 0.5;

export type VerificationRoute = 'AUTO' | 'PEER' | 'ADMIN';

export function routeVerification(confidence: number, fraudSignal = false): VerificationRoute {
  if (Number.isNaN(confidence) || confidence < 0 || confidence > 1) {
    throw new RangeError('confidence must lie within [0, 1]');
  }
  if (fraudSignal || confidence < PEER_QUORUM_THRESHOLD) {
    return 'ADMIN';
  }
  if (confidence >= AUTO_APPROVE_THRESHOLD) {
    return 'AUTO';
  }
  return 'PEER';
}

export interface AiVerdict {
  readonly verdict: 'ACCEPT' | 'NEEDS_REVIEW' | 'REJECT';
  readonly confidence: number;
  readonly explanation: string;
  readonly modelVersion: string;
}

export async function recordAiVerdict(
  db: PrismaClient,
  submissionId: string,
  verdict: AiVerdict,
): Promise<VerificationRoute> {
  const route = routeVerification(verdict.confidence);
  const status = route === 'AUTO' ? 'AUTO_VERIFIED' : route === 'PEER' ? 'PENDING' : 'ESCALATED';
  await db.verification.create({
    data: {
      submissionId,
      stage: 'AI',
      verdict: verdict.verdict,
      status,
      confidence: verdict.confidence,
      modelVersion: verdict.modelVersion,
      explanation: verdict.explanation,
      decidedAt: new Date(),
    },
  });
  return route;
}
```

Numeric confidence is stored for audit but deliberately omitted from the peer queue projection, implementing the anti-anchoring rule.

### 6.4.4 Peer Quorum Resolution and Accuracy Credit

A quorum is decided only by three agreeing votes; any other disposition escalates to an administrator. The accuracy hook marks which votes matched the final outcome so the XP engine can credit them.

```typescript
import type { PeerVote, PrismaClient } from '@enigma/db';

export const QUORUM_SIZE = 3;

export type QuorumOutcome =
  | { readonly status: 'PENDING' }
  | { readonly status: 'ESCALATED' }
  | { readonly status: 'DECIDED'; readonly verdict: 'ACCEPT' | 'REJECT' };

export function resolveQuorum(votes: ReadonlyArray<Pick<PeerVote, 'vote'>>): QuorumOutcome {
  if (votes.length < QUORUM_SIZE) {
    return { status: 'PENDING' };
  }
  const accepts = votes.filter((vote) => vote.vote === 'ACCEPT').length;
  const rejects = votes.length - accepts;
  if (accepts === QUORUM_SIZE) {
    return { status: 'DECIDED', verdict: 'ACCEPT' };
  }
  if (rejects === QUORUM_SIZE) {
    return { status: 'DECIDED', verdict: 'REJECT' };
  }
  return { status: 'ESCALATED' };
}

export interface AccuracyCreditHook {
  onPeerOutcome(input: {
    submissionId: string;
    finalVerdict: 'ACCEPT' | 'REJECT';
    votes: ReadonlyArray<Pick<PeerVote, 'verifierId' | 'vote'>>;
  }): Promise<void>;
}

export async function settlePeerQuorum(
  db: PrismaClient,
  submissionId: string,
  credits: AccuracyCreditHook,
): Promise<void> {
  const verification = await db.verification.findFirstOrThrow({
    where: { submissionId, stage: 'PEER', status: 'PENDING' },
    include: { votes: true },
  });
  const outcome = resolveQuorum(verification.votes);
  if (outcome.status === 'PENDING') {
    return;
  }
  if (outcome.status === 'ESCALATED') {
    await db.verification.create({
      data: {
        submissionId,
        stage: 'PEER',
        verdict: 'NEEDS_REVIEW',
        status: 'ESCALATED',
        explanation: 'Peer quorum split; escalated to admin review',
        decidedAt: new Date(),
      },
    });
    return;
  }
  await db.verification.create({
    data: {
      submissionId,
      stage: 'PEER',
      verdict: outcome.verdict,
      status: outcome.verdict === 'ACCEPT' ? 'PEER_VERIFIED' : 'REJECTED',
      explanation: `Peer quorum reached ${QUORUM_SIZE} agreeing votes`,
      decidedAt: new Date(),
    },
  });
  await credits.onPeerOutcome({
    submissionId,
    finalVerdict: outcome.verdict,
    votes: verification.votes,
  });
}
```

The production hook grants base vote XP when the vote is cast and the accuracy credit when the final verdict lands, using the idempotent grant from §6.4.5.

### 6.4.5 XP Engine

XP is event-sourced: a profile total is the sum of its events, and every grant is keyed by source and reference so replays are harmless. Daily caps are applied per bucket, and level is derived from the cumulative formula rather than stored by arithmetic drift.

```typescript
import type { PrismaClient, XPEventSource } from '@enigma/db';

export const XP_AWARDS: Record<XPEventSource, number> = {
  JOIN: 20,
  CHECK_IN: 10,
  MILESTONE: 50,
  COMPLETION: 200,
  PEER_VOTE: 15,
  ACCURATE_VOTE: 25,
  POST: 10,
  STORY: 5,
  NOTE: 2,
};

export const DAILY_CAPS = { SOCIAL: 500, CHALLENGE: 2000 } as const;

const SOCIAL_SOURCES: readonly XPEventSource[] = ['POST', 'STORY', 'NOTE', 'PEER_VOTE', 'ACCURATE_VOTE'];

const CHALLENGE_SOURCES: readonly XPEventSource[] = ['JOIN', 'CHECK_IN', 'MILESTONE', 'COMPLETION'];

export function xpForLevel(level: number): number {
  return Math.round(100 * Math.pow(level, 1.5));
}

export function levelForXp(xp: number): number {
  let level = 1;
  while (xpForLevel(level + 1) <= xp) {
    level += 1;
  }
  return level;
}

const LEVEL_TITLES: ReadonlyArray<readonly [number, string]> = [
  [1, 'Newcomer'],
  [2, 'Starter'],
  [3, 'Verifier'],
  [5, 'Contributor'],
  [8, 'Striver'],
  [12, 'Achiever'],
  [20, 'Veteran'],
  [35, 'Legend'],
];

export function titleForLevel(level: number): string {
  return LEVEL_TITLES.reduce((title, [minimum, name]) => (level >= minimum ? name : title), 'Newcomer');
}

function startOfUtcDay(now: Date): Date {
  return new Date(Date.UTC(now.getUTCFullYear(), now.getUTCMonth(), now.getUTCDate()));
}

export async function grantXp(
  db: PrismaClient,
  input: { userId: string; source: XPEventSource; refId: string; now: Date },
): Promise<number> {
  const alreadyGranted = await db.xPEvent.findUnique({
    where: { userId_source_refId: { userId: input.userId, source: input.source, refId: input.refId } },
  });
  if (alreadyGranted) {
    return 0;
  }
  const social = SOCIAL_SOURCES.includes(input.source);
  const cap = social ? DAILY_CAPS.SOCIAL : DAILY_CAPS.CHALLENGE;
  const totals = await db.xPEvent.aggregate({
    where: {
      userId: input.userId,
      createdAt: { gte: startOfUtcDay(input.now) },
      source: { in: social ? [...SOCIAL_SOURCES] : [...CHALLENGE_SOURCES] },
    },
    _sum: { amount: true },
  });
  const awarded = Math.max(0, Math.min(XP_AWARDS[input.source], cap - (totals._sum.amount ?? 0)));
  if (awarded === 0) {
    return 0;
  }
  await db.$transaction([
    db.xPEvent.create({
      data: { userId: input.userId, source: input.source, refId: input.refId, amount: awarded },
    }),
    db.user.update({
      where: { id: input.userId },
      data: { xp: { increment: awarded } },
    }),
  ]);
  return awarded;
}
```

Level projection runs asynchronously from the ledger and recomputes `xpForLevel`, so the grant transaction stays small. The unique constraint on `(user_id, source, ref_id)` is the idempotency key: a retried milestone, vote, or story grant returns zero rather than duplicating XP.

### 6.4.6 Anti-Cheat Duplicate Detection with Perceptual Hashing

Exact duplicates are caught by SHA-256 checksum; near-duplicates by comparing 64-bit perceptual hashes with a Hamming distance threshold that survives resizing and re-compression. Fingerprints are computed after sanitization, so stripped coordinates never persist.

```typescript
import { createHash } from 'node:crypto';
import type { PrismaClient } from '@enigma/db';
import sharp from 'sharp';

export interface ImageFingerprint {
  readonly sha256: string;
  readonly pHash: string;
}

export function computePerceptualHash(pixels: Uint8Array, width: number, height: number): string {
  const samples: number[] = [];
  for (let row = 0; row < 8; row += 1) {
    for (let column = 0; column < 8; column += 1) {
      const x = Math.floor((column * width) / 8);
      const y = Math.floor((row * height) / 8);
      samples.push(pixels[y * width + x] ?? 0);
    }
  }
  const mean = samples.reduce((sum, value) => sum + value, 0) / samples.length;
  return samples.map((value) => (value >= mean ? '1' : '0')).join('');
}

export function hammingDistance(left: string, right: string): number {
  let xor = BigInt(`0b${left}`) ^ BigInt(`0b${right}`);
  let distance = 0;
  while (xor > 0n) {
    distance += Number(xor & 1n);
    xor >>= 1n;
  }
  return distance;
}

export const DUPLICATE_HAMMING_THRESHOLD = 6;

export async function sanitizeEvidence(buffer: Buffer): Promise<Buffer> {
  return sharp(buffer, { failOn: 'error' }).rotate().toBuffer();
}

export async function screenDuplicate(
  db: PrismaClient,
  challengeId: string,
  print: ImageFingerprint,
): Promise<{ duplicate: boolean; kind?: 'EXACT' | 'NEAR'; distance?: number }> {
  const exact = await db.submission.findFirst({
    where: { checksum: print.sha256 },
    select: { id: true },
  });
  if (exact) {
    return { duplicate: true, kind: 'EXACT', distance: 0 };
  }
  const candidates = await db.submission.findMany({
    where: { challengeId, pHash: { not: null } },
    select: { pHash: true },
    take: 500,
  });
  for (const candidate of candidates) {
    if (candidate.pHash === null) {
      continue;
    }
    const distance = hammingDistance(print.pHash, candidate.pHash);
    if (distance <= DUPLICATE_HAMMING_THRESHOLD) {
      return { duplicate: true, kind: 'NEAR', distance };
    }
  }
  return { duplicate: false };
}
```

Sanitization decodes, auto-orients, and re-encodes each upload, which drops EXIF, GPS, and device tags before the object reaches S3. Only the sanitized bytes and their checksum are stored.

### 6.4.7 Quiz Integrity

Quiz questions are drawn from a bank in a per-attempt order derived from a seeded hash, so two members on the same module receive different sequences. Each attempt carries a checksum over its identity and question set, and per-question timing is checked for plausibility.

```typescript
import { createHash, timingSafeEqual } from 'node:crypto';

export interface QuizQuestion {
  readonly id: string;
  readonly prompt: string;
  readonly options: readonly string[];
  readonly answerIndex: number;
}

export interface QuizAttempt {
  readonly quizId: string;
  readonly userId: string;
  readonly questionIds: readonly string[];
  readonly startedAt: Date;
  readonly checksum: string;
}

export function rotateQuestionBank(
  bank: readonly QuizQuestion[],
  count: number,
  seed: string,
): QuizQuestion[] {
  const ranked = bank.map((question) => ({
    question,
    score: createHash('sha256').update(`${seed}:${question.id}`).digest('hex'),
  }));
  ranked.sort((left, right) => left.score.localeCompare(right.score));
  return ranked.slice(0, count).map((entry) => entry.question);
}

export function computeAttemptChecksum(attempt: Omit<QuizAttempt, 'checksum'>): string {
  return createHash('sha256')
    .update(
      [attempt.quizId, attempt.userId, attempt.startedAt.toISOString(), ...attempt.questionIds].join('|'),
    )
    .digest('hex');
}

export function verifyAttemptIntegrity(attempt: QuizAttempt): boolean {
  const expected = computeAttemptChecksum(attempt);
  return (
    expected.length === attempt.checksum.length &&
    timingSafeEqual(Buffer.from(expected), Buffer.from(attempt.checksum))
  );
}

export const MIN_SECONDS_PER_QUESTION = 8;

export function isAttemptPlausible(secondsPerQuestion: readonly number[]): boolean {
  return secondsPerQuestion.every((seconds) => seconds >= MIN_SECONDS_PER_QUESTION);
}
```

An attempt whose checksum fails or whose timing is implausible is accepted as a submission but routed with a fraud signal to administrator review rather than auto-approval.

### 6.4.8 Atomic Deadline Submission Close

The deadline is enforced by locking the challenge row and comparing the server-assigned time inside the write transaction. The idempotency check runs first, so a retried upload returns the original submission rather than creating a second attempt.

```typescript
import type { PrismaClient, ProofType, Submission } from '@enigma/db';

export class DeadlineClosedError extends Error {
  readonly code = 'DEADLINE_CLOSED';

  constructor(readonly challengeId: string, readonly receivedAt: Date) {
    super(`Submissions closed for challenge ${challengeId} at ${receivedAt.toISOString()}`);
  }
}

interface LockedChallenge {
  readonly id: string;
  readonly state: string;
  readonly end_at: Date;
}

export async function createSubmission(
  db: PrismaClient,
  input: {
    challengeMemberId: string;
    challengeId: string;
    userId: string;
    milestoneId: string | null;
    proofType: ProofType;
    artifactUrls: string[];
    checksum: string;
    idempotencyKey: string;
  },
): Promise<Submission> {
  return db.$transaction(
    async (tx) => {
      const existing = await tx.submission.findUnique({
        where: { idempotencyKey: input.idempotencyKey },
      });
      if (existing) {
        return existing;
      }
      const [challenge] = await tx.$queryRaw<LockedChallenge[]>`
        SELECT id, state, end_at FROM challenges WHERE id = ${input.challengeId} FOR UPDATE
      `;
      const receivedAt = new Date();
      if (!challenge || challenge.state !== 'LIVE' || receivedAt.getTime() > challenge.end_at.getTime()) {
        throw new DeadlineClosedError(input.challengeId, receivedAt);
      }
      const attempt =
        (await tx.submission.count({
          where: { challengeMemberId: input.challengeMemberId, milestoneId: input.milestoneId },
        })) + 1;
      return tx.submission.create({
        data: {
          challengeMemberId: input.challengeMemberId,
          challengeId: input.challengeId,
          userId: input.userId,
          milestoneId: input.milestoneId,
          proofType: input.proofType,
          artifactUrls: input.artifactUrls,
          checksum: input.checksum,
          attempt,
          receivedAt,
          idempotencyKey: input.idempotencyKey,
        },
      });
    },
    { isolationLevel: 'Serializable' },
  );
}
```

Serialization conflicts are retried with bounded backoff, and the unique constraint on `(challenge_member_id, milestone_id, attempt)` guards against two concurrent submissions computing the same attempt number.

## 6.5 Error Handling and Logging

- **Typed domain errors.** Failures such as `ChallengeTransitionError`, `PostingGateError`, and `DeadlineClosedError` carry stable codes and structured context; the tRPC layer maps codes to HTTP statuses, and clients render the denial reason.
- **No silent failures.** Every catch block retries, compensates, or surfaces the error; empty catches and swallowed rejections fail lint.
- **Queue retries.** BullMQ workers use exponential backoff with jitter, bounded attempts, and per-type dead-letter queues. AI jobs are keyed by submission and notification fan-out by recipient and time bucket, so retries cannot duplicate effects.
- **AI provider failures.** Provider calls run with timeouts and a circuit breaker. A failed inference becomes `NEEDS_REVIEW` with a system explanation and routes to admin; verification never fails open to auto-acceptance.
- **Audit logging.** Transitions, verdicts, overrides, appeals, and XP grants append through one audit service, each record carrying actor, request correlation identifier, and server timestamp.
- **Model version logging.** Every AI verdict stores the model version and prompt revision, so a decision remains auditable after a provider update.
- **Structured logs without sensitive data.** Logs are JSON with severity, service, correlation identifier, and event key; evidence bytes, weight values, and tokens are excluded.
- **Operational signals.** Counters track queue depth, AI latency against the 30-second SLA, peer queue age against the 24-hour SLA, scheduler ticks, and dead-letter volume; deadline and queue-age breaches alert immediately.

## 6.6 Coding for Testability

- **Pure domain functions.** The transition map, gate, routing, quorum, and XP arithmetic are pure and total, so TC-01 through TC-12 are unit-tested without a database or network.
- **Deterministic clock.** All time enters through a `Clock` interface; production uses `SystemClock` and tests use `FixedClock`, making cutoff-boundary tests exact.
- **Dependency injection for external clients.** The AI verifier, GitHub client, wearable APIs, and S3 storage are injected interfaces; tests substitute fakes, and contract tests catch provider changes at the boundary.
- **Repositories behind narrow interfaces.** Database access is wrapped in repository functions that expose intent (`findOpenPeerVerifications`, `lockChallengeForSubmission`) rather than raw queries, so service tests can substitute in-memory implementations.
- **Fixture builders.** `packages/testing` provides builders for users, challenges, submissions, and verifications, keeping tests declarative.
- **Layered test mapping.** Unit tests cover domain rules; integration tests cover Prisma transactions, queues, and provider adapters; end-to-end tests walk the five product scenarios from signup through archive.
- **Regression discipline.** Every defect adds a failing case first, and deadline, idempotency, and quorum defects are release blockers because they erode the trust model.
