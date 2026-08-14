# Enigma — Product Requirements Document

**Status:** Draft v0.9  
**Date:** 2026-08-06  
**Author:** Product Team  
**Product:** Enigma — Social Challenges with Real Outcomes

---

## 1. Executive Summary

Enigma is a social platform where communities set **real, time-bound challenges** and members
prove their outcomes through **AI + peer verification**. It is a deliberate hybrid of **Reddit**
(the feed, threads, upvotes, discussion), **Discord** (communities, channels, chat), and a
**level-up RPG system** (XP, ranks, badges, streaks).

The core insight: people don't fail because they lack goals — they fail because **nothing makes
them show proof**. Enigma replaces "content" with **evidence**. Nobody upvotes a video of someone
dancing; Enigma rewards check-ins that a computer and a group of peers agree are real.

Launch pilot communities: **Hackathon A** (builders/developers) and **Anytime Fitness** (fitness).

---

## 2. Problem & Opportunity

### 2.1 Problem

- People set goals (`read 10 books`, `learn Python`, `lose 5 kg`, `launch a startup`) and abandon
  them. Average goal completion is <10% before 12 weeks.
- Existing social platforms reward **performance**, not **outcomes**:
  - **TikTok/Instagram**: trends, filters, vibe — genuinely hard outcomes can't be surfaced or verified.
  - **Reddit**: great discussion, but no structure for time-boxed, provable goals.
  - **Discord**: great communities, but no accountability contract, no verification, no progression.
  - **Duolingo/Strava**: single-domain, single-player, no social accountability layer.
- There is no platform where *peers + AI* jointly certify that a real outcome happened.

### 2.2 Opportunity

- Accountability is a proven utility: people pay for coaches, gym partnerships, "stake" apps.
- AI vision/text/code verification is now cheap enough to run at consumer scale.
- A level-up system creates the retention loop gaming has proven for a decade.
- Two pilot communities give us a **vertical-focused launch**:
  - Hackathon A (knowledge/building outcomes)
  - Anytime Fitness (physiological/health outcomes)

---

## 3. Product Vision & Positioning

> **Enigma is the accountability layer for life.**
> Commit to an outcome, prove it, level up, and earn respect — not in a post, but in public evidence.

**Positioning statement:** For people who want real progress, Enigma is the community platform
that replaces "for you" feeds with "proof you" feeds; unlike TikTok/Instagram it requires actual
outcomes, and unlike Reddit/Discord it structures challenges and certifies results with AI and peers.

### 3.1 Pillars

1. **Real outcomes only.** Every feed post is tied to an active challenge; nothing to "perform" that isn't provable.
2. **AI + peer trust.** Verification happens at the point of submission, not after dispute.
3. **Progressive mastery.** XP, levels, ranks, and badges make long-term life growth visible and fun.
4. **Community-first.** Communities own their challenges; the platform provides the rails.

---

## 5. Target Users & Personas

| Persona | Description | Job-to-be-done | Core driver |
|---|---|---|---|
| **The Striver** | Wants to learn/build/transform but quits at week 3. | Be held to a deadline with peers watching. | Accountability, fear of showing an empty feed. |
| **The Overachiever** | Has a track record, wants to build reputation & level up. | Graph the ranking, prove alpha, collect badges. | Mastery, status, leaderboards. |
| **The Community Captain** | Runs Hackathon A or a local gym's challenge group. | Post challenges, keep members engaged, vet completers. | Community growth, organization, authority. |
| **The Spectator** | Lurks, likes, and votes on other people's proof. | Passive inspiration without committing. | Curiosity, FOMO → eventually committing. |
| **The Verifier (peer)** | Actively arbitrates submissions for XP. | Earn XP by mini-judging. | Incentivized stewardship, sense of fairness. |

**Reference personas**

- **Arjun ("Hackathon A")** — 26, backend developer. Wants to "ship 30-day SaaS" and has a GitHub repo to show for it.
- **Maya ("Anytime Fitness")** — 31, fitness enthusiast. Wants to lose 5kg with daily photo/of-device evidence.

---

## 6. Core Concepts & Domain Model

