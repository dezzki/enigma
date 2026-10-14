# 7. Testing

## 7.1 Test Strategy

The testing effort for Enigma is organised into four complementary layers, each mapped to specific functional and non-functional requirements defined in Chapter 1. The layers follow the conventional test pyramid: a broad base of fast unit tests, a middle band of integration tests, a smaller set of end-to-end system tests, and a final performance and acceptance tier. Every layer exists to produce evidence that the requirements are satisfied without relying on self-reported behaviour, which mirrors the product's own principle that claims must be verified rather than asserted. The strategy is deliberately requirement-driven: no test case exists without a traceable requirement identifier, and no functional requirement is considered delivered until at least one case exercises it.

The **unit layer** exercises pure domain logic in isolation. It covers the server-enforced challenge state machine (FR-3.2), the atomic submission cutoff (FR-3.6), the liveness gate that governs posts (FR-4.1), story time-to-live (FR-4.3), the AI confidence routing table (FR-5.3, FR-5.4), peer quorum resolution including split decisions (FR-5.5), XP accounting and daily caps (FR-6.1, FR-6.7), perceptual-hash duplicate detection (FR-10.4), the exclusion of self-verification (FR-10.7), and role-based access checks (FR-9.2). These functions carry the highest logical risk and the lowest execution cost, so they are tested exhaustively, including at boundary values.

The **integration layer** verifies that independent services cooperate correctly across real boundaries: the API gateway, the PostgreSQL relational store, the Redis and BullMQ job queues, S3 presigned uploads, and external providers such as GitHub, Google OAuth, and wearable APIs. Integration tests confirm that a repository link is genuinely validated against the GitHub REST API, that quiz submissions are screened for plagiarism and implausible timing, that scale-photo evidence is cross-checked against wearable trends, that authentication correctly yields a JWT and enforces roles downstream, and that offline drafts reconcile with the server on reconnect without falsifying timestamps.

The **system and end-to-end layer** replays the five product scenarios defined in the PRD against the complete application, spanning the mobile client, the administrative console, the API, and the verification pipeline. These tests follow a single persona across multiple screens and assert not only the visible outcome but also the underlying state changes, such as challenge status, XP events, badges, verification records, and leaderboard snapshots. The scenarios therefore function as living acceptance criteria, and each is expected to exercise several requirement groups at once.

The **performance and acceptance layer** measures the service-level objectives and key performance indicators that determine whether the MVP is fit to launch. It validates the response-time and throughput budgets from §1.4, the AI latency and cost envelope, the precision and recall targets, and the pilot KPI gates for completion and retention. Performance testing is treated as a release gate rather than a diagnostic afterthought, so a regression in latency or cost blocks deployment in the same way that a functional defect does.

**Tools.** Unit and integration tests are written with Jest and Vitest against the shared TypeScript codebase, with Prisma fixtures and disposable PostgreSQL and Redis instances provisioned for each continuous integration run. End-to-end scenarios are automated with Playwright, driving the web client and the admin console through realistic user journeys. Load, stress, and soak testing is performed with k6, which scripts virtual users against the API and websocket endpoints and emits percentile latency metrics. Coverage is reported with Istanbul instrumentation.

**Environments.** Four environments are used. The local developer environment runs through Docker Compose with seeded data. An ephemeral environment is created in continuous integration for every pull request and destroyed afterwards. A staging environment mirrors the production topology and is populated with synthetic pilot-scale data of approximately five thousand users and fifty communities. The production pilot environment, limited to the Hackathon A and Anytime Fitness communities, is observed with read-only smoke tests and canary checks.

**Test data.** All automated data is synthetic and generated from a seeded factory so that runs are reproducible. Fixtures include valid and tampered images, short and frozen videos, passing and failing quiz responses, repository links in valid, empty, and stale states, and wearable series with plausible and implausible trends. Accuracy is measured on anonymised pilot submissions. No real personally identifiable or health data is used in any test environment, in keeping with the GDPR and DPDP obligations recorded in FR-1.4.

**Continuous integration.** A GitHub Actions pipeline runs on every push. Lint and type checks run first, followed by the unit suite and then the integration suite against service containers. End-to-end scenarios run on the main branch and nightly, while k6 performance and soak tests run nightly and before each release candidate. A pull request cannot merge while any required stage is failing, and coverage thresholds are enforced on the verification and progression modules, where correctness is most consequential. Server time is injected through a clock abstraction in tests so that deadline and grace-period boundaries are exercised deterministically rather than by sleeping.

