# Enigma

**Software Engineering Project Report**

**Submitted by:** Vansh Garg, Natya Vidhan Biswas, Sarthak Maurya, Sehej Arora

**Supervisor:** Prof. Anjali Thukral

**2026**

Department of Computer Science
Keshav Mahavidyalaya
University of Delhi

---

## Content

| # | Chapter | Page |
|---|---|---|
| — | Problem Statement | 5 |
| — | Process Model | 6 |
| 1 | Software Requirement Specification | 7 |
| | 1.1 Overall Description | 7 |
| | 1.1.1 Product Functions | 7 |
| | 1.1.2 User Characteristics | 7 |
| | 1.1.3 General Constraints | 7 |
| | 1.1.4 Assumptions and Dependencies | 7 |
| | 1.2 External Interface Requirements | 7 |
| | 1.2.1 User Interfaces | 7 |
| | 1.2.2 Hardware Interfaces | 7 |
| | 1.2.3 Software Interfaces | 7 |
| | 1.2.4 Communication Interfaces | 7 |
| | 1.3 Functional Requirements | 7 |
| | 1.3.1 FR 1 — User Accounts & Authentication | 7 |
| | 1.3.2 FR 2 — Communities | 7 |
| | 1.3.3 FR 3 — Challenge Engine | 7 |
| | 1.3.4 FR 4 — Content Rules (Liveness Rule) | 7 |
| | 1.3.5 FR 5 — Verification Pipeline | 7 |
| | 1.3.6 FR 6 — Progression (XP / Levels / Badges) | 7 |
| | 1.3.7 FR 7 — Feed & Discovery | 7 |
| | 1.3.8 FR 8 — Notifications | 7 |
| | 1.3.9 FR 9 — Roles & Permissions | 7 |
| | 1.3.10 FR 10 — Moderation & Anti-Cheat | 7 |
| | 1.4 Performance Requirement | 7 |
| | 1.5 Design Constraints | 7 |
| | 1.6 Data Flow Diagram | 7 |
| | 1.7 Data Dictionary | 7 |
| 2 | Estimations | 8 |
| | 2.1 Function Points | 8 |
| | 2.2 Efforts | 8 |
| 3 | Scheduling | 9 |
| 4 | Risk Management | 10 |
| 5 | Design | 11 |
| | 5.1 System Design | 11 |
| | 5.2 Data Design | 11 |
| 6 | Coding | 12 |
| 7 | Testing | 13 |
| 8 | References | 14 |

---

# Problem Statement

Modern social media rewards *content* — dance trends, memes, curated lifestyles — rather than *outcomes* (skills acquired, weight lost, books read, startups shipped). People consume and post, but nothing in their real life changes.

Goal-oriented communities (fitness groups, study groups, startup groups) exist on WhatsApp, Discord and Telegram, but suffer three structural failures:

1. **The self-report problem** — progress is self-reported; anyone can claim "lost 2 kg" with no proof.
2. **The no-deadline problem** — goals drag on forever without time pressure, consequence, or reward.
3. **The engagement-without-distraction problem** — Reddit-style threads go off-topic and Discord servers are noisy, drowning a group's mission (lose weight, ship a product) in memes.

**Enigma's answer:** a social platform where communities set *real, time-bound challenges*, members prove outcomes with *evidence*, and progress is certified by **AI + peer verification** — not by self-report. A level-up RPG system (XP, levels, streaks, badges) keeps members committed until the outcome is achieved.

---

# Process Model

The project follows an **iterative process model**: Communication → Planning → Modeling → Construction, looping until deployment.

```
Communication ⇄ Planning → [Modeling ⇄] → Construction → (demo) → Communication ...
        ↑                                                  │
        └──────────────────────────────────────────────────┘
  repeated for Itr-1 → Itr-2 → Itr-3 → ends at Deployment → new cycle
```

| Iteration | Scope | Duration |
|---|---|---|
| **Itr-1** | P0 MVP — 2 pilot communities, challenge engine, check-in + AI pre-screen, peer votes, gated feed, XP/levels/badges, notifications, moderation | 6–8 weeks |
| **Itr-2** | P1 v1 — chat rooms, stakes, source integrations (GitHub/Apple Health/Garmin/Strava/Whoop), analytics, community self-serve | 3–4 months |
| **Itr-3** | P2 v2 — marketplace, sponsored challenges, native apps, B2B "Verified Skills" export | next term |

