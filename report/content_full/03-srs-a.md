# 1. Software Requirement Specification

This Software Requirement Specification (SRS) defines the functional and external-interface requirements for Enigma, a mobile-first social platform on which communities run real, time-boxed challenges whose progress is verified by artificial intelligence and peers rather than by self-report. Its purpose is to establish a testable contract between the development team and the project's stakeholders: the pilot communities *Hackathon A* and *Anytime Fitness*, their organizers, and the platform administrators. Requirements are prioritised so the minimum viable product (MVP) can be isolated from later releases, and each carries a stable identifier that traces into the test cases of Chapter 7. Implementation detail is excluded; architectural choices appear in Chapter 5.

## 1.1 Overall Description

Enigma is a standalone product that combines three familiar models: the community-and-thread structure popularised by Reddit, the channels, roles, and progression systems associated with Discord, and an outcome-verification layer no incumbent provides. A community operates a shared calendar of challenges, where a challenge is a goal with a measurable target — lose five kilograms, learn Python, read ten books, ship a working demo — together with a hard start date, a hard end date, defined proof requirements, and intermediate milestones. Members join, submit evidence at each milestone, and receive a verdict produced jointly by an AI pre-screen, a quorum of peer verifiers, and, when necessary, an administrator. The product is scoped to a two-pilot launch: *Hackathon A* for builders and *Anytime Fitness* for gym members pursuing weight-loss goals.

### 1.1.1 Product Functions

The following list summarises the major functions the product provides; each is elaborated as one or more requirements in Section 1.3.

- **Account and identity management.** Users register and authenticate through an OAuth provider or email credential and maintain a persistent profile carrying a handle, avatar, experience points (XP), level, streak, badges, and a verifiable win–loss record. Sensitive attributes such as body weight are protected by granular privacy controls.

- **Community management.** Members browse a directory of public communities and request to join; private communities admit by invitation or administrative approval. Admins configure a community's name, branding, category tags, join mode, permitted proof types, and verification policy, and can create moderated channels.

- **Challenge lifecycle management.** Organizers create challenges with a title, description, target metric, start and end timestamps, milestones, proof requirements, and an optional participant cap. The platform enforces a five-state lifecycle — `Draft`, `Announced`, `Live`, `Ending`, `Archived` — and applies deadlines atomically against the server clock.

- **Participation and progress tracking.** Members join a challenge in one tap, receive a per-member progress bar, and are tracked against numeric, binary, or checklist-style targets. Milestones carry due dates and missed-milestone warnings, with leaderboard flags for repeated misses.

- **Liveness-gated content.** Posts may be created on a challenge thread only while that challenge is `Live`; outside the live window the thread stays readable and comments remain open. Stories (photo or video, expiring after twenty-four hours) and Notes (short channel messages) remain available at all times.

- **Dual verification pipeline.** Every submission passes an anti-cheat pre-screen and then an AI verifier that returns a verdict, a confidence score, and a plain-language explanation. Submissions route to automatic approval, to a peer queue for members at Level 3 or above, or to administrative review according to that confidence, and any verdict can be overridden by an administrator with a logged audit record.

- **Progression economy.** The platform awards XP for joining, checking in, reaching milestones, completing challenges, and peer verification. XP accumulates into global levels that unlock peer voting at Level 3 and challenge creation at Level 5, and drives streaks, badges, and community, challenge, and global leaderboards.

- **Discovery, feed, and notifications.** A home feed surfaces live challenge threads with countdowns and engagement ranking; a discovery surface exposes communities, trending challenges, and archived proof walls; and notifications deliver deadline reminders, verdicts, and social activity.

- **Moderation, trust, and safety.** The product provides reporting, a moderation queue, graduated strikes and suspensions, automated content moderation, and a multi-layered anti-cheat subsystem covering duplicate detection, metadata integrity, device fingerprinting, and verifier integrity.

### 1.1.2 User Characteristics

Enigma serves four primary personas whose needs shape the requirements that follow.

