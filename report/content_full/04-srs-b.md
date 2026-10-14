### 1.3.6 FR 6 — Progression (XP / Levels / Badges)

Progression is the retention engine of Enigma. Every meaningful action is recorded as an immutable `XP_Event`, and a member's XP and level are derived projections over that log rather than hand-edited values. Because progression is earned through verified activity, a displayed level is a durable statement about what the member has delivered under a deadline.

| ID | Requirement | Priority |
|---|---|---|
| FR-6.1 | Award XP on a fixed schedule: join +20, daily check-in/streak +10, milestone +50, verified completion +200, peer vote +15, accurate vote +25, quality post +10, story +5, note +2; each award is written as an `XP_Event` with a source and reference id. | P0 |
| FR-6.2 | Maintain one global cumulative level from total XP using `XP for level L ≈ 100 × L^1.5`; levels carry titles from Newcomer to Legend, with Level 3 unlocking peer voting and Level 5 unlocking challenge creation. | P0 |
| FR-6.3 | Enforce level-gated privileges through the role model so capabilities derive from progression as well as explicit assignment. | P0 |
| FR-6.4 | Track challenge-activity and community-presence streaks with a flame indicator; a missed day resets the streak, offset by one streak-freeze earned per calendar month. | P0 |
| FR-6.5 | Award persistent one-time badges: First Blood, Triple Threat, Verification Guru, Habit Machine, Deadline Slayer, Comeback Kid. | P1 |
| FR-6.6 | Provide community leaderboards (weekly and all-time), a per-challenge progress leaderboard, and a global level leaderboard, snapshotted for archival. | P0 |
| FR-6.7 | Apply anti-farming guardrails: daily XP caps, peer-vote XP credited only when the vote matches the final outcome, and no XP decay in the MVP. | P1 |
| FR-6.8 | Trigger a level-up celebration and a shareable level card with the new level, title, and badges. | P1 |

The level curve is deliberately super-linear: cumulative requirements grow as `100 × L^1.5`, so early levels arrive quickly while later levels demand sustained delivery. The first thresholds are roughly 100 XP for Level 1, 283 for Level 2, 520 for Level 3, 800 for Level 4, and 1,118 for Level 5. This gives a newcomer a fast first reward while making the Level 5 challenge-creation privilege a credible signal of commitment. Titles map across the curve — Newcomer, Verifier, Contributor, Achiever, Veteran, and Legend at Levels 1, 3, 5, 12, 20, and 35.

The worked example below reconstructs Riya's XP path from Scenario 1, where she joins "Learn Python" in Hackathon A, clears eight weekly milestones, publishes one quality post, and completes the challenge.

| Event (Scenario 1) | Rate | Count | XP |
|---|---|---|---|
| Join "Learn Python" | +20 | 1 | +20 |
| Daily check-in / streak awards | +10 | 16 | +160 |
| Weekly quiz milestone submissions | +50 | 8 | +400 |
| Verified challenge completion | +200 | 1 | +200 |
| Quality post (Week 4, liked ≥ 5 times) | +10 | 1 | +10 |
| Stories linked to milestones | +5 | 2 | +10 |
| **Total** | | | **800** |

Against the level curve, 800 XP equals `100 × 4^1.5`, so Riya reaches Level 4 and unlocks the Verifier role. Roughly three-quarters of her XP comes from verified milestone and completion events rather than low-effort social actions, keeping the economy aligned with outcomes. Had she voted in the peer queue, each vote would add +15 XP and each accurate vote a further +25 XP.

### 1.3.7 FR 7 — Feed & Discovery

The feed is where the Liveness Rule becomes visible. Rather than a general timeline, the home feed ranks threads that are currently open, focusing attention on challenges happening now, while discovery lets prospective members evaluate a community through verified proof rather than marketing copy.

