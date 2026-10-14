# 5. Design

This chapter translates the requirements into an implementable structure. Section 5.1 specifies the architecture, module decomposition, challenge lifecycle, verification pipeline, posting gate, API surface, and security posture. Section 5.2 specifies the data model, indexing, and integrity rules. The guiding principle is server-authoritative design: every trust-critical rule is evaluated server-side against one clock, and clients only render state.

## 5.1 System Design

The system is a set of independently deployable services behind a single edge and one relational system of record. Commands flow synchronously through typed procedures; AI inference, notification fan-out, and media sweeps run asynchronously on queues; live updates return over WebSocket and server-sent events. State transitions, not clients, are the unit of consistency: a challenge becomes Live when the state engine commits that transition.

### 5.1.1 Architecture Overview

![Figure 5.1 — System architecture](diagrams/architecture.png)

Figure 5.1 shows the five architecture layers.

- **Clients.** A mobile-first application for iOS and Android, a progressive web app for members, and a browser administration dashboard. Clients share generated API types, so rules such as live-only posting are defined once in the domain core.
- **Edge.** The API gateway terminates traffic, verifies sessions, applies rate limits, and dispatches commands. The posting gate sits directly behind it: every write to a challenge-scoped surface passes through the gate before a content service persists data.
- **Core services.** User and authentication, community, challenge and state engine, feed, chat, story and note, notification, and XP and leaderboard services.
- **Verification subsystem.** Anti-cheat pre-screening, the AI verifier on an asynchronous job queue, and the peer queue, decoupled from the request path so inference never blocks a check-in upload.
- **Data layer.** PostgreSQL is the relational system of record; Redis with BullMQ provides queues, rate-limit counters, and hot-feed caching; S3 stores evidence and append-only audit exports.

Two guarantees shape the design. Media does not transit the API tier: clients upload evidence to S3 with short-lived presigned URLs, and submissions reference immutable object keys. Every submission carries an idempotency key, so retries cannot create duplicates. Because the gate and state engine read the primary database before writes, the design stays correct at challenge boundaries.

### 5.1.2 Module Decomposition

| Module | Responsibility | Key interfaces |
|---|---|---|
| API Gateway | Traffic termination, JWT verification, rate limits, dispatch | `auth.verify`, `rateLimit.consume` |
| Challenge and State Engine | Lifecycle ownership, transition validation, boundary scheduling | `transition`, `resolveScheduledTransition` |
| Posting Gate | Liveness Rule evaluation for challenge-scoped writes | `canPost`, typed denials |
| User and Authentication | OAuth, JWT rotation, profile, privacy, level projection | `auth.exchangeCode`, `session.issue`, `me.get` |
| Community | Directory, join modes, membership, roles, community XP | `community.join`, `community.configure` |
| Feed | Home and community feeds over live threads, proof walls | `feed.home`, `feed.community` |
| Chat | Real-time channel messaging, presence, reactions | `note.create`, `reaction.toggle` |
| Story and Note | 24-hour stories and short notes with streak credit | `story.create`, `story.ring`, `expiry.sweep` |
| Notification | Deadline, verdict, and social notifications with quiet hours | `notify.schedule`, `notify.push` |
| XP and Leaderboard | Event-driven XP with daily caps, levels, rankings | `xp.grant`, `level.project` |
| Verification Service | AI jobs, confidence routing, peer quorum, append-only verdicts | `verify.enqueue`, `routeVerification` |
| Anti-Cheat | Rate limits, pHash duplicates, metadata, device, chronology | `screenEvidence`, `fingerprint.match` |

The dependency direction is strictly downward: edge modules depend on core services, and the verification subsystem reaches challenge state only through the state engine. The transition function is the single writer of `Challenge.state`, which makes the lifecycle auditable.

### 5.1.3 Challenge State Machine

![Figure 5.2 — Challenge lifecycle state machine](diagrams/state-machine.png)

Figure 5.2 defines the lifecycle `Draft → Announced → Live → Ending → Archived` as an explicit state machine. The state engine is its only writer, and every transition is validated against a transition map before it is committed.

