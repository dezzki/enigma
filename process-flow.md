# Enigma — Iterative Process Flow Breakdown

**Process model:** Iterative process flow (Communication → Planning → Modeling → Construction, looping; ends at Deployment)

**Iterations:**
- **Itr-1** = P0 MVP (6–8 wks)
- **Itr-2** = P1 v1 (3–4 mo)
- **Itr-3** = P2 v2

---

## Block 1 — Communication
*(outer loop + re-entered every time Construction finishes)*

| # | Task | PRD source | Iteration |
|---|---|---|---|
| C-1 | Stakeholder elicitation — define the 5 personas (Striver, Overachiever, Captain, Spectator, Verifier) | §5 | Itr-1 |
| C-2 | Pilot community interviews — Hackathon A captain, Anytime Fitness rep | §1, §5 | Itr-1 |
| C-3 | Resolve Open Questions §14 (chat 24/7?, stakes legality, hero domain, AI model choice, verified PDF export) | §14 | Itr-1 |
| C-4 | Refine user stories (e.g., Captain creates "30-day startup" challenge; member watches feed unlock at Live) | §7.1–7.2 | Itr-1 |
| C-5 | Post-iteration demo + feedback with professor/course peers (feeds back into Planning) | — | Every loop |
| C-6 | Re-negotiate scope with stakeholders when a go/no-go gate fails | §12 | Every loop |

## Block 2 — Planning
*(loops with Communication; also re-entered after each Construction block)*

| # | Task | PRD source | Iteration |
|---|---|---|---|
| P-1 | Iteration plan: map PRD roadmap → sprints (MVP 6–8 wks → v1 3–4 mo → v2) | §12 | Itr-1 |
| P-2 | Risk planning & mitigations (cheating, AI false positives, dead-zone, legal, burnout) | §13 | Itr-1 |
| P-3 | Scope-cutting rules per iteration (drop anything not demoable) | §12, §14 | Itr-1 |
| P-4 | Tech stack decision (Next.js/T3, tRPC, Prisma, PostgreSQL, Redis, BullMQ, S3, AI provider) | §11 | Itr-1 |
| P-5 | KPI definition & measurement plan (completion ≥45%, verify latency <12h, D30 ≥25%, dispute <2%) | §10 | Itr-1 |
| P-6 | Non-functional budget planning (feed <150ms p95, AI cost <$0.03/check-in, 1000 concurrent) | §10 | Itr-1 |
| P-7 | Semester timeline & milestone scheduling aligned to course deliverables | Course | Itr-1 |
| P-8 | Re-plan after each demo: re-prioritize backlog, adjust gates | — | Every loop |
| P-9 | Monetization rollout plan (freemium → community monetization → sponsored/stakes → B2B) | §9 | Itr-2/3 |

## Block 3 — Modeling
*(internal iterative loop: model → review → remodel)*

| # | Task | PRD source | Iteration |
|---|---|---|---|
| M-1 | Domain model: Community, Challenge, Check-in, Verification, Post, Story, Note, XP, Reputation, Streak | §6 | Itr-1 |
| M-2 | Challenge lifecycle state machine: Draft → Scheduled → Registration → Live → Verification window → Closed → Archived | §7.2 | Itr-1 |
| M-3 | Posting-gate design (feed locked unless challenge Live; chat/stories/notes 24/7) | §5.6 | Itr-1 |
| M-4 | Verification pipeline model: AI pre-screen (confidence 0–1) → routing table → peer quorum / rep-weighted votes → verdict + audit log | §7.6 | Itr-1 |
| M-5 | Anti-abuse rule modeling (1 check-in/6h, 20 votes/day cap, duplicate-evidence rejection, time-window auto-reject) | §7.6 | Itr-1 |
| M-6 | System architecture design (API gateway, core services, verification service, data layer) | §11 | Itr-1 |
| M-7 | Database schema + Redis queue design (relational core, job queues, object store for evidence) | §11 | Itr-1 |
| M-8 | XP economy model (+10 check-in, +50 complete, streak +5×(d−1), levels table) | §7.9 | Itr-1 |
| M-9 | UI/UX modeling (mobile web: feed, check-in flow, profile ring, leaderboards) | §7.7–7.11 | Itr-1 |
| M-10 | API contract design (REST + WebSocket; challenge state server-enforced, absolute UTC) | §10, §11 | Itr-1 |
| M-11 | Security & privacy model (media E2E checksums, append-only tamper-evident logs, community-scoped visibility, GDPR/HIPAA care) | §10 | Itr-1 |
| M-12 | Moderation rule model (AI profanity/harassment/self-harm detection, 3 strikes = 7-day suspension, 5 = ban, health disclaimers) | §7.12 | Itr-1 |
| M-13 | Chat model (lobby/verdict/challenge-hq rooms, threads, mentions) | §7.8 | Itr-2 |
| M-14 | Stake mechanics model (deposit pool, payout to verified completers, legal review) | §7.2, §9 | Itr-2 |
| M-15 | Source-integration model (GitHub / Apple Health / Garmin / Strava / Whoop OAuth + data contract) | §7.6 | Itr-2 |
| M-16 | Marketplace/sponsored-challenge model (brand payout only on passed verification) | §9 | Itr-3 |