Each round ends in a demo that feeds feedback back into Communication, re-planning scope against go/no-go gates. Deployment releases the MVP to two pilot communities (**Hackathon A**, **Anytime Fitness**).

---

# 1. Software Requirement Specification

## 1.1 Overall Description

Enigma is a mobile-first social platform where communities run **real-world challenges with hard deadlines**, and progress is proven through **AI + peer-verified evidence**. It blends the structure of Reddit (communities, threaded posts, voting) with the energy of Discord (channels, roles, real-time chat, XP) and adds a game layer (XP, levels, streaks, badges).

**Product perspective:** standalone product; part of a two-pilot launch (Hackathon A — builders; Anytime Fitness — gym members).

### 1.1.1 Product Functions

The product provides the following functions (details in §1.3):

- Community creation, membership, roles, and dashboards.
- Challenge creation with a server-enforced lifecycle: `Draft → Announced → Live → Ending → Archived`.
- Gated content: **Posts** only while a challenge is Live; **Stories** (24h ephemeral) and **Notes** (chat) available 24/7.
- **Dual verification pipeline**: AI pre-screen (image/video/link/quiz/data) → auto-approve, peer quorum, or admin review.
- XP/levels/badges/streaks progression economy with leaderboards.
- Gated feed, discovery, search, and notifications.
- Moderation, reporting, appeals, and multi-layer anti-cheat.

### 1.1.2 User Characteristics

| Persona | Description | Job-to-be-done |
|---|---|---|
| **Riya** (CS student, Hackathon A) | New member; wants to learn Python and build a repo | Join challenges, submit quiz/repo proof, earn XP, level up |
| **Arjun** (gym member, Anytime Fitness) | Wants to lose 5 kg in 12 weeks | Weigh-in via scale photo, sync wearable data, win leaderboard |
| **Sara** (admin/fitness trainer) | Runs a community | Create challenges, review verdicts, override disputes, export reports |
| **Dev** (junior member) | Wants to earn verifier XP | Vote on peer queue, reach Level 5, create challenges |

### 1.1.3 General Constraints

- Mobile-first (iOS + Android); web/PWA for admin dashboards.
- Server time is the authoritative clock; no clock-skew cheating on deadlines.
- No cash staking/betting in MVP (legal complexity).
- English first; i18n-ready for Hindi + regional languages.
- No general-purpose friend/timeline feeds; posting gated by challenge liveness.

### 1.1.4 Assumptions and Dependencies

- AI provider (multimodal LLM + vision) is available at consumer scale with inference cost **< $0.03/check-in**.
- OAuth providers (Google) and wearable APIs (Apple Health, Garmin, Strava, Whoop) expose the data contracts assumed in §1.2.3.
- Pilot communities (Hackathon A, Anytime Fitness) supply captain/moderator resources for verification and moderation.
- Objective completion baseline (<10% typical) is correct; Enigma targets ≥45%.

## 1.2 External Interface Requirements

### 1.2.1 User Interfaces

- **Mobile app (5 tabs):** Home (live challenges, countdowns, streaks), Community (feed, channels, stories, challenges, members), Verify (peer queue), Discover (communities, trending challenges), Profile (level, XP, badges, streaks).
- **Web admin dashboard:** community settings, challenge wizard, verification/mod queue, reports/CSV export.
- **UI requirements:** app launch < 2.5s on mid-range devices; WCAG 2.1 AA (contrast, alt text, screen-reader labels); resumable, thumbnail-optimized media upload.

### 1.2.2 Hardware Interfaces

| Device | Interface |
|---|---|
| Camera (photo/video) | Evidence capture for check-ins; EXIF/GPS stripped on ingest |
| Connected scale | Weight captured via photo proof (AI OCR reads display) |
| Wearables (Apple Watch, Garmin, Fitbit, Whoop) | Step/workout/weight series via vendor APIs (Phase 1) |
| Notifications | Push notification services (APNs / FCM) |

### 1.2.3 Software Interfaces