| Persona | Description | Job-to-be-done |
|---|---|---|
| **Riya** (CS student, Hackathon A) | New member; wants to learn Python and build a repo | Join challenges, submit quiz/repo proof, earn XP, level up |
| **Arjun** (gym member, Anytime Fitness) | Wants to lose 5 kg in 12 weeks | Weigh-in via scale photo, sync wearable data, win leaderboard |
| **Sara** (admin/fitness trainer) | Runs a community | Create challenges, review verdicts, override disputes, export reports |
| **Dev** (junior member) | Wants to earn verifier XP | Vote on peer queue, reach Level 5, create challenges |

These personas map onto four broader audiences. **Members** are the largest group and need a low-friction path from discovery to verified completion, as Riya demonstrates by completing "Learn Python" at Level 4. **Community founders and administrators**, like Sara, need configuration depth and reporting to show outcomes without spreadsheets. **Peer verifiers**, like Dev, arbitrate submissions for XP and reputation and need an efficient, conflict-free queue. **Lurkers and observers** browse feeds and proof walls before committing; their conversion depends on a platform that looks alive between challenges, the role served by Stories and Notes. Common characteristics across audiences are moderate smartphone literacy, intermittent connectivity, and sensitivity to personal health data.

### 1.1.3 General Constraints

The following constraints bind any acceptable solution and influence requirement priorities.

- **Mobile-first delivery.** The primary client is a mobile application for iOS and Android. Web and progressive-web-app surfaces exist for administrative dashboards and reporting rather than core participation.

- **Server-authoritative time.** The server clock, in UTC, is the single source of truth for challenge deadlines, story expiry, and streak computation. Client clocks are never trusted, and submissions close atomically at the cutoff so clock skew cannot extend a deadline.

- **No cash staking in the MVP.** Competitively staked cash pools are excluded because of legal and payment-processing complexity. They are deferred and gated behind legal and payment-partner review.

- **English-first, internationalisation-ready.** User-facing content is authored in English in the MVP, but strings, dates, and numbers must be externalisable for future localisation into Hindi and other regional languages.

- **No general-purpose feed.** Enigma is not a generic social network. There is no unconstrained friend timeline or public wall; posting on a challenge thread is gated by liveness, and anonymous posting is disallowed because accountability requires a verified identity.

### 1.1.4 Assumptions and Dependencies

The specification rests on the following assumptions and dependencies; if any proves false, the affected requirements must be revisited.

- **Affordable AI verification.** A multimodal large-language-model and vision provider is available at consumer scale with an inference cost below US$0.03 per check-in. The verification economy depends on this cost envelope remaining viable at pilot volumes.

- **Third-party data contracts.** OAuth providers such as Google, and wearable and health platforms including Apple Health, Google Fit, Garmin, Strava, and Whoop, continue to expose the authentication flows and data contracts assumed in Section 1.2.3, including user-consented activity, workout, and weight series.

- **Pilot community resourcing.** Hackathon A and Anytime Fitness supply the captain, moderator, and administrative effort required to populate peer-verification queues and moderate content. Peer verification is human-in-the-loop and needs an engaged verifier base.

- **Baseline completion assumptions.** The assumption that fewer than ten per cent of people who join a free, unstructured goal complete it is treated as a conservative baseline; Enigma targets a verified completion rate of at least forty-five per cent. If the true baseline differs materially, the success metrics must be recalibrated rather than the requirements changed.

## 1.2 External Interface Requirements

This section describes the interfaces through which users and external systems interact with Enigma: the user-facing screens, the hardware capabilities the app uses, the integrated software services, and the communication protocols carrying data between client and server.

### 1.2.1 User Interfaces

The mobile application is organised around five primary tabs, each mapping to a distinct job in the participation loop.

- **Home.** Surfaces live challenges, live countdowns, current streaks, and a one-tap check-in action.

- **Community.** Presents the per-community feed of live challenge threads, channels, the story ring, the challenge list (past, current, and future), and the member and leaderboard views.

- **Verify.** Hosts the peer-verification queue for eligible members, along with a record of their past verdicts and verification accuracy.

- **Discover.** Exposes communities by category, trending challenges, and archived proof walls.

- **Profile.** Displays the member's level, XP breakdown, badges, streaks, and challenge history.

A separate web administrative dashboard provides community settings, a challenge-creation wizard, the verification and moderation queues, and reporting with CSV export. Across both surfaces, requirements demand an application launch under 2.5 seconds on mid-range devices; conformance with WCAG 2.1 AA, including contrast, alternative text, and screen-reader labels; and resumable, thumbnail-optimised media upload so evidence capture works on unreliable connections.

