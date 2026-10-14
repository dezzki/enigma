# 3. Scheduling

This chapter turns the iterative process into a dated plan. The project repeats four process blocks — Communication, Planning, Modeling and Construction — once per iteration. The developer works part-time, so durations are in working weeks with weekends closed.

![Figure 3.1 — Project schedule (Gantt)](diagrams/gantt.png)

Figure 3.1 shows the Gantt chart starting 3 August 2026. Itr-1 holds one week of elicitation and planning, one week of modeling, four weeks building the P0 core, one week of testing, and one week to deploy to two pilot communities. Itr-2 runs in weeks 9 to 16, and Itr-3 is planned for the next term. The bars are sequential, so a delay in the P0 build pushes everything after it.

## 3.1 Milestones

Each milestone has a deliverable and a gate.

| # | Milestone | Deliverable | Weeks | Gate |
|---|---|---|---|---|
| 1 | Elicitation and planning | Personas, risk register, KPI plan | 1 | Open questions resolved |
| 2 | Modeling | Domain model, state machine, architecture, schema | 2 | Design review |
| 3 | Itr-1 build (P0 core) | Challenge engine, AI pre-screen, peer votes, gated feed, XP | 3–6 | End-to-end demo |
| 4 | Itr-1 testing | Unit, integration and end-to-end tests, KPI harness | 7 | Completion ≥ 45%, D7 ≥ 25%, dispute < 2% |
| 5 | Deployment (MVP) | CI/CD, pilot release, load test | 8 | 1,000 concurrent users, AI cost < $0.03 |

Milestones 4 and 5 use the go/no-go targets. If a gate fails, scope is cut rather than dates extended.

## 3.2 Work Breakdown and Sequencing

Work follows the dependency order below.

1. Communication and Planning: personas, interviews, user stories, iteration plan, risk register, technology choice and KPI plan.
2. Modeling: domain model, state machine, verification pipeline, architecture, schema and XP economy.
3. Construction: authentication and roles, then community create/read/update/delete and join/leave.
4. Construction: the challenge engine, then check-in, AI pre-screen, peer queue and anti-abuse flow.
5. Construction: the posting gate, feed and badges, Stories and Notes, then XP, notifications and profiles.
6. Construction: the moderation panel, tests and the KPI harness.
7. Demo, re-plan and deploy through CI/CD to the two pilot communities.
8. Itr-2 and Itr-3 features, which extend the MVP and are not on the critical path.

The critical path runs through the Itr-1 build, testing and deployment. The check-in and verification paths must exist before tests are meaningful, and deployment gates cannot be checked until those tests pass.

## 3.3 Resource Allocation

The project is a single-developer effort supported by a supervisor and pilot stakeholders.

| Resource | Role | Allocation |
|---|---|---|
| Developer | Architect, developer, designer, tester, DevOps | 15–20 hours per week; 6–8 weeks for Itr-1, then 2–3 weeks per iteration |
| Supervisor | Reviewer, gatekeeper, mentor | Weekly review and demo evaluation |
| Course peers | Reviewers and demo users | Periodic demos and feedback |
| Pilot captains | Stakeholders and product owners | Biweekly elicitation during Itr-1 |
| AI provider | Verification compute | Metered, below $0.03 per check-in |

The developer sits on every critical-path task, which is why scope is kept small.