| Interface | Protocol / Contract | Purpose |
|---|---|---|
| OAuth / Social login (Google, email) | OAuth 2.0 / OpenID Connect | Authentication, JWT sessions |
| GitHub | REST API | Repo existence, commit freshness, README check for coding proof |
| Apple Health / Google Fit / Garmin / Strava / Whoop | Vendor REST/OAuth | First-party wearable data for fitness verification |
| AI verification service | HTTP (async job queue) | Media sanity, duplicate/reverse-image, confidence scoring |
| PostgreSQL | SQL | Relational data (users, communities, challenges, submissions, votes) |
| Redis + BullMQ | in-memory / queue protocol | Rate limits, job queues, live feed caching |
| Object store (AWS S3) | S3 API + presigned URLs | Evidence media, audit logs, tamper-evident log storage |

### 1.2.4 Communication Interfaces

- **REST + WebSocket** between client and API gateway — feeds, chat, live verdicts.
- **Server-sent real-time events** for countdowns, verdict results, and feed updates.
- **Push notifications** for deadlines, verdicts, and community activity.

## 1.3 Functional Requirements

> Priority legend: **P0** = MVP, **P1** = v1, **P2** = v2. IDs are traceable to test cases in §7.

### 1.3.1 FR 1 — User Accounts & Authentication

| ID | Requirement | Priority |
|---|---|---|
| FR-1.1 | User signs up/logs in via OAuth (Google) or email; sessions via JWT | P0 |
| FR-1.2 | Profile: handle, avatar, level, XP, streak, badges, verification record (W-L) | P0 |
| FR-1.3 | Granular privacy controls for sensitive data (weight/health: community-only default) | P0 |
| FR-1.4 | GDPR/DPDP compliance: export and delete account data | P1 |
| FR-1.5 | Draft posts/stories offline with sync queue on reconnect (honest timestamps) | P1 |

### 1.3.2 FR 2 — Communities

| ID | Requirement | Priority |
|---|---|---|
| FR-2.1 | Browse directory of public communities; request to join; private = invite/approval | P0 |
| FR-2.2 | Community profile: name, description, avatar, banner, tags, member count, challenge history, Community Level | P0 |
| FR-2.3 | Configure join mode, proof types, verification policy | P0 |
| FR-2.4 | Channels (`#build-logs`, `#gym-checkins`, `#general`) | P1 |
| FR-2.5 | Pin key threads; community XP/perks at higher community levels | P1/P2 |

### 1.3.3 FR 3 — Challenge Engine

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

### 1.3.4 FR 4 — Content Rules (Liveness Rule)

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

### 1.3.5 FR 5 — Verification Pipeline

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

### 1.3.6 FR 6 — Progression (XP / Levels / Badges)

| ID | Requirement | Priority |
|---|---|---|
| FR-6.1 | XP sources: join +20, daily check-in/streak +10, milestone +50, verified completion +200, peer vote +15, accurate vote +25, quality post +10, story +5, note +2 | P0 |
| FR-6.2 | Levels: cumulative; `XP for level L ≈ 100 × L^1.5`; titles Newcomer→Legend; L3 unlocks peer voting, L5 challenge creation | P0 |
| FR-6.3 | Level-gated privileges (role model) | P0 |
| FR-6.4 | Streaks: activity + presence; flame icon; 1 streak-freeze/month | P0 |
| FR-6.5 | Badges: First Blood, Triple Threat, Verification Guru, Habit Machine, Deadline Slayer, Comeback Kid | P1 |
| FR-6.6 | Leaderboards: community (weekly/all-time), challenge, global | P0 |
| FR-6.7 | Anti-farm: daily XP caps; vote XP only if verdict matches final outcome | P1 |
| FR-6.8 | Level-up celebration + shareable card | P1 |

### 1.3.7 FR 7 — Feed & Discovery

| ID | Requirement | Priority |
|---|---|---|
| FR-7.1 | Home feed = live challenge threads (recency + engagement) with "Live now" + countdowns | P0 |
| FR-7.2 | Feed sorts: hot, new, top-verified, burned | P0 |
| FR-7.3 | Verified badge on posts; verified weighting for hotness | P0 |
| FR-7.4 | Discovery tab: communities by category, trending challenges, proof walls | P1 |
| FR-7.5 | Search across communities, challenges, members | P1 |
| FR-7.6 | Personalized community suggestions | P2 |

### 1.3.8 FR 8 — Notifications