**Traceability.** Requirements are traced from FR-1 through FR-10 to the test cases below, and the coverage matrix in §7.6 demonstrates that every functional requirement is exercised by at least one case. The results reported throughout this chapter are the expected outcomes from the design-stage test plan; they represent the acceptance baseline against which the implemented suite will be measured once the build is executed.

## 7.2 Unit Testing

The unit suite targets the deterministic core of the domain logic. Because these functions decide routing, deadlines, and rewards, their behaviour is specified precisely and tested at boundaries as well as on the nominal path. The twelve cases below form the foundation of the suite and are the cases most likely to catch regressions introduced by later changes to the verification pipeline or the progression engine.

All twelve cases are expected to pass against the design specification. The status column records the design-stage expectation, not the output of an executed build; the values are retained here so that the implemented suite can be compared against the baseline during the construction phase.

| TC | Requirement | Test Case | Expected Result | Status |
|---|---|---|---|---|
| TC-01 | FR-3.2 | Challenge transitions only in valid order (e.g., Archived → Live) | Rejected by state machine; state unchanged | Pass (expected) |
| TC-02 | FR-3.6 | Submission at cutoff boundary (atomic close) | Accepted exactly until cutoff; rejected after | Pass (expected) |
| TC-03 | FR-4.1 | Create post when no challenge is Live | Post rejected; feed remains locked | Pass (expected) |
| TC-04 | FR-4.3 | Story TTL expiry | Story auto-deleted at 24h; no longer visible | Pass (expected) |
| TC-05 | FR-5.3 | AI confidence 0.95 | Routes to auto-approve | Pass (expected) |
| TC-06 | FR-5.4 | AI confidence 0.76 | Routes to peer quorum (3+ votes) | Pass (expected) |
| TC-07 | FR-5.4 | AI confidence 0.40 | Routes to admin review | Pass (expected) |
| TC-08 | FR-5.5 | 2 accept + 1 reject (split) | Escalates to admin; no automatic verdict | Pass (expected) |
| TC-09 | FR-6.1 | XP event sum equals profile XP with daily caps applied | Exact totals; no over-cap award | Pass (expected) |
| TC-10 | FR-10.4 | Duplicate image submitted (pHash match) | Rejected as duplicate; fraud flag raised | Pass (expected) |
| TC-11 | FR-10.7 | Verifier votes on own submission | Excluded from own queue; vote refused | Pass (expected) |
| TC-12 | FR-9.2 | Member calls admin endpoint | 403 Forbidden | Pass (expected) |

The boundary cases deserve particular emphasis. TC-02 fixes the semantics of the atomic cutoff so that a submission at exactly the deadline is accepted and a submission one unit later is rejected, while TC-01 confirms that the state machine cannot be driven backwards by a crafted request. The verification routing cases collectively demonstrate that the confidence thresholds of 0.90 and 0.50 partition the output space into auto-verification, peer quorum, and administrative review without gaps or overlaps.

## 7.3 Integration Testing

Integration testing measures the seams between components, where most defects in a distributed application concentrate. The seven cases below exercise the external interfaces described in §1.2, including the GitHub REST API, the quiz engine, wearable synchronization, OAuth and JWT issuance, scheduled notifications, the offline synchronization queue, and community join policies. Each case is run against disposable PostgreSQL and Redis instances seeded with representative fixtures.

| TC | Requirement | Test Case | Expected Result | Status |
|---|---|---|---|---|
| TC-13 | FR-5.2 | Submit repository link; GitHub API check (commits, README) | Valid repo verified; empty repo routed to review | Pass (expected) |
| TC-14 | FR-5.2 | Quiz submission with anti-plagiarism and time-per-question checks | Unique passing attempt verified; implausible timing routed to review | Pass (expected) |
| TC-15 | FR-5.10 | Scale photo plus wearable trend cross-check | Consistent trend awarded full credit; inconsistent trend routed to review | Pass (expected) |
| TC-16 | FR-1.1 | OAuth signup, JWT session, RBAC on endpoints | Session valid; roles enforced on every call | Pass (expected) |
| TC-17 | FR-8.1 | Milestone deadline triggers 24h, 6h, and 1h reminders | Three push notifications delivered on schedule | Pass (expected) |
| TC-18 | FR-1.5 | Offline draft, reconnect, synchronization queue | Post synced once, with an honest server timestamp | Pass (expected) |
| TC-19 | FR-2.3 | Community join_mode set to invite | Non-invited join rejected | Pass (expected) |

TC-13 isolates the repository contract so that network failure, a missing repository, and a stale repository are distinguished from one another. TC-14 binds the quiz engine to the anti-cheat module, verifying that a correct score is insufficient for verification when the attempt pattern is implausible. TC-15 is the principal evidence that weight-loss claims cannot be satisfied by a photograph alone, since the cross-check requires that the direction of change be consistent with the wearable series. TC-18 verifies that draft content created offline is accepted once and only once on reconnect, so that users cannot manufacture duplicated posts or false chronology.

