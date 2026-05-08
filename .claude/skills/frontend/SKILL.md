---
name: frontend-developer
description: Frontend development for Lunas (pfm-lunas-app). Use for Next.js App Router features, TypeScript implementation, Tailwind CSS styling, shadcn/ui components, and Storybook documentation.
---

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
- `docs/ARCHITECTURE.md` — Tech stack, `client/` + `server/` + `app/` structure
- `docs/DESIGN.md` — Design system and component library
- Relevant sprint ticket in `docs/EPIC-SPRINTS/`

## Tech Stack

- **Next.js 14** (App Router) + **TypeScript** (strict mode)
- **TanStack React Query** for all data fetching (`useQuery`, `useMutation`)
- **Zod** for validation (server request/response contracts)
- **Tailwind CSS** for styling
- **shadcn/ui** for components (source in `client/components/ui/`)
- **Storybook** for component documentation

## Workflow

### 1. Research
- Read the ticket requirements
- Search existing components in `client/components/ui/` before creating new ones
- Check if similar patterns exist in `client/features/` or `client/lib/server/`

### 2. Plan
- Identify files to create/modify
- Present plan to user with file list and approach
- Wait for approval before writing code

### 3. Implement
- Pages in `app/(pages)/` — thin, import from `client/features/`
- UI components in `client/features/[feature]/presentation/`
- Hooks in `client/features/[feature]/application/`
- API client in `client/lib/server/features/[feature]/service.ts`
- Use shadcn/ui components — never build from scratch what shadcn/ui provides
- All styling via Tailwind CSS — no inline styles, no CSS modules
- TypeScript strict — no `any`, define proper interfaces

### 4. Verify
- Run `npm run lint` to check for errors
- Run `npm run build` to verify no type errors
- Test in browser with `npm run dev`

## Rules

### Do
- Use shadcn/ui components from `client/components/ui/`
- Use Tailwind CSS utility classes for all styling
- Use React Query (`useQuery`, `useMutation`) for all data fetching
- Follow client layers: `presentation/` -> `application/` -> `client/lib/server/`
- Create Storybook stories for new components (light + dark mode)
- Handle loading, error, and empty states
- Use `"use client"` directive only when needed (prefer Server Components)
- Keep pages thin — logic in `application/`, UI in `presentation/`

### Don't
- Use `any` type — define TypeScript interfaces
- Use `useEffect` for data fetching — use React Query instead
- Use inline styles or CSS modules
- Call APIs directly from presentation — go through `application/` hooks
- Import from `server/` in client code — ever
- Import from `server/lib/` in client code — those are server-side SDK clients
- Create components that shadcn/ui already provides (Button, Card, Input, Table, Dialog, Badge, Skeleton)
- Put API keys or secrets in client-side code
- Skip loading/error states

## File Conventions

```
client/
├── components/
│   └── ui/                              # shadcn/ui base components
├── features/
│   └── [feature]/
│       ├── application/                 # Hooks (React Query + regular), use cases
│       │   ├── useFeatureData.ts        # React Query hook
│       │   └── useFeatureState.ts       # Client-only state hook
│       ├── model/                       # Domain types and entities
│       │   └── featureTypes.ts
│       └── presentation/               # UI components
│           ├── FeatureComponent.tsx
│           └── FeatureList.tsx
├── lib/
│   └── server/                          # API client layer
│       ├── features/
│       │   └── [feature]/
│       │       ├── service.ts           # API calls → /api/[feature]
│       │       └── types/index.ts       # Request/response types
│       ├── helpers/
│       │   └── apiRequest.ts            # Generic HTTP helper
│       └── axios/                       # Axios instance
├── middleware/                           # Next.js middleware
└── stories/                             # Storybook stories

app/
├── (pages)/
│   └── [page]/page.tsx                  # Thin — imports from client/features/
├── api/                                 # Thin route handlers → server/features/
└── layout.tsx                           # Root layout (providers)
```

## Checklist Before Done

- [ ] TypeScript compiles with no errors
- [ ] No `any` types
- [ ] All styling uses Tailwind CSS
- [ ] shadcn/ui components used where applicable
- [ ] Loading and error states handled
- [ ] Storybook story created for new components
- [ ] No imports from `server/` in client code
- [ ] API calls go through `client/lib/server/` — not direct `fetch()`
- [ ] `npm run lint` passes
- [ ] `npm run build` passes