| ID | Requirement | Priority |
|---|---|---|
| FR-8.1 | Deadline notifications 24h/6h/1h before milestones/submissions | P0 |
| FR-8.2 | Verification notifications: verdict ready, peer vote requested, needs review | P0 |
| FR-8.3 | Social: replies, reactions, mentions, channel activity (with mute) | P1 |
| FR-8.4 | Streak reminders + quiet hours | P1 |
| FR-8.5 | Admin community announcements | P1 |

### 1.3.9 FR 9 — Roles & Permissions

| ID | Requirement | Priority |
|---|---|---|
| FR-9.1 | Roles: Member, Verifier (L3+), Organizer (L5+), Moderator, Admin, Platform Admin | P0 |
| FR-9.2 | RBAC enforced on every endpoint | P0 |
| FR-9.3 | Moderator: approve/remove posts, warnings, verdict overrides, queue management | P0 |
| FR-9.4 | Admin: configure community, roles, challenge rules, reports | P0 |

### 1.3.10 FR 10 — Moderation & Anti-Cheat

| ID | Requirement | Priority |
|---|---|---|
| FR-10.1 | AI moderation: profanity, harassment, health-advice disclaimer (fitness), self-harm detection | P0 |
| FR-10.2 | Flag → report queue → mod panel | P0 |
| FR-10.3 | Strikes: 3 = 7-day suspension; 5 = permanent (appealable) | P0 |
| FR-10.4 | Image anti-cheat: EXIF/GPS strip, perceptual-hash duplicate/near-duplicate, reverse-image lookup | P0 |
| FR-10.5 | Device fingerprinting; new-device flag for sensitive claims; chronology/monotonic timestamp checks | P0 |
| FR-10.6 | AI-fraud guards: watermark/artifact detection; quiz IP-checksum with rotated question banks | P0 |
| FR-10.7 | Verifier integrity: no self-verification; accuracy tracked; bad actors lose verifier status | P0 |
| FR-10.8 | Transparency: plain-language AI explanations; model version logged for audit | P0 |
| FR-10.9 | Underage & medical safety: disclaimers, "consult a professional" notices on medical targets | P0 |

## 1.4 Performance Requirement

### Response Time

| Metric | Target |
|---|---|
| App launch (mid-range device) | < 2.5 s |
| Feed load (p95) | < 1 s; live feed < 150 ms p95 |
| Check-in upload + verdict | < 500 ms upload; AI verdict ≤ 10 s (async acceptable) |
| AI verification SLA | ≤ 30 s |
| Peer verdict SLA | ≤ 24 h |
| Admin review SLA | ≤ 72 h |
| Push notification delivery | near-real-time (≤ a few seconds) |

### Throughput

| Metric | Target |
|---|---|
| Concurrent users (MVP) | 1,000 |
| Design ceiling | 100,000 concurrent challenge submissions |
| Pilot scale | 5k users / 50 communities |
| Check-ins per live challenge | ≥ 0.8 per member |
| Peer votes per user/day | ≤ 20 |
| Check-in rate limit | 1 per challenge per 6h |
| AI inference budget | < $0.03 per check-in |

### Capacity & Reliability

- Horizontally scalable; queue-based async AI pipeline.
- 99.9% uptime during challenge periods; deadline integrity atomic at cutoff (server-time authoritative).
- AI accuracy targets: precision ≥ 95% (valid evidence), recall ≥ 90%.

## 1.5 Design Constraints

| Area | Constraint |
|---|---|
| **Technology** | Next.js/T3 stack, tRPC, Prisma, PostgreSQL, Redis + BullMQ, AWS S3 with presigned URLs, CDN, AI provider API |
| **Platform** | Mobile-first (iOS + Android via shared core); PWA/web admin |
| **Time integrity** | Server-enforced UTC; no client clock trust; atomic cutoffs |
| **Security** | OAuth + JWT, RBAC on every endpoint, upload scanning, rate limiting, metadata stripping, encryption at rest/in transit |
| **Privacy** | Evidence visible within community by default; sensitive health data scoped; GDPR/DPDP export & delete |
| **Regulatory** | No cash stakes in MVP; health disclaimers; no medical claims enforceable |
| **Cost** | AI inference < $0.03/check-in; advertising disallowed (degrades evidence trust) |

## 1.6 Data Flow Diagram

**Context diagram (Level 0):**

