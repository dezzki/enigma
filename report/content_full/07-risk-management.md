# 4. Risk Management

This chapter describes how the project identifies, analyses, prioritises and controls risk. Because Enigma is built iteratively, the register is reviewed at each iteration demo and updated whenever pilot feedback changes a risk's probability or impact. The process has four parts: identification of candidate threats, a register that scores probability, impact and exposure, an RMMM plan for the highest-exposure risks, and a contingency plan that defines fallbacks.

## 4.1 Risk Identification

Risks were drawn from the SRS risk table, the PRD risk list, the process-flow document, and elicitation with Hackathon A and Anytime Fitness. Identification concentrated on the parts of Enigma that are genuinely novel rather than on generic software risk. Three design decisions create most of the distinctive exposure. First, outcomes are certified by AI and peers rather than by self-report, which introduces both false-positive and false-negative verification risk. Second, the liveness rule opens the main feed only while a challenge is Live, which risks deliberate dead zones between challenges. Third, ordinary members carry part of the verification and moderation load, which makes community health and verifier motivation project risks rather than purely operational ones. Common execution risks also apply: the developer is the only full-time resource and appears on every critical-path task, so illness or coursework pressure can delay the build; legal and regulatory exposure exists around health claims and later around cash stakes; privacy obligations apply because evidence includes weight and health data; and AI inference carries a running cost that must remain within budget because advertising is disallowed.

## 4.2 Risk Register

Probability is scored 1 = rare (below 10 per cent) to 5 = almost certain (above 75 per cent). Impact is scored 1 = negligible to 5 = severe. Exposure is the product of the two, so it ranges from 1 to 25. Priority is assigned as 20–25 = Critical, 12–19 = High, 6–11 = Medium and 1–5 = Low.

| ID | Risk | Category | Probability | Impact | Exposure (P×I) | Priority |
|---|---|---|---|---|---|---|
| R-01 | Cheating and framed evidence corrupt the leaderboard | Trust / Product | 4 | 5 | 20 | Critical |
| R-02 | AI false positives block legitimate members | Technical / Trust | 4 | 5 | 20 | Critical |
| R-03 | Dead-zone churn caused by the posting gate | Product / Retention | 4 | 4 | 16 | High |
| R-04 | Health-safety blow-up or legal claim in the fitness pilot | Legal / Safety | 2 | 5 | 10 | Medium |
| R-05 | Stake-related legal exposure from money pools | Legal / Compliance | 2 | 4 | 8 | Medium |
| R-06 | Community-leader burnout stalls challenges | Human / Operational | 3 | 4 | 12 | High |
| R-07 | Privacy breach or misuse of health and evidence data | Privacy / Compliance | 3 | 5 | 15 | High |
| R-08 | Scope creep in the MVP delays the launch | Execution / Schedule | 4 | 3 | 12 | High |
| R-09 | Deadline and clock-skew disputes create support load | Technical / Operational | 3 | 3 | 9 | Medium |
| R-10 | Community toxicity drives members away | Community / Trust | 3 | 4 | 12 | High |
| R-11 | Verifier apathy leaves the peer queue empty | Human / Operations | 3 | 4 | 12 | High |
| R-12 | AI inference cost exceeds the per-check-in budget | Cost / Technical | 2 | 3 | 6 | Medium |

The two Critical risks are cheating and AI false positives, because each directly attacks the trust the product exists to create. The High band contains the retention, privacy, human and schedule risks that are likely or damaging enough to require active management. The Medium band contains lower-probability legal and cost risks that are planned for but not staffed continuously.

## 4.3 Risk Mitigation, Monitoring and Management (RMMM)

The eight highest-exposure risks are managed individually below. Each states the mitigation that reduces probability or impact, the monitoring that detects the risk while action is still possible, and the management response once it materialises.

### 4.3.1 Cheating and Framed Evidence (R-01)

- **Mitigation:** Apply the anti-cheat layers of §1.3.10: EXIF and GPS stripping, perceptual-hash duplicate and near-duplicate detection, reverse-image lookup, device fingerprinting, a one-check-in-per-challenge-per-six-hours limit, and monotonic chronology checks.
- **Monitoring:** Track duplicate-hash matches, reverse-image hits, new-device flags on weight claims, and verifier accuracy. A rising fraud-flag rate signals that a challenge's proof policy is too weak.
- **Management:** Reject flagged submissions with a plain-language reason, apply the three-strike rule, and escalate suspected collusion to the admin, who may drop participants and publish a community notice.

### 4.3.2 AI False Positives Blocking Legitimate Members (R-02)

