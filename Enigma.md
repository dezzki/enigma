# Enigma — Product Requirements Document (PRD)

| Field | Value |
|---|---|
| **Product Name** | Enigma |
| **Category** | Social / Community App with Gamification |
| **Status** | Draft v1.0 |
| **Date** | August 2026 |
| **Owner** | Software Engineering — Semester 5 |
| **Tagline** | *Social Challenges with Real Outcomes* |

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Problem Statement](#2-problem-statement)
3. [Vision & Goals](#3-vision--goals)
4. [Target Users & Personas](#4-target-users--personas)
5. [Competitive Landscape](#5-competitive-landscape)
6. [Core Concepts & Terminology](#6-core-concepts--terminology)
7. [Product Pillars](#7-product-pillars)
8. [Functional Requirements](#8-functional-requirements)
   - 8.1 Communities
   - 8.2 Challenges
   - 8.3 Posts, Stories & Notes (Content Rules)
   - 8.4 Verification System (AI + Peer)
   - 8.5 Level-Up System (XP, Levels, Badges)
   - 8.6 Feed & Discovery
   - 8.7 Notifications
   - 8.8 Roles & Permissions
9. [User Flows](#9-user-flows)
10. [Information Architecture](#10-information-architecture)
11. [Data Model (Draft)](#11-data-model-draft)
12. [Non-Functional Requirements](#12-non-functional-requirements)
13. [Trust, Safety & Anti-Cheat](#13-trust-safety--anti-cheat)
14. [Success Metrics (KPIs)](#14-success-metrics-kpis)
15. [Monetization](#15-monetization)
16. [MVP Scope & Roadmap](#16-mvp-scope--roadmap)
17. [Risks & Mitigations](#17-risks--mitigations)
18. [Open Questions](#18-open-questions)
19. [References](#19-references)

---

## 1. Executive Summary

Enigma is a mobile-first social app where **communities run real-world challenges with real outcomes and hard deadlines**, and progress is **verified by AI and peers** — not by self-reported claims. It blends the structure of **Reddit** (communities, threaded posts, voting) with the energy of **Discord** (channels, roles, real-time chat, XP levels) and adds a **game layer** (XP, levels, streaks, badges) to keep people committed until the outcome is achieved.

Examples of challenges a community can run:

| Community | Challenge | Duration | Proof of completion |
|---|---|---|---|
| Hackathon A | Build a startup in 30 days | 30 days | Repo link, demo video, AI code review, peer demo |
| Hackathon A | Learn Python | 8 weeks | AI quizzes per module, coding tasks |
| Hackathon A | Read 10 books | 60 days | AI-generated quiz per book, summary note |
| Anytime Fitness | Lose 5 kg | 12 weeks | Scale photo verification, wearable data, check-in selfies |

**The core rule:** members can only *post to the challenge feed while a challenge is live*. **Stories and Notes** (short-form, ephemeral/lighter content) are available **24/7** so communities never go silent, but the "main stage" stays focused on the outcome.

Enigma is built for the pilot launch with two communities: **Hackathon A** (builders, programmers, founders) and **Anytime Fitness** (gym-goers, weight-loss journeys).

---

## 2. Problem Statement

### 2.1 The hollow-follower problem

Modern social media rewards *content* (dance trends, memes, curated lifestyles) rather than *outcomes* (skills acquired, weight lost, books read, startups shipped). People consume and post, but **nothing in their real life changes**.

### 2.2 The self-report problem

Goal-oriented communities (fitness groups, study groups, startup groups) exist on WhatsApp, Discord and Telegram, but:

- Progress is **self-reported** — anyone can claim "lost 2 kg" or "finished chapter 3" with no proof.
- There is **no time pressure** — goals drag on forever without deadlines.
- **No consequence or reward** — nothing is on the line; quitters face no friction.
- **No verifiable status** — a 3-year member and a 3-day member look identical; there's no way to see who actually delivers.

### 2.3 The engagement-without-distraction problem

Reddit-style communities go off-topic; Discord-style servers are noisy. When a group has a *mission* (lose weight, ship a product), open-ended posting drowns the mission in memes and noise.

**Enigma's answer:** time-boxed, verifiable challenges as the primary content unit, with light content (Stories/Notes) as the always-on layer.

---

## 3. Vision & Goals

### 3.1 Vision

> Make Enigma the place where people go to **actually change their life** — in public, under a deadline, with proof.

### 3.2 Product goals (success =)

1. **Outcomes over content** — every post in a challenge feed is tied to a measurable goal and verifiable proof.
2. **Trust through verification** — no claim is accepted on faith; AI + peer verification establishes community-wide trust.
3. **Commitment through deadlines** — challenges have hard end dates; liveness of the feed forces participation *now*.
4. **Addiction through progression** — Discord/duolingo-style XP, levels, streaks and badges turn discipline into a game.
5. **Community as a service** — anyone (fitness center, coding bootcamp, college club, company) can launch and run a community with built-in verification tooling.

### 3.3 Non-goals (explicitly out of scope)

- No general-purpose timelines/friend feeds (this is not a generic social network).
- No anonymous posting — accountability requires identity (at least a verified handle).
- No cash-staking / betting (StepBet-style money pools are out of MVP scope for legal complexity).
- No built-in payments, e-commerce, or dating features.

---

## 4. Target Users & Personas

### 4.1 Primary audiences

- **Community members** — people who want to achieve a real goal with a group (lose weight, learn to code, read more, ship a project).
- **Community founders/admins** — organizers (college clubs, gyms, bootcamps, HR teams) who want a structured way to run cohort challenges.
- **Peer verifiers** — engaged members who review others' proof (rewarded with XP).
- **Lurkers/observers** — people who want to browse inspiring real outcomes before committing.

### 4.2 Personas

**Persona 1 — Riya, 20, CS student (Hackathon A)**
- Goal: learn Python and have a GitHub repo with real projects by the end of the semester.
- Pain: WhatsApp study groups are noisy; nobody checks progress; she quits by week 2.
- Enigma behavior: joins "Learn Python" challenge, submits weekly module quizzes, posts daily Notes (short updates), earns XP, levels up, sees leaderboard.

**Persona 2 — Arjun, 27, gym member (Anytime Fitness)**
- Goal: lose 5 kg in 12 weeks.
- Pain: gym staff don't track him; fitness apps are solo and boring.
- Enigma behavior: weighs in weekly via scale-photo proof, syncs wearable step data, gets AI-verified check-ins, competes on the community leaderboard, joins a "5k steps" story streak.

**Persona 3 — Sara, 24, community admin (fitness trainer)**
- Goal: run her gym's monthly challenge and prove member engagement to the owner.
- Pain: tracking is done on paper/excel; nobody can prove they completed workouts.
- Enigma behavior: creates challenges with configurable proof types, sets deadlines, reviews peer verdicts, exports completion reports.

**Persona 4 — Dev, 19, junior member**
- Goal: watch and learn first, then join.
- Enigma behavior: browses public community feeds, reads verified progress threads, earns "observer" XP for reactions; converts to member when a challenge speaks to him.

---

## 5. Competitive Landscape

| Reference | What it does | What we learn | Gap it leaves |
|---|---|---|---|
| **Reddit** | Communities, threaded posts, up/down votes | Threaded, vote-based structure; community-as-subreddit model | No deadlines, no outcomes, no verification |
| **Discord** | Servers, channels, roles, real-time chat, XP/levels | Roles-gated privileges, real-time engagement, level-up mechanics | No structured proof, noisy, no outcome tracking |
| **Habitica** | Gamifies habits/to-dos as RPG quests (XP, HP, pets) | Strong gamification of daily tasks | Solo/small groups; no real community verification |
| **Strava** | Clubs, challenges, GPS-verified workouts, kudos | **Device/sensor-verified achievements** as the gold standard of proof | Only covers fitness/sport |
| **Stridekick / StepBet** | Group step challenges with wearables; StepBet stakes money | Anti-cheat expectations (device data, sync), public leaderboards | Fitness-only; no content community; betting model |
| **Duolingo** | Streaks, XP, leagues, gamified learning | Streak psychology, league laddering, quiz-gated progress | Single-player learning |
| **BeReal** | One daily unfiltered post | Ephemeral authenticity, 24h story mechanic | No long-form goals |
| **LinkedIn (skills/endorsements)** | Peer endorsements of skills | Peer-verification mechanic | No proof, no deadlines, no community format |

**Positioning statement:** *Enigma = Reddit's community threads + Discord's level system + Strava-grade verification, focused exclusively on real-world challenges with deadlines.*

---

## 6. Core Concepts & Terminology

| Term | Definition |
|---|---|
| **Community** | A group (e.g., *Hackathon A*, *Anytime Fitness*) with its own feed, channels, members, admins, and challenges. Analogy: subreddit + Discord server. |
| **Challenge** | A goal with a measurable target, a start date, an end date, and defined proof requirements. The unit of commitment. |
| **Milestone** | An intermediate checkpoint inside a challenge (e.g., weekly weigh-in, module quiz, GitHub commit count). |
| **Submission** | A user's proof attempt for a milestone or final completion (photo, video, link, doc, quiz, device data). |
| **Verification** | The process of deciding a submission is authentic: AI first-pass + peer vote + optional admin override. |
| **Verified status** | A submission badge showing how it passed: `AI-Verified`, `Peer-Verified`, `Admin-Verified`, `Mixed`. |
| **Post** | Long-form contribution to a **live challenge's** thread (only allowed while the challenge is live). |
| **Story** | Ephemeral content (photo/video, auto-deletes in 24h) — allowed 24/7. |
| **Note** | Short-form text/message in community channels — allowed 24/7 (Discord-like chat). |
| **XP** | Experience points earned from challenge participation, verification activity, and community help. |
| **Level** | Global rank derived from XP; unlocks permissions (Discord-role style). |
| **Streak** | Consecutive days of challenge-relevant activity (check-in, submission, story). |
| **Badge** | One-time achievement marker (e.g., "Completed 3 challenges", "10 peer verifications"). |
| **Verifier** | A member at or above a minimum level who can vote on others' submissions. |

---

## 7. Product Pillars

1. **Outcome-first feed** — challenge posts appear only while a challenge is live; dead posts archived with results.
2. **Proof, not promises** — every claim carries an artifact; AI and peers audit it.
3. **Deadlines create energy** — liveness windows, countdowns, and grace periods shape behavior.
4. **Progress is visible** — XP, levels, streaks, leaderboards, and public "Verified" badges.
5. **Always-on community** — Stories and Notes keep members engaged 24/7, in between and during challenges.

---

## 8. Functional Requirements

> Priority legend: **P0** = MVP must-have, **P1** = soon after launch, **P2** = later.

### 8.1 Communities

| ID | Requirement | Priority |
|---|---|---|
| C-1 | Users can browse a directory of public communities and request to join (private ones require invite/admin approval). | P0 |
| C-2 | Each community has: name, description, avatar, banner, category tags, member count, challenge history, and a **Community Level**. | P0 |
| C-3 | Community creators can configure: public/private, join mode (open/invite/approval), challenge rules, allowed proof types, verification policy. | P0 |
| C-4 | Community members can be grouped into **channels** (e.g., Hackathon A: `#build-logs`, `#python-questions`, `#gym-checkins`, `#general`). | P1 |
| C-5 | Admins can pin key threads (rules, challenge schedule, FAQ) to the top of the feed. | P1 |
| C-6 | Communities earn **Community XP** from member challenge completions; community level unlocks perks (more channels, custom badges, featured placement). | P2 |

**Worked example (pilot):**

- **Hackathon A** — category: Builders/Education. Private invite-based. Runs "Build a startup in 30 days", "Learn Python", "Read 10 books".
- **Anytime Fitness** — category: Fitness/Health. Public join. Runs "Lose 5 kg", "Couch to 5k", weekly step streaks.

### 8.2 Challenges

| ID | Requirement | Priority |
|---|---|---|
| CH-1 | Admins (or level-gated members) create a challenge with: title, description, target metric, start date, end date (hard deadline), milestones, proof requirements, and max participants (optional). | P0 |
| CH-2 | Challenge lifecycle states: `Draft → Announced → Live → Ending → Archived`. | P0 |
| CH-3 | Members can **join** a live/announced challenge with one tap; joining registers them in the participant list and starts their per-member progress bar. | P0 |
| CH-4 | Challenge target types supported (MVP): numeric target (kg, km, books, commits), binary completion (ship a demo, pass a quiz), checklist of milestones. | P0 |
| CH-5 | Milestones: admins define checkpoints with dates (e.g., weekly weigh-in every Sunday). Missed milestones trigger a warning to the member; repeated misses flag them in the leaderboard. | P0 |
| CH-6 | **Hard deadline enforcement:** when a challenge ends, submissions close. Late entries require admin approval and are labeled "Late — admin approved". | P0 |
| CH-7 | Each challenge shows a live **countdown**, member count, completion %, and top-progress members. | P0 |
| CH-8 | Grace period: optional configurable buffer (e.g., 24h) for final submissions. | P1 |
| CH-9 | Recurring challenges (weekly/monthly auto-restart) for habits like steps or reading. | P1 |
| CH-10 | Challenge completion report: after archiving, admins get per-member outcome data (completed %, verified submissions, dropouts) and can export CSV. | P1 |
| CH-11 | **Private challenges** visible only to invited members (e.g., fitness trainer's client cohort). | P1 |

### 8.3 Posts, Stories & Notes (content rules)

**This is the heart of Enigma's content model — the "Liveness Rule":**

| ID | Requirement | Priority |
|---|---|---|
| P-1 | **Posting rule:** Members may create **Posts** on a challenge thread **only while that challenge is live**. Before start or after end, the challenge thread is read-only (comments still allowed). | P0 |
| P-2 | Posts are thread-rooted: every post belongs to a challenge (or to the community's general board when explicitly allowed by admins). | P0 |
| P-3 | **Stories:** photo/video-only, auto-expire after 24h, visible to community; a daily-activity count adds to streaks. Available **24/7**, including between challenges. | P0 |
| P-4 | **Notes:** short text (≤ 500 chars) in community channels, similar to Discord chat. Available **24/7**. Notes support replies and reactions; they do not earn challenge-progress XP (only small social XP). | P0 |
| P-5 | Posts may include: text, images, videos, file/link attachments (repo links, PDFs, CSV of wearable data), and embedded proof UI (scale readings, quiz results, commit graphs). | P0 |
| P-6 | Comments are available on posts 24/7, but **voting (up/down)** is only enabled while the challenge is live, to prevent post-hoc vote brigading. | P1 |
| P-7 | Archived challenges remain readable as **result showcases** — the "proof wall" of the community. | P1 |
| P-8 | Members can mark a Story/Note as linked to an active challenge milestone (e.g., "today's gym check-in" attached to Lose 5 kg), which routes it to the AI verifier for streak credit. | P1 |

**Rationale (documented decision):** restricting posts to live challenges keeps the main feed mission-focused, mimics real-world competition windows, and creates FOMO + urgency. Stories/Notes exist so the community never feels dead between challenges.

### 8.4 Verification System (AI + Peer)

The **dual-verification pipeline** is Enigma's moat. Every submission enters a pipeline:

```
Submission → Pre-checks → AI Verifier → Verdict + Confidence
                                    ├─ High confidence (≥0.9) → Auto-Verified (AI badge)
                                    ├─ Medium (0.7–0.9) → Peer queue → 3+ peer votes → verdict
                                    ├─ Low (<0.7) → Flagged → Admin review
                                    └─ Suspected fraud → Rejected + user flagged
```

| ID | Requirement | Priority |
|---|---|---|
| V-1 | Every challenge defines **proof types**; submissions without a valid proof type are rejected client-side. | P0 |
| V-2 | **AI Verifier** (multimodal LLM + specialized models):
  - *Photos* — object/food/scale/gym-equipment recognition; EXIF/metadata check; duplicate-reverse-image lookup.
  - *Videos* — motion/presence checks (e.g., running on treadmill, demoing the app).
  - *Docs/links* — repo existence, commit freshness, README content check; PDF extraction for essays/summaries.
  - *Quizzes* — AI-generated question banks per learning milestone; quiz score must be ≥ pass threshold.
  - *Wearable data* — validates imported step/workout/weight series for consistency (timestamp monotonicity, plausibility).
  | P0 |
| V-3 | AI returns: verdict (`accept` / `needs_review` / `reject`), confidence score, and a human-readable explanation shown to the submitter. | P0 |
| V-4 | **Peer verification:** for medium-confidence cases, the submission enters the community's peer queue; members at **Level ≥ 3** vote Accept/Reject with a short reason. **3 agreeing votes** decide; a split (2–1) sends it to admin. | P0 |
| V-5 | Peer verifiers earn **Verifier XP** (see §8.5); high accuracy verifiers get a "Trusted Verifier" badge. | P0 |
| V-6 | Admins can always override verdicts; overrides are logged. | P0 |
| V-7 | Submitters can appeal a rejection; appeal triggers admin review with the AI explanation attached. | P1 |
| V-8 | **Anti-cheat layer** (§13) runs before AI: duplicate detection, metadata stripping checks, device/session fingerprints, submission rate limits. | P0 |
| V-9 | Weight-loss verification policy example: weekly scale photo (visible date + weight), AI reads the display, cross-checked with wearable trend + check-in selfies; a consistent monotonic-down trend is required for full credit. | P1 |
| V-10 | Verification latency SLA: AI verdict in ≤ 30s; peer verdict ≤ 24h; admin review ≤ 72h. | P0 |

### 8.5 Level-Up System (XP, Levels, Badges)

| ID | Requirement | Priority |
|---|---|---|
| G-1 | **XP sources** (global): join a challenge +20; daily check-in (streak) +10; milestone submission +50; verified completion +200; peer verification vote +15; accurate verdict match (against final outcome) +25; quality post +10; story +5; note +2. | P0 |
| G-2 | **Levels:** global, cumulative; formula e.g. `XP needed for level L = 100 × L^1.5` (level 1 → 10 at ~700 XP). Levels carry titles, e.g.: 1 *Newcomer*, 2 *Starter*, 3 *Verifier* (unlocks peer voting), 5 *Contributor*, 8 *Striver*, 12 *Achiever*, 20 *Veteran*, 35 *Legend*. | P0 |
| G-3 | **Level-gated privileges** (Discord-role model): L3 → peer verification; L5 → create challenges; L8 → create community channels; L10 → moderator tools; L15 → community admin nomination. | P0 |
| G-4 | **Streaks:** per-challenge activity streak + community presence streak; visible flame icon; streak reset on missed daily check-in (grace: 1 streak-freeze item earned monthly). | P0 |
| G-5 | **Badges** (one-time, shown on profile): "First Blood" (first completion), "Triple Threat" (3 completed), "Verification Guru" (50 accurate votes), "Habit Machine" (30-day streak), "Deadline Slayer" (5 on-time completions), "Comeback Kid" (rejoin after dropout). | P1 |
| G-6 | **Leaderboards:** community leaderboard (weekly & all-time), challenge leaderboard (progress-based), and global level leaderboard. | P0 |
| G-7 | Anti-farm guardrails: daily XP caps (e.g., 500/day social + 2000 challenge), XP decay is *not* implemented in MVP (to avoid punishing lurkers), verification-vote XP only counts if vote agrees with final verdict. | P1 |
| G-8 | Level-up celebration: confetti animation + shareable "Level 8 Achiever" card. | P1 |

### 8.6 Feed & Discovery

| ID | Requirement | Priority |
|---|---|---|
| F-1 | Home feed = user's communities' **live challenge threads** sorted by recency + engagement, with a "Live now" section and countdowns. | P0 |
| F-2 | Discovery tab: browse communities by category, see "Trending challenges" (most joined, highest completion), and featured "Proof walls". | P1 |
| F-3 | Search across communities, challenges, and verified members. | P1 |
| F-4 | Personalized suggestions: recommend communities based on joined communities (e.g., Hackathon A → "Anytime Fitness"? no — rather fitness → nutrition challenges) and completed challenge categories. | P2 |

### 8.7 Notifications

| ID | Requirement | Priority |
|---|---|---|
| N-1 | Deadline notifications: 24h, 6h, and 1h before milestone/submission deadlines. | P0 |
| N-2 | Verification notifications: verdict ready (AI), peer vote requested, submission needs review. | P0 |
| N-3 | Social: replies, reactions, mentions, new community notes in subscribed channels (with mute options). | P1 |
| N-4 | Streak reminders ("Your streak is dying — check in!"), customizable quiet hours. | P1 |
| N-5 | Community announcements (admin push). | P1 |

### 8.8 Roles & Permissions

| Role | Permissions (abridged) |
|---|---|
| **Member** | Join challenges, post while live, stories/notes 24/7, comment, react, earn XP. |
| **Level 3+ (Verifier)** | Vote on peer queue. |
| **Level 5+ (Organizer)** | Create challenges (subject to admin approval). |
| **Moderator** | Approve/remove posts, issue warnings, override verdicts, manage queues. |
| **Admin** | Everything; configure community, roles, challenge rules, reports. |
| **Platform admin** | Global moderation, community creation approval, system config. |

---

## 9. User Flows

### 9.1 Core flow: join & complete a challenge

1. User opens Home → sees live challenge "Learn Python — Week 1" with countdown.
2. Taps **Join** → onboarding card explains proof types & deadlines → **Joined** (XP +20).
3. Daily: user posts a Story check-in (streak builds) and works toward milestones.
4. At milestone: user submits proof (photo / quiz / repo link).
5. AI verifier returns verdict (≤30s); if medium confidence → peer queue → 3 votes.
6. User gets "✓ Verified" on their milestone; progress bar advances; XP awarded.
7. At deadline: challenge ends → results thread + leaderboard + badges distributed.

### 9.2 Admin flow: create a challenge

1. Admin taps "New Challenge" in community settings.
2. Fills wizard: title, description, target metric, dates, milestones, proof types, verification policy (AI-only / AI+peers), grace period.
3. Submits → challenge enters `Announced` (members can pre-join) → auto-flips to `Live` at start date.
4. During live: monitors queue, overrides verdicts, approves late entries.
5. End: archives challenge, generates completion report, exports CSV.

### 9.3 Verification queue flow

1. Submission enters queue (medium confidence).
2. 3+ eligible verifiers get notifications; each sees AI explanation + artifact without seeing others' votes (avoid anchoring).
3. After 3 agreeing votes → final verdict; discordant → admin.
4. Submitter notified; XP/leaderboard updated on verification.

---

## 10. Information Architecture

```
App (5 tabs)
├── Home ───────────── Live challenges, countdowns, streaks, quick actions
├── Community (per community)
│   ├── Feed (live challenge threads; read-only when no live challenge)
│   ├── Channels (Notes chat, 24/7)
│   ├── Stories (24h ephemeral ring, 24/7)
│   ├── Challenges (list, past/current/future)
│   │   ├── Challenge detail (progress bar, milestones, leaderboard, proof wall)
│   │   └── Submit proof
│   └── Members / Leaderboards
├── Verify (queue, my verdicts, accuracy %)
├── Discover (communities, trending challenges, proof walls)
└── Profile (levels, XP breakdown, badges, streaks, my challenges)
```

---

## 11. Data Model (Draft)

```
User            (id, handle, name, level, xp, streak, badges[], created_at)
Community       (id, name, description, type[public|private], level, admin_ids[], config{join_mode, verify_policy})
Membership      (user_id, community_id, role[member|mod|admin], joined_at, per-community xp)
Channel         (id, community_id, name, type[chat|announcements], 24x7)
Challenge       (id, community_id, title, target_type, target_value, start_at, end_at, state, grace_minutes, created_by)
Milestone       (id, challenge_id, label, due_at, weight, proof_types[])
ChallengeMember  (challenge_id, user_id, joined_at, progress, status[active|dropped|completed], dropped_at)
Post            (id, challenge_id, user_id, body, media[], links[], status, created_at, votes)
Comment         (id, post_id | note_id, user_id, body, created_at)
Note            (id, channel_id, user_id, body, created_at)
Story           (id, community_id, user_id, media[], expires_at, linked_milestone_id?)
Submission      (id, user_id, challenge_id, milestone_id?, proof{type, artifact_urls, metadata}, created_at)
Verification    (id, submission_id, stage[ai|peer|admin], verdict, confidence, model_version, explanation, decided_at)
PeerVote        (id, verification_id, verifier_id, vote, reason, created_at, accurate?)
XP_Event        (id, user_id, amount, source, ref_id, created_at)
Badge           (id, user_id, type, earned_at)
LeaderboardSnap (id, community_id, period, ranking_json, created_at)
```

---

## 12. Non-Functional Requirements

| Area | Requirement |
|---|---|
| **Platform** | Mobile-first (iOS + Android); PWA/web for admin dashboards. |
| **Performance** | App launch < 2.5s on mid-range devices; feed load < 1s p95; AI verdict ≤ 30s; media upload resumable, thumbnail-optimized. |
| **Reliability** | 99.9% uptime during challenge periods; **deadline integrity**: server time is source of truth, submissions at cutoff are atomic (no clock-skew cheating). |
| **Scalability** | Horizontally scalable; pilot target 5k users/50 communities; design for 1M users/100k concurrent challenge submissions (queue-based AI pipeline, async jobs). |
| **Security** | OAuth/social login + email, JWT sessions, role-based access on every endpoint, file upload scanning, rate limiting, media metadata stripping, encrypted data at rest/in transit. |
| **Privacy** | Weight/health data marked sensitive; granular visibility controls (private vs community vs public); GDPR/DPDP compliance-ready (export & delete). |
| **Localization** | English first; i18n architecture for Hindi + regional languages. |
| **Offline** | Draft posts/stories offline → sync queue on reconnect (with timestamp honesty). |
| **Accessibility** | WCAG 2.1 AA: contrast, alt text, screen-reader labels, haptic feedback toggle. |

---

## 13. Trust, Safety & Anti-Cheat

1. **Image verification**: EXIF/GPS stripping on ingest; duplicate & near-duplicate detection (perceptual hashing); reverse-image lookup against known sources.
2. **Device fingerprinting**: per-device submission rate limits; new-device flag for sensitive claims (e.g., weight entries).
3. **Chronology checks**: wearable/photo timestamps must be monotonic; "old photo resubmission" detection.
4. **AI-fraud guards**: watermark/artifact detection for generated images (C2PA-style provenance if available); quiz IP-checksum to stop answer sharing (question banks rotated per attempt).
5. **Community moderation**: admin/mod reports, content flags, harassment & spam policies, graduated warnings → temp ban → permanent ban.
6. **Verifier integrity**: no self-verification (own submissions excluded from own queue), verifier accuracy tracked; systematic bad actors lose verifier status.
7. **Appeal system**: every rejection has an appeal path (§V-7).
8. **Transparency**: AI explanations shown in plain language; model version logged for audit.
9. **Underage & medical safety**: weight-loss challenges require disclaimer; medical targets flagged with "consult a professional" notice.

---

## 14. Success Metrics (KPIs)

**Product**
- **Activation**: % of signups who join a challenge in first 48h (target ≥ 40%).
- **Retention**: D7 ≥ 35%, D30 ≥ 20% among challenge joiners (vs ~10% generic social).
- **Completion rate**: % of challenge joiners with verified completion (target ≥ 50% — far above typical ~20% free-goal completion).
- **Verification quality**: AI acceptance precision ≥ 95%; peer/admin override rate ≤ 5%; appeal win-rate ≤ 2%.

**Engagement**
- Active challenges/community/month; posts per live challenge; daily story/note volume (24/7 baseline).
- Median streak length; level-ups per week; verifier votes per week.

**Community**
- New communities created/month; challenge organizer NPS ≥ 50.
- Dropout visibility: % of members flagged on milestones vs completions.

---

## 15. Monetization

| Model | Detail | Priority |
|---|---|---|
| **Freemium** | Free: join 1 community, standard challenges. Pro: unlimited communities, advanced analytics, custom badges, priority verification, export reports. | P1 |
| **Community plans** | Paid tier for organizations (gyms, bootcamps, HR) to run private cohort challenges with full verification tooling. | P1 |
| **Sponsored challenges** | Brands run challenges with prizes (e.g., fitness brand "Complete Couch-to-5k, win shoes"); clearly labeled. | P2 |
| **Verify credits** | Bulk AI-verification throughput for power communities (per-submission credit packs). | P2 |

*MVP is free for the two pilot communities; monetization is validated after retention/completion KPIs.*

---

## 16. MVP Scope & Roadmap

### Phase 0 — Pilot (weeks 1–8, MVP)
- Users, communities (Hackathon A, Anytime Fitness), memberships, roles.
- Challenges with milestones, deadlines, liveness posting rule.
- Posts, comments, stories (24h expiry), notes/channels.
- AI verifier for **photo + link + quiz** proof types; peer queue (Level 3+); admin override.
- XP, levels (up to L15), streaks, community + challenge leaderboards.
- Notifications (deadline, verdict).
- Mobile app (iOS/Android via shared core) + web admin.

### Phase 1 (weeks 9–16)
- Badges, story-link-to-milestone, grace periods, appeals, export reports.
- Wearable data sync (Apple Health / Google Fit / Fitbit / Garmin) for fitness proof.
- Discovery tab, search, notifications polish.
- Android/iOS release + pilot performance review.

### Phase 2 (next term)
- Public community creation, recurring challenges, sponsored challenges, Pro tier, i18n, advanced anti-cheat (C2PA provenance), AI-generated progress summaries.

---

## 17. Risks & Mitigations

| Risk | Impact | Mitigation |
|---|---|---|
| AI verification errors (false accepts/rejects) | Trust collapse | Human-in-the-loop: medium-confidence → peers; confidence thresholds calibrated on pilot data; model versioning; appeal path. |
| Verifier apathy (empty peer queues) | Slowed verdicts | XP incentives, 24h SLA reminders, auto-escalate to admin, gamified "clear the queue" quests. |
| Cheating culture ("proof farming") | Leaderboard distrust | Multi-layer anti-cheat (§13), accuracy tracking, public verification trail, harsh penalties for confirmed fraud. |
| Dropout at scale (join rush → quit week 2) | Bad KPIs | Milestones with early check-ins, streak-freeze, comeback rewards, small cohort size limits. |
| Scope creep in MVP | Delayed launch | Strict P0 cut; everything P1+ scheduled post-pilot. |
| Regulatory (medical claims, betting-adjacent) | Legal | No cash stakes; health disclaimers; data residency & DPDP/GDPR compliance. |
| Deadline disputes (clock skew) | Support load | Server-authoritative timestamps, atomic cutoffs, transparent rules before joining. |
| Community toxicity | Churn | Mod tooling, reports, auto-flag of harassment, channel-level muting. |

---

## 18. Open Questions

1. Should completed challenge threads remain **open for general community posts** (like a showcase) or strictly read-only? (Current: read-only + comments.)
2. Should XP be community-scoped or global? (Current: global level, community XP separate.) 
3. Does "Learn Python" benefit from an **anti-plagiarism check** on coding submissions (already planned) or a live coding exam?
4. Should verifiers ever see AI confidence scores before voting (anchoring risk)? (Current: they do NOT; only explanation.)
5. Pilot KPI for *completion rate* needs baseline research — compare vs. Habitica/Strava cohort completion rates.
6. Should there be an "anonymized mode" for sensitive challenges (weight loss) where leaderboard shows aliases?

---

## 19. References

- **Habitica** — gamified habit RPG: https://habitica.com/
- **Stridekick** — group step challenges with wearable verification: https://stridekick.com/
- **StepBet** — staked step challenges: https://www.stepbet.com/
- **Strava** — club challenges, device-verified activity: https://www.strava.com/
- **Social fitness challenge apps comparison (2026)**: https://www.fitcoin.co/articles/social-fitness-challenge-apps
- **Gamified fitness apps (2026)**: https://bitletics.com/blog/gamified-fitness-apps/
- **Discord gamification (XP, quests, tokens)**: https://www.blockchainappfactory.com/blog/discord-communities-gamification-xp-quests-tokens/
- **Duolingo** — streaks/XP/leagues: https://www.duolingo.com/
- **Reddit** — community/thread model: https://www.reddit.com/
- **BeReal** — daily ephemeral authenticity: https://bere.al/
- **Gamification of life / levels & progress bars**: https://pixelburn.tech/en/gamification-of-life-turn-everyday-routines-into-an-engaging-quest

---

## 20. End-to-End Scenarios

Five end-to-end scenarios covering all roles, all five app tabs, and the full challenge lifecycle. Each scenario follows one user across multiple screens and system interactions, including alternate paths.

### Scenario 1 — First-Time User Completes Her First Challenge ("Learn Python")

**Persona:** Riya, 20, CS student. **Goal:** go from signup to a verified challenge completion. **Place:** Home → Discover → Community → Challenge → Submit → Profile.

**Preconditions**
- Riya installs Enigma; no account. "Learn Python" challenge in Hackathon A is `Live`, Day 1 of 8 weeks, 240 joined.
- Platform has completed Phase 0; AI verifier + quiz engine enabled.

**Main flow**
1. **Onboarding (Signup)** — Riya signs up with Google OAuth. Onboarding asks: "What do you want to achieve?" — options: *Learn a skill / Get fit / Ship something / Read more*. She picks "Learn a skill" → suggested community card for **Hackathon A** (join request auto-approved, it's invite-based but she has a college link). Riya enters the community (Community tab).
2. **Browsing live challenge (Community → Challenges)** — She sees "Learn Python — 8 weeks" with a countdown (7w 6d), 240 members, 40% projected completion. The **Liveness Rule** is visible: "Posts open while challenge is live."
3. **Joining (Challenge detail)** — Riya taps **Join**. A pre-join card explains: 8 milestones (one per week), proof = quiz per module + repo link at final. She accepts → XP +20, streak starts (Day 1), she's added to `#python-questions` channel.
4. **Daily check-in (Home)** — Next day, Home shows her streak (🔥 1), live countdown, and a "Check in" pill. She posts a **Story** (photo of her IDE + 10-min code session) — Story auto-expires in 24h, +5 XP, streak → 2.
5. **Week 1 milestone (Community → Challenge → Submit proof)** — Quiz opens. Riya takes the 10-question AI-generated quiz (Module 1: variables & loops). Passes with 80% (threshold 70%). Submission → **AI Verifier** checks: answers unique, time-per-question plausible (≥ 8s each), no IP copy (anti-plagiarism). High confidence → `AI-Verified` in ~20s. Milestone 1/8 done, progress bar 12.5%, +50 XP, post on challenge thread (still live ✓).
6. **Mid-challenge post (Challenge thread)** — At Week 4 she posts a text + screenshot post: "My first mini-project — a to-do CLI." Comments from the community; upvotes enabled (challenge live). +10 XP for quality post (liked ≥ 5 times).
7. **Final submission (Submit proof)** — Week 8: repo link + 3-min demo video. AI checks: repo exists, ≥ 8 commits, README, video ≥ 90s non-frozen. Confidence 0.88 → **peer queue** (3 votes from Level 3+ members) → all Accept → `Peer-Verified`.
8. **Challenge end & results** — Countdown hits zero; submissions close (server-time cutoff). Challenge → `Archived`. Completion ceremony: +200 XP, badge **"First Blood"**, certificate card "Verified: Learn Python (8 weeks)". Community leaderboard: Riya 23rd of 148 finishers.
9. **Level up (Profile)** — XP total ~500 → **Level 4 "Verifier"** unlocked. Confetti animation + shareable card. Profile shows: level, XP breakdown chart, streak 56, badges [First Blood].

**Alternate paths**
- *Quiz fail* → retake after 24h cooldown (2 attempts per week); if failed twice → milestone marked `At Risk`, admin notified.
- *Repo link broken* → AI returns `needs_review` → Riya gets "fix within 48h" prompt, resubmits.
- *Misses a milestone* → warning notification at 6h before deadline; at miss → flag on profile; 2 misses → `dropped` status (see Scenario 5).

**Postconditions / state changes**
- `Membership(Hackathon A)`, `ChallengeMember(Learn Python → completed)`, 9 `XP_Event`s, `Badge(First Blood)`, 8 `Verification` records, challenge `Archived`, Riya at Level 4.

---

### Scenario 2 — Member Wins a Weight-Loss Challenge With Wearable-Backed Proof

**Persona:** Arjun, 27, gym member. **Goal:** complete "Lose 5 kg" in 12 weeks and top the leaderboard. **Place:** Community → Challenge → Story ring → Settings (wearable) → Submit → Leaderboard.

**Preconditions**
- Arjun has been a member of **Anytime Fitness** for a month (Level 2). "Lose 5 kg" challenge is `Live`, Day 30/84, 96 members.
- Phase 1 shipped: wearable sync (Apple Health), scale-photo proof type, story-link-to-milestone.

**Main flow**
1. **Join + setup (Challenge detail)** — Arjun joins. Pre-join card shows the proof policy: *weekly scale photo + wearable trend required; medical disclaimer shown*. He connects Apple Health → his step/workout data starts flowing (Privacy: community sees aggregate trends only, not raw health data).
2. **Baseline weigh-in (Submit proof)** — Day 1: he photographs the gym scale (date + weight visible). AI Verifier: OCR reads **93.4 kg**, checks EXIF stripped & photo taken today (timestamp monotonic with his Apple Health daily steps). High confidence → `AI-Verified`. Baseline 93.4 kg saved; progress bar starts.
3. **Daily presence (Story ring)** — He posts a **Story** after each workout (photo in gym, linked to the challenge milestone "Daily check-in"). His streak grows; the 24h story ring in the Community tab shows 🟡 from 38 members today.
4. **Weekly weigh-in (Submit)** — Week 2: photo → OCR 92.1 kg. AI confidence 0.76 (slightly blurry) → **peer queue**. His friend Dev (Level 4) and 2 others vote Accept → `Peer-Verified` ~2h later. Notifications: "✓ Weigh-in verified".
5. **Wearable cross-check (auto, background)** — System compares weekly delta (−1.3 kg) vs his step/workout trend (plausible range ±20%) → trend flag `consistent`. Full milestone credit. (Had it been inconsistent → `needs_review` + admin check.)
6. **Mid-challenge social (Notes + thread post)** — He drops a **Note** in `#gym-checkins`: "PR: 5 km run today". Replies + reactions. At Week 6 he posts a progress photo on the challenge thread (still live) — comments encouraging him; upvotes push his post to top of thread.
7. **Struggle & support (Notifications + Notes)** — Week 9: scale shows +0.2 kg (bounce). System sends *non-shaming* nudge: "Weight bounced — 3 more check-ins to finish. Your weekly average is still on track." He replies in Notes, community gives tips. Streak intact (stories continue).
8. **Final weigh-in + result** — Week 12: 88.1 kg → **−5.3 kg**; AI verified (high confidence, trend consistent). Challenge ends → `Archived`. **1st place on the challenge leaderboard**; badge **"Deadline Slayer"** (5 on-time completions incl. earlier step challenges). +200 XP → **Level 6 "Contributor"**.
9. **Proof wall (Archived challenge)** — His verified timeline (baseline → photos → trend graph) is now on the community's **proof wall** — the community's result showcase. Profile privacy setting keeps exact weight visible to community only, not the public Discovery feed.

**Alternate paths**
- *Scale photo rejected (AI)* → shows reason "scale display unreadable / date not visible" → retake prompt with live guide overlay (bounding box for display).
- *Wearable off all week* → cross-check skipped, weight entry alone gets lower weight (0.7) → peer review required; streak unaffected.
- *Weight gain trend* → auto-flagged; challenge remains completable (goal is cumulative), admin alerted if medical risk pattern (rapid loss > 2 kg/week → flagged as unhealthy).

**Postconditions**
- `Submission` × 13 (baseline, 11 weekly, final), 12 `AI-Verified`, 1 `Peer-Verified`, wearable link record, challenge completed, leaderboard snapshot #1, badge issued, proof-wall entry created.

---

### Scenario 3 — Active Member as Peer Verifier ("Clearing the Queue")

**Persona:** Dev, 19, Level 4 (cross-community). **Goal:** earn verifier XP, reach Level 5 (challenge-creator privilege), and build a "Trusted Verifier" reputation. **Place:** Verify tab → Submission cards → Profile (accuracy) → Challenge creation wizard.

**Preconditions**
- Dev crossed Level 3 last week → verifier role active. Verify tab badge shows 4 submissions awaiting votes across Hackathon A and Anytime Fitness.
- Verification policy: peer votes hidden from each other until 3 are in (anti-anchoring).

**Main flow**
1. **Notification (push)** — "🟡 4 submissions need your vote" arrives after lunch. Dev opens the **Verify tab**.
2. **Queue overview** — Queue lists: (a) blurry scale photo (Anytime Fitness), (b) week-2 Python quiz result (Hackathon A, medium confidence), (c) repo link "build a startup" Day-10 demo (Hackathon A), (d) a duplicate-looking gym selfie (flagged by anti-cheat).
3. **Vote on (a) — photo** — Dev sees the artifact + AI explanation ("Scale reading 92.1 kg, date visible, confidence 0.76") — he does **not** see the numeric confidence, only the explanation (per open question #4 policy). He zooms the photo, checks the date string, votes **Accept** with reason "Date + weight legible, matches user's trend".
4. **Vote on (b) — quiz** — Screenshot shows score 80%. AI says answers are unique but attempt took 6 min (suspiciously fast). Dev votes **Reject** with "80% in 6 min = likely help; recommend retake". (He's the second reject.)
5. **Vote on (c) — repo** — Checks GitHub link in-app (sandboxed preview): 12 commits, demo video 2:41. Votes **Accept**.
6. **Vote on (d) — duplicate flag** — Perceptual-hash matches a story he posted 5 days ago. Dev votes **Reject**, marking it as duplicate → auto-sends "fraud suspicion" flag to admin (second strike for that user → 14-day submission ban).
7. **Verdict resolution + accuracy credit** — After his votes, outcomes: (a) accepted (3 accepts) → +15 XP; (b) rejected (3 rejects) → +15 XP; (c) accepted → +15 XP; (d) rejected → +15 XP + **Fraud-flag bonus +10 XP**. A day later the "correctness pass" runs — his votes matched all final verdicts → **+25 XP × 4 accurate** (double because his verdicts matched, per G-7).
8. **Level up (Profile)** — XP crosses 700 → **Level 5 "Contributor"** unlocked: *Create Challenge* appears in community menu. Celebration card.
9. **First challenge creation (Community → New Challenge)** — He creates a community challenge: "Ship a static portfolio site in 7 days" for Hackathon A with link + screenshot proof types, start Monday. Admin Sara approves in 3h. Challenge → `Announced`.

**Alternate paths**
- *Vote contradicts final verdict* → no XP for that vote; accuracy % on profile drops (visible to admins); < 60% accuracy → verifier role suspended for 2 weeks.
- *Queue idles 24h* → auto-escalates remaining submission to admin; verifier reminder push at 12h.
- *Self-submission appears in queue* → system excludes own submissions from own queue (hard rule, no edge case).

**Postconditions**
- 4 `Verification` records updated, 4 `PeerVote` records (Dev), +190 XP total, Level 5 reached, 1 `Challenge` created (`Announced`), Dev accuracy 100% on profile.

---

### Scenario 4 — Community Admin Runs a Challenge End-to-End (With a Dispute)

**Persona:** Sara, 24, admin of Hackathon A. **Goal:** launch "Build a startup in 30 days", keep verification healthy, resolve a dispute, archive with a report. **Place:** Community settings → Challenge wizard → Queue/Mod tools → Reports.

**Preconditions**
- Sara is admin of Hackathon A (Level 15). Previous challenge archived cleanly. 8 moderators, 600 members.

**Main flow**
1. **Challenge creation (Community settings → New Challenge)** — Wizard: title "Build a startup in 30 days"; target type *binary completion* (ship a working demo); duration 30 days; milestones at Days 7/15/22 (demo checkpoints); proof types: repo link, demo video ≤ 5 min, one-paragraph metric summary; verification policy `AI + peers`; grace period 24h; max 50 participants. She schedules start in 7 days → challenge `Announced`, 34 pre-joins by Day 1.
2. **Launch day (Home + Challenge)** — Challenge flips to `Live` at 00:00 server time. Announcement push to all 50. Feed switches to the new live thread; old challenge thread goes read-only (comments open).
3. **Monitoring the milestone (Verify queue + Mod tools)** — Day 7: 47 of 50 submit demos. Queue spikes: 9 medium-confidence AI reviews → peers clear 7; 2 escalate to admin (1 video frozen for 3 min, 1 repo empty at check time). Sara reviews artifacts, approves the frozen-video one with a note "re-record audio if needed, re-upload"; rejects the empty-repo one with reason + appeal path.
4. **Dispute resolution (Appeal)** — Rejected member appeals (V-7): claims the repo went public after the snapshot. Sara sees the appeal with the original AI explanation attached; verifies commit history (commits dated before cutoff, pushed after) → **overrides to Accepted** with comment "commit dates pre-cutoff". Audit log records the override (V-6).
5. **Late entry (Grace period)** — At Day-30 23:40, a member submits a demo 40 min before cutoff — fine. Another submits at 00:20 (inside 24h grace) → labeled **"Late — admin approved"** after Sara's click, +8% completion visibility penalty on leaderboard.
6. **Anti-cheat alert** — Day 22: duplicate-demo flag: two members submitted near-identical repos. Perceptual hash + commit-tree similarity = 91%. Sara confirms collusion → both demos marked `Rejected`, accounts flagged (Scenario 3 mechanics apply to verifiers too), members dropped with "integrity violation" notice; community announcement reminds of the verification policy.
7. **Archive & report (Reports)** — Challenge ends, submissions close atomically at server cutoff. `Archived`. Sara opens the **completion report**: 50 joined, 41 completed (82%!), 2 integrity violations, 3 late-approved, 4 dropped mid-way; verification stats: 89% AI-auto, 8% peer, 3% admin. Exports CSV → shares with the college club. Community XP +500 (C-6) → Hackathon A levels up to **Community Level 4**, unlocks a custom badge slot.
8. **Showcase** — Top 5 demos pinned as **proof wall** features. The 41 verified completions are displayed as a "verified by AI + 180 peer votes" trust banner on the community page.

**Alternate paths**
- *Bot-attack on join* → join rate limit + captcha; private mode toggle mid-challenge blocked by design (config locked once `Live`).
- *Admin conflict-of-interest* → admins cannot override verdicts on submissions where they're a participant (co-admin handles).
- *Server issue at cutoff* → 99.9% SLA + atomic cutoff; on outage, submissions received ≤ 15 min after resume are accepted with "outage grace" label.

**Postconditions**
- Challenge lifecycle completed (`Draft → Announced → Live → Archived`), 41 `ChallengeMember(completed)`, 2 integrity flags, 1 override (audited), 1 late approval, CSV report generated, Community Level 4, proof wall updated.

---

### Scenario 5 — Dropped Member Stages a Comeback (Not a Quit Story)

**Persona:** Same as Riya but at Week 5 (fictionalized as "member X"). **Goal:** face a miss, drop out, feel the friction, come back and redeem. **Place:** Notifications → Profile (dropped state) → Discover → Community → Join → Complete → Profile.

**Preconditions**
- Member X joined "Read 10 books" (60 days) in Hackathon A, completed 4 books, streak 22. Now Week 5, project crunch from college.
- Comeback mechanics from Phase 1: `Comeback Kid` badge, rejoin-with-benefit.

**Main flow**
1. **The miss (Notifications)** — Day 35: milestone "Book 5 — submit quiz" due 8 PM. Reminders at 24h/6h/1h. He's mid-crash — misses it. 22-day streak breaks (flame → 0), milestone marked `Missed`.
2. **Warning & status (Profile)** — Miss #1 → warning card: "1 of 2 misses used. Next miss marks you Dropped." Profile shows `At Risk` ribbon on the challenge.
3. **The drop (system action)** — Week 6: he misses the Day-42 check-in too → status → `dropped`. Notifications: "You've been dropped from Read 10 books. Your verified 4-book progress stays on your profile." Leaderboard removes him; thread shows him greyed with "Dropped".
4. **Friction, not punishment (Home + Notes)** — He feels the FOMO: live thread active without him, community notes buzzing. A *soft* nudge (non-spammy, once per 72h): "Comeback is a feature. Join the next cohort — your verified progress carries over."
5. **Browsing (Discover)** — He opens **Discover**: sees "Read 10 books — Cohort 2" (starts in 6 days, 180 joined) and the *proof wall* entries from Cohort 1 finishers — verified book lists with quiz scores. He remembers why he joined.
6. **Comeback join (Community → Challenge)** — He taps **Join** on Cohort 2. System recognizes prior verified progress (4 books carried over — quizzes banked) → starts at 40% progress, "returning member" ribbon, +20 XP, streak restarts Day 1.
7. **Redeem (Submit + thread)** — He completes books 5–10 over the next 5 weeks with clean AI-verified quiz milestones, finishing 6 days early. Final: 10/10 books, badge **"Comeback Kid"**, +200 XP, and a pinned post on the thread: "I dropped out of Cohort 1. Here's how I finished Cohort 2." — upvoted to top (challenge live).
8. **State of profile (Profile)** — Level 5 reached (cross-community XP banked during dropout via Notes/Stories/verifying — kept him warm), streak 41, badges [First Blood, Comeback Kid], 10 verified book records on his proof wall.

**Alternate paths**
- *Dropped twice* → third join requires admin approval (moderation touchpoint).
- *Missed due to medical/emergency* → documented reason → miss waived via admin, no streak loss.
- *Never returns* → gentle re-engagement cadence caps at 3 nudges; profile keeps verified history (data retained for export per GDPR/DPDP).

**Postconditions**
- ChallengeMember: dropped → re-joined → completed (Cohort 2), 6 new `Verification` records, carry-over of 4 quiz banks, `Badge(Comeback Kid)`, +220 XP across 2 challenges, retention KPI example: D45 return.

---

## 21. Scenario Coverage Matrix

| Scenario | Role | App areas touched | Content types | Verification path | Outcome |
|---|---|---|---|---|---|
| 1. First completion (Learn Python) | New member | Onboarding, Discover, Community, Challenge, Submit, Profile | Post, Story, Quiz, Link, Video | AI (high conf) → Peer queue | Level 4, First Blood badge |
| 2. Weight-loss win | Fitness member | Community, Story ring, Settings (wearable), Submit, Leaderboard, Proof wall | Story, Scale photo, Wearable data, Notes, Post | AI + wearable cross-check → Peer | Challenge winner, Dead. Slayer |
| 3. Peer verifier | Level 4 member | Verify tab, Profile, Challenge wizard | Photo, Quiz, Repo, Video | Peer voting + accuracy credit | Level 5, challenge creator |
| 4. Admin run + dispute | Admin | Settings, Wizard, Queue, Mod tools, Reports | Post, Demo video, Repo, Appeal | Admin override + anti-cheat | 82% completion, archive report |
| 5. Comeback | Dropped member | Notifications, Profile, Discover, Community, Submit, Thread | Quiz, Post, Notes | AI auto (banked quizzes) | Comeback Kid badge, retention win |

**Coverage notes:** All 5 tabs appear across the five scenarios; both pilot communities are exercised; both verification branches (AI-auto and AI→peer→admin) are hit; the Liveness Rule, 24/7 Stories/Notes, streaks, XP/levels/badges, anti-cheat, appeals, grace periods, and the archive/proof-wall lifecycle are each demonstrated at least once.

---

*End of PRD — next steps: wireframes + UI/UX spec, API contract, and the Phase 0 task breakdown.*