| ID | Requirement | Priority |
|---|---|---|
| FR-7.1 | Present live challenge threads from the user's communities ranked by recency and engagement, with a "Live now" section and countdowns. | P0 |
| FR-7.2 | Support sort modes hot, new, top-verified, and burned, each with a documented ranking function. | P0 |
| FR-7.3 | Attach a verified badge to posts whose submissions passed verification and apply a verified weighting bonus to hotness. | P0 |
| FR-7.4 | Provide a discovery tab browsing communities by category, trending challenges by joins and completion, and public proof walls. | P1 |
| FR-7.5 | Provide typo-tolerant search across communities, challenges, and verified members. | P1 |
| FR-7.6 | Produce personalized community suggestions from the user's joined communities and completed categories. | P2 |

Verified weighting is central to the thesis: a verified post reflects an artifact that both the AI and the community validated, so it is a stronger signal than popularity alone. The hotness function therefore combines recency, engagement, and a verification multiplier so verified evidence outranks unverified commentary within the same live window.

### 1.3.8 FR 8 — Notifications

Notifications keep the deadline contract legible. A challenge works only if members know when a milestone closes and when a verdict awaits them, so notification is part of the verification pipeline rather than a marketing channel.

| ID | Requirement | Priority |
|---|---|---|
| FR-8.1 | Send deadline reminders 24h, 6h, and 1h before every milestone and submission deadline, using server time. | P0 |
| FR-8.2 | Notify when an AI verdict is ready, when a peer vote is requested, and when a submission needs administrative review. | P0 |
| FR-8.3 | Deliver social notifications for replies, reactions, mentions, and channel activity, with per-channel and per-class mutes. | P1 |
| FR-8.4 | Send streak reminders with quiet hours, suppressed once a member has checked in that day. | P1 |
| FR-8.5 | Allow administrators to broadcast announcements to all members or a selected role. | P1 |

Deadline reminders are the highest-priority class because they protect timeline integrity; they are scheduled server-side against absolute UTC deadlines, so an incorrect client clock cannot suppress them. If the peer queue idles for 24 hours, the submission escalates automatically to administrative review.

### 1.3.9 FR 9 — Roles & Permissions

Enigma applies role-based access control to every endpoint, blending explicit assignment with progression-derived authority. A member earns the Verifier capability at Level 3 and the Organizer capability at Level 5, while Moderator, Admin, and Platform Admin are assigned, letting committed members graduate into stewardship without manual promotion.

| ID | Requirement | Priority |
|---|---|---|
| FR-9.1 | Support Member, Verifier (Level 3+), Organizer (Level 5+), Moderator, Admin, and Platform Admin, each with a documented capability set. | P0 |
| FR-9.2 | Enforce RBAC on every endpoint, returning 403 on unauthorized calls and recording the attempt in the audit log. | P0 |
| FR-9.3 | Grant Moderators approval or removal of posts, warnings, verdict overrides, and queue management. | P0 |
| FR-9.4 | Grant Admins configuration of the community, roles, challenge rules, and reports. | P0 |

| Role | Key permissions |
|---|---|
| Member | Join challenges and communities; post while live; stories and notes 24/7; comment and react; submit check-ins; earn XP. |
| Verifier (Level 3+) | All Member permissions, plus cast peer votes in the verification queue and earn verifier XP and accuracy credit. |
| Organizer (Level 5+) | All Verifier permissions, plus create challenges subject to administrative approval and maintain drafts and milestones. |
| Moderator | Approve or remove posts, issue warnings and strikes, override verdicts with an audit entry, manage verification and report queues. |
| Admin | Configure the community, roles, proof types, and verification policy; approve late entries; export reports; override any verdict. |
| Platform Admin | Global moderation, community-creation approval, system configuration, and cross-community action where required. |

Two integrity constraints sit on top of the matrix. No member may verify their own submission; own submissions are excluded from a verifier's queue as a hard rule. An administrator who is a participant may not override verdicts affecting that challenge, so a co-administrator must act.

