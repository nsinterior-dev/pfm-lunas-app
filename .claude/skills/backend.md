# Skill: Backend Developer

> Invoke with `/backend` for API routes, database operations, external service integrations, and server-side logic.

## When to Use

- Creating or modifying Next.js API routes (`/app/api/`)
- Working with Google Sheets API, Firestore, Claude API, or Gemini API
- Implementing authentication (NextAuth.js + Google OAuth)
- Server-side data processing or validation
- Translation layer (sheet parsing, mapping, detection)

## Context

Read these before starting:
- `CLAUDE.md` — Project conventions
- `docs/ARCHITECTURE.md` — Tech stack, server-side layers, architecture decisions
- `docs/DATA-SCHEMA.md` — Translation layer, sheet mappings, Firestore collections, API gotchas
- Relevant sprint ticket in `docs/EPIC-SPRINTS/`

## Tech Stack

- **Next.js API Routes** (App Router — `route.ts` files)
- **NextAuth.js** for Google OAuth 2.0
- **Google Sheets API v4** via `/lib/sheets.ts`
- **Firestore** via `/lib/firestore.ts` — **glue only** (mappings, analysis history, session)
- **Anthropic API** (Claude) via `/lib/claude.ts`
- **Google Gemini API** via `/lib/gemini.ts`
- **Google Secret Manager** for secrets in production
- **Zod** for request/response validation in `server/features/[feature]/model/`

## Data Architecture

**Google Sheets IS the database** for financial data. Firestore does NOT store transactions, balances, or budgets.

All sheet reads go through a **translation layer**:
- **Linear sheets** (debt per card, savings) — row-by-row with header mapping
- **Multi-section sheets** (monthly budget) — formatting anchors + section mapping
- Mapping is created once (Claude assists) and saved to Firestore `sheet_mappings` collection
- `spreadsheets.values.get` for daily reads, `spreadsheets.get` + `includeGridData` for onboarding only

See `docs/DATA-SCHEMA.md` for full sheet-by-sheet reference and API gotchas.

## Server-Side Architecture

Adapted from wizdam-webapp's NestJS pattern for Next.js.

### Layer Structure

```
server/
├── common/                          # Shared infrastructure
│   ├── errors/                      # AppError + subclasses (BadInput, NotFound, SheetAccess)
│   ├── middleware/                   # Auth checks, rate limiting
│   └── validation/                  # Zod helpers, shared validators
├── features/
│   └── [feature]/
│       ├── model/
│       │   ├── types/               # Domain types (shared between service + repository)
│       │   ├── request/             # Zod schemas for incoming requests
│       │   ├── response/            # Zod schemas for outgoing responses
│       │   └── index.ts
│       ├── service/                 # Business logic, orchestration
│       ├── repository/              # Data access (Firestore, Sheets API, external)
│       ├── parser/                  # Data transformation (optional, complex features)
│       └── index.ts
└── lib/                             # Server-side SDK clients
    ├── sheets.ts                    # Google Sheets API v4
    ├── claude.ts                    # Anthropic API
    ├── gemini.ts                    # Google Gemini API
    └── firestore.ts                # Firestore SDK
```

### Layer Rules

**Route handlers** (`app/api/[route]/route.ts`):
- Thin — validate request, extract auth, delegate to service, return response.
- Never contain business logic.
- Always return `{ data }` or `{ error, code }`.
- Catch `AppError` → map to HTTP status.

**Services** (`service/`):
- All business logic lives here.
- Never calls `lib/` directly — goes through `repository/`.
- Throws `AppError` subclasses — never raw errors.
- Pure TypeScript — no Next.js imports, no React.

**Repositories** (`repository/`):
- Data access only. Calls `server/lib/` SDK clients.
- Returns typed domain models — never raw API responses.
- One repository per data source.

**Parsers** (`parser/`) — optional:
- Pure functions — transform raw data into domain models.
- No side effects, no API calls, no Firestore.
- Used by `service/` after `repository/` returns raw data.

**SDK Clients** (`server/lib/`):
- Low-level wrappers around external SDKs.
- Never imported from `client/` — server-only.
- Only called by `repository/` — never by `service/` directly.

### Server-Side Flow

```
app/api/route.ts → Zod validates → extracts auth
    ↓
server/features/service/ → business logic, orchestration
    ↓
server/features/repository/ → data access
    ↓
server/lib/ → SDK wrappers (sheets.ts, firestore.ts, claude.ts)
```

### Error Handling

