# 3. Scheduling

This chapter translates the iterative process model into a dated plan. The schedule follows the four process blocks of the process-flow document — Communication, Planning, Modeling and Construction — and repeats them once per iteration, ending in deployment. Because the project is delivered by a single developer working part-time, the schedule is expressed in working weeks with weekends closed, and every iteration terminates in a demo that feeds back into Communication and re-planning.

![Figure 3.1 — Project schedule (Gantt)](diagrams/gantt.png)

Figure 3.1 presents the resulting Gantt chart. The project starts on 3 August 2026, and Saturdays and Sundays are marked as closed, so the durations are working weeks rather than calendar weeks and the elapsed calendar span is slightly longer than the bar lengths suggest. The first five bars form the Itr-1 sequence: one week of elicitation and planning, one week of modeling and design, four weeks building the P0 core, one week of testing with the KPI harness, and one week to deploy the MVP to the two pilot communities. The deployment marker sits at the end of the MVP deployment bar. The Itr-2 v1 build then occupies the following seven-week window, and the Itr-3 v2 build is scheduled for the next term. Because the bars are sequential with no deliberate overlap, the chart shows a single unbroken critical chain: a delay in the P0 build pushes testing, deployment and every later milestone. That property is the reason the plan reserves explicit buffer and applies strict scope-cutting rules rather than allowing milestones to slide.

## 3.1 Milestones

The milestone plan below is aligned to the iterative process model and the product roadmap. Each milestone has a deliverable and a gate that must be satisfied before the next milestone is considered on track.

| # | Milestone | Deliverable | Weeks | Gate / Check |
|---|---|---|---|---|
| 1 | Elicitation & planning | Personas, risk register, KPI plan | 1 | Open questions resolved |
| 2 | Modeling | Domain model, state machine, architecture, DB schema | 2 | Design review |
| 3 | Itr-1 build (P0 core) | Challenge engine, check-in, AI pre-screen, peer votes, gated feed, XP, notifications, moderation | 3–6 | Demo: end-to-end challenge → verified check-in |
| 4 | Itr-1 testing | Unit/integration/E2E, KPI harness | 7 | Completion ≥45%, D7 ≥25%, dispute <2% |
| 5 | Deployment (MVP) | CI/CD, release to 2 pilot communities, load test | 8 | 1000 concurrent, AI cost <$0.03 |
| 6 | Itr-2 (v1) | Chat, stakes, source integrations, analytics, global leaderboards | 9–16 | 20 communities, 10k MAU |
| 7 | Itr-3 (v2) | Marketplace, sponsored challenges, native apps, B2B export | next term | 2+ revenue streams |

The gate column is quantitative wherever the requirement allows it. Milestone 4 measures the product KPI targets of §1.4, namely completion at or above 45 per cent, day-seven retention at or above 25 per cent, and a dispute rate below 2 per cent. Milestone 5 verifies throughput and cost by load-testing 1,000 concurrent users and confirming an AI inference cost below $0.03 per check-in. Later milestones carry growth and revenue gates because v1 and v2 are roadmap phases rather than semester deliverables. Milestones 1 and 2 are prerequisites rather than product increments: they resolve the open questions and fix the domain model, state machine and database schema that the build depends on.

The milestone durations are consistent with the estimation chapter. Itr-1 spans weeks 1 to 8, matching the 6 to 8 person-week effort estimate for the P0 MVP once the one-week elicitation and modeling phases are included. Itr-2 occupies weeks 9 to 16 in the milestone plan; the Gantt chart illustrates the core seven-week build window and reserves the remaining time for its demo, feedback and re-planning. Itr-3 is explicitly dependent on the next academic term and is therefore scheduled without fixed dates.

## 3.2 Work Breakdown and Sequencing

The work breakdown structure is derived directly from the four process blocks and their tasks, arranged in the order required by their dependencies. The ordering below is the planned construction sequence; each numbered item is only started when the items it depends on are complete or stable enough to build against.