- **Draft → Announced.** An organizer or administrator publishes a draft. Publication validates the title, description, target, dates, proof types, and milestones, and requires the start time to precede the end time. Rules then become immutable, and `challenge.announced` opens pre-joining and schedules announcements.
- **Draft → cancelled.** A draft, or an announced challenge that has not started, may be cancelled to the terminal state with no downstream effects.
- **Announced → Live.** Automatic. The scheduler compares `start_at` with the server clock; when the time is reached, the state engine locks the row, re-reads the state in the transaction, and commits. Posts, check-ins, and voting open at that instant.
- **Live → Ending.** When the server clock reaches `end_at`, the engine atomically closes submissions and moves the challenge to Ending. A submission whose server-assigned `received_at` is later is rejected by default. New posts and votes stop, comments remain, and verification in flight continues to resolve.
- **Ending → Archived.** Ending is an adjudication window bounded by `grace_minutes`. Queued peer votes drain, escalations and appeals are decided, and a late proof is admitted only by explicit administrator decision, then labeled "Late — admin approved" with a visible progress penalty. When the window elapses, the engine archives the challenge, snapshots the leaderboard, distributes completion XP and badges, and exposes a read-only proof wall.

Three invariants are enforced. Only edges in the transition map are legal; any other request, including Archived to Live, raises `ILLEGAL_CHALLENGE_TRANSITION`. Time-based transitions use server UTC time inside a database transaction, and repeated scheduler ticks are idempotent because state is re-read before the write. The gate consults stored state, so a challenge that has left Live never accepts a post, and configuration freezes at publication.

### 5.1.4 Verification Design

![Figure 5.3 — Verification sequence](diagrams/verification-sequence.png)

Figure 5.3 presents the medium-confidence path. A submission exists only after evidence reaches S3 through a presigned URL, so verification operates on checksummed artifacts. Anti-cheat pre-screening runs on the request path: it enforces the one-check-in-per-six-hours limit, computes an exact SHA-256 checksum and a perceptual hash per image, checks metadata and device fingerprint, and verifies chronology. The AI verifier then consumes a BullMQ job and analyzes by proof type: photographs by OCR and object recognition, videos by motion and frozen-frame analysis, links by repository existence, commit freshness, and README content, quizzes by score, timing, and answer-bank integrity, and wearable series by plausibility and monotonicity. It returns a verdict, a confidence from zero to one, a plain-language explanation, and the model version.

Routing applies two thresholds. Confidence of 0.90 or higher is auto-verified with an `AI-Verified` badge within the 30-second SLA. Confidence from 0.50 to 0.89 enters the peer queue, where members at Level 3 or above vote accept or reject with a short reason; three agreeing votes decide, and a split such as 2–1 escalates to an administrator. Confidence below 0.50, or any fraud signal from the pre-screen, routes directly to administrator review with the AI explanation attached. Peer verdicts are due within 24 hours and admin reviews within 72; an idle queue auto-escalates after 24 hours.

Anti-anchoring is deliberate. Peer verifiers see the artifact and the explanation but never the numeric confidence, and never each other's votes before a quorum forms. Queue selection excludes the submitter, excludes conflicted verifiers, and rotates assignments. Accuracy tracking closes the loop: when a final verdict lands, the accuracy pass marks every vote that agreed. Correct votes earn the base verifier XP of 15 plus an accuracy credit of 25; incorrect votes earn neither and reduce the accuracy ratio. Sustained accuracy below the threshold suspends voting eligibility, and a strong record earns the Trusted Verifier distinction. Every decision is appended to one log carrying actor, stage, confidence, model version, and timestamp, so a verdict's history is reconstructable and overrides never erase earlier records.

### 5.1.5 The Posting Gate

The Liveness Rule is the product's central constraint, and the posting gate enforces it. A long-form Post is a claim on the community's main stage and is permitted only while its challenge is Live. Stories expire after 24 hours and Notes are short channel messages; both remain available around the clock. Comments stay open on read-only threads, while post voting is treated like posting rather than commenting, so an archived thread cannot be brigaded after its outcome is known.

The guard algorithm is intentionally small and total:

1. Resolve the request to a challenge-scoped resource and load the acting user's membership and role; deny non-members before any content logic runs.
2. Load challenge state and `start_at`, `end_at`, and `grace_minutes` from the primary database rather than a cache, because staleness near a boundary would violate the rule.
3. Derive the permitted window for the surface and compare it with the current server UTC time.
4. For submissions, repeat the state and time check inside the write transaction that locks the challenge row, so the cutoff is decided once at commit.
5. Return an allow decision or a typed denial such as `CHALLENGE_NOT_LIVE`, `DEADLINE_PASSED`, or `NOT_JOINED`.