- **Community** — a self-managed group (e.g. Hackathon A, Anytime Fitness) with its own rules, moderator team, channel rooms, and verification standards.
- **Challenge** — a time-bound, verifiable goal created by the community (title, description, success criteria, duration, start/end time, verification method, rewards/XP).
- **Check-in** — a member's progress proof submitted against the live challenge (media or data + optional note).
- **Verification** — the act (AI pre-screen or peer vote or moderator) that marks a check-in **Verified**, **Rejected**, or **Needs Review**.
- **Post** — any discussion thread. **Gated:** only visible/creatable while at least one community challenge is **Live**.
- **Story** — short-lived update (24h) that anyone can post **anytime** (2424/7/7).
- **Note** — a lightweight permanent micro-update (like a status) allowed **24/7**.
- **XP / Levels** — the progression economy earned from verified activity (see §8.9).
- **Reputation** — long-term, per-community trust score influenced by verification history.
- **Streak** — consecutive days with a verified check-in.

---

## 7. Core Features (Specifications)

Priorities: **P0** = MVP, **P1** = v1, **P2** = v2.

---

### 7.1 Communities

| # | Requirement | Priority |
|---|---|---|
| FC-1 | Create community: name, handles currently a description, avatar, banner | P0 |
| FC-2 | Join/leave community freely (or by approval for private ideals) | P0 |
| FC-3 | Community dashboard: stats (members, active challenges, completed %, leaderboard) | P0 |
| FC-4 | Role system: **Owner / Captain / Moderator / Member / Spectator (verified)** | P0 |
| FC-5 | Verification standard per community (prob. per-montos AI vs peers configurability) | P1 |

**User story:** *As a Captain, I can create "Hackathon A" with visible challenge policies (AI-authentic), and invite my dev guild.*

---

### 7.2 Challenge Engine (the heart of Enigma)

**Lifecycle:**

```
Draft → Scheduled → Registration → Live → Verification window → Closed → Archived
```

| State | Description | Posting allowed? |
|---|---|---|
| **Draft** | Created by Captain; invisible to members. | ❌ |
| **Scheduled** | Public countdown + pre-registration. | ❌ |
| **Registration** | Members commit ("I'm in"). Skin may be staked (P1) | ❌ |
| **Live** | Start date <= now, end date > now. WS:
  - Check-ins open
  - **Posts/threads allowed** (only here) | ✅ |
| **Verification window** | End-of-use grace (default 48h) to submit final proof & disputes. | ✅ (final check-in) |
| **Closed** | Results published, leaderboard final, XP granted. | ❌ (read-only) |
| **Archived** | Permanent record, stats, badges, links to final evidence. | Read-only |

**Challenge fields (CRUD):**
- Title, description, tagline
- Duration & schedule (start/end, auto-publish)
- Success criteria (what exactly counts as "done")
- Verification type: `AiAuto`, `Peers`, `Hybrid` (default: **Hybrid — AI pre-screen + peer quorum**)
- Evidence type required: `media`, `link(s)`, `data-source`, `mixed`
- Min participa (capacity), Stake (P1), Post gate: yes
- Badge(s) awarded on completion
- XP grant on completion

**User stories (FC):**

> *As the Hackathon A Captain, I set "Build a startup in 30 days" with success criteria = "launched site + repo (+ seed user)" so it ends 2026-08-30, hybrid AI+peer verification.*

> *As a member, I can see the countdown, register, and watch the live feed become "Live" the moment the window opens.*

**FB-1 P0 — Challenge creation, schedule, state machine (server-enforced).**
**FB-2 P0 — Registration & display list of committed members.**
**FB-3 P1 — Stakes (optional pool deposits).**
**FB-4 P1 — Recurring templates ("Monthly streak challenge").**

---

### 5.6 Posting Gate & Time-based wall

**FC-P0 profile wall.**

> The single most distinctive rule: **community feed posts are only allowed when a challenge is Live. Stories and Notes are 24/7.**

| Surface | Gated by Live challenge? |
|---|---|
| **Challenge threads** (dedicated to a challenge) | ✅ Only while that challenge is Live (or verification window) |
| **Community general feed posts** | ✅ At least one challenge Live required |
| **Chat (discord-style rooms)** | 24/7 ✅ |
| **Stories (ephemeral, 24h)** | 24/7 ✅ |
| **Notes (lightweight permanent)** | 24/7 ✅ |
| **DMs** | 24/7 ✅ |
| **Comments inside a thread** | See parent thread gate |

**WHY:** Prevents content spam and keeps the feed as an **evidence-token**: a feed is a living
proof tapestry of an active challenge. When there is no active challenge, members can only hang
out (chat/stories/notes), they can't do "clap-back content". **"Posting is a privilege that unlocks with a live challenge."**

---

### 7.5 Stories & Notes (24/7 surfaces)

