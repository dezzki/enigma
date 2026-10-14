# 1. Software Requirement Specification

Enigma is a mobile-first app where communities run time-boxed challenges verified by AI and peers; this SRS lists its functional and external-interface requirements.

## 1.1 Overall Description

A community runs a calendar of challenges with targets. Members join, submit evidence, and get a verdict from AI, peers, or an admin.

### 1.1.1 Product Functions

- **Accounts:** signup, login, profiles, privacy.
- **Communities:** discovery, joins, settings.
- **Challenges:** server-enforced lifecycle.
- **Participation:** join, progress, gated posts, Stories/Notes.
- **Verification:** AI/peer/admin verdicts; XP and badges.
- **Moderation:** feeds, reporting, anti-cheat.

### 1.1.2 User Characteristics

| Persona | Who | Action |
|---|---|---|
| **Riya** | CS student, Hackathon A | Python challenges |
| **Arjun** | Gym member, Anytime Fitness | Loses 5 kg |
| **Sara** | Admin and trainer | Creates challenges |
| **Dev** | Junior member | Votes, levels up |

### 1.1.3 General Constraints

- **Mobile-first.** Android/iOS app; web for admins.
- **Server time.** UTC controls deadlines.
- **No cash staking.** Paid stakes deferred.

### 1.1.4 Assumptions and Dependencies

- AI under US$0.03 per check-in.
- OAuth and wearables keep data contracts.
- Pilots supply moderators and verifiers.

## 1.2 External Interface Requirements

### 1.2.1 User Interfaces

- **Home:** live challenges, countdowns, streaks, check-in.
- **Community:** threads, channels, stories, members, boards.
- **Verify:** peer-verification queue.
- **Discover:** communities, trends, proof walls.
- **Profile:** level, XP, badges, histories.

### 1.2.2 Hardware Interfaces

Enigma reads cameras, scales, wearables, and push.

| Device | Interface |
|---|---|
| Camera | Evidence capture; GPS stripped |
| Scale | Weight read by AI OCR |
| Wearables | Steps, workouts, weight via APIs |
| Notifications | Push (APNs / FCM) |

### 1.2.3 Software Interfaces

Enigma integrates identity, GitHub, AI, and data stores.

| Interface | Protocol / Contract | Purpose |
|---|---|---|
| OAuth / email | OAuth 2.0 / OIDC | Auth and JWT sessions |
| GitHub | REST API | Repo checks |
| AI service | HTTP queue | Media checks |
| PostgreSQL | SQL | Relational data |

### 1.2.4 Communication Interfaces

- **REST, WebSocket, push** carry feeds, chat, verdicts, and reminders.

![Figure 1.1 — Enigma use-case diagram](diagrams/use-case.png)

### Use Case Overview

Enigma's use cases link members, verifiers, and admins to the challenge lifecycle. Actors act within their roles.

| Use case | Actor | Description |
|---|---|---|
| Sign up / Log in | Member | Authenticate |
| Join Challenge | Member | Enrol and track |
| Submit Proof | Member | Send evidence |
| Post / Comment | Member | Post while Live |
| Vote on Peer Queue | Verifier (L3+) | Review submissions |
| Create Challenge | Admin | Set targets and proof |

## 1.3 Functional Requirements

**P0** is MVP, **P1** next, **P2** later.

### 1.3.1 FR 1 — User Accounts & Authentication

| ID | Requirement | Priority |
|---|---|---|
| FR-1.1 | OAuth/email login, JWT sessions | P0 |
| FR-1.2 | Profile: handle, avatar, level, XP | P0 |
| FR-1.3 | Privacy for sensitive data | P0 |
| FR-1.4 | GDPR/DPDP export and deletion | P1 |
| FR-1.5 | Offline drafts sync with timestamps | P1 |

### 1.3.2 FR 2 — Communities

| ID | Requirement | Priority |
|---|---|---|
| FR-2.1 | Browse public; private need invite | P0 |
| FR-2.2 | Profile: name, tags, level | P0 |
| FR-2.3 | Admins set join and verification policy | P0 |
| FR-2.4 | Channels like #build-logs, #general | P1 |
| FR-2.5 | Pin threads; level perks | P1/P2 |

### 1.3.3 FR 3 — Challenge Engine

| ID | Requirement | Priority |
|---|---|---|
| FR-3.1 | Create: target, dates, milestones, proof | P0 |
| FR-3.2 | Lifecycle `Draft → Announced → Live → Ending → Archived` | P0 |
| FR-3.3 | One-tap join, progress bar | P0 |
| FR-3.4 | Numeric, binary, checklist targets | P0 |
| FR-3.5 | Milestones: due dates, miss flags | P0 |
| FR-3.6 | Close at cutoff; late needs approval | P0 |
| FR-3.7 | Countdown, members, top members | P0 |
| FR-3.8 | Admin grace period for late entries | P1 |
| FR-3.9 | Reusable recurring templates | P1 |
| FR-3.10 | Export completion reports as CSV | P1 |
| FR-3.11 | Invite-only challenges | P1 |

### 1.3.4 FR 4 — Content Rules (Liveness Rule)

| ID | Requirement | Priority |
|---|---|---|
| FR-4.1 | Posts only while **Live**; comments open | P0 |
| FR-4.2 | Post belongs to challenge or board | P0 |
| FR-4.3 | **Stories**: photo/video, 24 h expiry | P0 |
| FR-4.4 | **Notes**: text up to 500 chars | P0 |
| FR-4.5 | Posts: text, media, links, proof | P0 |
| FR-4.6 | Post voting only while Live | P1 |
| FR-4.7 | Archived challenges are proof walls | P1 |
| FR-4.8 | Link Story/Note to milestone for streak | P1 |

### 1.3.5 FR 5 — Verification Pipeline

| ID | Requirement | Priority |
|---|---|---|
| FR-5.1 | Valid proof type required | P0 |
| FR-5.2 | AI checks photo, video, link, quiz, wearable | P0 |
| FR-5.3 | AI: verdict, confidence, explanation | P0 |
| FR-5.4 | ≥0.90 auto-verify; 0.50–0.89 peers; <0.50 admin | P0 |
| FR-5.5 | L3+ vote; three agree decide, split to admin | P0 |
| FR-5.6 | Verifiers earn XP, Trusted Verifier badge | P0 |
| FR-5.7 | Admins override any verdict; logged | P0 |
| FR-5.8 | Rejections appealable to admin | P1 |
| FR-5.9 | Anti-cheat: duplicates, metadata, devices, limits | P0 |
| FR-5.10 | Weight proof vs wearable trends | P1 |
| FR-5.11 | AI 30 s, peers 24 h, admin 72 h | P0 |
