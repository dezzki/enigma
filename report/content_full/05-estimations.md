# 2. Estimations

This chapter quantifies the size of Enigma and converts that size into effort, duration and cost. Two standard techniques are applied together. Function Point Analysis (FPA) measures functional size independently of the implementation language, and the Constructive Cost Model (COCOMO) converts that size into effort through an empirical formula. Because Enigma is a single-developer course project with a deliberately narrowed MVP, the two techniques function as a planning baseline rather than a fixed contract. The figures below are recalibrated after Phase 0 and re-checked after every iteration demo.

## 2.1 Function Points

Function Point Analysis counts the information a system moves and stores rather than the lines of code used to build it. Each identified function is classified into one of five component types and assigned a complexity weight; the sum of the weighted components is the Unadjusted Function Point (UFP) count. The five component classes are:

- ILF (Internal Logical File) — a logical group of data that the system maintains internally.
- EIF (External Interface File) — data that the system references but does not maintain.
- EI (External Input) — a process that brings data into the system and maintains an internal file.
- EO (External Output) — a process that produces derived output for the user.
- EQ (External Inquiry) — a process that retrieves and displays data without updating it.

Applying this classification to the functional requirements in §1.3 and the data dictionary in §1.7, and assuming average complexity throughout, gives the counts in the following table. The ILF group covers the persistent domain entities; the single EIF represents read-only wearable and health data pulled from vendor APIs; the EI group covers the state-changing user actions; the EO group covers derived views computed by the system; and the EQ group covers read-only lookups.

| Class | Illustrative function group | Count | Weight | FP |
|---|---|---|---|---|
| ILF | User, Community, Membership, Challenge, Submission, Verification, XP/Badge | 7 | 10 | 70 |
| EIF | Wearable/health data referenced read-only from vendor APIs | 1 | 7 | 7 |
| EI | Join/leave, submit check-in, create community, create challenge, post/comment, vote, upload evidence, report/flag | 8 | 6 | 48 |
| EO | Feed, leaderboard, search, discovery, reports, proof wall | 6 | 5 | 30 |
| EQ | Challenge queries, verification queue, notification lookup, profile/history, leaderboard query | 5 | 4 | 20 |

Summing the subtotals gives the Unadjusted Function Point count:

- UFP = 70 + 7 + 48 + 30 + 20 = 175 FP.

The unadjusted count deliberately ignores non-functional characteristics such as performance, reuse and operational complexity. These are captured by the fourteen General System Characteristics (GSC) of the standard Function Point model. Each characteristic is rated from 0 (no influence) to 5 (strong influence), and the ratings are summed into the Degree of Influence (DI). The ratings below reflect a mobile-first, networked product with strong performance and online-entry needs, modest reuse, and modest multi-site requirements during the pilot.

| # | General System Characteristic | Rating (0–5) |
|---|---|---|
| 1 | Data communications | 3 |
| 2 | Distributed data processing | 2 |
| 3 | Performance | 4 |
| 4 | Heavily used configuration | 1 |
| 5 | Transaction rate | 3 |
| 6 | Online data entry | 3 |
| 7 | End-user efficiency | 3 |
| 8 | Online update | 2 |
| 9 | Complex processing | 1 |
| 10 | Reusability | 0 |
| 11 | Installation ease | 1 |
| 12 | Operational ease | 1 |
| 13 | Multiple sites | 0 |
| 14 | Facilitate change | 1 |
| | **Total (DI)** | **25** |

The Value Adjustment Factor (TDI) and the Adjusted Function Points (AFP) are then computed explicitly:

```
TDI = 0.65 + 0.01 × ΣGSC
    = 0.65 + 0.01 × 25
    = 0.65 + 0.25
    = 0.90

AFP = UFP × TDI
    = 175 × 0.90
    = 157.5  ≈ 158 FP
```

The adjusted size of the full product is therefore approximately 158 function points. The result is sensitive to the subjective GSC ratings: a change of ±5 in the DI total moves the TDI by 0.05, and the AFP by 175 × 0.05 = 8.75 FP. The honest reading is a range of roughly 150 to 166 FP centred on 158 FP, and this range is carried forward into the effort estimate.

## 2.2 Efforts

The COCOMO organic model is appropriate because Enigma is a small, self-contained product with a familiar application domain and largely flexible requirements in its early iterations. For a modern TypeScript, React and Next.js codebase that reuses framework scaffolding, a language factor of approximately 40 to 50 lines of code per function point is used. Taking the midpoint of 45 LOC per FP gives the following size in thousands of lines of code (KLOC):

- KLOC = 158 FP × 45 LOC/FP = 7,110 LOC ≈ 7.1 KLOC.

The organic COCOMO effort equation, where effort is expressed in person-months, is then evaluated:

```
Effort = 2.4 × KLOC^1.05
       = 2.4 × 7.11^1.05
       ≈ 2.4 × 7.85
       ≈ 18.8 person-months  ≈ 18 person-months
```

The corresponding nominal schedule and average team size follow from the standard organic development-time equation:

```
TDEV = 2.5 × Effort^0.38
     = 2.5 × 18.8^0.38
     ≈ 2.5 × 3.05
     ≈ 7.6 months

Average staffing = Effort / TDEV
                 = 18.8 / 7.6
                 ≈ 2.5 persons
```

The model therefore describes a nominal effort of roughly 18 person-months, delivered by a team of about two and a half people over approximately seven and a half months. Expressed in person-weeks this is 18.8 × 4.33 ≈ 81 person-weeks, which is far beyond what a single developer can produce part-time within a semester. The conclusion is deliberate: the 158 FP and 18 person-month figures describe the full product roadmap, not the semester deliverable, and a disciplined scope cut is required.

