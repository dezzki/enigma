# Process Model

## Choice of Process Model

Enigma uses an **iterative process model**: the team builds a small part of the system, shows it, collects feedback, and repeats. Many design questions, such as how strict the posting rule should be and whether peer checks are fast enough, can only be answered by building and testing. Waterfall finishes all requirements before coding, so a wrong assumption is found late and is expensive to fix.

## The Iterative Loop

The loop starts with **Communication**, which decides what to build and why. It then moves to **Planning**, **Modeling** and **Construction**, and returns to Communication after a demonstration. Each full pass is one iteration.

![Figure 2.1 — Iterative process flow](diagrams/process-loop.png)

Communication gathers needs from the two pilot communities and chooses a realistic scope. Planning turns that into a schedule and a risk list. Modeling draws the designs before coding. Construction writes and tests the software. The iteration ends with a demonstration, where the supervisor and course peers give feedback that shapes the next pass.

## Iterations and Scope

Three iterations are planned, following priority levels P0 for the MVP, P1 for the first release and P2 for later work.

| Iteration | Scope | Duration | Main deliverables | Exit gate |
|---|---|---|---|---|
| **Itr-1 — P0 MVP** | Core challenge lifecycle and verification | 6–8 weeks | Challenge engine, evidence upload, AI pre-screen, peer queue, liveness rule, Stories, Notes, XP and levels | End-to-end demo; completion ≥ 45%, D7 ≥ 25% |
| **Itr-2 — P1 v1** | Depth and integrations | 3–4 months | Chat rooms, GitHub and wearable links, appeals, analytics | Retention and verification targets met |
| **Itr-3 — P2 v2** | Scale and revenue | Next term | Marketplace, sponsored challenges, extra proof types | Two viable revenue streams |

Each iteration builds on the last, so every exit gate decides whether the next begins.

## Activities per Phase

Communication collects requirements and feedback. Planning sets scope, schedule and risks. Modeling produces the designs. Construction implements and tests the software. Deployment releases the MVP and tracks results.

## Roles and Responsibilities

This is a single-developer course project, so there is no internal team: the student does all engineering work and keeps the risk list. The supervisor checks scope and design, as an internal team would. Course peers act as test users during demonstrations, and the two pilot communities become the first users.

## Entry/Exit Criteria and Feedback

Each iteration starts only after the previous one passes its exit gate. The main gate is the **demonstration**: a working end-to-end path, from joining a live challenge to a verdict and an archived result. Numbers form the second gate: the MVP advances only if completion reaches 45 per cent and day-seven retention reaches 25 per cent. If a gate fails, the team replans. All feedback returns through Communication into the next Planning pass.