- **Story:** photo/video/text, 24h auto-expire, no reactions: only reactions+replies. Shows "+" streaks "(edge of a shadow)".
- **Note:** permanent "bullet" on a profile/community feed: e.g. "Joined Hackathon A". Light — does not increment XP what enough.
- These keep the app alive between challenge windows ("dead time" → keeps the community warm, FOMO hooks, reminds people to join next window).

---

### 7.6 Verification Pipeline (AI + Peer)

**Submission flow:**

1. Member submits **check-in**: evidence (photo/video/repo link/device data/API) + optional note + challenge tag.
2. **AI pre-screen** (ms-level):
   - Is the media NSFW/bot/promotional/spammy? 
   - Is the evidence tampered (metadata, watermarks, reverse-image match against duplicates)?
   - Is the evidence **fresh** and **plausible** for the challenge type?
   - Output: `confidence: 0–1` and one of {pass, fail, uncertain}.
3. **Routing** based on AI + challenge policy:
   | AI confidence | Route |
   |---|---|
   | ≥ 0.90 | **Auto-approve** (immediate) |
   | 0.50–0.89 | **Peer-quorum** — randomly select 3+ active members, majority passes |
   | < 0.50 or double AI signals | **Needs review** → moderators/leader + AI re-scan |
4. **Peer vote** options: «Cleared», «Flagged», «Not sure». Weight = user's rep score (badge/historical correctness).
5. **Outcome:** Passed / Rejected / Dispute (member appeals). Evidence + verdict recorded in **publicly auditable log** (transparent per challenge).

**FC-6 P0 — AI pre-screening media check-ins (image/video, with confidence, duplicate detection).**
**FC-7 P0 — Peer verification queue with random sampling + rep-weighted votes.**
**FC-8 P1 — Auto-approve threshold per community policy, model per-category (code link, weight tracker).**
**FC-9 P1 — Source verification: connect GitHub / Apple Health / Garmin / Strava / Whoop as first-party data.**

**Dispute flow:**

```
Submit → AI pre-screen → (Peer or Auto) → Verdict
   ↑                                             |
   └──────── appeal (within 24h) ←← judge: mods + AI re-review
```

**Anti-abuse rules (FC-10, P0):**
- Rate limit: max 1 check-in per challenge per 6h (no spam).
- Vote cap: 20 peer votes/day, non-abstain required.
- Duplicate-evidence rejection hard.
- Checks-in before Challenge start and after window → auto-reject.

---

### 7.7 Feed (Reddit-like)

- Sort: `hot` (engagement+recency), `new`, `Top certifies` (highest verified), `Burned` (expired).
- Thumbnail-verified badge: ✅ 显示 that a post has passed verification.
- Upvote/downvote with **verified weighting**: a verified check-in's vote counts more for hotness.
- Comments + nested threads; **comment gate** = same. It's only readable/writable for the challenge's members / community.
- Cross-challenge crosspost: no (avoid diluted evidence).

---

### 7.8 Chat (the Discord layer) — allows 24/7 (P1)