## Block 4 — Construction
*(after finishing, loop back to Communication; repeat the whole flow)*

| # | Task | PRD source | Iteration |
|---|---|---|---|
| K-1 | Auth, roles & permissions (Owner/Captain/Moderator/Member/Spectator) — FC-4 | §7.1 | Itr-1 |
| K-2 | Community CRUD + join/leave + dashboard stats — FC-1/2/3 | §7.1 | Itr-1 |
| K-3 | Challenge engine: CRUD, schedule, server-enforced state machine — FB-1 | §7.2 | Itr-1 |
| K-4 | Registration + committed-members list — FB-2 | §7.2 | Itr-1 |
| K-5 | Check-in submission flow with evidence upload (media/link/data) | §8.2 | Itr-1 |
| K-6 | AI pre-screen integration (media sanity, duplicate hash, reverse-image, confidence output) — FC-6 | §7.6 | Itr-1 |
| K-7 | Peer verification queue (random sampling, rep-weighted votes) — FC-7 | §7.6 | Itr-1 |
| K-8 | Anti-abuse enforcement (rate limits, vote caps, time gates) — FC-10 | §7.6 | Itr-1 |
| K-9 | Posting-gate + time-based wall (feed unlocks on Live) | §5.6 | Itr-1 |
| K-10 | Feed: hot/new/top-verified/burned sorts, verified badges, weighted upvotes, nested comments | §7.7 | Itr-1 |
| K-11 | Stories (24h ephemeral) & Notes (permanent bullets) | §7.5 | Itr-1 |
| K-12 | XP/levels/badges/streaks engine | §7.9 | Itr-1 |
| K-13 | Notifications (challenge reminders, verdict results, level-ups, badges) | §7.10 | Itr-1 |
| K-14 | Profiles & leaderboards (community/challenge/global) | §7.11 | Itr-1 |
| K-15 | Moderation panel: flag/report queue, strikes, health disclaimer masks | §7.12 | Itr-1 |
| K-16 | Testing: unit/integration/E2E + KPI measurement harness | §10 | Itr-1 |
| K-17 | **Demo Itr-1 → back to Communication (C-5) → re-plan (P-8)** | — | Loop 1 |
| K-18 | Chat rooms (24/7 layer) | §7.8 | Itr-2 |
| K-19 | Stakes deposit + payout pool — FB-3 | §7.2 | Itr-2 |
| K-20 | Recurring challenge templates — FB-4 | §7.2 | Itr-2 |
| K-21 | Per-community verification policy + auto-approve threshold config — FC-5/8 | §7.6 | Itr-2 |
| K-22 | Source verification: GitHub/Apple Health/Garmin/Strava/Whoop — FC-9 | §7.6 | Itr-2 |
| K-23 | Freemium + community monetization (revenue split) | §9 | Itr-2 |
| K-24 | Analytics + global leaderboards + community self-serve | §9, §12 | Itr-2 |
| K-25 | **Demo Itr-2 → back to Communication (C-5) → re-plan (P-8)** | — | Loop 2 |
| K-26 | Sponsored challenges (brand payout gated on verification) | §9 | Itr-3 |
| K-27 | Challenge marketplace | §12 | Itr-3 |
| K-28 | Native apps + voice AI evidence | §12 | Itr-3 |
| K-29 | B2B "Verified Skills" export | §9 | Itr-3 |

## Final Block — Deployment & Release
*(standard end phase of the flow; then re-enters Communication for the next release)*

| # | Task |
|---|---|
| D-1 | CI/CD pipeline + staging/production deploy |
| D-2 | Release MVP to 2 pilot communities (Hackathon A, Anytime Fitness) |
| D-3 | Monitor §10 KPIs against go/no-go gates (completion ≥45%, D7 ≥25%, dispute <2%) |
| D-4 | Scale validation: load test 1000 concurrent, AI cost <$0.03/check-in |
| D-5 | Post-release review → feeds new Communication/Planning cycle |

---

## Flow Shape

```
Communication ⇄ Planning → [Modeling ⇄] → Construction → (demo) → Communication ...
        ↑                                                  │
        └──────────────────────────────────────────────────┘
  repeated for Itr-1 → Itr-2 → Itr-3 → ends at Deployment → new cycle
```