### 1.2.2 Hardware Interfaces

The application uses the device capabilities below. Where a capability captures personal or health-related data, the interface includes the safeguards noted.

| Device | Interface |
|---|---|
| Camera (photo/video) | Evidence capture for check-ins; EXIF/GPS stripped on ingest |
| Connected scale | Weight captured via photo proof (AI OCR reads display) |
| Wearables (Apple Watch, Garmin, Fitbit, Whoop) | Step/workout/weight series via vendor APIs (Phase 1) |
| Notifications | Push notification services (APNs / FCM) |

Weight proof therefore needs no proprietary device: the scale photo is read and validated by the AI verifier, with wearable trend data as corroboration.

### 1.2.3 Software Interfaces

Enigma integrates with the external software services summarised below. Each interface is governed by a defined protocol or contract and serves a specific verification or platform purpose.

| Interface | Protocol / Contract | Purpose |
|---|---|---|
| OAuth / Social login (Google, email) | OAuth 2.0 / OpenID Connect | Authentication, JWT sessions |
| GitHub | REST API | Repo existence, commit freshness, README check for coding proof |
| Apple Health / Google Fit / Garmin / Strava / Whoop | Vendor REST/OAuth | First-party wearable data for fitness verification |
| AI verification service | HTTP (async job queue) | Media sanity, duplicate/reverse-image, confidence scoring |
| PostgreSQL | SQL | Relational data (users, communities, challenges, submissions, votes) |
| Redis + BullMQ | in-memory / queue protocol | Rate limits, job queues, live feed caching |
| Object store (AWS S3) | S3 API + presigned URLs | Evidence media, audit logs, tamper-evident log storage |

The relational store holds authoritative domain records; the queue layer absorbs asynchronous verification work and enforces rate limits; the object store keeps evidence media and append-only audit logs.

### 1.2.4 Communication Interfaces

Communication between client and platform uses three complementary channels. A REST interface, carried alongside a WebSocket connection, provides the primary request–response and real-time paths for feeds, chat, and live verdicts. Server-sent real-time events push countdown updates, verdict outcomes, and feed changes to connected clients without a poll. Push notifications reach members through the platform notification services for deadline reminders, verification outcomes, and community activity, even when the app is closed.

![Figure 1.1 — Enigma use-case diagram](diagrams/use-case.png)

The use-case diagram shows the actors that interact with the platform and the principal use cases each participates in. Members drive the participation loop through sign-up, community and challenge joining, proof submission, gated posting, story and note creation, and progression and leaderboard viewing. Verifiers at Level 3 or above operate the peer queue. Administrators and organizers create challenges, review and override verdicts, resolve appeals, export reports, and manage communities; platform administrators hold oversight of community management.

### Use Case Overview

| Use case | Actor | Description |
|---|---|---|
| Sign up / Log in | Member | Registers or authenticates via OAuth or email and establishes a session |
| Join Community | Member | Discovers a community and joins it through open access or approval |
| Join Challenge | Member | Enrols in a challenge and initialises a personal progress record |
| Submit Proof (Check-in) | Member | Submits evidence for a milestone or completion and enters verification |
| Post / Comment (Live only) | Member | Creates or replies to posts while a challenge is Live |
| Post Story / Note (24/7) | Member | Publishes ephemeral stories or short notes at any time |
| Vote on Peer Queue | Verifier (L3+) | Reviews medium-confidence submissions and casts a vote |
| Create Challenge | Admin / Organizer | Configures a challenge with targets, dates, and proof requirements |
| Review / Override Verdict | Admin / Organizer | Inspects a submission and overrides an AI or peer verdict with an audit entry |
| Resolve Appeal | Admin / Organizer | Reviews a rejected submission on appeal and issues a final decision |
| Earn XP / Level Up | Member | Accumulates XP from verified activity and reaches new levels |
| View Leaderboards | Member | Views community, challenge, and global rankings |
| Export Completion Report | Admin / Organizer | Generates and exports per-member outcome data after archiving |
| Manage Community | Admin / Organizer, Platform Admin | Configures community settings, roles, and moderation policy |