## 7.4 System / End-to-End Testing

System testing exercises the complete application along the five end-to-end scenarios defined in the PRD. Each scenario is named in the corresponding row, and each is run with Playwright against a full stack so that the mobile client, the API, the verification pipeline, and the administrative console participate in the same journey. Assertions check the postconditions recorded in the scenarios in addition to the user-visible result.

| TC | PRD Scenario | Test Case | Expected Result | Status |
|---|---|---|---|---|
| TC-20 | Scenario 1 — First-Time User Completes Her First Challenge ("Learn Python") | Signup, join, quiz check-in, AI-verified, archive, Level 4 plus First Blood | Full lifecycle completes; XP, badge, and leaderboard consistent | Pass (expected) |
| TC-21 | Scenario 2 — Member Wins a Weight-Loss Challenge With Wearable-Backed Proof | Scale photo, peer verdict, wearable cross-check, leaderboard first place plus Deadline Slayer | Thirteen submissions verified; proof wall updated | Pass (expected) |
| TC-22 | Scenario 3 — Active Member as Peer Verifier ("Clearing the Queue") | Verifier clears the queue; votes match final verdicts | XP awarded, accuracy 100%, Level 5 unlocks challenge creation | Pass (expected) |
| TC-23 | Scenario 4 — Community Admin Runs a Challenge End-to-End (With a Dispute) | Admin runs a challenge with a dispute; anti-cheat flags collusion; late entry | Override audited; colluders rejected; CSV report generated | Pass (expected) |
| TC-24 | Scenario 5 — Dropped Member Stages a Comeback | Dropped member rejoins Cohort 2 with carried-over progress | Comeback Kid badge; progress resumes at 40% | Pass (expected) |

Together the scenarios cover all five application tabs, both pilot communities, and both verification branches from AI pre-screen to peer quorum and administrative override. They also demonstrate the liveness rule, the always-on stories and notes, streaks, XP and badges, the appeal and grace-period mechanics, and the archive and proof-wall lifecycle. Passing these cases is therefore a stronger signal than passing any individual unit test, because it confirms that the requirements integrate into a coherent whole.

## 7.5 Performance and Acceptance Testing

The final tier measures whether the system meets its contractual budgets under realistic load and whether the pilot achieves its product objectives. Load and soak tests are executed with k6 against staging with pilot-scale data, while accuracy and cost are measured against a held-out sample of anonymised pilot submissions.

| TC | Requirement | Test Case | Expected Result | Status |
|---|---|---|---|---|
| TC-25 | §1.4 response time | Feed load p95 with 1000 concurrent users | Feed p95 under 1 s; live feed under 150 ms | Pass (expected) |
| TC-26 | FR-5.11 | AI verdict latency | At most 30 s end-to-end; at most 10 s asynchronous | Pass (expected) |
| TC-27 | §1.4 throughput and reliability | Load test 1000 concurrent; soak through a Live challenge | No degradation; 99.9% availability | Pass (expected) |
| TC-28 | §1.4 AI cost | Inference cost per check-in | Under $0.03 | Pass (expected) |
| TC-29 | §1.4 accuracy | AI precision and recall on pilot data | Precision at least 95%; recall at least 90% | Pass (expected) |
| TC-30 | KPI gates | Completion, D7 and D30 retention, and dispute rate in pilots | Completion at least 45%; D7 and D30 at least 25%; dispute under 2% | Pass (expected) |

The KPI targets in this table follow the project acceptance gates: completion at least 45 per cent, day-seven and day-thirty retention at least 25 per cent each, and a dispute rate below 2 per cent. These gates are deliberately stricter than the baseline self-reported completion rate observed in open goal communities, and they are evaluated over the pilot cohort rather than over synthetic traffic.

## 7.6 Requirement Coverage Matrix

The matrix below traces every functional requirement group to the test cases that exercise it. Every group from FR-1 through FR-10 is covered by at least one case across the unit, integration, and system layers, and the non-functional requirements are covered by the performance tier.

| FR | Requirement Area | Covering Test Cases |
|---|---|---|
| FR-1 | User Accounts and Authentication | TC-16, TC-18 |
| FR-2 | Communities | TC-19 |
| FR-3 | Challenge Engine | TC-01, TC-02, TC-20, TC-21, TC-23, TC-24 |
| FR-4 | Content Rules (Liveness Rule) | TC-03, TC-04, TC-20 |
| FR-5 | Verification Pipeline | TC-05, TC-06, TC-07, TC-08, TC-13, TC-14, TC-15, TC-26, TC-28, TC-29 |
| FR-6 | Progression (XP, Levels, Badges) | TC-09, TC-20, TC-21, TC-22, TC-24 |
| FR-7 | Feed and Discovery | TC-25 |
| FR-8 | Notifications | TC-17 |
| FR-9 | Roles and Permissions | TC-12, TC-16, TC-22 |
| FR-10 | Moderation and Anti-Cheat | TC-10, TC-11, TC-23 |