| Surface | Allowed window | Guard condition |
|---|---|---|
| Long-form post | Live only | state = LIVE |
| Check-in or submission | Live until the cutoff; Ending only by admin decision | state = LIVE and now ≤ end_at |
| Post voting | Live only | state = LIVE |
| Comment | Always | membership |
| Story | Always, with a 24-hour TTL | membership |
| Note | Always, up to 500 characters | membership and channel access |

The rationale is threefold. Restricting the main stage to live windows keeps the feed mission-focused. Gating votes to the live window removes post-hoc brigading. Because the gate reads server state, the boundary cannot be shifted by a device clock. Stories and notes keep the community alive between windows, while the thread remains where outcomes are proven.

### 5.1.6 API Design

The API is a typed procedure layer over HTTP and WebSocket. Mutating endpoints require an authenticated JWT and pass role and ownership checks; the table names the minimum role.

| Method and path | Purpose | Auth and role |
|---|---|---|
| POST /auth/oauth/google | Exchange an OAuth code and issue a token pair | Public |
| POST /communities | Create a community | Platform admin |
| POST /communities/{id}/join | Join or request to join per join mode | Authenticated |
| PATCH /communities/{id}/config | Edit join mode, proof types, verification policy | Community admin |
| POST /communities/{id}/challenges | Create a challenge in Draft | Organizer L5+ or admin |
| POST /challenges/{id}/join | Join an announced or live challenge | Member |
| POST /challenges/{id}/transition | Advance lifecycle (publish, cancel, archive) | Community admin |
| POST /challenges/{id}/submissions | Create a check-in from uploaded evidence | Member, gate applies |
| GET /verify/queue | Peer queue assignments for the caller | Verifier L3+ |
| POST /submissions/{id}/votes | Cast an accept or reject vote with a reason | Verifier L3+, not submitter |
| POST /verifications/{id}/override | Override a verdict with justification | Moderator or admin, not participant |
| GET /feed/home | Live threads from joined communities | Authenticated |
| POST /challenges/{id}/posts | Create a long-form post | Member, gate requires Live |
| POST /communities/{id}/stories | Create a story from uploaded media | Member, available 24/7 |
| POST /channels/{id}/notes | Create a note of up to 500 characters | Member, available 24/7 |
| GET /leaderboards | Community, challenge, and global rankings | Authenticated |
| POST /reports | Flag content or behavior for moderation | Member |
| GET /admin/reports | Moderation and report queue | Moderator or admin |
| GET /communities/{id}/reports/export | Completion report as CSV | Community admin |

Media never passes through these endpoints: the client requests a presigned upload target, uploads to S3, and references the returned key in the create call.

### 5.1.7 Security and Privacy Design

- **Identity and sessions.** Authentication uses OAuth 2.0 and OpenID Connect with Google and email login. Access tokens are short-lived JWTs; refresh tokens rotate per use, bind to a device session, and are revocable. Web sessions keep tokens in HTTP-only, SameSite cookies; traffic uses TLS 1.3.
- **Authorization.** Role-based access control runs on every endpoint against the `Role` enum, with level-gated capabilities: peer voting at Level 3, challenge creation at Level 5, channel creation at Level 8, moderator tooling at Level 10. Verifiers never receive their own submissions, and administrators cannot override verdicts on their own submissions.
- **Upload and media security.** Presigned PUT targets are scoped to a content type, size, and key prefix, and expire quickly. Images are decoded and re-encoded server-side, malformed files are rejected, and documents are scanned before storage. Buckets stay private; media is served through short-lived signed CDN URLs.
- **Metadata stripping.** EXIF, GPS coordinates, and device tags are removed on ingest before hashing and storage. Only a stripped derivative and its checksum are retained, protecting locations and removing a channel for backdating.
- **Encryption.** Data is encrypted in transit with TLS 1.3 and at rest, including PostgreSQL volumes and S3 objects. Sensitive health fields such as scale readings and wearable series add column-level encryption, so a database backup alone does not expose them.
- **Privacy scoping.** Evidence is visible within the owning community by default. Weight and health data default to community-only under member control: private, community, or public. Export and deletion workflows satisfy GDPR and DPDP obligations, and deletion resolves every reference, including derived fingerprints.
- **Audit.** Verification decisions and administrative actions are append-only. Each record captures actor, stage, verdict, confidence, model version, and timestamp, and the stream is exported to tamper-evident storage with hash chaining. Overrides are new records, preserving the original decision.

