### 1.3.6 FR 6 — Progression (XP / Levels / Badges)

| ID | Requirement | Priority |
|---|---|---|
| FR-6.1 | XP for joins, check-ins, milestones, completion, votes | P0 |
| FR-6.2 | Global level from XP: `XP ≈ 100 × L^1.5` | P0 |
| FR-6.3 | Level-gated privileges via roles | P0 |
| FR-6.4 | Streaks with a flame; missed day resets | P0 |
| FR-6.5 | One-time badges (First Blood, Verification Guru) | P1 |
| FR-6.6 | Weekly, all-time, per-challenge, global leaderboards | P0 |
| FR-6.7 | Daily XP caps; vote XP only when correct | P1 |
| FR-6.8 | Level-up celebration and shareable card | P1 |

### 1.3.7 FR 7 — Feed & Discovery

| ID | Requirement | Priority |
|---|---|---|
| FR-7.1 | Live challenge threads from joined communities | P0 |
| FR-7.2 | Sort hot, new, top-verified, burned | P0 |
| FR-7.3 | Mark verified posts and boost ranking | P0 |
| FR-7.4 | Discovery: categories, trends, proof walls | P1 |
| FR-7.5 | Typo-tolerant search across all content | P1 |
| FR-7.6 | Suggest communities from activity | P2 |

### 1.3.8 FR 8 — Notifications

| ID | Requirement | Priority |
|---|---|---|
| FR-8.1 | Deadline reminders at 24 h, 6 h, 1 h | P0 |
| FR-8.2 | Notify on AI verdicts, peer requests, reviews | P0 |
| FR-8.3 | Social replies, reactions, mentions | P1 |
| FR-8.4 | Streak reminders with quiet hours | P1 |
| FR-8.5 | Admin broadcasts to members or roles | P1 |

### 1.3.9 FR 9 — Roles & Permissions

| ID | Requirement | Priority |
|---|---|---|
| FR-9.1 | Roles: Member, Verifier, Organizer, Moderator, Admin, Platform Admin | P0 |
| FR-9.2 | RBAC on every endpoint; log unauthorized calls | P0 |
| FR-9.3 | Moderators: approvals, warnings, overrides, queues | P0 |
| FR-9.4 | Admins: community config, roles, reports | P0 |

### 1.3.10 FR 10 — Moderation & Anti-Cheat

| ID | Requirement | Priority |
|---|---|---|
| FR-10.1 | AI moderation: profanity, harassment, self-harm; disclaimers | P0 |
| FR-10.2 | Flagged content to moderator report queue | P0 |
| FR-10.3 | Graduated strikes leading to suspension or ban | P0 |
| FR-10.4 | Strip EXIF/GPS; detect duplicate images | P0 |
| FR-10.5 | Fingerprint devices; check timestamp chronology | P0 |
| FR-10.6 | Detect AI-generated media; rotate quiz questions | P0 |
| FR-10.7 | Prevent self-verification; revoke bad verifiers | P0 |
| FR-10.8 | Plain explanations; log the model version | P0 |
| FR-10.9 | Underage and medical safety controls | P0 |

## 1.4 Performance Requirement

### Response Time

| Metric | Target |
|---|---|
| App launch | under 2.5 s |
| Feed load | under 1 s |
| AI verdict | at most 30 s |
| Peer verdict | at most 24 h |
| Admin review | at most 72 h |

### Throughput

| Metric | Target |
|---|---|
| Concurrent users (MVP) | 1,000 |
| Peer votes per user per day | at most 20 |
| Check-in limit | one per 6 hours |
| AI cost | under $0.03 per check-in |

### Capacity and Reliability

The API is stateless and scales; AI runs on queue-backed workers. A 99.9% uptime objective applies during challenge periods.

## 1.5 Design Constraints

| Area | Constraint |
|---|---|
| Technology | Next.js/T3, tRPC, Prisma, PostgreSQL, Redis, S3, AI |
| Platform | Mobile-first iOS/Android, plus web admin |
| Time integrity | Server-enforced UTC; atomic cutoffs |
| Security and privacy | OAuth/JWT, RBAC, upload scanning, GDPR/DPDP |
| Regulatory and cost | No cash staking; health disclaimers; AI under $0.03 per check-in |

## 1.6 Data Flow Diagram

A data flow diagram (DFD) shows how data moves between processes, external entities, and data stores. It exposes a system's inputs, processes, and outputs.

![Figure 1.2 — DFD Level 1: check-in and verification](diagrams/dfd-level1.png)

### Challenge Lifecycle Flow

A challenge moves only through Draft, Announced, Live, Ending, and Archived, and posts are allowed only while it is Live.

## 1.7 Data Dictionary

| Data | Meaning |
|---|---|
| `status` | Challenge lifecycle state. |
| `verdict` | accept, needs_review, reject. |
| `confidence` | AI score from 0 to 1. |
| `User` | Platform account with level and XP. |
| `Community` | Group owning challenges and channels. |
| `Challenge` | Time-boxed, verifiable goal. |
| `Milestone` | Checkpoint inside a challenge. |
| `Post` | Challenge-thread contribution. |
| `Note` | Short channel message, up to 500 chars. |
| `Story` | Ephemeral media; 24-hour life. |
| `Submission` | Proof for a milestone. |
| `Verification` | Append-only decision. |
| `PeerVote` | Verifier's peer-queue decision. |
| `XP_Event` | Immutable experience-point award. |