Together these use cases cover every role and all five mobile tabs and form the spine the Chapter 7 scenarios exercise from registration through completion.

## 1.3 Functional Requirements

The requirements below are grouped into functional areas, each identified as FR-x.y. Priorities use a three-level legend: **P0** denotes a requirement that must be present in the MVP, **P1** denotes the first post-MVP release, and **P2** denotes a later version. Identifiers are stable, so every requirement maps to one or more test cases in Chapter 7 and back to a product goal. Each subsection states its intent, presents its requirements in tabular form, and closes with acceptance criteria and rationale.

### 1.3.1 FR 1 — User Accounts & Authentication

This area establishes identity, the persistent user profile, and the privacy controls on which accountable participation depends.

| ID | Requirement | Priority |
|---|---|---|
| FR-1.1 | User signs up/logs in via OAuth (Google) or email; sessions via JWT | P0 |
| FR-1.2 | Profile: handle, avatar, level, XP, streak, badges, verification record (W-L) | P0 |
| FR-1.3 | Granular privacy controls for sensitive data (weight/health: community-only default) | P0 |
| FR-1.4 | GDPR/DPDP compliance: export and delete account data | P1 |
| FR-1.5 | Draft posts/stories offline with sync queue on reconnect (honest timestamps) | P1 |

Acceptance for FR-1.1 requires that a session established through OAuth yields a valid JWT honoured on every protected endpoint, and that role-based access control rejects unauthorised calls. FR-1.2 and FR-1.3 ensure that a member's public identity communicates earned progress while sensitive health data defaults to community-only visibility, a precondition for the fitness pilot. FR-1.4 and FR-1.5 follow in the first release, since export and delete obligations and offline draft integrity are compliance and reliability concerns that need not block the core launch.

### 1.3.2 FR 2 — Communities

This area defines how communities are discovered, joined, configured, and organised.

| ID | Requirement | Priority |
|---|---|---|
| FR-2.1 | Browse directory of public communities; request to join; private = invite/approval | P0 |
| FR-2.2 | Community profile: name, description, avatar, banner, tags, member count, challenge history, Community Level | P0 |
| FR-2.3 | Configure join mode, proof types, verification policy | P0 |
| FR-2.4 | Channels (`#build-logs`, `#gym-checkins`, `#general`) | P1 |
| FR-2.5 | Pin key threads; community XP/perks at higher community levels | P1/P2 |

The join and configuration requirements are P0 because the pilot cannot operate without at least one public community and one invite-based community, matching Anytime Fitness and Hackathon A respectively. Channels, pinned threads, and community-level perks are deferred because they extend engagement rather than enable the core verification loop. Acceptance requires that an invite-only community rejects a non-invited join attempt, and that a verification-policy change applies to challenges created thereafter.

### 1.3.3 FR 3 — Challenge Engine

The challenge engine is the heart of the product: it defines the goal, its proof requirements, its deadlines, and its lifecycle.

| ID | Requirement | Priority |
|---|---|---|
| FR-3.1 | Create challenge: title, description, target metric, start/end date, milestones, proof requirements, max participants | P0 |
| FR-3.2 | Server-enforced lifecycle: `Draft → Announced → Live → Ending → Archived` | P0 |
| FR-3.3 | One-tap join; participant list; per-member progress bar | P0 |
| FR-3.4 | Target types: numeric (kg/km/books/commits), binary (ship demo/pass quiz), checklist milestones | P0 |
| FR-3.5 | Milestones with due dates; missed-milestone warnings; repeated misses flag leaderboard | P0 |
| FR-3.6 | Hard deadline enforcement: submissions close atomically at server cutoff; late entries admin-approved + labeled | P0 |
| FR-3.7 | Live countdown, member count, completion %, top-progress members | P0 |
| FR-3.8 | Configurable grace period for final submissions | P1 |
| FR-3.9 | Recurring challenge templates | P1 |
| FR-3.10 | Completion report + CSV export for admins | P1 |
| FR-3.11 | Private challenges (invite-only cohort) | P1 |