## 5.2 Data Design

The data model separates durable relational state, derived high-frequency state in Redis, and immutable artifacts in object storage. The schema carries the guarantees the product depends on: one membership per community, one vote per verifier, one active attempt per milestone, and an XP ledger that can be recomputed.

### 5.2.1 Entity Relationship Model

![Figure 5.4 — Entity relationship diagram](diagrams/er-diagram.png)

Figure 5.4 presents the core entities and cardinalities.

- **Identity and community.** A `User` participates in many communities through `Membership`, which carries role, join timestamp, and community XP.
- **Challenge scope.** A `Community` owns many challenges; a challenge owns many milestones and `ChallengeMember` records, each one user's participation with a progress value and a status of active, at risk, dropped, or completed.
- **Evidence and verification.** A `ChallengeMember` produces submissions, each optionally targeting one milestone. Every submission is examined through one or more `Verification` records, one per stage, and a peer-stage verification accumulates `PeerVote` records. This makes the verification history append-only by construction.
- **Content.** A `Post` belongs to one challenge and one authoring user. A `Story` is scoped to a community and a user with an expiry, a `Note` belongs to a channel within a community, and comments attach to posts or notes.
- **Progression.** `XPEvent` and `Badge` records belong to a user; the first is the append-only ledger from which level and leaderboard projections are derived.

The diagram elides `Channel`, `Comment`, and `LeaderboardSnap`, which complete the schema below.

### 5.2.2 Relational Schema

The schema below is the Prisma representation. Identifiers are UUIDs, timestamps are UTC, JSON columns hold media descriptors and ranking payloads, and constraints are declared inline so migrations carry the invariants.

