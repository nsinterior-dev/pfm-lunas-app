# Skill: Frontend Developer

> Invoke with `/frontend` for feature implementation, bug fixes, and refactoring in the Next.js frontend.

## When to Use

- Implementing new pages or components
- Building features from sprint tickets (LUN-xxx)
- Fixing UI bugs
- Refactoring existing frontend code

## Context

Read these before starting:
- `CLAUDE.md` — Project conventions
- `docs/ARCHITECTURE.md` — Tech stack and repo structure
- `docs/DESIGN.md` — Design system and component library
- Relevant sprint ticket in `docs/EPIC-SPRINTS/`

## Tech Stack

- **Next.js 14** (App Router) + **TypeScript** (strict mode)
- **TanStack React Query** for all data fetching (`useQuery`, `useMutation`)
- **Zod** for validation (server request/response contracts)
- **Tailwind CSS** for styling
- **shadcn/ui** for components (source in `/components/ui/`)
- **Storybook** for component documentation

## Workflow

### 1. Research
- Read the ticket requirements
- Search existing components in `/components/ui/` before creating new ones
- Check if similar patterns exist in `/app/` or `/lib/`

### 2. Plan
- Identify files to create/modify
- Present plan to user with file list and approach
- Wait for approval before writing code

### 3. Implement
- Use App Router conventions (`page.tsx`, `layout.tsx`, `loading.tsx`, `error.tsx`)
- Use shadcn/ui components — never build from scratch what shadcn/ui provides
- All styling via Tailwind CSS — no inline styles, no CSS modules
- TypeScript strict — no `any`, define proper interfaces
- Descriptive variable names — no single-letter names

### 4. Verify
- Run `npm run lint` to check for errors
- Run `npm run build` to verify no type errors
- Test in browser with `npm run dev`

## Rules

### Do
- Use shadcn/ui components from `/components/ui/`
- Use Tailwind CSS utility classes for all styling
- Use React Query (`useQuery`, `useMutation`) for all data fetching
- Follow clean architecture layers: `presentation/` -> `application/` -> `data/` -> `model/`
- Create Storybook stories for new components (light + dark mode)
- Handle loading, error, and empty states
- Use `"use client"` directive only when needed (prefer Server Components)
- Keep pages thin — logic in `application/`, UI in `presentation/`

### Don't
- Use `any` type — define TypeScript interfaces
- Use `useEffect` for data fetching — use React Query instead
- Use inline styles or CSS modules
- Call APIs directly from presentation components — go through `application/` layer
- Import from `server/` in client code — ever
- Create components that shadcn/ui already provides (Button, Card, Input, Table, Dialog, Badge, Skeleton)
- Put API keys or secrets in client-side code
- Skip loading/error states

## File Conventions

```
features/
└── [feature]/
    ├── application/        # Use cases, business logic (no UI, no React)
    ├── data/               # Repositories (talks to APIs, Firestore)
    ├── model/              # Domain types and entities
    └── presentation/       # Components, hooks, pages
        ├── FeatureComponent.tsx
        └── useFeatureHook.ts

components/
├── ui/                     # shadcn/ui base components (don't modify directly)
├── dashboard/              # Feature-specific components
│   ├── summary-cards.tsx
│   └── category-table.tsx
lib/
├── sheets.ts               # Google Sheets client
├── claude.ts               # Claude API client
├── gemini.ts               # Gemini API client
└── firestore.ts            # Firestore client
```

## Checklist Before Done

- [ ] TypeScript compiles with no errors
- [ ] No `any` types
- [ ] All styling uses Tailwind CSS
- [ ] shadcn/ui components used where applicable
- [ ] Loading and error states handled
- [ ] Storybook story created for new components
- [ ] `npm run lint` passes
- [ ] `npm run build` passes