- Per-community `#lobby`, `#verdict-announce`, challenge-consolidated `#challenge-hq`.
- Channels; reactions; thread-in-chat; mention @Captain.
- The chat is the **hangout layer**, the feed is the **evidence layer**. They're separate surfaces, driven by the same identity.
- (Chat #"always on" is the intentional Discord-baby-trade: otherwise the community dies between windows.)

---

### 7.9 Level Up / Progression System (the motive)

**XP economy rules (P0 draft):**

| Action | XP | Caps / notes |
|---|---|---|
| Verified check-in | +**10** | per challenge |
| Challenge **completed** | +**50** | once per challenge |
| Streak day (check-in on consecutive days) | **+5×(day-1)** | capped +20/day |
| Creating a challenge that runs | +**30** | per challenge |
| Peer-verification given (alignment) | +**2** | max +20/day |
| Positive reaction on a Story/Note | +**1** | max +10/day |
| Community milestone (e.g., first 10 members) | +**25** | per milestone |

**Levels:**

| Level | Name | Cumulative XP required |
|---|---|---|
| 1 | Seeker | 0 |
| 5 | Apprentice | 500 |
| 10 | Competitor | 1000 |
| 25 | Specialist | 2500 |
| 50 | Master | 5000 |
| 75 | Grandmaster | 7500 |
| 100 | **Enigma** | 10,000+ |

- **Ranks per community:** Member → Veteran (10xp) → Hero (50) → Legend (100).
- **Badges (P0, sample):**

| Badge | Unlock |
|---|---|
| First Steps | 1st verified check-in |
| Consistency | 7-day streak |
| Iron Will | 30-day streak |
| Builder | Complete a 30-day build challenge |
| Page Turner | Complete a 10-books challenge |
| Scale-Breaker | Complete weight-loss (5 kg) challenge |
| Polyglot | Complete learn-a-language/Python challenge |
| Gregarious | Give 100 peer verifications |
| No-Cap | 10 challenge completions with 0 disputes |
| Grandmaster | Reach level 50 |

- **Profile:** level ring, XP bar, badges, challenge history, lean-per-real-prooff ledger.
- XP never decays (no stress-laddering; we deliberately don't punish).

---

### 7.10 Notifications

- Challenge: min 24h and min 1h before Live opens.
- Challenge Live, New story from a friend/community, Vote result of your check-in, Challenge ended (final verdict window), You've levelled (XP breakdown), You won a badge.

---

### 7.11 Profiles & Leaderboards

| Surface | Content |
|---|---|
| Profile | Avatar, level, XP, rank, badges, active/live challenge, streak, verification record (W-L) |
| Community leaderboard | XP this week / overall; "Most completed"; "Most verified-others". |
| Challenge leaderboard | top completers, best evidence, streak runs |
| Global leaderboard | optional, opt-in by community Captain |

---

### 7.12 Moderation & Safety

- **AI moderation:** profanity, harassment, health-advice safety disclaimer (fitness), self-harm detection.
- **Flag → report queue → mod panel** (Captain/mods).
- **Strikes:** 3 = 7-day suspension; 5 = permanent, appealable.
- Health-related content: community must include a disclaimer mask; no medical claims enforceable.

---

## 8. Key Flows (deep detail)

### 8.1 Challenge creation flow

1. Captain → `New challenge` → fill: name, criteria, verification type, evidence type, duration, schedule, XP/badges.
2. Preview → Publish → **Scheduled**.
3. Optionally `Notify all` (`@here` for challenge announcement + calendar invite).
4. On start time: auto-transitions to Live; feed gate unlocks. All posts/threads created now render with "Challenge X" chip and live countdown.

### 8.2 Check-in flow (the money moment)

1. User taps "Check-in 🔍" on Challenge → chooses evidence: gallery/camera/repo-link/data-link.
2. Enigma shows: *(uncommitted run sent to AI + bundled queue)*
3. If AI auto-approves: check-in posted instantly to the feed (verified badge) + XP granted.
4. If quued: "Under review" until 3 peers vote; verdict then posted (or re-run via AI on dispute).
5. User is notified; the leaderboard updates.

### 8.3 Challenge end/verdict flow

1. Timer ends → **Verification window** (48h quiet while stragglers finalize).
2. Final proofs run through the pipeline.
3. **Verdict publish:** posterboard hideout (top completers), XP & badges allocated; messy grievances `<24h`.
4. Archived: read-only + `stats for the record` (completion rate %, top 3, archives).

---

## 9. Monetization (non-exclusive; illustrative)

- **Freemium** — Pro ($4.99/mo): unlimited communities, advanced analytics, custom themes, off genre.
- **Community monetization** — Captains can charge entry for premium communities (revenue share 90%) — "Hackathon A" as a showcase community.
- **Sponsored challenges (P1)** — brands (eg. a notebook brand) promote a verified reading challenge; payout only if verification passes.
- **Stakes (P1)** — optional cash/crypto deposit per challenge; entire pool split among verified completers (this doubles as the "real stakes" hook, but be edge-careful legally).
- **B2B/B2Careers (P2)** — Hackathon A sl = portfolio evidence; "verified skills" verification badges on LinkedIn.

Advertising: disallow (degrades evidence trust).

---

## 10. Metrics & KPIs

| KPI | Target (MVP) | Why it matters |
|---|---|---|
| Challenge completion rate | ≥ 45% | vs 10% industry baseline — the value proof |
| Median verify latency | < 12h, aim < 1h (auto) | friction to evidence → UX |
| D30 retention | ≥ 25% | is the progression system sticky |
| Verification volume / live challenge | ≥ 0.8 check-ins per member | shows the feed is evidence-full |
| Dispute rate | < 2% | anti-abuse false-positive control |
| AI confusion matrix | Precision ≥ 0.95 (valid evidence), Recall ≥ 0.90 | don't block legit evidence |
| Community growth | 2 pilots → invite-only 20 | flywheel |

---

## 10. Non-Functional Requirements

- **Performance:** Live feed < 150ms p95; check-in posted <500ms upload + AI verdict <10s (async ok).
- **Reliability:** scheduled challenges machine-compensated; no clock-skew allows posting outside gates (server/time enforced: absolute UTC).
- **Security:** media E2E checksum + stores hashes; verify logs tamper-evident (append-only).
- **Privacy:** evidence visible only within its community (default); private communities: invite-only. Health data: EU-GDPR + HIPAA-adjacent care.
- **Compliance:** AGENTS / ToS: founds for stakes & health disclaimers.
- **Cost ceiling:** AI inference budget < $0.03 / check-in.
- **Scale:** 1000 concurrent at MVP; design for 100k.

---

## 11. High-Level Technical Architecture

```
[Client: Web (React/TS), Native (Flutter or Swift/Kotlin)]
          │
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
│ - media sanity, deductive/plausible, timestamps, │
│   duplicate hash, reverse-image                │
└──────────────────────────────────────────────────┘
     ▼
┌──────────────────────────────────────────────────┐
│ Data: PostgreSQL (relational), Redis (queues/    │
│ rate-limits), object store (evidence/audit logs) │
└──────────────────────────────────────────────────┘
```

**Suggested stack (nice-to-have):** Next.js/T3 app, tRPC, Prisma, PostgreSQL, Redis + BullMQ,
AWS S3 (media) with presigned URLs, OpenAI/Card in-line AI, Agora lightweight (for voice), CDN.

---

## 12. Roadmap

| Phase | Scope | Go/no-go gate |
|---|---|---|
| **MVP / 0 (6–8 wks)** | 2 pilot communities, challenge engine + lifecycle, gate posting, check-in, AI pre-screen (image/video), peer votes + rep, feed + stories + notes, XP/levels/badges, notifications, moderation, mobile web UI | Completion accuracy ≥ 45%, D7 ≥ 25% in pilots; dispute < 2%, set the False Positive |
| **v1 (3–4 mo)** | Chat rooms, stakes deposit, data integrations (GitHub/Garmin/Health), community self-serve opening, analytics, global leaderboards | 20 communities, 10k MAU |
| **v2** | Marketplace of challenges, sponsored brand, native apps, voice AI evidence, B2B "Verified Skills" export | 2+ revenue streams |

---

## 13. Risks & Mitigations

| Risk | Likelihood | Mitigation |
|---|---|---|
| Gamers cheat / 押 frames | High | AI duplicate + timestamps + peer flush; dispute loop |
| AI false positives stop legit members | Med-High | confidence thresholds per community; human override; Recall priority over Precision revenue |
| Posting gate kills activity ("dead-zone") | High | Chat + stories 24/7; challenge windows of varying lengths; weekly challenges |
| Health-safety blowups (fitness) | Med | disclaimers, content review, no medical advice |
| Stake-related legalities | Med | P1: legal + payment partner before shipping; cap sizes; delay to v1 |
| Community leaders burn out (publishing challenges) | Med | challenge templates; auto-cycles; AI moderation; Captain XP + perks |
| Privacy (health data, proofs) | Med-H | community-scoped default privacy, encryption, audit designs, GDPR/HIPAA-C |
| Engine over-dev | Med | start with deterministic boolean state machine; no open-source/editing blow-ups in MVP |

---

## 14. Open Questions (for a follow-up session)

1. Should chat be 24/7 (discord-birth) or also gated for the "dead zone" — even if it costs retention?
2. Is staking/P2P money worth the legal risk early? (A: hold till v2.)
3. Which of Hackathon A vs Anytime Fitness should MVP prioritize for the "hero" domain-see both for MVP?
4. Should the challenge host be an OG - Member in the challenge too (or only the community)?
5. AI: do we run a dedicated fine-tuned model on media, or a general VLLM + heuristics?
6. Data exports: want a "Verified" PDF export for brag/resume (name brand)?

---

## 15. Appendix — Glossary

| Term | Definition |
|---|---|
| Challenge Live | The only state where the feed unlocks |
| Check-in | Evidence submission against a challenge |
| Verification | AI+Peer process that legitimizes a check-in |
| Post | Discussion thread, gate by challenge-live |
| Story | 24-hour ephemeral update, always open |
| Note | Micro status update, always open |
| XP | Progression currency |
| Rep | Correctness of verification history |

---

*End of PRD.*