1. Communication tasks C-1 to C-4: define the personas, interview the two pilot communities, resolve the open questions, and refine the user stories.
2. Planning tasks P-1 to P-7: produce the iteration plan, the risk register, the scope-cutting rules, the technology decision, the KPI measurement plan, the non-functional budget, and the semester timeline.
3. Modeling tasks M-1 to M-12: the domain model, the challenge lifecycle state machine, the posting-gate design, the verification pipeline model, the anti-abuse rules, the architecture, the database schema, the XP economy, the UI model, the API contract, the security and privacy model, and the moderation rules.
4. Construction tasks K-1 and K-2: authentication, roles and permissions, followed by community CRUD and join/leave.
5. Construction tasks K-3 and K-4: the challenge engine with its server-enforced state machine, then registration and the committed-members list.
6. Construction tasks K-5 to K-8: the check-in submission flow, the AI pre-screen integration, the peer verification queue, and anti-abuse enforcement.
7. Construction tasks K-9 to K-11: the posting gate, the feed with its sorts and verified badges, and the Stories and Notes surfaces.
8. Construction tasks K-12 to K-14: the XP, levels, badges and streaks engine, notifications, and profiles with leaderboards.
9. Construction task K-15: the moderation panel, flag queue, strikes and health disclaimers.
10. Construction task K-16: unit, integration and end-to-end tests together with the KPI measurement harness.
11. Demo of Itr-1 (K-17), returning to C-5 for feedback and P-8 for re-planning.
12. Deployment tasks D-1 to D-5: the CI/CD pipeline, release to the two pilot communities, monitoring of the KPI gates, the scale and cost validation, and the post-release review.
13. Itr-2 tasks K-18 to K-24: chat rooms, stakes, source integrations, verification policy configuration, monetisation plumbing, analytics and global leaderboards, ending in the second demo and re-plan (K-25).
14. Itr-3 tasks K-26 to K-29: sponsored challenges, the challenge marketplace, native apps and the B2B Verified Skills export.

The critical path runs through the Itr-1 build, then Itr-1 testing, then MVP deployment — that is, K-1 through K-15, followed by K-16 and then D-1 through D-4. The dependency is structural rather than optional: the check-in and verification paths must exist before integration and end-to-end tests can be meaningful, and the deployment gates cannot be evaluated until those tests pass. Modeling is a further prerequisite, because the state machine and schema are fixed before the challenge engine is built. Itr-2 and Itr-3 are not on the deployment critical path, but they are strictly sequential with respect to Itr-1, since they extend surfaces and integrations that only exist once the MVP works.

## 3.3 Resource Allocation

Because the project is a single-developer effort supported by a supervisor and pilot stakeholders, the resource plan is deliberately small. The table below lists each resource, its role, and how much of it is allocated.

| Resource | Role | Allocation |
|---|---|---|
| Single developer | Architect, developer, designer, tester, DevOps | ~15–20 hours per week, part-time; 6–8 weeks for Itr-1, then 2–3 weeks per later iteration |
| Project supervisor | Reviewer, gatekeeper, technical mentor | Weekly review session plus evaluation of each demo |
| Course peers | Peer reviewers, demo users, verifier testers | Periodic demos and informal usability feedback |
| Pilot captain (Hackathon A) | Stakeholder and product owner | Biweekly elicitation during Itr-1 |
| Anytime Fitness representative | Stakeholder and product owner | Biweekly elicitation during Itr-1 |
| Multimodal AI provider | Verification compute | Metered API calls, bounded at under $0.03 per check-in |
| Cloud platform (PostgreSQL, Redis/BullMQ, S3, CDN) | Managed runtime infrastructure | Pay-as-you-go at pilot scale |
| GitHub and CI runners | Version control and CI/CD | Continuous throughout construction |
| Two pilot communities | Early test users | From MVP release onward |

The developer is the only full-time resource and therefore sits on every critical-path task, which is why the schedule keeps scope small and prioritises the P0 cut. The supervisor provides the gate review that must be passed at the end of each iteration. The pilot representatives supply requirements and acceptance feedback rather than implementation labour. The remaining resources are external services that are consumed rather than staffed, so their allocation is expressed as metered or pay-as-you-go usage.

## 3.4 Scheduling Assumptions

The schedule rests on the following assumptions, which are reviewed whenever an iteration demo changes the plan.

- The developer works part-time under a normal course load, estimated at 15 to 20 hours per week, so effort measured in person-weeks consumes more calendar time than full-time work would.
- Weekends are closed, consistent with the Gantt chart, and are not counted in milestone durations.
- A buffer is reserved inside the test and deployment weeks. If a gate fails, the buffer is consumed before any later milestone is allowed to move.
- Every iteration ends in a demo that feeds back into Communication, and the plan explicitly permits re-planning; scope is cut before dates are extended.
- External dependencies — the multimodal AI provider, OAuth login and wearable APIs — are assumed to be available and stable throughout construction.
- The KPI gates are treated as genuine decision points. A failed gate triggers the contingency in §4.4 rather than an automatic schedule extension.
- Itr-3 depends on the next academic term and on the results of the first two iterations, so its dates remain provisional until those inputs are known.
- Durations are estimates rather than commitments, and the first milestone may revise them once elicitation resolves the open questions.
