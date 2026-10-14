# 2. Estimations

This chapter estimates the size of Enigma and turns it into effort. Function Point Analysis measures functional size, and the Constructive Cost Model (COCOMO) converts size into effort. The results are a planning baseline, reviewed after each iteration.

## 2.1 Function Points

Function Point Analysis counts the information a system stores and moves, rather than lines of code. Each function is placed in one of five classes and given a complexity weight:

- ILF (Internal Logical File) — data the system keeps and maintains.
- EIF (External Interface File) — data the system reads but does not maintain.
- EI (External Input) — an action that brings data in and updates internal data.
- EO (External Output) — a process that produces derived output for users.
- EQ (External Inquiry) — a read-only lookup that displays data.

Applying these classes to the requirements, with average complexity, gives the counts below.

| Class | Description | Count | Weight | FP |
|---|---|---|---|---|
| ILF | User, Community, Membership, Challenge, Submission, Verification, XP/Badge | 7 | 10 | 70 |
| EIF | Read-only wearable and health data from vendor APIs | 1 | 7 | 7 |
| EI | Join/leave, check-in, create community or challenge, post/comment, vote, upload evidence, report | 8 | 6 | 48 |
| EO | Feed, leaderboard, search, discovery, reports, proof wall | 6 | 5 | 30 |
| EQ | Challenge queries, verification queue, notification lookup, profile/history, leaderboard | 5 | 4 | 20 |

The subtotals give the Unadjusted Function Points (UFP):

- UFP = 70 + 7 + 48 + 30 + 20 = 175 FP.

The UFP ignores non-functional factors such as performance and ease of use. The standard model captures these through fourteen General System Characteristics (GSC), each rated from 0 to 5. For Enigma the ratings total a Degree of Influence (DI) of 25, reflecting a mobile-first, networked product with strong performance and online-entry needs. The Value Adjustment Factor is then:

- TDI = 0.65 + 0.01 × 25 = 0.90.
- AFP = 175 × 0.90 = 157.5 ≈ 158 FP.

The full product is therefore about 158 function points; small changes in the GSC ratings move it by a few points.

## 2.2 Efforts

The COCOMO organic model suits Enigma, a small, self-contained product in a familiar domain. For a TypeScript and Next.js codebase that reuses framework code, about 45 lines of code are counted per function point:

- KLOC = 158 FP × 45 LOC/FP = 7,110 LOC ≈ 7.1 KLOC.

The organic COCOMO effort equation gives:

- Effort = 2.4 × KLOC^1.05 = 2.4 × 7.1^1.05 ≈ 18 person-months.

This is the effort for a full product built by a normal team, far more than one student can build in a semester. The project therefore cuts scope to the P0 MVP, which keeps roughly 55 to 60 per cent of the full size. Framework reuse, managed services and reduced non-functional depth mean the real effort is far below a second COCOMO run. In practice the P0 MVP is planned at about 6 to 8 person-weeks for a single student.

| Iteration | Duration | Deliverable |
|---|---|---|
| Itr-1 — P0 MVP | 6–8 person-weeks | Challenge engine, AI pre-screen, peer verification, gated feed, XP/badges, moderation |
| Itr-2 — v1 | 2–3 person-weeks | Chat, integrations, analytics, leaderboards |
| Itr-3 — v2 | 2–3 person-weeks | Marketplace, sponsored challenges, native apps |

The dominant recurring cost is AI inference, kept below $0.03 per check-in. Hosting at pilot scale is modest, so direct cash cost for the semester is small; the main cost is developer time. Estimates are checked again once coding starts.