### 1.3.10 FR 10 — Moderation & Anti-Cheat

Trust is the product, so moderation and anti-cheat are first-class requirements rather than operational afterthoughts. The anti-cheat layer runs before AI verification on every submission, and moderation provides the human backstop when automated layers are uncertain or when members report abuse.

| ID | Requirement | Priority |
|---|---|---|
| FR-10.1 | Run AI moderation for profanity, harassment, and self-harm detection, and attach health-advice disclaimers to fitness challenges. | P0 |
| FR-10.2 | Route flagged content through a report queue into the moderator panel with the triggering signal attached. | P0 |
| FR-10.3 | Apply graduated strikes: three produce a 7-day suspension and five a permanent, appealable ban. | P0 |
| FR-10.4 | Strip EXIF and GPS metadata on ingest and detect duplicate or near-duplicate images via perceptual hashing and reverse-image lookup. | P0 |
| FR-10.5 | Fingerprint devices, flag new devices on sensitive claims, and enforce chronology and monotonic timestamp checks. | P0 |
| FR-10.6 | Detect AI-generated or watermarked media, and protect quizzes with rotated question banks and per-attempt IP checksums. | P0 |
| FR-10.7 | Prevent self-verification, track verifier accuracy, and revoke verifier status from systematically inaccurate verifiers. | P0 |
| FR-10.8 | Present plain-language AI explanations and log the model version used for every verdict. | P0 |
| FR-10.9 | Provide underage and medical safety controls, including disclaimers and "consult a professional" notices on medical targets. | P0 |

The anti-cheat controls form concentric layers: duplicate detection stops recycled evidence, metadata stripping removes forgeable provenance signals, device fingerprinting and rate limits stop scripted abuse, and chronology checks catch backdated artifacts. When a control fires it produces an explanation and, where appropriate, routes the case to a moderator, preserving the appeal path; deliberate fraud accumulates strikes toward suspension.

## 1.4 Performance Requirement

Performance targets are expressed as service-level objectives so they can be tested directly. The binding moments are those where members are most sensitive: opening the app and receiving a verdict. Latency targets are measured at the 95th percentile unless stated otherwise.

### Response Time

| Metric | Target |
|---|---|
| App launch on a mid-range device | less than 2.5 s to first interactive screen |
| Feed load (p95) | less than 1 s |
| Live feed update (p95) | less than 150 ms from event to render |
| Media upload acknowledgement | less than 500 ms to accept and begin processing |
| AI verification verdict | at most 30 s; asynchronous result acceptable within this bound |
| Peer verdict | at most 24 h |
| Administrative review | at most 72 h |
| Push notification delivery | within a few seconds of the trigger event |
| Search response | less than 1 s for community, challenge, and member queries |
| Leaderboard read | less than 500 ms from cache |

### Throughput

| Metric | Target |
|---|---|
| Concurrent users (MVP) | 1,000 |
| Design ceiling | 100,000 concurrent challenge submissions |
| Pilot scale | 5,000 users across 50 communities |
| Check-ins per live challenge | at least 0.8 per member |
| Peer votes per user per day | at most 20 |
| Check-in rate limit | one per challenge per 6 hours |
| AI inference budget | less than $0.03 per check-in |
| Notification fan-out | a community broadcast reaches all members without queue collapse |

### Capacity and Reliability

- The platform scales horizontally; the API tier is stateless and the AI pipeline runs on queue-backed asynchronous workers so submission bursts do not block interactive traffic.
- A 99.9% uptime objective applies during challenge periods, with deadline integrity guaranteed by atomic, server-time-authoritative cutoff transactions.
- AI verification targets precision of at least 95% and recall of at least 90%, with medium-confidence cases routed to peers rather than to a fragile automated decision.
- Live feed caching, rate-limit counters, and job queues are held in memory, while durable evidence and audit records live in the relational database and object store.

## 1.5 Design Constraints