The matrix is reviewed whenever a requirement is added or amended. A requirement with no covering case is treated as untested, and the build cannot be accepted until the gap is closed by a new case or by an explicit, recorded decision to defer the requirement.

## 7.7 Defect Management

Defects are recorded, classified by severity, and tracked to closure in the issue tracker. Severity is assigned by impact rather than by the effort required to fix. A **critical** defect causes data loss, a security breach, an incorrect verification outcome that damages trust, or a breach of deadline integrity. A **high** defect blocks a core flow or degrades a key performance indicator with no workaround. A **medium** defect degrades a flow but a workaround exists. A **low** defect is cosmetic or confined to documentation and copy.

Every defect passes through a lifecycle of New, Triaged, Assigned, In Progress, Fixed, Verified, and Closed. A defect may instead be marked Rejected, Duplicate, or Deferred, in each case with a recorded justification. Triage assigns a severity and a target iteration within one working day. Each corrective change must be accompanied by a regression test that fails before the fix and passes after it, so that the same fault cannot silently return. Defect density is tracked per module, and root causes are tagged to reveal whether faults cluster around a particular interface, such as the cutoff calculation or the perceptual-hash threshold.

The table below is a representative defect log from the design and early construction review. It is illustrative of the classes of defects the plan anticipates and does not represent production incident statistics.

| Defect ID | Severity | Description | Status |
|---|---|---|---|
| DEF-001 | Critical | Grace-window off-by-one: with a grace period of 1440 minutes, a submission received one second past the boundary was accepted because the comparison was inclusive; corrected to reject strictly after the boundary. | Fixed (verified by TC-02) |
| DEF-002 | High | Perceptual-hash false positive: two distinct members' gym selfies with similar lighting and pose produced a hash distance below threshold and were wrongly flagged as duplicates. | Fixed (threshold retuned; TC-10 extended) |
| DEF-003 | High | Streak-freeze double award: consuming the monthly streak-freeze preserved the streak but also re-credited the daily check-in XP, inflating totals. | Fixed (guarded by TC-09) |
| DEF-004 | Medium | Quiz time-per-question check rejected legitimate slow typists because the minimum threshold was applied per question rather than per attempt. | Fixed |
| DEF-005 | Medium | Late-entry records omitted the "Late, admin approved" label from the exported CSV completion report. | Fixed |
| DEF-006 | Low | Verdict explanation text was truncated in the push notification on small screens; the full text remained available in the application. | Closed (copy shortened) |

Defects DEF-001, DEF-002, and DEF-003 illustrate the three most dangerous fault classes for Enigma: a boundary error that undermines deadline integrity, a false positive in duplicate detection that harms honest members, and an accounting error that corrupts the progression economy. Each of these classes is now covered by an explicit regression test at the unit layer, and the corresponding fixtures are retained in the suite.

## 7.8 Acceptance Criteria

The MVP is accepted for release to the two pilot communities only when all of the following criteria hold. Each criterion is objective and is evaluated against the recorded results of the test suite and the pilot measurements.

- All unit and integration cases, TC-01 through TC-19, pass with no open critical or high severity defect.
- All system and end-to-end scenarios, TC-20 through TC-24, pass, including the postcondition state changes asserted by each scenario.
- All performance and acceptance cases, TC-25 through TC-30, meet their stated targets.
- Requirement coverage is complete: every functional requirement from FR-1 through FR-10 is exercised by at least one passing case in the coverage matrix.
- Reliability holds at 99.9 per cent availability when sustained through a full Live challenge window.
- AI accuracy reaches precision of at least 95 per cent and recall of at least 90 per cent on the pilot sample.
- Inference cost remains below $0.03 per check-in at pilot throughput.
- The KPI gates are met: completion at least 45 per cent, day-seven retention at least 25 per cent, day-thirty retention at least 25 per cent, and a dispute rate below 2 per cent.

The overall decision is a go or no-go gate. If every criterion holds, the MVP is approved for deployment to Hackathon A and Anytime Fitness. If any criterion fails, deployment is blocked; a failed functional criterion returns the affected requirement to construction, while a failed performance or KPI criterion triggers a calibration and scope review before the next iteration begins.
