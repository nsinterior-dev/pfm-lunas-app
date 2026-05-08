# Architecture — Lunas

> v4.0 — April 2026. Source: [Notion — Tech Stack & Architecture](https://www.notion.so/33f85edeff758163805ec368386dd03e)

## Tech Stack

| Layer | Technology | Notes |
|-------|-----------|-------|
| Data Fetching | TanStack React Query | `useQuery` + `useMutation` — replaces useEffect for all async ops |
| Validation | Zod | Request/response contracts in server layer — validated before anything runs |
| Frontend | Next.js (App Router) | TypeScript, Tailwind CSS |
| Components | shadcn/ui | Own the code, Tailwind-native, zero lock-in |
| Component Docs | Storybook | Document + test components in isolation |
| Design | Figma + shadcn/ui Figma Kit | Design source of truth before any code |
| Backend | Next.js API Routes | No separate backend for MVP 1-3 |
| Auth | Google OAuth 2.0 (NextAuth.js) | Covers Sheets permission in same flow |
| Spreadsheet | Google Sheets API v4 | Read/write user's own Sheet |
| Database | Firestore (Google Cloud) | Free tier — 1GB storage, 50k reads/day |
| AI — Deep Analysis | Anthropic API (Claude Sonnet) | Server-side only, $100 cap, manual trigger |
| AI — Fast / Fallback | Google Gemini API | Free tier, fallback for quick queries |
| Hosting | Cloud Run (us-central1) | Free tier — 2M req/month |
| Secrets | Google Secret Manager | 6 active secrets free — never plain .env in prod |
| Version Control | GitHub | Issues + Projects for ticketing |

---

## Folder Architecture

Three top-level directories: `client/`, `server/`, `app/`.

```
pfm-lunas-app/
│
├── client/                              # All client-side code
│   ├── components/
│   │   └── ui/                          # shadcn/ui base components
│   ├── features/
│   │   └── [feature]/                   # e.g. dashboard, sheets, auth, analysis
│   │       ├── application/             # Hooks (React Query + regular), use cases
│   │       ├── model/                   # Domain types, entities
│   │       └── presentation/            # UI components, view models
│   ├── lib/
│   │   └── server/                      # API client layer (like wv-admin-v2's lib/wv-server)
│   │       ├── features/
│   │       │   └── [feature]/
│   │       │       ├── service.ts       # API calls → /api/[feature]
│   │       │       └── types/           # Request/response types (client-side)
│   │       ├── helpers/
│   │       │   └── apiRequest.ts        # Generic HTTP helper
│   │       └── axios/                   # Axios instance, interceptors
│   ├── middleware/                       # Next.js middleware (auth redirects, etc.)
│   └── stories/                         # Storybook stories
│
├── server/                              # All server-side code (wizdam-webapp pattern)
│   ├── common/                          # Shared infrastructure
│   │   ├── errors/                      # AppError base class + subclasses
│   │   ├── middleware/                  # Auth checks, rate limiting
│   │   └── validation/                 # Zod validation helpers
│   ├── features/
│   │   └── [feature]/
│   │       ├── model/
│   │       │   ├── types/               # Domain types (server-side)
│   │       │   ├── request/             # Zod request schemas
│   │       │   └── response/            # Zod response schemas
│   │       ├── service/                 # Business logic, orchestration
│   │       ├── repository/              # Data access (Firestore, Sheets API, external)
│   │       └── parser/                  # Data transformation (optional)
│   └── lib/                             # Server-side SDK clients
│       ├── sheets.ts                    # Google Sheets API v4
│       ├── claude.ts                    # Anthropic API
│       ├── gemini.ts                    # Google Gemini API
│       └── firestore.ts                # Firestore SDK
│
├── app/                                 # Next.js App Router — pages + thin API routes
│   ├── api/                             # API route handlers (thin — delegate to server/)
│   │   ├── auth/[...nextauth]/route.ts
│   │   ├── sheets/
│   │   │   ├── detect/route.ts
│   │   │   ├── mapping/route.ts
│   │   │   ├── read/route.ts
│   │   │   └── write/route.ts
│   │   ├── analyze/route.ts
│   │   └── user/route.ts
│   ├── (pages)/                         # Page routes
│   │   ├── dashboard/page.tsx
│   │   ├── sheets/page.tsx
│   │   └── analysis/page.tsx
│   ├── layout.tsx                       # Root layout (QueryClientProvider, auth)
│   └── page.tsx                         # Landing page
│
├── .storybook/
└── docs/
```

---

## Layer Responsibilities

### `client/` — Frontend

| Layer | Responsibility |
|-------|---------------|
| `client/features/[feature]/presentation/` | React components, UI logic only |
| `client/features/[feature]/application/` | Hooks (React Query + regular), use cases — no UI, no React components |
| `client/features/[feature]/model/` | Domain types and entities — no framework dependencies |
| `client/lib/server/features/[feature]/service.ts` | API client — calls `/api/` routes (like wv-admin-v2's `lib/wv-server/`) |
| `client/lib/server/helpers/apiRequest.ts` | Generic HTTP helper |
| `client/lib/server/axios/` | Axios instance, interceptors |
| `client/components/ui/` | shadcn/ui base components |
| `client/middleware/` | Next.js middleware (auth redirects) |

### `server/` — Backend (wizdam-webapp pattern)

| Layer | Responsibility | wizdam-webapp equivalent |
|-------|---------------|--------------------------|
| `app/api/[route]/route.ts` | Route handler — thin, delegates to service | Controller |
| `server/features/[feature]/service/` | Business logic, orchestration | Service |
| `server/features/[feature]/repository/` | Data access — Firestore, Sheets API, external | ModelService / ORM |
| `server/features/[feature]/model/` | DTOs (Zod) + domain types | DTOs + Entities |
| `server/features/[feature]/parser/` | Data transformation (optional) | — |
| `server/common/errors/` | AppError + subclasses | WVError classes |
| `server/common/middleware/` | Auth checks, rate limiting | Guards + Interceptors |
| `server/common/validation/` | Zod helpers, shared pipes | Pipes |
| `server/lib/` | SDK clients (Sheets, Claude, Gemini, Firestore) | — |

### `app/` — Next.js Routing

| Layer | Responsibility |
|-------|---------------|
| `app/api/` | Thin route handlers — validate, delegate to `server/`, respond |
| `app/(pages)/` | Page components — import from `client/features/` |
| `app/layout.tsx` | Root layout — QueryClientProvider, auth session |

### Full request flow

```
Client                          Server
──────                          ──────
presentation/
    ↓ calls hook
application/
    ↓ calls API client
client/lib/server/service.ts
    ↓ HTTP request
                                app/api/route.ts (thin)
                                    ↓ Zod validates request
                                    ↓ extracts auth
                                server/features/service/
                                    ↓ business logic
                                server/features/repository/
                                    ↓ data access
                                server/lib/ (SDK clients)
                                    ↓ Google Sheets, Firestore, Claude, Gemini
                                    ↓ returns typed domain models
                                ← { data } or { error, code }
```

### Error handling pattern

Inspired by wizdam-webapp's `WVError` tuple pattern, adapted for Next.js:

```typescript
// server/common/errors/index.ts
export class AppError extends Error {
  constructor(
    public message: string,
    public statusCode: number,
    public code: string,
  ) {
    super(message)
  }
}

export class BadInputError extends AppError {
  constructor(message: string) {
    super(message, 400, 'BAD_INPUT')
  }
}

export class NotFoundError extends AppError {
  constructor(resource: string) {
    super(`${resource} not found`, 404, 'NOT_FOUND')
  }
}

export class SheetAccessError extends AppError {
  constructor(sheetName: string) {
    super(`Cannot access sheet: ${sheetName}`, 403, 'SHEET_ACCESS_DENIED')
  }
}

// Usage in route.ts
export async function GET(request: Request) {
  try {
    const data = await sheetsService.readSheet(params)
    return NextResponse.json({ data })
  } catch (error) {
    if (error instanceof AppError) {
      return NextResponse.json({ error: error.message, code: error.code }, { status: error.statusCode })
    }
    return NextResponse.json({ error: 'Internal server error' }, { status: 500 })
  }
}
```

---

## Examples

### Example: `analysis` feature (simple)

```
client/features/analysis/                       # Client
├── application/
│   └── useAnalysis.ts                          # React Query hook
├── model/
│   └── analysis.ts                             # AnalysisResult, AnalysisStatus
└── presentation/
    ├── AnalysisPanel.tsx
    └── PromptSuggestions.tsx

client/lib/server/features/analysis/            # API client
├── service.ts                                  # analyzeData() → POST /api/analyze
└── types/
    └── index.ts                                # AnalyzePayload, AnalyzeResponse

server/features/analysis/                       # Server
├── model/
│   ├── request/AnalyzeRequest.ts               # Zod: { sheetData, prompt, model }
│   ├── response/AnalyzeResponse.ts             # Zod: { result, tokensUsed, model }
│   └── index.ts
├── repository/
│   └── analysisHistoryRepository.ts            # Firestore CRUD
├── service/
│   └── analysisService.ts                      # Claude/Gemini orchestration
└── index.ts

app/api/analyze/route.ts                        # Thin handler → analysisService
```

### Example: `sheets` feature (complex — full architecture)

```
client/features/sheets/                          # Client
├── application/
│   ├── useSheetData.ts                          # React Query hook for dashboard reads
│   └── useSheetConnect.ts                       # Onboarding flow hook
├── model/
│   ├── creditCard.ts                            # CreditCardData, Transaction
│   ├── savings.ts                               # SavingsAccountData, SavingsEntry
│   ├── budget.ts                                # MonthlyBudgetData, BudgetSection
│   └── sheetMapping.ts                          # LinearSheetMapping, SectionMapping
└── presentation/
    ├── SheetConfirmDialog.tsx                    # Read/write permission confirmation
    └── MappingReviewPanel.tsx                    # User reviews detected mapping

client/lib/server/features/sheets/               # API client
├── service.ts                                   # detectSheet(), readSheet(), writeSheet()
└── types/
    └── index.ts                                 # DetectPayload, ReadPayload, etc.

server/features/sheets/                          # Server (full architecture)
├── model/
│   ├── types/
│   │   ├── sheetMapping.ts                      # SheetMappingDoc, LinearMapping
│   │   ├── cellData.ts                          # CellValue, FormattedCell, GridData
│   │   └── parsedData.ts                        # CreditCardData, SavingsData, BudgetData
│   ├── request/
│   │   ├── DetectSheetRequest.ts                # Zod: { spreadsheetId }
│   │   ├── ReadSheetRequest.ts                  # Zod: { spreadsheetId, sheetName, mappingId }
│   │   └── WriteSheetRequest.ts                 # Zod: { spreadsheetId, sheetName, row, data }
│   └── response/
│       ├── DetectSheetResponse.ts               # { tabs[], suggestedMappings[] }
│       ├── ReadSheetResponse.ts                 # { data: NormalizedSheetData }
│       └── WriteSheetResponse.ts                # { updatedRange, rowIndex }
├── repository/
│   ├── sheetsApiRepository.ts                   # Google Sheets API (values.get, includeGridData)
│   ├── mappingRepository.ts                     # Firestore CRUD for sheet_mappings
│   └── sessionRepository.ts                     # Firestore CRUD for user_session
├── service/
│   ├── sheetDetectionService.ts                 # Onboarding: detect structure
│   ├── sheetMappingService.ts                   # Claude-assisted mapping
│   ├── sheetReadService.ts                      # Daily reads via saved mapping
│   └── sheetWriteService.ts                     # Write-back via mapping
├── parser/
│   ├── linearParser.ts                          # Path A: row-by-row
│   ├── multiSectionParser.ts                    # Path B: formatting anchors
│   ├── installmentParser.ts                     # "11/24" → { current: 11, total: 24 }
│   └── anchorDetector.ts                        # Bold + colored bg = section header
└── index.ts

app/api/sheets/                                  # Thin route handlers
├── detect/route.ts                              # → sheetDetectionService
├── mapping/route.ts                             # → sheetMappingService
├── read/route.ts                                # → sheetReadService
└── write/route.ts                               # → sheetWriteService
```

### Flow: `sheets` onboarding

```
client/lib/server/features/sheets/service.ts
    ↓ POST /api/sheets/detect
app/api/sheets/detect/route.ts (thin)
    ↓ Zod validates → extracts auth
server/features/sheets/service/sheetDetectionService.ts
    ↓ calls sheetsApiRepository.getWithGridData()
    ↓ calls anchorDetector.findSectionAnchors()
    ↓ calls sheetMappingService.classifySections() (Claude)
    ↓ returns suggestedMappings[]
server/features/sheets/repository/mappingRepository.ts
    ↓ saves confirmed mapping to Firestore
← { data: { mappingId, sections[] } }
```

### Flow: `sheets` daily read

```
client/features/sheets/application/useSheetData.ts
    ↓ useQuery → client/lib/server/features/sheets/service.ts
    ↓ GET /api/sheets/read?mappingId=xxx
app/api/sheets/read/route.ts (thin)
    ↓ Zod validates → extracts auth
server/features/sheets/service/sheetReadService.ts
    ↓ mappingRepository.getMapping()
    ↓ sheetsApiRepository.batchGetValues(UNFORMATTED_VALUE)
    ↓ linearParser.parse() or multiSectionParser.parse()
← { data: NormalizedSheetData }
```

---

## Layer Rules (Non-Negotiable)

### `client/` rules

**Presentation** (`client/features/[feature]/presentation/`):
- Only renders UI. Receives props or calls hooks from `application/`.
- **Never imports from `server/`.** The server directory does not exist to the client.
- Never breaks if the data source changes.

**Application** (`client/features/[feature]/application/`):
- React Query hooks + regular hooks + use cases.
- Calls `client/lib/server/` for API communication — never `fetch()` directly.
- Business rules that are client-only (e.g., "if debt > savings, flag as critical").

**API Client** (`client/lib/server/`):
- Like wv-admin-v2's `lib/wv-server/` — thin API wrapper layer.
- `service.ts` per feature: wraps HTTP calls to `/api/` routes.
- Uses `apiRequest` helper + axios instance.
- Returns typed responses — no business logic.

```typescript
// client/lib/server/features/sheets/service.ts
import { apiRequest } from '@/client/lib/server/helpers/apiRequest'
import type { ReadSheetPayload, ReadSheetResponse } from './types'

export const readSheetData = (payload: ReadSheetPayload) =>
  apiRequest<ReadSheetPayload, ReadSheetResponse>('sheets/read', 'get', payload)

// client/features/sheets/application/useSheetData.ts
import { readSheetData } from '@/client/lib/server/features/sheets/service'

export function useSheetData(mappingId: string) {
  return useQuery({
    queryKey: ['sheet-data', mappingId],
    queryFn: () => readSheetData({ mappingId }),
  })
}

// client/features/sheets/presentation/DashboardSummary.tsx
export function DashboardSummary() {
  const { data, isLoading } = useSheetData(mappingId)
  return <div>{data?.totals?.savings}</div>
}
```

### `server/` rules

Adapted from wizdam-webapp's NestJS pattern for Next.js.

**Route handlers** (`app/api/[route]/route.ts`):
- Thin — validate request with Zod, extract auth, delegate to `server/features/` service, return response.
- Never contain business logic. If it's more than 10 lines, move to `service/`.
- Always return `{ data }` or `{ error, code }`.
- Catch `AppError` → map to HTTP status.

**Services** (`server/features/[feature]/service/`):
- All business logic lives here. Orchestrates repositories + parsers.
- Never calls `server/lib/` directly — goes through `repository/`.
- Throws `AppError` subclasses — never raw errors.
- Pure TypeScript — no Next.js imports, no React.

**Repositories** (`server/features/[feature]/repository/`):
- Data access only. Calls `server/lib/` SDK clients.
- Returns typed domain models — never raw API responses.
- One repository per data source.

**Parsers** (`server/features/[feature]/parser/`) — optional:
- Pure functions that transform raw data into domain models.
- No side effects, no API calls — just transformation logic.

**Model** (`server/features/[feature]/model/`):
- `request/` — Zod schemas for incoming requests.
- `response/` — Zod schemas for outgoing responses.
- `types/` — Domain types shared between service and repository.

**SDK Clients** (`server/lib/`):
- Low-level wrappers around external SDKs (Google Sheets, Firestore, Claude, Gemini).
- Never imported from `client/` — server-only.

### `app/` rules

- Pages import from `client/features/` — never from `server/`.
- API route handlers import from `server/features/` — never contain business logic.
- `layout.tsx` wraps app in providers (QueryClientProvider, SessionProvider).

---

## On useEffect

> **As much as possible, refrain from using `useEffect`.**

useEffect is the most misused hook in React. In Lunas, it is almost never the right answer.

| You want to... | Use this instead |
|-----------------|-----------------|
| Fetch data on mount | React Query (`useQuery`) |
| Fetch data on user action | React Query (`useMutation`) |
| Sync with external state | Zustand or React context |
| Derive state from props | Compute inline or `useMemo` |
| Run on route change | Next.js layout / `searchParams` |
| Subscribe to real-time data | Firestore `onSnapshot` in a custom hook with cleanup |

useEffect is **only acceptable** for:
- Setting up a non-React subscription with cleanup (e.g. Firestore real-time listener)
- Imperative DOM operations that have no React equivalent

```typescript
// WRONG
useEffect(() => {
  setLoading(true)
  getSheetData().then(data => {
    setData(data)
    setLoading(false)
  })
}, [])

// RIGHT
const { data, isLoading } = useQuery({
  queryKey: ['sheet-data'],
  queryFn: getSheetData,
})
```

---

## Why These Choices

| Decision | Reason |
|----------|--------|
| React Query over useEffect | Enforces clean data fetching — no side-effect soup, pairs with Clean Architecture |
| Zod for validation | Server layer contracts validated at runtime — catches bad data before business logic |
| shadcn/ui | Own the code, Tailwind-native, pairs with Figma kit |
| Storybook | Document components as we build — future-proofs MVP 4 |
| Firestore over Cloud SQL | Cloud SQL costs $7-10/month min. Firestore free tier is enough |
| Cloud Run over Vercel | Real GCP deployment experience, free tier, better resume value |
| Gemini as Claude fallback | Cost control — Gemini free tier handles quick queries |
| Secret Manager | Proper GCP secrets practice — not just .env files |
| us-central1 region | Only US regions qualify for Cloud Run free tier |

## Monthly Cost Estimate

| Service | Free Limit | Will We Exceed? |
|---------|-----------|-----------------|
| Cloud Run | 2M requests, 180k vCPU-seconds | No — personal use |
| Firestore | 1GB, 50k reads/day | No — personal use |
| Google Sheets API | 500 req/100 sec | No |
| Gemini API | Generous free tier | No — personal use |
| Secret Manager | 6 active secrets | No |
| **Claude API** | **$100 cap (manual trigger only)** | **Only cost** |

## Key Architecture Decisions

### Google Sheets IS the database
Financial data (transactions, balances, budgets) stays in the user's Google Sheet. Firestore does NOT store financial data — it stores only sheet mappings, analysis history, user session, and recurring budget items. All reads go through a **translation layer** that maps sheet structure to normalized Lunas models. See [DATA-SCHEMA.md](DATA-SCHEMA.md) for the full translation layer spec.

### No separate backend
Next.js API Routes handle all server-side logic for MVP 1-3. A separate backend is only considered for MVP 4 (multi-user).

### AI is server-side only
All AI calls go through API routes. No client-side API keys. Manual trigger only (button press) to control costs.

### Dual data source
Users can choose Google Sheets (primary) or Firestore (app-managed). MVP 1 ships with Google Sheets only; Firestore toggle shows "Coming soon".

### Auth scopes
Google OAuth 2.0 grants both authentication and Google Sheets API access in a single flow via NextAuth.js.

### Zod validation
All server-side request/response contracts are validated with Zod before processing.

### React Query for data fetching
All client-side data fetching uses React Query. No raw `fetch()` or `useEffect` for API calls.
