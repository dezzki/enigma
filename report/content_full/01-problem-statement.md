# Problem Statement

## Background and Context

Modern social platforms are optimised for attention rather than achievement. Their recommendation systems measure success in watch time, impressions and engagement, and so surface content that is easy to consume and react to: short videos, memes, trends and curated lifestyles. A user can spend hours each day producing and consuming this material and still finish the year with no new skill, no completed project and no measurable change in health or knowledge.

This produces the **hollow-follower problem**: an account whose activity is high but whose attainments are absent. Hollow followers post, react and comment, yet nothing they claim to be working towards is ever verified or completed. The cause is structural: the platforms offer no way to state a goal, no deadline against which progress can be measured, and no evidence trail distinguishing a genuine achievement from a confident claim. A user who completes a demanding twelve-week programme and a user who merely announces an intention look identical in the feed.

Goal-oriented communities do form wherever people share a target: fitness groups on WhatsApp, study cohorts on Telegram, builder guilds on Discord, weight-loss threads on Reddit. They prove the demand for accountability, but they lack infrastructure: progress is recorded in free-form messages, deadlines are easily renegotiated, and evidence is scattered across camera rolls with no way to audit it.

## The Three Structural Failures

The hollowness described above is the combined effect of three recurring failures in how goal-oriented communities are hosted online.

### The Self-Report Problem

In nearly every existing group, progress is self-reported. A member types "lost two kilograms this week" or "finished chapter three", and the group must accept the statement on trust, with no artefact to inspect. Honest members receive no more credit than persuasive ones, inflated claims go unchallenged because challenging a peer is socially costly, and the group's shared record of achievement becomes unreliable. Evidence must therefore be examined by default before a claim counts as complete: every submission is assessed by an AI service and, where confidence is insufficient, by a quorum of peers.

### The No-Deadline Problem

The second failure is the absence of hard deadlines. Goals are announced as intentions rather than as commitments with a start and an end, so there is never a moment at which the goal is either met or missed. Without that moment, effort is infinitely deferrable: the member who falls behind in week two can always resolve to catch up later, and the pressure that would have generated early momentum never appears. Even groups that set a target date rarely enforce it. A credible deadline must be enforced by the platform, not by goodwill, which leads to Enigma's server-enforced challenge lifecycle and to submissions closing atomically at the deadline according to the server's clock.

### The Engagement-without-Distraction Problem

The third failure is that the surfaces which keep a community active are the same surfaces that dilute its mission. Reddit-style threaded communities are excellent for discussion but have no structural link to a goal, so a fitness or study community gradually fills with memes and tangential posts until its original purpose is one topic among hundreds. Discord-style servers are excellent for presence but inherently noisy: with many channels and open-ended chat, the one message that matters — the weigh-in reminder, the milestone deadline — is easily buried. The insight is that community content should be layered by purpose: mission-critical content constrained to a time-boxed window in which the mission is active, and lighter, always-available surfaces carrying the social life of the community. This is the rationale for Enigma's **liveness rule**.

## Why Existing Platforms Fall Short

| Platform | What it does well | Gap for Enigma's problem |
|---|---|---|
| **Reddit** | Communities, threaded discussion, upvote-based quality signalling, strong subculture formation | No goals, deadlines or verification; threads drift off-topic; a claim of progress carries no proof |
| **Discord** | Real-time chat, channels, roles, XP and levels, strong presence and belonging | No structured proof or outcome tracking; notification noise buries the mission; levels reward activity, not achievement |
| **TikTok / Instagram** | Unmatched reach and creative expression; effortless consumption and sharing | Reward content over outcomes; curated visuals are easy to fake; no verification and no follow-through mechanics |
| **Duolingo** | Streaks, XP, leagues and quiz-gated progress; proven motivation mechanics | Single-player and single-domain; no community accountability and no evidence beyond the app's own exercises |
| **Strava** | Clubs, challenges and sensor-verified activities; device data as a trust anchor | Limited to fitness and sport; no general challenge or content community; no peer verification for non-sensor evidence |
| **Habitica** | Gamifies habits and tasks as an RPG with XP, levels and rewards | Solo or small-group habit tracking; no real-world community verification and no meaningful shared deadlines |
| **StepBet / Stridekick** | Group step challenges with wearable sync, leaderboards and anti-cheat expectations | Fitness-only; StepBet adds cash stakes and their legal complexity; neither offers an open community or a learning track |

The pattern is consistent: social products are strong on community and weak on outcomes, while goal products are strong on outcomes within one domain but weak on open community, or they add financial stakes the project excludes. No reference product combines a community content model with a general-purpose verification layer and hard deadlines. That combination is Enigma's position: the community structure of Reddit, the presence and progression of Discord, and the evidence discipline of a sensor-verified fitness platform, applied to real-world challenges of any kind.

## The Enigma Solution

Enigma is a mobile-first social platform in which communities run **real, time-boxed challenges and progress is proved rather than asserted**. Four connected ideas define it.