Acceptance for FR-3.2 requires that transitions occur only in the defined order and that an invalid transition such as `Archived → Live` is rejected. FR-3.6 makes deadlines meaningful: a submission received exactly at the cutoff is accepted, while one received immediately after is rejected unless an administrator approves it as a labelled late entry. The three target types in FR-3.4 let one engine serve a numeric weight-loss challenge, a binary "ship a demo" challenge, and a checklist reading challenge. Grace periods, recurring templates, completion reports, and private cohorts are scheduled for the first release.

### 1.3.4 FR 4 — Content Rules (Liveness Rule)

The liveness rule is the defining content decision of Enigma: the main feed stays focused on an active outcome, while lightweight content keeps the community alive at all times.

| ID | Requirement | Priority |
|---|---|---|
| FR-4.1 | **Posts** only allowed while a challenge is **Live**; thread read-only before/after (comments allowed) | P0 |
| FR-4.2 | Every post thread-rooted to a challenge or community board | P0 |
| FR-4.3 | **Stories**: photo/video-only, auto-expire 24h, count toward streaks, available 24/7 | P0 |
| FR-4.4 | **Notes**: text ≤ 500 chars, replies + reactions, small social XP, available 24/7 | P0 |
| FR-4.5 | Posts support text, images, video, links, PDFs/CSVs, embedded proof UI | P0 |
| FR-4.6 | Voting on posts enabled only while challenge Live (anti vote-brigading) | P1 |
| FR-4.7 | Archived challenges readable as "proof wall" showcases | P1 |
| FR-4.8 | Link a Story/Note to a challenge milestone for streak credit | P1 |

Acceptance for FR-4.1 requires that an attempt to create a post when no challenge is live is rejected and the feed shows a locked state, and that the thread becomes writable again only if a challenge returns to the live state. FR-4.3 requires a story to be removed automatically twenty-four hours after publication. The rationale is deliberate: an unconstrained feed drifts from the community's mission, so posting is constrained to the live window, while Stories and Notes provide a permanent, low-effort layer that prevents the community from appearing dead between challenges. Restricting voting to the live window prevents post-hoc vote brigading, and archived proof walls convert completed challenges into durable social proof.

### 1.3.5 FR 5 — Verification Pipeline

The verification pipeline is the platform's core trust mechanism and its principal differentiator, combining automated screening, peer judgement, and administrative oversight.

| ID | Requirement | Priority |
|---|---|---|
| FR-5.1 | Submissions require a valid proof type; invalid rejected client-side | P0 |
| FR-5.2 | AI verifier per proof type: photo (OCR/EXIF/duplicate), video (motion/frozen), link (repo/README), quiz (question bank, pass threshold), wearable (plausibility/monotonicity) | P0 |
| FR-5.3 | AI returns verdict + confidence (0–1) + human-readable explanation shown to submitter | P0 |
| FR-5.4 | Routing: confidence ≥0.90 → auto-verified; 0.50–0.89 → peer quorum (3+ votes); <0.50 → admin review | P0 |
| FR-5.5 | Peer voting by Level ≥ 3 members; 3 agreeing votes decide; split → admin | P0 |
| FR-5.6 | Verifier XP; "Trusted Verifier" badge for high accuracy | P0 |
| FR-5.7 | Admin override of any verdict, logged | P0 |
| FR-5.8 | Appeal path for rejections (admin review with AI explanation attached) | P1 |
| FR-5.9 | Anti-cheat pre-screen: duplicate detection, metadata checks, device fingerprinting, rate limits | P0 |
| FR-5.10 | Weight-loss policy: scale photo + wearable trend cross-check, monotonic-down trend | P1 |
| FR-5.11 | SLA: AI verdict ≤ 30s; peer verdict ≤ 24h; admin review ≤ 72h | P0 |

Acceptance for FR-5.4 requires that a submission with confidence 0.95 routes to automatic approval, 0.76 routes to the peer queue, and 0.40 routes to administrative review. FR-5.5 requires that a split verdict of two accepts and one reject escalates to an administrator rather than being decided by simple majority. FR-5.9 guarantees that anti-cheat checks run before any verdict, so duplicate evidence, inconsistent metadata, suspicious device behaviour, and submission flooding are caught early. The pipeline deliberately favours human-in-the-loop handling for medium-confidence cases, since rejecting legitimate evidence harms trust more than extra manual review. FR-5.8 and FR-5.10 extend the pipeline with an appeal path and a fitness-specific cross-check policy in the first release.
