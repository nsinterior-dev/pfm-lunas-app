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

## Project Structure (Feature-First Clean Architecture)

```
pfm-lunas-app/
├── features/
│   └── [feature]/              # e.g. dashboard, sheets, auth, analysis
│       ├── application/        # Use cases, hooks (use*.ts), React Query hooks (use*.ts)
│       ├── data/               # Repositories, data sources
│       ├── model/              # Domain types, entities
│       └── presentation/       # UI components, hooks
├── server/
│   ├── axios/                  # HTTP client, interceptors
│   └── features/[feature]/
│       ├── model/{request,response}  # Typed contracts (Zod-validated)
│       └── service/            # Server-side business logic
├── components/ui/              # shadcn/ui base components
├── lib/                        # Shared clients (sheets, claude, gemini, firestore)
├── stories/                    # Storybook stories
├── docs/
└── .storybook/
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
- **Feature-first layers**: `presentation/` -> `application/` -> `data/` -> `model/`
- **Presentation never calls APIs directly** — use hooks that call `application/` use cases
- **No `useEffect` for data fetching** — use React Query (`useQuery`, `useMutation`)
- **Server contracts validated with Zod** — `server/features/[feature]/model/`
- **If the data source changes, zero UI components should break**

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

