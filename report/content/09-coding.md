# 6. Coding

This chapter explains how the design is implemented. Section 6.1 lists the technology stack, Section 6.2 shows the project structure, Section 6.3 describes the coding approach, and Section 6.4 gives two short code samples.

## 6.1 Technology Stack

| Layer | Technology |
|---|---|
| Language | TypeScript (strict mode) |
| Web framework | Next.js with the T3 stack (React, tRPC, Prisma) |
| Database | PostgreSQL with the Prisma ORM |
| Cache and queues | Redis with BullMQ |
| Object storage | AWS S3 |
| Authentication | OAuth 2.0 with JWTs |

The same TypeScript types flow from the database to the client, which catches many errors at compile time.

## 6.2 Project Structure

The project is a monorepo with one application and a few shared packages.

```text
enigma/
├── apps/web/               Next.js app and admin dashboard
├── packages/core/          state machine, posting gate
├── packages/verification/  AI and peer logic
├── packages/db/            Prisma schema
├── packages/ui/            shared components
└── package.json
```

The core package holds the domain rules, while the db package holds the Prisma schema and client.

## 6.3 Coding Approach

- Domain rules are pure functions that do not touch the database.
- All time comes from the server, so clients cannot affect deadlines.
- State changes and verdicts are appended, never edited.
- Every retryable write is idempotent, and tests are written with each rule.

## 6.4 Sample Code

The examples below show two central rules in simplified TypeScript.

### Valid State Transitions

A challenge may only move to the next allowed state.

```typescript
const VALID_TRANSITIONS = {
  DRAFT: ['ANNOUNCED'],
  ANNOUNCED: ['LIVE'],
  LIVE: ['ENDING'],
  ENDING: ['ARCHIVED'],
  ARCHIVED: [],
} as const;
```

### The Posting Gate

Posts and votes are allowed only while the challenge is Live.

```typescript
function canPost(surface: Surface, challenge: { state: State }) {
  if ((surface === 'POST' || surface === 'VOTE') && challenge.state !== 'LIVE') {
    return { allowed: false, reason: 'CHALLENGE_NOT_LIVE' };
  }
  return { allowed: true };
}
```
