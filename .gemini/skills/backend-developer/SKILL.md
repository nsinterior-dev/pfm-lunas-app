---
name: backend-developer
description: Backend development for Lunas (pfm-lunas-app). Use for Next.js API routes, database operations (Firestore), external service integrations (Google Sheets, Claude, Gemini), and server-side logic/authentication.
---

# Skill: Backend Developer

> Invoke with `/backend` for API routes, database operations, external service integrations, and server-side logic.

## When to Use

- Creating or modifying Next.js API routes (`/app/api/`)
- Working with Google Sheets API, Firestore, Claude API, or Gemini API
- Implementing authentication (NextAuth.js + Google OAuth)
- Server-side data processing or validation

## Context

Read these before starting:
- `CLAUDE.md` — Project conventions
- `docs/ARCHITECTURE.md` — Tech stack, architecture decisions, cost constraints
- Relevant sprint ticket in `docs/EPIC-SPRINTS/`

## Tech Stack

- **Next.js API Routes** (App Router — `route.ts` files)
- **Zod** for request/response validation
- **NextAuth.js** for Google OAuth 2.0
- **Google Sheets API v4** via `/lib/sheets.ts`
- **Firestore** via `/lib/firestore.ts`
- **Anthropic API** (Claude) via `/lib/claude.ts`
- **Google Gemini API** via `/lib/gemini.ts`
- **Google Secret Manager** for secrets in production

## Workflow

### 1. Research
- Read the ticket requirements
- Check existing clients in `/lib/` before creating new ones
- Review API route patterns in `/app/api/` and `server/features/`

### 2. Plan
- Identify API routes to create/modify
- Define **Zod schemas** for request/response models in `server/features/[feature]/model/`
- Identify business logic for `server/features/[feature]/service/`
- Present plan to user — wait for approval

### 3. Implement
- Use Next.js Route Handlers (`route.ts` with `GET`, `POST`, etc.)
- Implement **Feature-First Server Structure**:
    - `model/`: Zod schemas for request/response validation
    - `service/`: Business logic and external service orchestration
    - `route.ts`: Entry point, calls service and handles validation
- Validate all incoming request data using Zod
- Return proper HTTP status codes and typed responses
- Handle errors gracefully — never expose internal details to client

### 4. Verify
- Test API routes manually or with tests
- Run `npm run lint` and `npm run build`
- Verify secrets are not hardcoded

## Rules

### Do
- Keep all API keys and secrets server-side only
- **Validate all request bodies and query params with Zod**
- Use the **Feature-First** structure in the `server/` directory
- Return consistent response shapes: `{ data }` or `{ error }`
- Use TypeScript interfaces generated from Zod schemas
- Handle rate limits for external APIs (Sheets: 500 req/100s)
- Reuse existing clients in `/lib/` — don't duplicate

### Don't
- Expose API keys to the client — ever
- Auto-trigger AI calls — manual trigger only (cost control)
- Store raw secrets in `.env` for production — use Secret Manager
- **Skip Zod validation on API routes**
- Return raw error messages from external services to the client

## Server Structure (Feature-First)

```
server/
├── features/
│   └── [feature]/
│       ├── model/
│       │   ├── request/        # Zod schemas for requests
│       │   ├── response/       # Zod schemas for responses
│       │   └── index.ts
│       ├── service/            # Business logic
│       └── index.ts            # Entry point for the feature logic
└── axios/                      # Shared axios instance/interceptors

app/api/
└── [feature]/
    └── route.ts                # Calls server/features/[feature]
```

## Client Libraries (`/lib/`)

| File | Purpose | Key Methods |
|------|---------|-------------|
| `sheets.ts` | Google Sheets API v4 | `getSheetData`, `updateCell`, `appendRow`, `addColumn` |
| `claude.ts` | Anthropic API | `analyze(data, prompt)` — structured prompt builder |
| `gemini.ts` | Google Gemini API | `analyze(data, prompt)` — same interface as Claude |
| `firestore.ts` | Firestore client | User docs, sheet config, analysis history |

## AI Integration Rules

- All AI calls go through `/app/api/analyze/route.ts`
- Claude for deep analysis, Gemini for quick queries
- $100 monthly cap on Claude — enforce server-side
- Every AI response must include at least one actionable next step
- Log token usage for cost tracking
- Include privacy disclosure data in response metadata

## Auth Flow

1. User clicks "Sign in with Google"
2. NextAuth.js redirects to Google OAuth
3. Scopes: `openid`, `email`, `profile`, `https://www.googleapis.com/auth/spreadsheets`
4. Store session + refresh token
5. Use refresh token for Sheets API calls

## Checklist Before Done

- [ ] No secrets hardcoded — all via env vars or Secret Manager
- [ ] All request inputs validated
- [ ] Consistent response shape (`{ data }` or `{ error }`)
- [ ] TypeScript interfaces for all request/response types
- [ ] Error handling — no raw errors exposed to client
- [ ] `npm run lint` passes
- [ ] `npm run build` passes
