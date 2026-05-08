# Lunas - Personal Finance Manager with AI

## Project Overview

**Lunas** (`pfm-lunas-app`) — Filipino for *solution*, a nod to *buwan* (moon). A personal finance app that connects to your spreadsheet, understands your real situation, and tells you exactly what to do next — without judgment.

- **Repo**: [nsinterior-dev/pfm-lunas-app](https://github.com/nsinterior-dev/pfm-lunas-app)
- **Project Board**: [GitHub Projects #6](https://github.com/users/nsinterior-dev/projects/6)
- **Planning**: [Notion workspace](https://www.notion.so/Lunas-Personal-Finance-App-33f85edeff75812b9c7bd7335fe8bffb)

## Current Status

- **Phase**: Pre-build — Planning complete, Sprint 1 starts Apr 14
- **Branch strategy**: `main` (production) + `development` (active work)
- **Author**: Nicolle S. Interior (nsinterior-dev)

## Tech Stack (Finalized)

| Layer | Technology | Notes |
|-------|-----------|-------|
| Data Fetching | TanStack React Query | `useQuery` + `useMutation` — replaces useEffect |
| Validation | Zod | Server contracts validated at runtime |
| Frontend | Next.js (App Router) | TypeScript, Tailwind CSS |
| Components | shadcn/ui | Own the code, Tailwind-native, zero lock-in |
| Component Docs | Storybook | Document + test components in isolation |
| Design | Figma + shadcn/ui Figma Kit | Design source of truth before any code |
| Backend | Next.js API Routes | No separate backend for MVP 1-3 |
| Auth | Google OAuth 2.0 | Covers Sheets permission in same flow |
| Spreadsheet | Google Sheets API v4 | Read/write user's own Sheet |
| Database | Firestore (Google Cloud) | Free tier — 1GB storage, 50k reads/day |
| AI (Deep) | Anthropic API (Claude Sonnet) | Server-side only, $100 cap, manual trigger |
| AI (Fast) | Google Gemini API | Free tier, fallback for quick queries |
| Hosting | Cloud Run | Free tier — us-central1 only |
| Secrets | Google Secret Manager | Never plain .env in prod |

## Project Structure

Three top-level directories: `client/`, `server/`, `app/`.

```
pfm-lunas-app/
├── client/                     # All client-side code
│   ├── components/ui/          # shadcn/ui base components
│   ├── features/[feature]/     # Feature-first architecture
│   │   ├── application/        # Hooks (React Query + regular), use cases
│   │   ├── model/              # Domain types, entities
│   │   └── presentation/       # UI components
│   ├── lib/server/             # API client (like wv-admin-v2's lib/wv-server)
│   │   ├── features/[feature]/ # service.ts + types/ per feature
│   │   ├── helpers/            # apiRequest.ts
│   │   └── axios/              # Axios instance
│   ├── middleware/              # Next.js middleware
│   └── stories/                # Storybook stories
│
├── server/                     # All server-side code (wizdam-webapp pattern)
│   ├── common/                 # Errors (AppError), middleware, validation
│   ├── features/[feature]/     # Per-feature server logic
│   │   ├── model/{request,response,types}  # Zod schemas + domain types
│   │   ├── service/            # Business logic, orchestration
│   │   ├── repository/         # Data access (Firestore, Sheets API, external)
│   │   └── parser/             # Data transformation (optional)
│   └── lib/                    # Server-side SDK clients (sheets, claude, gemini, firestore)
│
├── app/                        # Next.js App Router — pages + thin API routes
│   ├── api/                    # Thin route handlers → server/features/
│   ├── (pages)/                # Page routes → client/features/
│   └── layout.tsx              # Root layout (providers)
│
├── .storybook/
└── docs/
```

See [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) for full layer rules, examples, and conventions.

## Documentation

| Doc | Contents |
|-----|----------|
| [docs/PRODUCT-MANAGEMENT/](docs/PRODUCT-MANAGEMENT/README.md) | MVP phases, team, vision, decisions, open questions |
| [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) | Tech stack, clean architecture layers, useEffect rules, cost analysis |
| [docs/DESIGN.md](docs/DESIGN.md) | Design system, Figma workflow, design principles |
| [docs/DESIGN-TOKENS.md](docs/DESIGN-TOKENS.md) | Colors (teal + purple), category semantics, CSS variables, typography |
| [docs/STORYBOOK.md](docs/STORYBOOK.md) | Component library plan, story conventions, addons, workflow |
| [docs/DATA-SCHEMA.md](docs/DATA-SCHEMA.md) | Translation layer, sheet mappings, Firestore collections, API reference |
| [docs/EPIC-SPRINTS/](docs/EPIC-SPRINTS/) | Sprint tickets (LUN-001 through LUN-028) |

## Skills

Invoke these for specialized task guidance:

| Skill | Command | When to Use |
|-------|---------|-------------|
| Frontend | `/frontend` | Pages, components, features, bug fixes |
| Backend | `/backend` | API routes, Sheets/Firestore/AI integrations, auth |
| UI Designer | `/ui-designer` | Visual design, layouts, component styling, Figma-to-code |
| UX Designer | `/ux-designer` | User flows, interaction patterns, microcopy, edge cases |
| UI/UX Researcher | `/ui-ux-researcher` | Competitive analysis, heuristics, best practices, accessibility |
| Deployment | `/deployment` | Docker, Cloud Run, CI/CD, secrets, GCP setup |
| Code Review | `/code-review` | Quality, security, conventions, pre-merge review |

## Development Workflow

Follow the **Research -> Strategy -> Execution** lifecycle:

1. **Research** — Understand the problem, explore options, read existing code
2. **Strategy** — Present a plan before writing code; wait for approval
3. **Execution** — Implement, test, verify

## Conventions

### Architecture (Non-Negotiable)
- **Three directories**: `client/` (frontend), `server/` (backend), `app/` (pages + thin API routes)
- **Client feature layers**: `presentation/` -> `application/` -> `client/lib/server/` (API client)
- **Server feature layers**: `route.ts` (thin) -> `service/` -> `repository/` -> `server/lib/`
- **Presentation never imports from `server/`** — the server directory does not exist to client code
- **No `useEffect` for data fetching** — use React Query (`useQuery`, `useMutation`)
- **Server contracts validated with Zod** — `server/features/[feature]/model/`
- **Services never call `server/lib/` directly** — go through `repository/` layer
- **Errors use `AppError` subclasses** — never raw throws in server code
- **Google Sheets IS the database** — financial data stays in user's Sheet, Firestore = glue only
- **Translation layer** — all sheet reads go through saved mappings (see [DATA-SCHEMA.md](docs/DATA-SCHEMA.md))

### File Naming (Non-Negotiable)
- **React Query hooks** — file name starts with `use` (e.g., `useTransactions.ts`, `useDashboardStats.ts`)
- **Custom hooks** — file name starts with `use` (e.g., `useFilterState.ts`, `useFormValidation.ts`)
- All hook files live in `application/` layer

### Code Style
- **TypeScript** with strict types — no `any`
- **Tailwind CSS** for styling via shadcn/ui
- Descriptive variable names — no single-letter names
- Tests for all new features and bug fixes

### Git
- Work on the `development` branch
- PR target: `main`
- Clear, concise commit messages

### Quality
- Prioritize **visual polish and UX** for UI components
- Design in Figma first, then code
- Document architecture decisions as they are made
- Verify changes with build and lint commands

### AI Integration
- AI calls are **server-side only** (API routes)
- AI trigger is **manual** (button) — never auto-analyze (cost control)
- Claude for deep reasoning; Gemini for quick calculations
- Every AI response must include at least one actionable next step
- Clear privacy disclosure on what data is sent

## Commands

_(To be updated once project is scaffolded)_

```bash
# Development
# npm run dev

# Storybook
# npm run storybook

# Testing
# npm test

# Linting
# npm run lint

# Build
# npm run build
```

