# Product Management — Lunas

> Source of truth: [Notion workspace](https://www.notion.so/Lunas-Personal-Finance-App-33f85edeff75812b9c7bd7335fe8bffb)

## Current Phase

**Pre-build — Planning complete, Sprint 1 starts Apr 14.** PM + BA done. Design tokens decided. Dev starts Monday.

## The Team

| Hat | Who |
|-----|-----|
| Product Manager | nsinterior-dev + Claude |
| Business Analyst | nsinterior-dev + Claude |
| Solutions Architect | Claude |
| UI/UX Designer | nsinterior-dev |
| Dev | nsinterior-dev + Claude Code |

## Problem Statement

People with debt, inconsistent savings, or high income and low financial clarity don't need another budgeting app. They need a tool that:
- Understands their **full financial picture**
- Tells them **what to do next** in plain language
- Guides them with **real financial literacy**, not just alerts
- Doesn't make them feel judged or overwhelmed

## Product Vision

> A personal finance companion that connects to your spreadsheet, understands your real situation, and tells you exactly what to do next — without judgment.

## MVP Roadmap

| Phase | Theme | Doc |
|-------|-------|-----|
| MVP 1 | "Can I see and manage my data?" | [MVP-1.md](MVP-1.md) |
| MVP 2 | "Can the app understand my situation?" | [MVP-2.md](MVP-2.md) |
| MVP 3 | "Can the app teach me while it helps me?" | [MVP-3.md](MVP-3.md) |
| MVP 4 | "Can more people use this?" | [MVP-4.md](MVP-4.md) |

See also: [Out of Scope](OUT-OF-SCOPE.md)

## Open Questions

| Question | Owner | Status |
|----------|-------|--------|
| What is the exact schema of the Google Sheet? | BA / Dev | Next session |
| What exact prompts do we send to Claude and Gemini? | PM / BA | Next session |
| Do we use Gemini at all in MVP 2 or just Claude? | PM | Decided — yes, as fast/fallback |
| What counts as trusted financial literacy sources? | PM / BA | To decide |
| Sanitize mode — opt-in or opt-out default? | PM / UX | To decide |
| What does the Sheet schema look like for Lunas-created sheets? | Dev / BA | Parked |

## Key Decisions

| Decision | Choice | Reason |
|----------|--------|--------|
| Product name | Lunas | Filipino for solution, nods to buwan |
| Framework | Next.js (App Router) | Full-stack, pairs well with Cloud Run |
| Hosting | Cloud Run (us-central1) | GCP experience, free tier (Vercel was earlier draft, superseded) |
| Auth | Google OAuth 2.0 | Covers Sheets access in same flow |
| Phase 1 users | Solo (owner only) | Cost control, simplicity |
| AI trigger | Manual (button) | Cost control |
| Data sources | Google Sheet OR Firestore | User choice, spreadsheet-friendly |
| Component library | shadcn/ui | Own the code, Tailwind-native |
| Component docs | Storybook | Document + test in isolation |
| React Query | @tanstack/react-query | Replaces useEffect for all data fetching |
| Zod | Yes — official | Validates server request/response contracts |
| Figma account | Personal (not work) | Claude MCP only has access to work Figma; Lunas file managed manually |