- **Mitigation:** Prioritise recall over precision, route medium-confidence cases to peer quorum rather than auto-rejecting, and allow per-community confidence thresholds.
- **Monitoring:** Track the confusion matrix against precision of at least 95 per cent and recall of at least 90 per cent, the admin override rate and the appeal win rate. A shortfall triggers threshold recalibration.
- **Management:** Every rejection has an appeal path carrying the AI explanation. An admin override is logged as a new append-only verification record that preserves the original verdict and the model version.

### 4.3.3 Dead-Zone Churn from the Posting Gate (R-03)

- **Mitigation:** Keep Stories and Notes available 24 hours a day, seven days a week so communities never go silent, and vary challenge window lengths with recurring weekly challenges.
- **Monitoring:** Watch D7 and D30 retention, days without a Live challenge, and the volume of 24/7 Stories and Notes. A community trending toward an empty window is flagged for a template challenge.
- **Management:** Prompt the organiser to launch a short template challenge. If retention still fails the KPI gate, re-examine the liveness rule for that community during re-planning.

### 4.3.4 Privacy of Health and Evidence Data (R-07)

- **Mitigation:** Default evidence to community-only visibility, scope weight and health fields as sensitive, encrypt data at rest and in transit, strip metadata on ingest, and keep audit logs append-only.
- **Monitoring:** Audit access to sensitive records, track export and delete requests, and verify GDPR and DPDP obligations in tests.
- **Management:** A confirmed exposure triggers containment, member notification and a compliance review. Export and delete are exercised through the P1 requirement so the capability is not merely theoretical.

### 4.3.5 Community-Leader Burnout (R-06)

- **Mitigation:** Supply reusable challenge templates and automatic cycling, offload routine moderation to AI and level-gated verifiers, and reward captains with Captain XP and perks.
- **Monitoring:** Track active organisers per community, the time between challenge publications, and template versus scratch usage.
- **Management:** If an organiser becomes inactive, nominate a co-organiser and switch the community to template-driven cycles so no challenge stalls for want of an author.

### 4.3.6 Scope Creep in the MVP (R-08)

- **Mitigation:** Enforce a strict P0 cut and drop anything not demonstrable within an iteration. The 92-function-point MVP subset is the agreed boundary, and P1 and P2 features are deferred by default.
- **Monitoring:** Track the priority mix of work in progress and compare completed function points with the iteration plan. An unplanned P1 feature entering the build is itself a trigger.
- **Management:** Cut scope before extending the deadline. A failed KPI gate also triggers re-planning, and deferred items return to the backlog.

### 4.3.7 Community Toxicity (R-10)

- **Mitigation:** Apply AI moderation for profanity, harassment and self-harm signals, provide reporting and a moderation panel, allow channel muting, and mask health content with a disclaimer.
- **Monitoring:** Track report volume, the flagged-to-removed ratio, and departures following moderation incidents.
- **Management:** Confirmed harassment follows graduated warnings through suspension to a permanent, appealable ban. Serious cases escalate to the platform administrator.

### 4.3.8 Verifier Apathy and Empty Peer Queues (R-11)

- **Mitigation:** Incentivise verification with verifier XP and a Trusted Verifier badge, cap daily votes at twenty, and auto-escalate any idle submission to an admin.
- **Monitoring:** Track median peer-queue wait against the 24-hour verdict SLA, eligible verifiers per community, and the number of auto-escalations.
- **Management:** Send reminders to eligible verifiers and escalate to admin review under its 72-hour SLA. Persistent apathy triggers a review of the community's verifier population and level thresholds.

## 4.4 Contingency and Re-planning

Contingency planning defines the triggers that indicate a risk has materialised and the fallback taken in response. The guiding rule is that scope is reduced before dates are extended, and fallbacks preserve the trust properties of the product rather than bypassing them.

- If AI precision falls below 95 per cent, lower the auto-approve threshold and route more cases to peer or admin review until the model is recalibrated.
- If the peer queue cannot be sustained because verifier supply is too thin, drop to AI-only verification with admin spot-checks and treat peer verification as a v1 feature.
- If verifier coverage collapses, shorten the peer-verdict SLA and auto-escalate idle submissions to admin review.
- If the pilot scope proves too large for the semester, reduce to a single community and demonstrate both the coding and fitness proof types within it before widening.
- If scope creep threatens the MVP date, invoke the strict P0 cut and re-baseline the estimates as described in §2.3.
- If AI cost exceeds the under-$0.03-per-check-in ceiling, reduce verification depth, cache duplicate checks, batch non-urgent jobs and renegotiate the provider plan.
- If a privacy or health-safety incident occurs, suspend the affected feature, notify affected members and complete a compliance review before re-enabling it.
- Deadline and clock-skew disputes are contained by server-authoritative UTC timestamps and atomic cutoffs, with late entries admin-approved and labelled.
- If the developer becomes unavailable, the supervisor and course peers cover review and documentation while the buffer absorbs a short interruption; a long interruption triggers a scope reduction to protect deployment.