```typescript
// server/common/errors/index.ts
class AppError extends Error {
  constructor(message: string, statusCode: number, code: string) { ... }
}

class BadInputError extends AppError { ... }     // 400
class NotFoundError extends AppError { ... }     // 404
class SheetAccessError extends AppError { ... }  // 403
class RateLimitError extends AppError { ... }    // 429

// In route.ts
try {
  const data = await sheetsService.readSheet(params)
  return NextResponse.json({ data })
} catch (error) {
  if (error instanceof AppError) {
    return NextResponse.json({ error: error.message, code: error.code }, { status: error.statusCode })
  }
  return NextResponse.json({ error: 'Internal server error' }, { status: 500 })
}
```

## Workflow

### 1. Research
- Read the ticket requirements
- Check existing services/repositories before creating new ones
- Review API route patterns in `/app/api/`

### 2. Plan
- Identify which layers need changes (route → service → repository)
- Define Zod schemas for request/response
- Consider error cases and which `AppError` subclass to throw
- Present plan to user — wait for approval

### 3. Implement
- Route handler: thin, delegates to service
- Service: business logic, calls repositories
- Repository: data access, returns typed models
- Parser: transformation logic (if needed)
- Zod schemas for all contracts

### 4. Verify
- Test API routes manually or with tests
- Run `npm run lint` and `npm run build`
- Verify secrets are not hardcoded

## API Route Structure

```
app/api/
├── auth/
│   └── [...nextauth]/
│       └── route.ts              # NextAuth.js config + Google provider
├── sheets/
│   ├── detect/
│   │   └── route.ts             # POST — onboarding: detect sheet structure
│   ├── mapping/
│   │   └── route.ts             # POST save mapping, GET get mapping
│   ├── read/
│   │   └── route.ts             # GET — daily reads via saved mapping
│   ├── write/
│   │   └── route.ts             # POST — append row, update cell
│   └── route.ts                 # GET list connected sheets
├── analyze/
│   └── route.ts                 # POST trigger AI analysis (Claude or Gemini)
└── user/
    └── route.ts                 # GET/PUT user session + preferences
```

## SDK Clients (`server/lib/`)

Server-side only. Never imported from `client/`. Only called from `repository/`.

| File | Purpose | Key Methods |
|------|---------|-------------|
| `sheets.ts` | Google Sheets API v4 | `getValues`, `batchGetValues`, `getWithGridData`, `appendRow`, `updateCell` |
| `claude.ts` | Anthropic API | `complete(prompt, options)` — raw SDK wrapper |
| `gemini.ts` | Google Gemini API | `complete(prompt, options)` — same interface as Claude |
| `firestore.ts` | Firestore SDK | `getDoc`, `setDoc`, `queryCollection` — generic CRUD |

## Rules

### Do
- Follow the layer flow: route → service → repository → lib
- Keep route handlers thin (validate + delegate + respond)
- Validate all requests with Zod schemas
- Throw `AppError` subclasses — never raw errors
- Return consistent response shapes: `{ data }` or `{ error, code }`
- Use TypeScript interfaces for all data shapes
- Handle rate limits for external APIs (Sheets: 500 req/100s)
- Reuse existing services/repositories — don't duplicate
- Keep all API keys and secrets server-side only

### Don't
- Put business logic in route handlers
- Call `lib/` clients directly from services — go through `repository/`
- Expose API keys to the client — ever
- Auto-trigger AI calls — manual trigger only (cost control)
- Store raw secrets in `.env` for production — use Secret Manager
- Skip input validation on API routes
- Return raw error messages from external services to the client
- Return raw API responses from repositories — always map to domain types

## AI Integration Rules

- All AI calls go through `/app/api/analyze/route.ts`
- Claude for deep analysis, Gemini for quick queries
- $100 monthly cap on Claude — enforce server-side
- Every AI response must include at least one actionable next step
- Log token usage for cost tracking
- Include privacy disclosure data in response metadata
- Claude-assisted sheet mapping goes through a separate service (not `/api/analyze`)

## Auth Flow

1. User clicks "Sign in with Google"
2. NextAuth.js redirects to Google OAuth
3. Scopes: `openid`, `email`, `profile`, `https://www.googleapis.com/auth/spreadsheets`
4. Store session + refresh token
5. Use refresh token for Sheets API calls

## Checklist Before Done

- [ ] Route handlers are thin — logic in service layer
- [ ] Services call repositories, not `lib/` directly
- [ ] Zod schemas defined for all request/response contracts
- [ ] Errors use `AppError` subclasses with proper status codes
- [ ] Consistent response shape (`{ data }` or `{ error, code }`)
- [ ] TypeScript interfaces for all data shapes
- [ ] No secrets hardcoded — all via env vars or Secret Manager
- [ ] `npm run lint` passes
- [ ] `npm run build` passes