The design is bounded by a few non-negotiable constraints that follow from the product thesis. The most important is time integrity: because every challenge is defined by a deadline, the system must never trust a client clock, and all cutoffs must be enforced atomically on the server.

| Area | Constraint |
|---|---|
| **Technology** | Next.js/T3 stack with tRPC, Prisma, PostgreSQL, Redis with BullMQ, AWS S3 using presigned URLs, a CDN, and a multimodal AI provider API. |
| **Platform** | Mobile-first for iOS and Android from a shared core, with a progressive web app for administrative dashboards. |
| **Time integrity** | Server-enforced absolute UTC; no client clock is trusted; submission cutoffs are applied atomically within a single database transaction. |
| **Security** | OAuth with JWT sessions, RBAC on every endpoint, upload scanning, rate limiting, metadata stripping, and encryption in transit and at rest. |
| **Privacy** | Evidence is community-visible by default; sensitive health data is scoped more narrowly; export and delete support GDPR and DPDP compliance. |
| **Regulatory** | No cash staking or betting in the MVP; health disclaimers are mandatory; no enforceable medical claims are permitted. |
| **Cost** | AI inference must remain below $0.03 per check-in; advertising is disallowed because it would degrade trust in evidence. |

The technology constraint fixes a TypeScript-first stack so domain logic, API contracts, and client types are shared, reducing divergence between client and server. The cost constraint forces cheap pre-screens and batched AI calls rather than unlimited model invocation. The privacy and regulatory constraints determine the default visibility of weight and health evidence, which is community-scoped unless the member widens it.

## 1.6 Data Flow Diagram

A data flow diagram describes a system as processes transforming inputs into outputs, connected to external entities, data stores, and flows. Enigma is modelled at two levels. The Level 0 diagram, or context diagram, treats the whole platform as a single process and shows the external entities exchanging data with it: members and administrators, plus OAuth, GitHub, wearable providers, and the AI verification service. The Level 1 diagram decomposes that process into the sub-processes that matter for trust, following a check-in from submission through pre-screening, state validation, AI verification, peer quorum, and administrative review to the verdict record.

The context view makes the boundary explicit: the platform owns challenges, verification, and progression, while identity, source data, and inference are supplied externally under §1.2.3.

![Figure 1.2 — Context diagram (DFD Level 0)](diagrams/dfd-context.png)

The Level 1 view exposes the ordering guarantee that makes verification trustworthy. A check-in is pre-screened for rate-limit, duplicate-hash, and metadata violations before any model is invoked; the state engine then confirms the challenge is Live so a submission cannot attach to an inactive challenge; only then does the AI verifier assign a confidence score and route the submission. High confidence is auto-approved, medium confidence enters the peer queue, and low confidence escalates to administrative review. Every outcome terminates in an append-only verdict record that feeds XP, leaderboards, and notifications.

![Figure 1.3 — DFD Level 1: check-in and verification](diagrams/dfd-level1.png)

### Challenge Lifecycle Flow

The challenge lifecycle is a server-enforced state machine that governs where submissions and posts are permitted. It is the operational expression of the Liveness Rule, and it is intentionally rigid: transitions may only occur forward and only through the state engine.

- **Draft** — the organizer composes the challenge; it is invisible and closed to joins, posts, and submissions.
- **Announced** — the challenge is published with its countdown and proof requirements; members may pre-join, but posting and check-ins remain closed.
- **Live** — the challenge is open. Check-ins and submissions are accepted, posts are permitted on the challenge thread, and the feed treats the thread as active. This is the only state in which the main-stage content channel is open.
- **Ending** — the deadline has passed and submissions have closed atomically at the server cutoff; a configurable grace window resolves final proofs and disputes, and late entries require administrative approval and carry a label.
- **Archived** — the challenge becomes a read-only proof wall. Results, leaderboards, and badges are final, and verified evidence remains inspectable within the community.