```
                 ┌──────────────────────┐
   Member ─────► │      Enigma          │ ◄──── OAuth (Google)
   evidence      │                      │ ◄──── GitHub / Wearables
                 │   (challenges,       │ ◄──── AI verification service
   Admin ───────►│   verification,      │
   reports       │   XP, feed)          │
                 └──────────────────────┘
```

**Level 1 — Check-in & Verification flow:**

```
 Member submit check-in (evidence: media/link/quiz/data)
   │
   ▼
┌──────────────┐   pre-checks: rate limit, duplicate hash, metadata
│ API Gateway  │───────────────►┌──────────────────────────┐
└──────┬───────┘                │ Anti-cheat pre-screen     │
       ▼                        └────────────┬─────────────┘
┌──────────────┐        AI confidence        │
│ Challenge &  │ ◄───────────────────────────┤
│ State Engine │   challenge must be Live    │
└──────┬───────┘                             ▼
       │                            ┌──────────────────┐
       ▼                            │ AI Verifier       │
┌──────────────┐      ≥0.90         │ (confidence 0–1)  │
│ Feed / Post  │◄──── auto-approve  └────────┬─────────┘
│ (Verified)   │                             │ 0.50–0.89
└──────────────┘                             ▼
                                   ┌──────────────────┐   <0.50
                                   │ Peer queue (3+)  │──► Admin review
                                   └────────┬─────────┘
                                            ▼
                                   ┌──────────────────┐
                                   │ Verdict + audit  │
                                   │ log (append-only)│
                                   └──────────────────┘
                                            │
                 XP event → Leaderboard → Member profile
```

**Challenge lifecycle flow:**

```
Draft → Announced → Live → (submissions close at cutoff) → Ending → Archived
                                  │                                  │
                             verification window                 proof wall,
                                 (48h grace)                      stats, badges
```

## 1.7 Data Dictionary

| Data | Description | Type / Notes |
|---|---|---|
| `status` | Lifecycle state of a challenge | enum: draft=0, announced=1, live=2, ending=3, archived=4 |
| `verdict` | Verification outcome of a submission | enum: accept / needs_review / reject |
| `confidence` | AI confidence score | float 0–1 |
| `User` | id, handle, name, level, xp, streak, badges[] | 1:N to XP_Event |
| `Community` | id, name, description, type (public/private), level, admin_ids, config (join_mode, verify_policy) | |
| `Membership` | user_id, community_id, role (member/mod/admin), joined_at, per-community xp | |
| `Channel` | id, community_id, name, type (chat/announcements), 24x7 | |
| `Challenge` | id, community_id, title, target_type, target_value, start_at, end_at, state, grace_minutes | state machine §1.6 |
| `Milestone` | id, challenge_id, label, due_at, weight, proof_types[] | |
| `ChallengeMember` | challenge_id, user_id, joined_at, progress, status (active/dropped/completed) | |
| `Post` | id, challenge_id, user_id, body, media[], links[], status, votes | gated by liveness |
| `Comment` | id, post_id/note_id, user_id, body | |
| `Note` | id, channel_id, user_id, body | ≤ 500 chars |
| `Story` | id, community_id, user_id, media[], expires_at, linked_milestone_id? | 24h TTL |
| `Submission` | id, user_id, challenge_id, milestone_id?, proof (type, artifact_urls, metadata) | |
| `Verification` | id, submission_id, stage (ai/peer/admin), verdict, confidence, model_version, explanation, decided_at | append-only audit |
| `PeerVote` | id, verification_id, verifier_id, vote, reason, accurate? | |
| `XP_Event` | id, user_id, amount, source, ref_id, created_at | |
| `Badge` | id, user_id, type, earned_at | |
| `LeaderboardSnap` | id, community_id, period, ranking_json | |

---

# 2. Estimations

## 2.1 Function Points

Function point counting based on the FR set in §1.3 (ILF/EIF/EI/EO/EQ), assuming average complexity.

| Function | Type | Count | Weight | FP |
|---|---|---|---|---|
| User, Community, Membership, Challenge, Submission, Verification, XP/Badge | ILF | 7 | 10 | 70 |
| Wearable/health data (read-only external) | EIF | 1 | 7 | 7 |
| Join/challenge, submit check-in, create community, create challenge, post/comment, vote, upload evidence, report/flag | EI | 8 | 6 | 48 |
| Feed, leaderboard, search, discovery, reports, proof wall | EO | 6 | 5 | 30 |
| Challenge queries, verification queue, notifications lookup | EQ | 5 | 4 | 20 |
| **Total (Unadjusted FP)** | | | | **175** |

