# Architecture — Lunas

> v2.1 — April 2026. Source: [Notion — Tech Stack & Architecture](https://www.notion.so/33f85edeff758163805ec368386dd03e)

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

## Folder Architecture — Feature-First (Clean Architecture)

```
pfm-lunas-app/
├── features/
│   └── [feature]/                  # e.g. dashboard, sheets, auth, analysis
│       ├── application/            # Use cases, business logic
│       ├── data/                   # Repositories, data sources
│       ├── model/                  # Domain models, entities
│       └── presentation/           # UI components, pages, view models
│
├── server/                         # Next.js API routes (middleware layer)
│   ├── axios/                      # HTTP client setup, interceptors
│   └── features/
│       └── [feature]/
│           ├── model/
│           │   ├── types/          # Shared TypeScript types
│           │   ├── response/       # API response shapes
│           │   ├── request/        # API request shapes
│           │   └── index.ts
│           ├── service/            # Business logic, API calls
│           └── index.ts
│
├── components/
│   └── ui/                         # shadcn/ui base components
│
├── lib/                            # Shared clients
│   ├── sheets.ts
│   ├── claude.ts
│   ├── gemini.ts
│   └── firestore.ts
│
├── stories/                        # Storybook stories
├── .storybook/
└── docs/
```

---

## Layer Responsibilities

| Layer | Responsibility |
|-------|---------------|
| `presentation/` | React components, pages, hooks, UI logic only |
| `application/` | Use cases — orchestrates data + domain, no UI |
| `data/` | Repository pattern — talks to Firestore, Sheets API, external APIs |
| `model/` | Pure domain types and entities — no framework dependencies |
| `server/features/[feature]/service/` | Server-side business logic called by API routes |
| `server/features/[feature]/model/` | Request/response contracts between client and server |
| `server/axios/` | Axios instance, interceptors, auth headers |

### Example: `analysis` feature

```
features/
└── analysis/
    ├── application/
    │   └── triggerAnalysis.ts       # use case: sends data to AI, saves result
    ├── data/
    │   └── analysisRepository.ts    # reads/writes Firestore analysis history
    ├── model/
    │   └── analysis.ts              # AnalysisResult, AnalysisStatus types
    └── presentation/
        ├── AnalysisPanel.tsx        # UI component
        ├── PromptSuggestions.tsx    # pre-built prompt chips
        └── useAnalysis.ts          # React hook

server/
└── features/
    └── analysis/
        ├── model/
        │   ├── request/
        │   │   └── AnalyzeRequest.ts    # { sheetData, prompt, model }
        │   ├── response/
        │   │   └── AnalyzeResponse.ts   # { result, tokensUsed, model }
        │   └── index.ts
        ├── service/
        │   └── analysisService.ts   # calls Claude or Gemini, returns result
        └── index.ts                 # Next.js API route handler
```

---

## Layer Rules (Non-Negotiable)

### Presentation layer

- **Only renders UI.** Receives props or calls custom hooks. That's it.
- **Never calls an API directly.** No `fetch()`, no axios, no Firestore calls inside a component.
- **Never imports from `server/`.** The server layer does not exist to the presentation layer.
- **Never breaks if the data source changes.** If we switch from Google Sheets to Firestore, zero presentational components should need editing.
- Custom hooks (`useAnalysis`, `useSheetData`) live in `presentation/` but only orchestrate — they call `application/` use cases, not APIs.

```typescript
// WRONG — presentation calling API directly
export function SummaryCard() {
  const [data, setData] = useState(null)
  useEffect(() => {
    fetch('/api/sheets').then(r => r.json()).then(setData)
  }, [])
  return <div>{data?.total}</div>
}

// RIGHT — presentation receiving data via hook
export function SummaryCard() {
  const { totals, isLoading } = useDashboardSummary()
  return <div>{totals?.savings}</div>
}
```

### Application layer

- Orchestrates: calls `data/` repositories, applies business rules, returns results.
- No UI imports. No React. Pure TypeScript functions or classes.
- This is where "if debt > savings, flag as critical" logic lives — not in the component.

### Data layer

- Talks to the outside world: Firestore, Google Sheets API, Claude, Gemini.
- Returns typed domain models from `model/` — never raw API responses.
- If the API response shape changes, only `data/` needs updating.

### Server layer (`server/features/`)

- Next.js API route handlers delegate here immediately.
- `service/` contains server-side business logic (calling Claude, formatting prompts).
- `model/request` and `model/response` are the typed contracts — validated with Zod before anything runs.

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
