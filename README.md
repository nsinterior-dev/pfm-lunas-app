# Lunas

> *Lunas* — Filipino for *solution*, a nod to *buwan* (moon).

A personal finance app that connects to your spreadsheet, understands your real situation, and tells you exactly what to do next — without judgment.

## Status

**Pre-build — Discovery & Planning**

No code yet. PM + BA phase only.

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | Next.js (App Router), TypeScript, Tailwind CSS |
| Components | shadcn/ui + Storybook |
| Backend | Next.js API Routes |
| Auth | Google OAuth 2.0 (NextAuth.js) |
| Data | Google Sheets API v4 + Firestore |
| AI | Anthropic Claude (deep analysis) + Google Gemini (fast/fallback) |
| Hosting | Cloud Run (us-central1) |
| Secrets | Google Secret Manager |

## MVP Roadmap

| Phase | Theme |
|-------|-------|
| **MVP 1** | Foundation — connect Sheet, dashboard, CRUD |
| **MVP 2** | AI Analysis — Claude + Gemini on-demand insights |
| **MVP 3** | Financial Literacy — knowledge cards, calculators, projections |
| **MVP 4** | Expansion — multi-user, pricing, onboarding |

## Project Structure

```
pfm-lunas-app/
├── app/
│   ├── api/                # API routes
│   └── (routes)/           # App pages
├── components/
│   └── ui/                 # shadcn/ui components
├── lib/
│   ├── sheets.ts           # Google Sheets client
│   ├── claude.ts           # Anthropic client
│   ├── gemini.ts           # Gemini client
│   └── firestore.ts        # Firestore client
├── docs/                   # Project documentation
└── .storybook/             # Storybook config
```

## Getting Started

> Setup instructions will be added once the project is scaffolded (Sprint 1).

```bash
# npm install
# npm run dev          # Start dev server
# npm run storybook    # Component docs
# npm run lint         # Lint check
# npm run build        # Production build
```

## Documentation

| Doc | What's Inside |
|-----|---------------|
| [Product Management](docs/PRODUCT-MANAGEMENT/README.md) | MVPs, team, vision, decisions |
| [Architecture](docs/ARCHITECTURE.md) | Tech stack, repo structure, costs |
| [Design](docs/DESIGN.md) | Figma workflow, shadcn/ui, Storybook |
| [Sprint Tickets](docs/EPIC-SPRINTS/) | LUN-001 through LUN-028 |

## Links

- [GitHub Project Board](https://github.com/users/nsinterior-dev/projects/6)
- [Notion Workspace](https://www.notion.so/Lunas-Personal-Finance-App-33f85edeff75812b9c7bd7335fe8bffb)

## Author

**Nicolle S. Interior** ([@nsinterior-dev](https://github.com/nsinterior-dev))
