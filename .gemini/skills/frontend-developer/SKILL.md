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
- `docs/ARCHITECTURE.md` — Tech stack and repo structure
- `docs/DESIGN.md` — Design system and component library
- Relevant sprint ticket in `docs/EPIC-SPRINTS/`

## Tech Stack

- **Next.js 14** (App Router) + **TypeScript** (strict mode)
- **TanStack React Query** (v5) for data fetching and state management
- **Tailwind CSS** for styling
- **shadcn/ui** for components (source in `/components/ui/`)
- **Storybook** for component documentation

## Workflow

### 1. Research
- Read the ticket requirements
- Search existing components in `/components/ui/` or `features/[feature]/presentation/`
- Check if similar patterns exist in other features

### 2. Plan
- Identify feature-first layers to create/modify:
    - `presentation/`: UI components and hooks
    - `application/`: Use cases and business logic
    - `data/`: Repositories and data sources
    - `model/`: Domain models and entities
- Define React Query keys and fetchers
- Present plan to user with file list and approach
- Wait for approval before writing code

### 3. Implement
- Use App Router conventions (`page.tsx`, `layout.tsx`, `loading.tsx`, `error.tsx`)
- **Use TanStack React Query (`useQuery`, `useMutation`) for all data operations**
- Follow **Feature-First Clean Architecture** in `features/[feature]/`
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
- **Use `useQuery` and `useMutation` for data fetching**
- Create Storybook stories for new components
- Handle loading, error, and empty states
- Use `"use client"` directive only when needed (prefer Server Components)
- Keep components focused on presentation — logic in `application/` or hooks

### Don't
- **Use `useEffect` for data fetching — use React Query instead**
- Use `any` type — define TypeScript interfaces
- Use inline styles or CSS modules
- Create components that shadcn/ui already provides (Button, Card, Input, Table, Dialog, Badge)
- Put API keys or secrets in client-side code
- Skip loading/error states

## File Conventions (Feature-First)

```
features/
└── [feature]/
    ├── presentation/           # UI components, pages, hooks
    │   ├── components/
    │   └── hooks/              # useQuery/useMutation wrappers
    ├── application/            # Use cases (orchestration)
    ├── data/                   # Repositories (API calls)
    └── model/                  # Domain models/types

components/
└── ui/                         # shadcn/ui base components
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
