# Process Model

## Choice of Process Model

The project adopts an **iterative process model**, in which Communication, Planning, Modeling and Construction repeat in successive passes, each concluding with demonstration and feedback until the work is ready for Deployment. Enigma's requirements are broad, several assumptions are unproven, and central questions — how strict the posting gate should be, whether peer verification is fast enough in practice, and how well AI confidence thresholds separate honest from fraudulent evidence — can only be answered empirically. The process must therefore produce working software early and treat feedback as a first-class input.

Waterfall was rejected because it fixes a complete requirements baseline before construction, yet Enigma's planning documents record open questions and provisional scope; a linear model would hide any misjudgement until integration, when it is most expensive to correct. A parallel process was rejected because Enigma's modules are tightly coupled — the challenge engine, verification pipeline, progression engine and feed all depend on one another — requiring their interfaces to be correct in advance. Incremental delivery is closer but insufficient: it suits a task whose main risk is integration, whereas Enigma's risks are conceptual and behavioural, so validating a complete lifecycle late would postpone the most important learning. The iterative model instead produces a thin end-to-end slice each pass and feeds its results back into Communication.

| Model | Core idea | Strengths | Fit for Enigma |
|---|---|---|---|
| **Waterfall** | Sequential single pass; requirements fixed up front | Clear milestones, strong documentation, simple to manage | Poor: assumptions are provisional, so feedback arrives too late to change scope |
| **Parallel** | Independent teams build separate components concurrently | Fast when components are decoupled and interfaces are stable | Poor: Enigma's modules are interdependent and its interfaces evolve |
| **Incremental** | Deliver a completed subset at each increment, then integrate | Early partial delivery; integration faults surface sooner | Partial: validates integration but delays learning about behaviour and usability |
| **Iterative** | Repeat all phases in small passes, each producing a demonstrable slice and refining scope from feedback | Early end-to-end working software; assumptions tested each pass; scope cut gracefully | Strong: matches uncertainty, coupling and the need for visible demonstrations |

## The Iterative Loop

The loop begins with **Communication** and returns to it after each build; each traversal is one iteration, ending with a demonstration that seeds the next Planning pass.

![Figure 2.1 — Iterative process flow](diagrams/process-loop.png)

**Communication** establishes what is to be built and why: eliciting requirements from the two pilot communities, defining personas and their jobs-to-be-done, resolving open questions and refining user stories. Its most important contribution is scope negotiation, since the requirements far exceed a course timetable; Communication decides which capabilities belong in the current pass.

**Planning** converts those decisions into a workable plan. It maps the roadmap onto iterations, maintains the risk register, fixes the technology stack, specifies measurable indicators and non-functional budgets, and schedules against course milestones, enforcing the rule that each iteration must yield something demonstrable: anything that cannot be shown is deferred.

**Modeling** produces the designs that make construction predictable: the domain model of communities, challenges, submissions and verification; the challenge lifecycle state machine; the posting gate as a function of challenge state and server time; the verification pipeline and its routing thresholds; the progression economy; and the system architecture. Models are reviewed before coding and are cheap to revise.

**Construction** turns the models into working software through implementation, testing and assembly of a demonstrable slice. The first iteration builds the core: communities, the challenge engine, check-in submission, the AI pre-screen, the peer queue, the gated feed, XP, notifications and moderation.

The iteration closes with a **demonstration and feedback** step: the slice is presented to the supervisor and course peers, what works and what confuses users is recorded, and the team returns to Communication to replan. A sound slice proceeds to **Deployment**: for the MVP, releasing to the two pilot communities, monitoring the go/no-go indicators and feeding the post-release review into the next Communication pass.

## Iterations and Scope

Three iterations are planned, each with a distinct scope and exit gate; priorities follow the legend P0 for the MVP, P1 for the first release and P2 later.

| Iteration | Scope | Duration | Principal deliverables | Exit gate |
|---|---|---|---|---|
| **Itr-1 — P0 MVP** | Core challenge lifecycle and verification | 6–8 weeks | Communities and memberships; challenge engine with lifecycle and milestones; check-in submission and evidence upload; AI pre-screen for photo, link and quiz proofs; peer queue for Level 3 and above; posting gate with the liveness rule; Stories and Notes; XP, levels, streaks and leaderboards; deadline and verdict notifications; moderation panel | End-to-end demonstration from challenge creation to verified completion; completion ≥ 45 per cent, D7 ≥ 25 per cent, dispute < 2 per cent; 1,000 concurrent users; AI cost < 0.03 USD per check-in |
| **Itr-2 — P1 v1** | Depth, integrations and self-service | 3–4 months | Chat rooms as the always-on layer; optional stakes subject to legal review; source integrations for GitHub and wearables; per-community verification policy; appeals and grace periods; completion reports and CSV export; analytics and global leaderboards; community self-service | Retention and verification-throughput targets met; 20 communities onboarded |
| **Itr-3 — P2 v2** | Scale and commercialisation | Next term | Challenge marketplace; sponsored challenges with verification-gated payouts; native applications and additional evidence types; business-to-business verified-skills export | At least two viable revenue streams; production load and reliability targets met |

