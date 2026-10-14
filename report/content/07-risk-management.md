# 4. Risk Management

This chapter describes how Enigma identifies, scores and controls risk. Because the project is iterative, the risk register is reviewed at each iteration demo and updated when pilot feedback changes a risk.

## 4.1 Risk Identification

Risks were drawn from the requirements, the process document and talks with the two pilot communities. Most come from the parts of Enigma that are genuinely new. Outcomes are certified by AI and peers instead of self-report, which creates both false-positive and false-negative risk. The main feed opens only while a challenge is Live, which risks quiet periods between challenges. Ordinary members also carry part of the verification and moderation load, so community health matters, and verifier inactivity is watched because an empty peer queue delays every check-in. Common execution risks apply too: the developer is the only full-time resource, health and legal claims need care, and evidence can contain private health data.

## 4.2 Risk Register

Probability is rated 1 (rare) to 5 (almost certain). Impact is rated 1 (negligible) to 5 (severe). Priority is the product of the two: 20–25 Critical, 12–19 High, 6–11 Medium, 1–5 Low.

| ID | Risk | Probability | Impact | Priority |
|---|---|---|---|---|
| R-01 | Cheating and framed evidence corrupt the leaderboard | 4 | 5 | Critical |
| R-02 | AI wrongly rejects a valid proof | 4 | 5 | Critical |
| R-03 | Low activity between challenges | 4 | 4 | High |
| R-04 | Privacy breach of health and evidence data | 3 | 5 | High |
| R-05 | Community toxicity drives members away | 3 | 4 | High |
| R-06 | Scope creep delays the MVP | 4 | 3 | High |

The two Critical risks attack the trust the product exists to create. The High risks are likely or damaging enough to need active management.

## 4.3 Mitigation Plans

- R-01 Cheating: strip metadata, detect duplicate and near-duplicate images, limit one check-in per challenge per six hours, and apply a three-strike rule.
- R-02 AI false rejection: prefer recall over precision, send medium-confidence cases to peer review, and give every rejection an appeal path.
- R-03 Low activity: keep Stories and Notes open all day, and use short recurring challenge templates to avoid quiet periods.
- R-04 Privacy: default evidence to community-only, mark health fields as sensitive, encrypt data, and keep audit logs append-only.
- R-05 Toxicity: use AI moderation, reporting, muting and a moderation panel, with warnings leading to bans.
- R-06 Scope creep: enforce the P0 cut, defer P1 and P2 features, and cut scope before extending a deadline.

A failed KPI gate triggers re-planning and a scope review, so the plan stays realistic.
