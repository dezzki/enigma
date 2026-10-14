# 5. Design

This chapter describes the structure of Enigma. Section 5.1 explains the system architecture, and Section 5.2 describes the data model. The guiding rule is that the server decides everything; clients only render state.

## 5.1 System Design

Enigma is a mobile-first application with a typed API. A React client sits at the front and also works as a progressive web app. The API layer is the only entry point for requests: it handles login, checks permissions, and routes each command to the correct service.

Behind the API lie small services that each own one part of the system, such as users, communities, challenges, or verification. They share one PostgreSQL database, so the whole system keeps a consistent view. Redis provides queues for background work and caches frequently read data, while AWS S3 stores media that users upload through short-lived signed links.

![Figure 5.1 — System architecture](diagrams/architecture.png)

Figure 5.1 shows the client, the API layer, the core services, the data stores, and the external AI provider. The main modules are listed below.

| Module | Responsibility |
|---|---|
| API Gateway | Login, sessions, rate limits, request routing |
| Challenge & State Engine | Owns the challenge lifecycle and its transitions |
| Posting Gate | Allows posts only while a challenge is Live |
| User & Community | Profiles, levels, communities, memberships, roles |
| Verification Service | Runs AI checks, peer votes, and admin review |
| Feed & Notification | Feed, comments, stories, notes, alerts |

A challenge moves through fixed states in one direction: Draft → Announced → Live → Ending → Archived. The state engine is the only component that may change a state, and it rejects any illegal move, such as going from Archived back to Live.

Every submission is checked before it counts. Anti-cheat first rejects obvious fraud, then an AI model returns a verdict with a confidence score: high confidence is auto-verified, medium confidence goes to peers at Level 3 or higher, and low confidence or fraud goes to an administrator.

A long-form Post is allowed only while the challenge is Live, and the posting gate reads server state so a changed device clock cannot bypass a deadline. Stories and notes remain available at all times, so the community stays active between challenge windows.

## 5.2 Data Design

PostgreSQL is the main database and holds all important records, including users, communities, challenges, submissions, and verification results. Keeping them in one database protects the relationships between records, so the data stays consistent.

Redis holds short-lived data such as queues and rate limits, and S3 stores media files. The core tables are kept small and focused. Each challenge belongs to a community, each submission belongs to an author and a challenge, and each verification belongs to one submission.

![Figure 5.2 — Entity relationship diagram](diagrams/er-diagram.png)

| Table | Purpose |
|---|---|
| users | Profile, level, XP, streak |
| communities | Community details and settings |
| challenges | Rules, dates, and current state |
| submissions | Proof uploaded by a member |
| verifications | AI, peer, or admin result for a submission |
| posts | Content created while a challenge is Live |

All dates are stored as UTC timestamps and compared with the server clock, never the client clock, so the boundary of a challenge is decided once.
