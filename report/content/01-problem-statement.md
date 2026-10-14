# Problem Statement

## Background and Context

Modern social platforms reward attention, not achievement. Their feeds promote short videos and memes, so a user can spend hours each day without gaining a skill or improving health. Goal-focused groups do form on WhatsApp, Telegram, Reddit and Discord, which shows people want accountability. These groups, however, lack infrastructure: progress is free text, deadlines move, and proof is scattered.

## The Three Structural Failures

This gap comes from three repeated problems.

### The Self-Report Problem

Progress is usually self-reported. A member types "lost two kilograms this week" and the group must trust it with no evidence. False claims go unchallenged because challenging a friend is awkward. Enigma avoids this: AI reviews every submission first, and peers decide when unsure.

### The No-Deadline Problem

Goals are announced as wishes, not commitments, so there is no moment when a goal is met or missed. A member who falls behind can always promise to catch up, so early effort never builds. Enigma gives every challenge a fixed, server-enforced deadline.

### The Engagement-without-Distraction Problem

The features that keep a community active also pull it off course. Reddit-style threads are good for discussion but are not tied to a goal, so a fitness group fills with memes. Discord-style servers are lively but noisy, and the important message gets buried. Enigma allows mission posts only during a live challenge, while social features stay open.

## Why Existing Platforms Fall Short

| Platform | Strength | Gap |
|---|---|---|
| **Reddit** | Strong communities and discussion | No goals, deadlines or proof; threads drift |
| **Discord** | Real-time chat, roles and levels | Rewards activity, not achievement; noise buries the mission |
| **Duolingo** | Streaks and XP motivate daily practice | Single-user, single-subject; no community proof |
| **Strava** | Club challenges with device-verified activity | Fitness only; no general challenges or peer checks |
| **Habitica** | Gamified habits with levels and rewards | Solo tracking; no shared real-world deadlines |

Social apps are weak on outcomes, while goal apps are narrow. Enigma combines both.

## The Enigma Solution

Enigma is a mobile-first social app where communities run time-boxed challenges and progress must be proved, not claimed. Four rules define it.

- **Liveness**: posts only while a challenge is `Live`; Stories and Notes stay open.
- **Dual verification**: AI reviews each submission; low confidence goes to peers at Level 3 or above, and a split goes to an admin.
- **Deadlines**: challenges move through Draft → Announced → Live → Ending → Archived and close submissions on time.
- **Progression**: XP, levels and badges reward real activity.

## Objectives and Scope

The objectives are measurable: run at least two challenge cycles, support both verification paths, enforce the liveness rule and deadlines, and move a pilot user up three levels through verified activity. Targets include completion of at least 45 per cent and day-seven retention of at least 25 per cent.

Enigma is not a general social network and does not allow anonymous posting. Cash staking, payments, e-commerce, dating and advertising are excluded; chat rooms, wearable integrations and a marketplace are left for later versions.

## Significance and Expected Impact

The main benefit is restored accountability: when proof is required and goals have real deadlines, honest effort counts for more than confident talk. Verified work also builds reputation, showing a community or employer what a member has done. The project is also a useful engineering exercise in authentication, state machines, AI pipelines, feeds and moderation.