```prisma
enum ChallengeState {
  DRAFT
  ANNOUNCED
  LIVE
  ENDING
  ARCHIVED
}

enum Verdict {
  ACCEPT
  NEEDS_REVIEW
  REJECT
}

enum VerificationStatus {
  PENDING
  AUTO_VERIFIED
  PEER_VERIFIED
  ADMIN_VERIFIED
  REJECTED
  ESCALATED
  OVERRIDDEN
}

enum VerificationStage {
  AI
  PEER
  ADMIN
}

enum PeerVoteChoice {
  ACCEPT
  REJECT
}

enum Role {
  MEMBER
  VERIFIER
  ORGANIZER
  MODERATOR
  ADMIN
  PLATFORM_ADMIN
}

enum XPEventSource {
  JOIN
  CHECK_IN
  MILESTONE
  COMPLETION
  PEER_VOTE
  ACCURATE_VOTE
  POST
  STORY
  NOTE
}

enum ProofType {
  PHOTO
  VIDEO
  LINK
  QUIZ
  WEARABLE
  DOCUMENT
  METRIC
}

enum TargetType {
  NUMERIC
  BINARY
  CHECKLIST
}

enum CommunityType {
  PUBLIC
  PRIVATE
}

enum JoinMode {
  OPEN
  APPROVAL
  INVITE
}

enum ChallengeMemberStatus {
  ACTIVE
  AT_RISK
  DROPPED
  COMPLETED
}

model User {
  id        String   @id @default(uuid()) @db.Uuid
  handle    String   @unique
  name      String
  level     Int      @default(1)
  xp        Int      @default(0)
  streak    Int      @default(0)
  createdAt DateTime @default(now()) @map("created_at")

  memberships       Membership[]
  challengeMembers  ChallengeMember[]
  submissions       Submission[]
  peerVotes         PeerVote[]
  posts             Post[]
  comments          Comment[]
  stories           Story[]
  notes             Note[]
  xpEvents          XPEvent[]
  badges            Badge[]

  @@map("users")
}

model Community {
  id           String        @id @default(uuid()) @db.Uuid
  name         String
  description  String
  type         CommunityType @default(PUBLIC)
  joinMode     JoinMode      @default(OPEN)
  level        Int           @default(1)
  verifyPolicy String        @default("AI_PLUS_PEERS") @map("verify_policy")
  createdAt    DateTime      @default(now()) @map("created_at")

  memberships Membership[]
  challenges  Challenge[]
  channels    Channel[]
  stories     Story[]
  notes       Note[]

  @@index([type])
  @@map("communities")
}

model Membership {
  id          String   @id @default(uuid()) @db.Uuid
  userId      String   @map("user_id") @db.Uuid
  communityId String   @map("community_id") @db.Uuid
  role        Role     @default(MEMBER)
  communityXp Int      @default(0) @map("community_xp")
  joinedAt    DateTime @default(now()) @map("joined_at")

  user      User      @relation(fields: [userId], references: [id])
  community Community @relation(fields: [communityId], references: [id])

  @@unique([userId, communityId])
  @@index([communityId, role])
  @@map("memberships")
}

model Challenge {
  id              String         @id @default(uuid()) @db.Uuid
  communityId     String         @map("community_id") @db.Uuid
  createdById     String         @map("created_by_id") @db.Uuid
  title           String
  description     String
  targetType      TargetType     @map("target_type")
  targetValue     Float?         @map("target_value")
  proofTypes      ProofType[]    @map("proof_types")
  startAt         DateTime       @map("start_at")
  endAt           DateTime       @map("end_at")
  graceMinutes    Int            @default(0) @map("grace_minutes")
  state           ChallengeState @default(DRAFT)
  maxParticipants Int?           @map("max_participants")
  createdAt       DateTime       @default(now()) @map("created_at")

  community   Community         @relation(fields: [communityId], references: [id])
  milestones  Milestone[]
  members     ChallengeMember[]
  posts       Post[]
  submissions Submission[]

  @@index([communityId, state])
  @@index([state, endAt])
  @@map("challenges")
}

model Milestone {
  id          String      @id @default(uuid()) @db.Uuid
  challengeId String      @map("challenge_id") @db.Uuid
  label       String
  dueAt       DateTime    @map("due_at")
  weight      Float       @default(1)
  proofTypes  ProofType[] @map("proof_types")
  sortOrder   Int         @map("sort_order")

  challenge   Challenge    @relation(fields: [challengeId], references: [id])
  submissions Submission[]

  @@unique([challengeId, sortOrder])
  @@index([dueAt])
  @@map("milestones")
}

model ChallengeMember {
  id          String                @id @default(uuid()) @db.Uuid
  challengeId String                @map("challenge_id") @db.Uuid
  userId      String                @map("user_id") @db.Uuid
  progress    Float                 @default(0)
  status      ChallengeMemberStatus @default(ACTIVE)
  joinedAt    DateTime              @default(now()) @map("joined_at")
  droppedAt   DateTime?             @map("dropped_at")

  challenge   Challenge    @relation(fields: [challengeId], references: [id])
  user        User         @relation(fields: [userId], references: [id])
  submissions Submission[]

  @@unique([challengeId, userId])
  @@index([challengeId, status])
  @@map("challenge_members")
}

model Submission {
  id                String    @id @default(uuid()) @db.Uuid
  challengeMemberId String    @map("challenge_member_id") @db.Uuid
  challengeId       String    @map("challenge_id") @db.Uuid
  userId            String    @map("user_id") @db.Uuid
  milestoneId       String?   @map("milestone_id") @db.Uuid
  proofType         ProofType @map("proof_type")
  artifactUrls      Json      @map("artifact_urls")
  checksum          String
  pHash             String?   @map("p_hash")
  attempt           Int       @default(1)
  late              Boolean   @default(false)
  idempotencyKey    String?   @unique @map("idempotency_key")
  receivedAt        DateTime  @map("received_at")
  createdAt         DateTime  @default(now()) @map("created_at")

  challengeMember ChallengeMember @relation(fields: [challengeMemberId], references: [id])
  challenge       Challenge       @relation(fields: [challengeId], references: [id])
  user            User            @relation(fields: [userId], references: [id])
  milestone       Milestone?      @relation(fields: [milestoneId], references: [id])
  verifications   Verification[]

  @@unique([challengeMemberId, milestoneId, attempt])
  @@index([challengeId, userId])
  @@index([checksum])
  @@map("submissions")
}

model Verification {
  id           String             @id @default(uuid()) @db.Uuid
  submissionId String             @map("submission_id") @db.Uuid
  stage        VerificationStage
  verdict      Verdict
  status       VerificationStatus @default(PENDING)
  confidence   Float?
  modelVersion String?            @map("model_version")
  explanation  String
  actorId      String?            @map("actor_id") @db.Uuid
  decidedAt    DateTime?          @map("decided_at")
  createdAt    DateTime           @default(now()) @map("created_at")

  submission Submission @relation(fields: [submissionId], references: [id])
  votes      PeerVote[]

  @@index([submissionId, createdAt])
  @@index([status])
  @@map("verifications")
}

model PeerVote {
  id             String         @id @default(uuid()) @db.Uuid
  verificationId String         @map("verification_id") @db.Uuid
  verifierId     String         @map("verifier_id") @db.Uuid
  vote           PeerVoteChoice
  reason         String?
  accurate       Boolean?
  createdAt      DateTime       @default(now()) @map("created_at")

  verification Verification @relation(fields: [verificationId], references: [id])
  verifier     User         @relation(fields: [verifierId], references: [id])

  @@unique([verificationId, verifierId])
  @@index([verifierId, accurate])
  @@map("peer_votes")
}

model Post {
  id          String   @id @default(uuid()) @db.Uuid
  challengeId String   @map("challenge_id") @db.Uuid
  userId      String   @map("user_id") @db.Uuid
  body        String
  media       Json?
  links       Json?
  voteCount   Int      @default(0) @map("vote_count")
  status      String   @default("VISIBLE")
  createdAt   DateTime @default(now()) @map("created_at")

  challenge Challenge @relation(fields: [challengeId], references: [id])
  user      User      @relation(fields: [userId], references: [id])
  comments  Comment[]

  @@index([challengeId, createdAt])
  @@map("posts")
}

model Comment {
  id        String   @id @default(uuid()) @db.Uuid
  postId    String?  @map("post_id") @db.Uuid
  noteId    String?  @map("note_id") @db.Uuid
  userId    String   @map("user_id") @db.Uuid
  body      String
  createdAt DateTime @default(now()) @map("created_at")

  post Post? @relation(fields: [postId], references: [id])
  note Note? @relation(fields: [noteId], references: [id])
  user User  @relation(fields: [userId], references: [id])

  @@index([postId, createdAt])
  @@map("comments")
}

model Channel {
  id          String   @id @default(uuid()) @db.Uuid
  communityId String   @map("community_id") @db.Uuid
  name        String
  type        String   @default("CHAT")
  createdAt   DateTime @default(now()) @map("created_at")

  community Community @relation(fields: [communityId], references: [id])
  notes     Note[]

  @@unique([communityId, name])
  @@map("channels")
}

model Note {
  id          String   @id @default(uuid()) @db.Uuid
  channelId   String   @map("channel_id") @db.Uuid
  communityId String   @map("community_id") @db.Uuid
  userId      String   @map("user_id") @db.Uuid
  body        String   @db.VarChar(500)
  createdAt   DateTime @default(now()) @map("created_at")

  channel   Channel   @relation(fields: [channelId], references: [id])
  community Community @relation(fields: [communityId], references: [id])
  user      User      @relation(fields: [userId], references: [id])
  comments  Comment[]

  @@index([channelId, createdAt])
  @@map("notes")
}

model Story {
  id                String   @id @default(uuid()) @db.Uuid
  communityId       String   @map("community_id") @db.Uuid
  userId            String   @map("user_id") @db.Uuid
  media             Json
  linkedMilestoneId String?  @map("linked_milestone_id") @db.Uuid
  expiresAt         DateTime @map("expires_at")
  createdAt         DateTime @default(now()) @map("created_at")

  community Community @relation(fields: [communityId], references: [id])
  user      User      @relation(fields: [userId], references: [id])

  @@index([communityId, expiresAt])
  @@map("stories")
}

model XPEvent {
  id        String         @id @default(uuid()) @db.Uuid
  userId    String         @map("user_id") @db.Uuid
  amount    Int
  source    XPEventSource
  refId     String?        @map("ref_id") @db.Uuid
  createdAt DateTime       @default(now()) @map("created_at")

  user User @relation(fields: [userId], references: [id])

  @@unique([userId, source, refId])
  @@index([userId, createdAt])
  @@map("xp_events")
}

model Badge {
  id       String   @id @default(uuid()) @db.Uuid
  userId   String   @map("user_id") @db.Uuid
  type     String
  earnedAt DateTime @default(now()) @map("earned_at")

  user User @relation(fields: [userId], references: [id])

  @@unique([userId, type])
  @@map("badges")
}

model LeaderboardSnap {
  id          String   @id @default(uuid()) @db.Uuid
  communityId String?  @map("community_id") @db.Uuid
  challengeId String?  @map("challenge_id") @db.Uuid
  period      String
  ranking     Json
  createdAt   DateTime @default(now()) @map("created_at")

  @@index([communityId, period, createdAt])
  @@map("leaderboard_snaps")
}
```