Adjusted FP = 175 × [0.65 + 0.01 × Σ(GSC)]. Assuming Σ(GSC) = 25 (avg complexity, incl. performance, distributed processing, online updates) → **AFP = 175 × 0.90 = 158 FP** (~158).

## 2.2 Efforts

Using a language factor for TypeScript/React/Next.js (~40–50 LOC per FP):

- KLOC = 158 FP × 45 ≈ **7.1 KLOC**
- Using COCOMO organic model: `Effort = 2.4 × KLOC^1.05 ≈ 2.4 × 7.5 ≈ 18 person-months` for a nominal team.
- For a **single-developer course project** with scope cut to P0/MVP, adjusted effort ≈ **6–8 person-weeks** (MVPs at Itr-1), plus **2–3 weeks** each for Itr-2/Itr-3 phases.
- Development schedule: ~16 weeks total across three iterations + deployment.

> **Assumption:** function point and effort figures are estimates derived from the FR set; final values calibrated after Phase 0.

---

# 3. Scheduling

Milestones aligned to the iterative process model and PRD roadmap. Durations for a single-developer course timeline.

| # | Milestone | Deliverable | Weeks | Gate / Check |
|---|---|---|---|---|
| 1 | Elicitation & planning | Personas, risk register, KPI plan | 1 | Open questions resolved |
| 2 | Modeling | Domain model, state machine, architecture, DB schema | 2 | Design review |
| 3 | Itr-1 build (P0 core) | Challenge engine, check-in, AI pre-screen, peer votes, gated feed, XP, notifications, moderation | 3–6 | Demo: end-to-end challenge → verified check-in |
| 4 | Itr-1 testing | Unit/integration/E2E, KPI harness | 7 | Completion ≥45%, D7 ≥25%, dispute <2% |
| 5 | Deployment (MVP) | CI/CD, release to 2 pilot communities, load test | 8 | 1000 concurrent, AI cost <$0.03 |
| 6 | Itr-2 (v1) | Chat, stakes, source integrations, analytics, global leaderboards | 9–16 | 20 communities, 10k MAU |
| 7 | Itr-3 (v2) | Marketplace, sponsored challenges, native apps, B2B export | next term | 2+ revenue streams |

---

# 4. Risk Management

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Gamers cheat / frame evidence | High | Leaderboard distrust | Multi-layer anti-cheat (§1.3.10), duplicate + timestamp + peer checks, dispute loop |
| AI false positives block legit members | Med–High | Trust collapse | Human-in-the-loop (medium→peers), per-community thresholds, recall over precision, appeal path |
| Posting gate creates "dead zones" | High | Churn | Chat + stories/notes 24/7, varied challenge windows, weekly challenges |
| Health-safety blowups (fitness) | Med | Legal/reputational | Disclaimers, content review, no medical advice |
| Stakes-related legalities | Med | Legal | Defer to v1; legal + payment partner before shipping; cap sizes |
| Community leaders burn out | Med | Stalled challenges | Templates, auto-cycles, AI moderation, Captain XP + perks |
| Privacy (health data, proofs) | Med–High | Compliance | Community-scoped default privacy, encryption, audit designs, GDPR/DPDP |
| Scope creep in MVP | High | Delayed launch | Strict P0 cut; drop anything not demoable per iteration |
| Deadline disputes (clock skew) | Med | Support load | Server-authoritative timestamps, atomic cutoffs, transparent rules |

---

# 5. Design

## 5.1 System Design

**Architecture:**