The course project therefore reduces scope to the P0/MVP cut. Applying the priority legend of §1.3 conservatively, the MVP retains roughly 55 to 60 per cent of the full functional size. At 58 per cent:

- MVP function points = 158 × 0.58 ≈ 92 FP
- MVP size = 92 FP × 45 LOC/FP = 4,140 LOC ≈ 4.1 KLOC.

This 4.1 KLOC slice is the quantity against which the single-developer plan is actually built. It is important to note why the planned effort is smaller than a second COCOMO run on 4.1 KLOC would suggest. A COCOMO evaluation of the reduced slice, 2.4 × 4.1^1.05 ≈ 2.4 × 4.40 ≈ 10.6 person-months, still assumes a full lifecycle of specification, reviews, documentation and non-functional depth. The course project substitutes mature frameworks (T3, tRPC, Prisma), managed services (PostgreSQL, Redis, S3) and open-source UI libraries for custom infrastructure, and reduces non-functional depth to what the pilot needs rather than the full 100,000-concurrent design ceiling. These substitutions raise effective productivity well above the COCOMO nominal, and the demoable increments are scoped accordingly, as shown below.

| Iteration | Duration | Deliverable |
|---|---|---|
| Itr-1 — P0 MVP | 6–8 person-weeks | Challenge engine and server-enforced lifecycle, check-in with AI pre-screen, peer verification queue, gated feed, Stories and Notes, XP/levels/badges, notifications, moderation |
| Itr-2 — v1 (course slice) | 2–3 person-weeks (3–4 months at full roadmap scope) | Chat rooms, stakes plumbing, source integrations (GitHub, Apple Health, Garmin, Strava, Whoop), analytics, global leaderboards |
| Itr-3 — v2 (course slice) | 2–3 person-weeks (next term at full roadmap scope) | Marketplace, sponsored challenges, native apps, B2B Verified Skills export |

The duration column distinguishes the reduced scope that the single developer demonstrates from the full roadmap duration that the same features would require at production depth.

Productivity can be cross-checked against the MVP slice. The 4.1 KLOC estimate spread over 6 to 8 person-weeks, at a five-day working week, represents 30 to 40 working days. This yields 4,100 / 40 ≈ 103 to 4,100 / 30 ≈ 137 net lines of production code per working day. A planning figure of roughly 100 to 140 net LOC per day is therefore consistent with the MVP effort, remembering that physical lines including tests, configuration and generated types are higher than this net measure.

A rough cost estimate can also be derived. The dominant recurring cost is AI inference, contractually bounded at under $0.03 per check-in. For a pilot of two communities with approximately 2,500 members each, a user base of 5,000, and at least 0.8 check-ins per member per challenge, the number of verifications per challenge cycle is 5,000 × 0.8 = 4,000, giving 4,000 × $0.03 = $120 per cycle. At the 100,000-concurrent design ceiling the same arithmetic gives 100,000 × $0.03 = $3,000 per full verification sweep. Hosting is illustrative and not part of the functional estimate: managed PostgreSQL might cost $50–200 per month, Redis $20–60, object storage and CDN $10–80, and application compute $50–300, for a total of roughly $130–640 per month at pilot scale, excluding AI. Vendor pricing varies, so these figures are planning aids only. The direct cash cost for the semester pilot consequently remains in the low hundreds of dollars, and the dominant real cost is developer time rather than infrastructure.

All estimates are recalibrated after Phase 0. Once elicitation and modeling produce a first working vertical slice, actual lines of code and story throughput are measured, the LOC/FP ratio is recomputed, the GSC ratings are re-scored, and the AFP and effort figures are updated. Each iteration demo triggers the same re-planning cycle described in §3.4.

## 2.3 Estimation Assumptions and Risks

The following assumptions bound the estimates, and each carries an associated risk that is monitored during the project.

- The function point count is derived from the functional requirement set, and every component is assumed to be of average complexity. Re-classifying boundary cases, such as whether the verification audit log is a separate ILF, would change the UFP total.
- The GSC ratings are subjective. A ±5 shift in the Degree of Influence changes the adjusted size by approximately 9 FP and the effort estimate by roughly 8 per cent, so the 158 FP value should be read as a central estimate within a range.
- The 45 LOC-per-FP language factor is an approximation for a TypeScript and Next.js codebase. A factor of 40 or 50 moves the KLOC estimate by about ±11 per cent and the COCOMO effort by a similar proportion.
- COCOMO was calibrated on projects with formal process overhead. Framework reuse and managed services materially change productivity, so the single-developer plan is anchored to observed throughput rather than to the model alone.
- The developer works part-time under a course load of roughly 15 to 20 hours per week, with weekends closed. Effort measured in person-weeks therefore consumes more calendar time than the same effort at full-time intensity.
- AI provider pricing, latency and model quality may change. The under-$0.03-per-check-in ceiling is a requirement, not a guarantee, and exceeding it would invalidate the cost estimate and trigger the mitigation in §4.3.
- Scope may change as open questions are resolved during elicitation. Any material change to the P0 cut invalidates the 92 FP MVP subset and requires a re-baseline.
- The pilot-scale infrastructure figures are illustrative and depend on vendor selection. They are presented to bound cash cost, not to commit to a procurement decision.
- If any go/no-go KPI gate fails at an iteration demo, the response is to re-scope rather than to extend the deadline, so schedule and effort figures may be revised downward in later iterations.