The core rule is the **liveness rule**. Posts may be created on a challenge thread only while that challenge is `Live`; before the start and after the end the thread is read-only, with comments permitted. This keeps the main feed focused on the active mission and gives participation natural urgency. So that a community never falls silent, two lighter surfaces remain available at all hours: **Stories**, photo or video updates that expire after twenty-four hours and contribute to streaks, and **Notes**, short text messages in community channels. The feed is the evidence layer; chat, stories and notes are the social layer.

The second idea is **dual verification**. Every submission carries a proof type appropriate to the challenge — a scale photograph, a repository link, a quiz, a demo video or wearable data. An anti-cheat pre-screen removes duplicates and strips metadata, after which an AI verifier returns a verdict, a confidence score and a plain-language explanation. High-confidence submissions are auto-verified; medium-confidence submissions enter a peer queue where members at Level 3 or above vote, three agreeing votes deciding the outcome and a split escalating to an administrator; low-confidence or suspicious submissions go to admin review. Decisions are recorded in an append-only audit log, and administrators may override any verdict with the override logged.

The third idea is **deadlines**. Challenges follow the server-enforced lifecycle Draft → Announced → Live → Ending → Archived, with milestones at intermediate points and submissions closing atomically at the cutoff. Server time is authoritative, so deadline integrity does not depend on any client device.

The fourth idea is **progression**. Experience points, levels, streaks and badges turn discipline into a cumulative record: joining a challenge, checking in daily, reaching a milestone, completing a challenge with verified proof and voting accurately all contribute XP, while levels three and five respectively unlock peer verification and challenge creation. Reputation is earned through verified participation rather than volume of content.

Two contrasting pilots validate the design across knowledge and physiological outcomes: **Hackathon A**, a builders' community running "Build a startup in 30 days" and "Learn Python", and **Anytime Fitness**, which runs the twelve-week "Lose 5 kg". Riya, a computer-science student, submits weekly quizzes that the AI checks for uniqueness and plausible timing and finishes with a repository link verified by a peer quorum; Arjun, a gym member, submits a scale photograph the AI reads by optical character recognition and supports weekly weigh-ins with a wearable trend the system cross-checks for plausibility. The two differ in domain, proof type and verification path, yet both pass through the same lifecycle, verification pipeline and progression economy.

## Objectives and Scope

The project's objectives are measurable. The platform must run at least two complete end-to-end challenge cycles, in the two pilot communities; verify submissions through both the AI-only and the AI-then-peer paths, with automated verification within thirty seconds and peer verification within twenty-four hours; enforce the liveness rule and the deadline atomically, rejecting posts outside a live window; and advance a pilot user at least three levels through verified activity. The adopted success metrics are a verified challenge-completion rate of at least forty-five per cent, day-seven retention of at least twenty-five per cent among joiners, a dispute rate below two per cent, and AI precision of at least ninety-five per cent with recall of at least ninety per cent. Verification cost is bounded at under three United States cents per check-in, and the MVP must sustain one thousand concurrent users. Against the roughly ten per cent completion typical of unstructured personal goals, taken as the working baseline, these targets constitute the value proposition to be tested.

The scope is bounded by explicit non-goals. Enigma is **not a general-purpose social network**: it will not provide a generic friend or timeline feed, because unfettered content is the failure mode the product corrects. It does not permit **anonymous posting**, because accountability depends on attributable identity. It excludes **cash staking or betting** in the MVP, since the legal and payment implications are disproportionate to a first release. It provides **no built-in payments, e-commerce or dating features**, and disallows advertising, which would create pressure to optimise for attention and undermine the credibility of the evidence layer. Chat rooms, wearable integrations, a challenge marketplace and sponsored challenges are deferred to later iterations.

## Significance and Expected Impact

The most immediate significance is the restoration of accountability. By making evidence the default requirement for claiming progress, and by fixing an enforceable deadline to every goal, the platform gives honesty a structural advantage over persuasion. Members who deliver are visibly rewarded, members who fall behind receive timely and non-shaming nudges, and members who leave have a documented route back through carried-over verified progress. Accountability of this kind has precedent in coaching, gym partnerships and stake-based products, but Enigma offers it without money at risk.

A second consequence is **verifiable reputation**. In existing communities, tenure and volume are weak proxies for competence; a three-year lurker and a consistently effective contributor are hard to distinguish. Enigma's append-only verification record, verifier accuracy tracking and level-gated privileges produce a portable signal of what a member has actually accomplished, with completion certificates and proof walls that a community, or a prospective employer, can inspect.

The third consideration is the project's value as a software-engineering artefact. Enigma spans authentication and role-based access control, a state machine with server-authoritative time, an asynchronous AI verification pipeline, an anti-cheat subsystem, a real-time feed, a progression economy and moderation. Delivering a coherent slice under course constraints exercises explicit estimation, scheduling, risk management and a process model that tolerates uncertainty, so the project also demonstrates the engineering disciplines the course teaches.
