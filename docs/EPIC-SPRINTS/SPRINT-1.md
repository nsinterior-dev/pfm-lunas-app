# Sprint 1 — Project Setup

**Dates**: Apr 14 – Apr 25, 2026
**MVP**: 1 — Foundation
**Goal**: Repo is running locally and deployed on Cloud Run. Nothing broken, nothing fancy.

---

## LUN-001 — Initialize Next.js + TypeScript + Tailwind + React Query + Zod

- Create Next.js 14 app with App Router
- Configure TypeScript strict mode
- Set up Tailwind CSS
- Install `@tanstack/react-query` + wrap app in `QueryClientProvider`
- Install `zod`
- Add ESLint + Prettier

**Acceptance**: `npm run dev` works. QueryClient wraps the app. No type errors.
**Labels**: setup, sprint-1

---

## LUN-002 — Install and configure shadcn/ui with Lunas tokens

- Run shadcn init
- Install base components: Button, Card, Input, Table, Dialog, Badge, Skeleton
- Apply Lunas CSS variables to `globals.css`:
  - `--primary: #1D9E75` (teal)
  - `--secondary: #EEEDFE` (purple light)
  - `--accent: #7F77DD`
  - `--background: #F8F7F4`
  - `--destructive: #E24B4A`
- Configure dark mode tokens

**Acceptance**: Components render in dev with correct Lunas colors in both modes.
**Labels**: setup, design, sprint-1

---

## LUN-003 — Set up Storybook with light/dark + a11y

- Install Storybook for Next.js
- Install addons: `@storybook/addon-a11y`, `@storybook/addon-themes`, `@storybook/addon-docs`
- Configure theme toggle (light/dark) in `.storybook/preview.ts`
- Write stories for: Button, Card, Input, Badge, Skeleton
- Create `stories/tokens/Colors.stories.tsx` with full Lunas palette

**Acceptance**: `npm run storybook` works. Light/dark toggle works. All base components visible.
**Labels**: setup, design, sprint-1

---

## LUN-004 — Set up Google Secret Manager

- Create GCP project (pfm-lunas)
- Enable Secret Manager API
- Add secrets: GOOGLE_CLIENT_ID, GOOGLE_CLIENT_SECRET, ANTHROPIC_API_KEY, GEMINI_API_KEY, SPREADSHEET_ID
- Access secrets from local env via `.env.local` for dev

**Acceptance**: All secrets accessible locally. Never committed to repo.
**Labels**: setup, security, sprint-1

---

## LUN-005 — Set up Firestore

- Enable Firestore in GCP project (pfm-lunas)
- Create Firestore client in `/lib/firestore.ts`
- Write + read test document

**Acceptance**: Test document readable in Firestore console.
**Labels**: setup, backend, sprint-1

---

## LUN-006 — Dockerize + Deploy to Cloud Run (us-central1)

- Write Dockerfile for Next.js
- Deploy to Cloud Run free tier region (us-central1 only)
- Verify app accessible via Cloud Run URL

**Acceptance**: App live on Cloud Run URL. Free tier confirmed.
**Labels**: setup, devops, sprint-1