### 5.2.3 Indexing and Persistence Choices

PostgreSQL carries relational guarantees, Redis latency-sensitive derived state, and S3 immutable artifacts. The index plan follows the access patterns of the feed, the posting gate, the verification queue, and the XP projections.

| Index | Columns | Serves |
|---|---|---|
| memberships_user_community | unique (user_id, community_id) | one membership per community; idempotent joins |
| challenge_members_challenge_user | unique (challenge_id, user_id) | progress reads; duplicate-join prevention |
| challenges_community_state | (community_id, state) | community challenge lists; feed assembly |
| challenges_state_end | (state, end_at) | scheduler sweep for automatic transitions |
| submissions_milestone_attempt | unique (challenge_member_id, milestone_id, attempt) | one active attempt per milestone |
| verifications_submission_created | (submission_id, created_at) | append-only audit traversal |
| verifications_status | (status) | adjudication, escalation and reporting queries |
| peer_votes_verification_verifier | unique (verification_id, verifier_id) | one vote per verifier per verification |
| peer_votes_verifier_accurate | (verifier_id, accurate) | verifier accuracy statistics |
| stories_community_expires | (community_id, expires_at) | story ring queries and TTL sweeps |
| posts_challenge_created | (challenge_id, created_at) | thread pagination |
| xp_events_user_created | (user_id, created_at) | profile XP breakdown and daily cap checks |
| xp_events_source_ref | unique (user_id, source, ref_id) | idempotent XP grants |