```
[Client: Web (React/TS), Native (Flutter/Swift/Kotlin)]
          │  REST + WebSocket (feeds, chat, live verdicts)
          ▼
┌─────────┐   ┌──────────────┐   ┌──────────────────┐
│ API     │   │ Challenge &  │   │ PostingGate      │
│ Gateway │   │ State Engine │   │ (time+state check)│
└────┬────┘   └──────┬───────┘   └────────┬─────────┘
     │               │                    │
     ▼               ▼                    ▼
┌──────────────────────────────────────────────────┐
│ Core services: User, Community, Challenge, Feed, │
│ Chat, Story, Note, Notification, Leaderboard      │
└──────────────────────────────────────────────────┘
     │
     ▼
┌──────────────────────────────────────────────────┐
│ Verification service (AI job queue)  →  Peer q.   │
│ media sanity, plausibility, timestamps, duplicate │
│ hash, reverse-image                               │
└──────────────────────────────────────────────────┘
     ▼
┌──────────────────────────────────────────────────┐
│ Data: PostgreSQL (relational), Redis (queues/rate-│
│ limits), object store (evidence/audit logs)       │
└──────────────────────────────────────────────────┘
```

**Module decomposition:** API gateway; challenge/state engine; posting gate; core services (user, community, challenge, feed, chat, story, note, notification, leaderboard); verification service; anti-cheat module; XP/progression engine.

**Challenge state machine:**

```
Draft → Announced → Live → Ending → Archived
          (pre-join)  (posts open,   (48h grace,   (proof wall,
                       check-ins)     final proofs)  results, badges)
```

## 5.2 Data Design

- **PostgreSQL:** relational core — User, Community, Membership, Channel, Challenge, Milestone, ChallengeMember, Post, Comment, Note, Story, Submission, Verification, PeerVote, XP_Event, Badge, LeaderboardSnap (schema in §1.7).
- **Redis + BullMQ:** job queues (AI verification, notification fan-out), rate-limit counters, live-feed cache.
- **Object store (S3):** evidence media with E2E checksums + stored hashes; append-only tamper-evident verification audit logs.
- **Key design decisions:** `Challenge.state` server-enforced and immutable except through the state machine; `Verification` records append-only (never mutated — overrides logged as new records); timestamps in absolute UTC; indexes on `(challenge_id, user_id)`, `(community_id, state)`, `(verification.status)`.

---

# 6. Coding

- **Stack:** Next.js/T3, tRPC, Prisma, PostgreSQL, Redis + BullMQ, AWS S3 (presigned URLs), OpenAI/multimodal AI, CDN.
- **Structure:** monorepo with `apps/web`, `apps/admin`, `apps/api`, `packages/core` (domain), `packages/verification` (AI pipeline), `packages/anti-cheat`.
- **Key algorithms:**
  - *Verification routing* (FR-5.4): confidence thresholds → auto / peer / admin; rep-weighted peer votes.
  - *XP calculation* (FR-6.1): event-driven `XP_Event` with daily caps and level formula `100 × L^1.5`.
  - *Anti-cheat* (FR-10.4–10.6): perceptual hashing (pHash) for near-duplicate evidence; EXIF/GPS stripping; device fingerprint + rate limits; quiz bank rotation with per-attempt checksums.
  - *Deadline integrity*: absolute UTC server timestamps; atomic submission close via DB transaction at cutoff.
- **Conventions:** TypeScript strict mode, Prisma migrations versioned, server-authoritative state transitions, append-only audit logs. No comments unless required; follow existing patterns.

---

# 7. Testing

## Strategy

Four layers, all mapped to requirements: **unit** (domain/state machine/XP), **integration** (services + DB + queues), **system/E2E** (the five PRD scenarios), and **performance/acceptance** (SLA + KPIs).

### 7.1 Unit Test Cases

| TC | Requirement | Test Case | Expected Result |
|---|---|---|---|
| TC-01 | FR-3.2 | Challenge transitions only in valid order (e.g., Archived→Live) | Rejected by state machine |
| TC-02 | FR-3.6 | Submission at cutoff boundary (atomic close) | Accepted exactly until cutoff; rejected after |
| TC-03 | FR-4.1 | Create post when no challenge Live | Post rejected; feed locked |
| TC-04 | FR-4.3 | Story TTL expiry | Story auto-deleted at 24h |
| TC-05 | FR-5.3 | AI confidence 0.95 | Routes to auto-approve |
| TC-06 | FR-5.4 | AI confidence 0.76 | Routes to peer quorum |
| TC-07 | FR-5.4 | AI confidence 0.40 | Routes to admin review |
| TC-08 | FR-5.5 | 2 accept + 1 reject (split) | Escalates to admin |
| TC-09 | FR-6.1 | XP event sum = profile XP; daily caps applied | Exact totals; no over-cap |
| TC-10 | FR-10.4 | Duplicate image submitted (pHash match) | Rejected as duplicate; fraud flag |
| TC-11 | FR-10.7 | Verifier votes on own submission | Excluded from own queue |
| TC-12 | FR-9.2 | Member calls admin endpoint | 403 Forbidden |

