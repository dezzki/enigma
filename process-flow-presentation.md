# Enigma — My Process Flow (Plain Talk Version)

*This is the simplified version I'll use for the presentation.*

---

## The short version

I'm building Enigma — a social app where people set real challenges (like "ship a startup in 30 days" or "lose 5kg") and have to actually **prove** they did it, with AI and other members checking the proof. Think Reddit meets Duolingo, but nobody gets to post unless they're working on a real challenge.

The project is big, so I'm not doing everything at once. I'm using an **iterative process** — build a little, show it to people, learn, build again. Rinse and repeat.

## Why iterative and not the other ways

- **Linear (waterfall)** — you plan everything upfront and then just build. Problem: I don't know everything yet. The PRD has open questions, and I'll learn stuff as I go. If I build for 3 months and only then realize an assumption was wrong, I'm dead.
- **Parallel** — everyone works on different things at the same time. Great in a big team, but my modules are all tangled together (the challenge engine controls when you can post, the feed shows verified check-ins, etc.). Too risky.
- **Iterative** — this one. I build the core in small chunks, each chunk is a working slice of the app, and I get feedback between chunks. If something's wrong, I find out early, not on the last day.

## How the flow works (my loop)

It's basically: **talk → plan → design → build → repeat.**

### 1. Communication
First, I talk to people. Who are the users? What do they actually need? I have 5 personas — the Striver (wants to improve but quits at week 3), the Overachiever (wants to level up and show off), the Community Captain (runs the group), the Spectator (just watches), and the Verifier (judges other people's proofs for XP). I also talk to the two pilot communities — a dev group and a gym.

### 2. Planning
Then I plan. What am I building in this round? What's the deadline? What are the risks (e.g., people cheating the proof system, AI wrongly rejecting valid proofs)? And most importantly — what do I cut if I run out of time? The PRD is way too big to build everything, so each round I pick the parts that make a good demo.

### 3. Modeling (design)
Before coding, I design. The big ones:

- The **challenge state machine** — a challenge goes Draft → Scheduled → Registration → Live → Verification → Closed → Archived. The server enforces this, so nobody can cheat the timing.
- The **posting gate** — this is the core rule of the app. You can only post to the feed when a challenge is live. Between challenges, you can only chat, post stories (that vanish in 24h), or notes. That keeps the feed as pure "proof", not random content.
- The **verification pipeline** — when someone checks in with a photo or a repo link, the AI looks at it (is it real? is it a duplicate? is it spam?), gives a confidence score. High confidence = auto-approved. Medium = 3 random peers vote. Low = moderators review. Everything is logged.
- The **XP system** — verified check-in = 10 XP, completed challenge = 50 XP, streaks give more. Levels from Seeker up to Enigma.
- The **architecture** — an API gateway, core services, a verification job queue, PostgreSQL + Redis, and an object store for evidence.

I design in a loop too — draw it, review it, change it. Design is cheap to change; code isn't.

### 4. Construction (building)
Now I actually code. Round 1 (MVP): communities, the challenge engine, check-in with AI pre-screen, peer votes, the gated feed, XP, notifications, moderation. Round 2: chat, stakes (people put money in a pool), data integrations (GitHub, smartwatches). Round 3: the fancy stuff — marketplace, native apps, sponsored challenges.

### 5. The loop
After building a round, I go **back to communication** — show the demo to the teacher and classmates, hear what's wrong, then re-plan, re-design, and build again. The loop is the point. Every round I get smarter.

### 6. At the end: deployment
When it's good enough, I deploy it to the two pilot communities and watch the numbers — completion rate, how long verification takes, whether people come back. Those numbers decide if I move on or fix things.

## Why this flow fits a course project

- Each round produces something **demoable** — I can show the teacher a working slice, not just slides.
- Every round hits the classic phases (requirements, design, coding, testing), so it covers what the course expects.
- If I fall behind, I can cut scope gracefully instead of failing at the end.
- Honest answer: a one-person course project *needs* feedback loops, because nobody else is there to catch my blind spots.

## My three rounds at a glance

| Round | What I build | What I check |
|---|---|---|
| 1 (MVP) | Challenge engine, check-in + AI, peer votes, feed, XP, badges, notifications | Can someone make a challenge, check in, and get verified end-to-end? |
| 2 (v1) | Chat, stakes, GitHub/smartwatch integrations, analytics | Is it fun enough to keep people coming back? |
| 3 (v2) | Marketplace, native apps, sponsored challenges | Can it become a real product? |

---

*The full, formal version is in `process-flow.md`. This one is just the plain-English walkthrough for the presentation.*