Posts are gated by this flow, while stories and notes remain available at all times so the community never goes silent between challenges. Every transition is logged with the acting principal and server timestamp, allowing disputes to be adjudicated against a factual timeline.

## 1.7 Data Dictionary

The data dictionary defines the canonical entities and enumerations implemented by the persistence layer and is the single reference for schema, API, and test design.

| Data | Description | Type / Notes |
|---|---|---|
| `status` | Lifecycle state of a challenge. | enum: draft=0, announced=1, live=2, ending=3, archived=4; forward-only. |
| `verdict` | Outcome of a verification stage. | enum: accept, needs_review, reject. |
| `confidence` | AI confidence score for a submission. | float 0 to 1; routing threshold read from community policy. |
| `User` | A platform account. | id (UUID PK), handle (unique), name, level, xp, streak, badges[], created_at; 1:N to XP_Event. |
| `Community` | A group that owns challenges and channels. | id (UUID PK), name, description, type (public/private), level, admin_ids[], config (join_mode, verify_policy). |
| `Membership` | Association between a user and a community. | user_id, community_id (composite PK), role (member/mod/admin), joined_at, per-community xp. |
| `Channel` | A named room within a community. | id (UUID PK), community_id (FK), name, type (chat/announcements), available 24/7. |
| `Challenge` | A time-boxed, verifiable goal. | id (UUID PK), community_id (FK), title, target_type, target_value, start_at, end_at, state, grace_minutes, created_by. |
| `Milestone` | An intermediate checkpoint inside a challenge. | id (UUID PK), challenge_id (FK), label, due_at, weight, proof_types[]. |
| `ChallengeMember` | A user's participation in a challenge. | challenge_id, user_id (composite PK), joined_at, progress, status (active/dropped/completed), dropped_at. |
| `Post` | Long-form contribution to a challenge thread. | id (UUID PK), challenge_id (FK), user_id (FK), body, media[], links[], status, votes; gated by liveness. |
| `Comment` | A reply to a post or note. | id (UUID PK), post_id or note_id (FK), user_id (FK), body, created_at. |
| `Note` | A short-form channel message. | id (UUID PK), channel_id (FK), user_id (FK), body, created_at; body ≤ 500 characters. |
| `Story` | Ephemeral community media. | id (UUID PK), community_id (FK), user_id (FK), media[], expires_at, linked_milestone_id (nullable); 24-hour TTL. |
| `Submission` | A proof attempt for a milestone or completion. | id (UUID PK), user_id (FK), challenge_id (FK), milestone_id (FK, nullable), proof (type, artifact_urls, metadata), created_at. |
| `Verification` | A single decision in the verification pipeline. | id (UUID PK), submission_id (FK), stage (ai/peer/admin), verdict, confidence, model_version, explanation, decided_at; append-only. |
| `PeerVote` | A verifier's decision on a peer-queue submission. | id (UUID PK), verification_id (FK), verifier_id (FK), vote, reason, created_at, accurate (nullable until correctness pass). |
| `XP_Event` | An immutable award of experience points. | id (UUID PK), user_id (FK), amount, source, ref_id, created_at; profile XP is a projection over these events. |
| `Badge` | A one-time achievement held by a user. | id (UUID PK), user_id (FK), type, earned_at. |
| `LeaderboardSnap` | A point-in-time ranking snapshot. | id (UUID PK), community_id (FK), period, ranking_json, created_at. |

Three conventions apply across every entity. First, all primary keys are application-generated UUIDs, avoiding collisions across shards and preventing enumeration of users or submissions. Second, all timestamps are absolute UTC instants assigned by the server; client times are untrusted metadata, which allows deadline cutoffs to be atomic. Third, the verification record is append-only: an override appends a superseding record rather than mutating the earlier verdict, so the full decision history remains reconstructable for audit. Foreign-key constraints enforce referential integrity, and composite keys on `Membership` and `ChallengeMember` guarantee at most one role per community and one participation record per challenge.