### 7.2 Integration Test Cases

| TC | Requirement | Test Case | Expected Result |
|---|---|---|---|
| TC-13 | FR-5.2 | Submit repo link → GitHub API check (commits, README) | Valid repo verified; empty repo → review |
| TC-14 | FR-5.2 | Quiz submission → anti-plagiarism + time-per-question check | Passing score → AI-Verified |
| TC-15 | FR-5.10 | Scale photo + wearable trend cross-check | Consistent trend → full credit; inconsistent → review |
| TC-16 | FR-1.1 | OAuth signup → JWT → RBAC on endpoints | Session valid; roles enforced |
| TC-17 | FR-8.1 | Milestone deadline → 24h/6h/1h reminders | 3 push notifications delivered |
| TC-18 | FR-1.5 | Offline draft → reconnect → sync queue | Post synced with honest timestamp |
| TC-19 | FR-2.3 | Community join_mode = invite | Non-invited join rejected |

### 7.3 System / End-to-End Test Cases

| TC | PRD Scenario | Test Case | Expected Result |
|---|---|---|---|
| TC-20 | Scenario 1 | First-time user: signup → join "Learn Python" → quiz check-in → AI-verified → challenge archived → Level 4 + First Blood | Full lifecycle completes; XP/badge/leaderboard consistent |
| TC-21 | Scenario 2 | Weight-loss: scale photo → peer verdict → wearable cross-check → leaderboard #1 + Deadline Slayer | 13 submissions verified; proof wall updated |
| TC-22 | Scenario 3 | Verifier clears queue; votes match final verdicts | +XP, accuracy 100%, Level 5 unlocks challenge creation |
| TC-23 | Scenario 4 | Admin runs challenge with dispute; anti-cheat flags collusion; late entry | Override audited; colluders rejected; CSV report generated |
| TC-24 | Scenario 5 | Dropped member rejoins Cohort 2 with carried-over progress | Comeback Kid badge; progress resumes at 40% |

### 7.4 Performance / Acceptance Test Cases

| TC | Requirement | Test Case | Expected Result |
|---|---|---|---|
| TC-25 | §1.4 response time | Feed load p95 with 1000 concurrent users | < 1 s; live feed < 150 ms |
| TC-26 | FR-5.11 | AI verdict latency | ≤ 30 s (≤ 10 s async) |
| TC-27 | §1.4 throughput | Load test 1000 concurrent; soak during a Live challenge | No degradation; 99.9% availability |
| TC-28 | §1.4 AI cost | Inference cost per check-in | < $0.03 |
| TC-29 | §1.4 accuracy | AI precision/recall on pilot data | Precision ≥ 95%, Recall ≥ 90% |
| TC-30 | KPI gates | Completion, D7/D30 retention, dispute rate in pilots | Completion ≥ 45%, D7 ≥ 25%, dispute < 2% |

**Acceptance criteria (go/no-go for MVP):** all TC-01–TC-19 pass; TC-20–TC-24 (scenarios) pass; TC-25–TC-30 meet targets.

---

# 8. References

1. *Enigma — Product Requirements Document* (`Enigma.md`, `Enigma-PRD.md`), Software Engineering Semester 5, 2026.
2. *Enigma — Iterative Process Flow Breakdown* (`process-flow.md`), 2026.
3. *SE Report Template* (`SE Report Template.docx`), Department of Computer Science, Keshav Mahavidyalaya.
4. Habitica — gamified habit RPG: https://habitica.com/
5. Stridekick — group step challenges with wearable verification: https://stridekick.com/
6. StepBet — staked step challenges: https://www.stepbet.com/
7. Strava — club challenges, device-verified activity: https://www.strava.com/
8. Duolingo — streaks/XP/leagues: https://www.duolingo.com/
9. Reddit — community/thread model: https://www.reddit.com/
10. BeReal — daily ephemeral authenticity: https://bere.al/

---

*End of SE Project Report — Enigma.*