The iterations are cumulative, so each gate is a release decision for the next pass.

## Activities per Phase

| Phase | Principal activities | Key artefacts |
|---|---|---|
| **Communication** | Elicit requirements from pilot communities; define personas and jobs-to-be-done; resolve open questions; refine user stories; demonstrate each iteration and gather feedback; renegotiate scope when a gate fails | Personas, user stories, resolved open questions, demonstration feedback log, revised scope |
| **Planning** | Plan iterations and sprints; maintain the risk register and mitigations; fix the technology stack; define key-performance indicators and the measurement approach; set non-functional budgets; schedule against course milestones; re-prioritise after each demonstration | Iteration plan, risk register, indicator plan, schedule, effort estimate |
| **Modeling** | Design the domain model; specify the challenge state machine; design the posting gate; model the verification pipeline and anti-abuse rules; design the architecture, database schema, queues and API contracts; design the progression economy; design the privacy, security and moderation models | Domain model, state machine, verification model, data dictionary, architecture and schema diagrams, API contract |
| **Construction** | Implement core services and interfaces; integrate the AI pre-screen and peer queue; enforce rate limits and deadline integrity; build the feed, Stories, Notes and progression engine; write unit, integration and end-to-end tests; assemble the demonstrable slice | Working software, test suites, build and configuration, iteration demonstration |
| **Deployment** | Operate continuous integration and delivery; release the MVP to the two pilot communities; monitor indicators against gates; validate load and inference cost; review the release and feed lessons into the next Communication pass | Release to pilots, monitoring dashboards, load and cost reports, post-release review |

## Roles and Responsibilities

The project is a single-developer course effort, so there is no internal division of labour and the process relies heavily on external feedback. Responsibilities are therefore defined by function rather than team.

The **student developer** performs every engineering role — elicitation, planning, design, implementation, testing, deployment and process artefacts — and owns the risk register and indicator harness, deciding at each demonstration which deferred items are reprioritised.

The **project supervisor** supplies the requirements validation and design review a team would provide internally, reviewing each iteration's scope and models, challenging assumptions during demonstrations and confirming whether the entry and exit criteria have been met.

**Course peers** form the demonstration audience and act as surrogate users: their feedback on clarity, usability and credibility is the primary qualitative input between iterations, and their review of design and test artefacts substitutes for peer inspection.

The **pilot communities** — the captain of Hackathon A and a representative of Anytime Fitness — supply the real requirements and become the first users at Deployment, validating whether the challenge model fits community practice.

## Entry/Exit Criteria and Feedback

Each iteration is entered only when its predecessor's exit criteria are satisfied, expressed as testable gates rather than subjective judgements. The first iteration requires reviewed requirements, an agreed scope, a technology decision and an initial risk register; later ones begin once the preceding slice has passed its demonstration and its deferrals have been replanned.

The **demonstration gate** is the central control. Before an iteration is complete, the developer must present a working end-to-end path — for the first iteration, a member joins a live challenge, submits evidence, receives a verdict through the automated or peer path, accrues experience and sees the outcome on an archived challenge. The supervisor and course peers review this demonstration, and their observations feed directly into Communication.

The **go/no-go indicators** are the quantitative component of the exit criteria. The MVP advances only if the verified completion rate reaches at least forty-five per cent, day-seven retention at least twenty-five per cent, and the dispute rate stays below two per cent. Supporting gates require AI precision of at least ninety-five per cent and recall of at least ninety per cent, an automated verdict within thirty seconds, a peer verdict within twenty-four hours, one thousand concurrent users and an inference cost below three United States cents per check-in. A failed gate triggers replanning rather than stopping the project: the offending area is fixed in a corrective pass or moved to a later iteration.

Feedback re-enters the process at a single well-defined point: every demonstration, pilot observation and failed gate is recorded in Communication and carried into Planning, where the backlog is re-prioritised and the next iteration's scope fixed. The loop is therefore not merely a cycle of build-and-test but the mechanism by which uncertainty is reduced: each pass replaces an assumption with a measurement, until the remaining work is small enough to deploy with confidence.
