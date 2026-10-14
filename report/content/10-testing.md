# 7. Testing

## 7.1 Test Strategy

Enigma is tested at four levels. Unit tests check small pieces of logic, integration tests check that separate services work together, system tests replay complete user journeys, and performance tests measure speed, cost and accuracy under load. Every test is linked to a requirement, so a feature is treated as delivered only when a test exercises it.

## 7.2 Unit Testing

Unit tests cover the core logic that decides routing, deadlines and rewards. These rules are precise, so they are tested at boundary values as well as on the normal path.

| TC | Tests what | Expected result |
|---|---|---|
| TC-01 | Valid state order only | Invalid move rejected |
| TC-02 | Submission exactly at deadline | Accepted until cutoff; rejected after |
| TC-03 | Post when no challenge Live | Rejected; feed locked |
| TC-04 | Story after 24 hours | Auto-deleted |
| TC-05 | AI confidence high, medium, low | Auto-approve, peer quorum or admin |
| TC-06 | Duplicate image (pHash) | Rejected; fraud flag |

TC-05 shows the 0.90 and 0.50 thresholds divide verdicts into auto-verification, peer quorum and admin review without gaps. TC-01 proves the state machine cannot run backwards.

## 7.3 Integration Testing

Integration tests check the seams between components, where most defects appear. Each case runs against disposable PostgreSQL and Redis instances filled with test data.

| TC | Tests what | Expected result |
|---|---|---|
| TC-10 | Repository link via GitHub REST API | Valid repo verified; empty sent to review |
| TC-11 | Quiz with anti-plagiarism and timing | Unique passing attempt verified |
| TC-12 | Scale photo vs wearable trend | Consistent credited; inconsistent reviewed |
| TC-13 | OAuth signup, JWT and roles | Session valid; roles enforced |

TC-12 shows a weight-loss claim cannot pass on a photo alone, because the direction of change must match the wearable data. TC-13 confirms authentication and permissions hold across service boundaries.

## 7.4 System / End-to-End Testing

System tests run the whole application along the five user scenarios. Each scenario follows one persona across several screens and checks the resulting state changes.

| TC | Scenario | Expected result |
|---|---|---|
| TC-20 | Scenario 1 — first-time user finishes "Learn Python" | Full lifecycle; Level 4 and First Blood |
| TC-21 | Scenario 2 — weight-loss win with wearable proof | Submissions verified; proof wall updated |
| TC-22 | Scenario 3 — active member verifies peers | XP awarded; Level 5 unlocks creation |
| TC-23 | Scenario 4 — admin runs a challenge with a dispute | Override audited; cheaters rejected |
| TC-24 | Scenario 5 — dropped member stages a comeback | Comeback Kid badge; progress resumes |

Together these cases cover all five app tabs, both verification branches, and the archive and proof-wall lifecycle.

## 7.5 Performance Testing

Performance tests measure the targets that decide whether the system is ready for the pilot. Load and soak tests run against a staging system with pilot-scale data.

| TC | Tests what | Expected result |
|---|---|---|
| TC-30 | Feed load, 1000 concurrent users | p95 under 1 s; live feed under 150 ms |
| TC-31 | AI verdict latency | At most 30 s; at most 10 s async |
| TC-32 | Load and soak through a Live challenge | 99.9% availability; no degradation |
| TC-33 | Inference cost and AI accuracy | Below $0.03; precision ≥ 95%, recall ≥ 90% |

The KPI gates are completion of at least 45 per cent, day-seven retention of at least 25 per cent, and a dispute rate below 2 per cent.

## 7.6 Acceptance Criteria

The MVP is accepted for the two pilot communities only when all of the following hold.

- All unit and integration cases pass with no open critical or high defects.
- All five end-to-end scenarios pass, including their state changes.
- Performance targets are met, including 1000 concurrent users, 99.9 per cent availability, and AI precision of at least 95 per cent with recall of at least 90 per cent.
- Pilot KPIs meet their gates: completion at least 45 per cent, day-seven retention at least 25 per cent, and a dispute rate below 2 per cent.

If any criterion fails, deployment is blocked until the issue is fixed.