Redis and BullMQ absorb work that would otherwise pressure the database: separate queues for AI verification and notification fan-out with retry and dead-letter configuration, rate-limit counters for check-ins, votes, joins, and authentication attempts, and a short-lived cache of the hot home feed invalidated by state transitions. S3 stores evidence under community-scoped prefixes with SHA-256 checksums recorded in the submission row, lifecycle policies for cold evidence, and object-lock retention for audit exports. The verification log is append-only at the database level as well: the application role holds no UPDATE or DELETE grant, and audit exports are hash-chained so gaps are detectable.

### 5.2.4 Data Integrity and Deadline Handling

- **Single clock.** All timestamps are stored and compared in UTC, and the server assigns `received_at` when the submission transaction commits. Client-provided times never influence a deadline decision.
- **Atomic cutoff.** The Live to Ending transition runs in one transaction that locks the challenge row, and submission creation re-reads that row immediately before insert. The boundary is decided once, and same-millisecond submissions are ordered by the database.
- **One submission per milestone.** The unique constraint on `(challenge_member_id, milestone_id, attempt)` permits resubmission after rejection while guaranteeing one active attempt; the service computes the next attempt transactionally, and only an accepted attempt counts toward progress.
- **Idempotency.** Submission creation accepts a client idempotency key with a unique constraint, XP grants are unique on `(user_id, source, ref_id)`, and transition commands are no-ops when the requested state already holds, so retries from the mobile sync queue cannot double-apply.
- **Referential integrity.** Foreign keys protect relationships; deletes are restrictive for audit-bearing verifications and cascade only for dependent media descriptors. Verification rows cannot be deleted by the application role.
- **Grace window integrity.** When configured, late entries are accepted only through an administrator decision recorded as its own verification row, labeled and penalized in progress. The grace window never reopens posting or voting.
- **Transition recovery.** Scheduler outages do not extend deadlines: on recovery the state engine derives missed transitions from stored timestamps and applies each once, and submissions received during a documented outage are adjudicated under the outage-grace rule.
