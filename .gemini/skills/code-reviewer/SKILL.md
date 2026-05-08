---
name: code-reviewer
description: Code review and quality assurance for Lunas (pfm-lunas-app). Use for reviewing TypeScript code, Next.js patterns, security practices, AI integration rules, and project convention alignment.
---

# Skill: Code Reviewer

> Invoke with `/code-review` to review code for quality, conventions, security, and project alignment.

## When to Use

- After completing a feature or ticket
- Before merging a PR
- When refactoring existing code
- Periodic codebase health check

## Context

Read these before reviewing:
- `CLAUDE.md` — Project conventions and rules
- `docs/ARCHITECTURE.md` — Tech stack and architecture decisions
- `docs/DESIGN.md` — Design system conventions

## Review Checklist

### 1. TypeScript
- [ ] No `any` types — all values properly typed
- [ ] Interfaces defined for props, API responses, and data shapes
- [ ] Strict mode compliance (no implicit any, no unused vars)
- [ ] Descriptive variable names — no single-letter names

### 2. Clean Architecture Layers
- [ ] Client feature code in `client/features/[name]/{application,model,presentation}`
- [ ] Presentation components never call APIs directly — use hooks from `application/`
- [ ] `application/` hooks call `client/lib/server/features/[feature]/service.ts` for API access
- [ ] No imports from `server/` in client code — ever
- [ ] Server feature code in `server/features/[name]/{model,service,repository,parser}`
- [ ] Services call `repository/`, never `server/lib/` directly
- [ ] Repositories return typed domain models, never raw API responses
- [ ] If data source changed, would zero UI components break?

### 3. Data Fetching & State
- [ ] React Query (`useQuery`, `useMutation`) used for all data fetching
- [ ] No `useEffect` for API calls — use React Query
- [ ] No `useState` + `useEffect` + `fetch` pattern
- [ ] `useEffect` only for subscriptions with cleanup or imperative DOM ops

### 4. Next.js Conventions
- [ ] App Router patterns used correctly (`page.tsx`, `layout.tsx`, `route.ts`)
- [ ] Server Components by default — `"use client"` only when needed
- [ ] Loading (`loading.tsx`) and error (`error.tsx`) boundaries present

### 5. Components & Styling
- [ ] shadcn/ui components used — no recreating Button, Card, Input, Table, Dialog, Badge, Skeleton
- [ ] All styling via Tailwind CSS — no inline styles, no CSS modules, no `style` props
- [ ] Components in correct location (`client/components/ui/` for shared, `client/features/*/presentation/` for specific)
- [ ] Storybook story exists for new components (light + dark mode)
- [ ] Lunas color tokens used (teal primary, purple accent) — no hardcoded hex

### 6. API Routes & Backend
- [ ] Route handlers are thin — validate, delegate to service, respond
- [ ] No business logic in route handlers — all in `service/`
- [ ] Services call `repository/`, never `server/lib/` directly
- [ ] Repositories return typed domain models, never raw API responses
- [ ] Request/response validated with Zod schemas (`server/features/*/model/`)
- [ ] Consistent response shape: `{ data }` or `{ error, code }`
- [ ] Errors use `AppError` subclasses — never raw throws
- [ ] No secrets or API keys in client-side code
- [ ] Rate limits considered for external APIs

### 7. Security
- [ ] No API keys, secrets, or tokens in client bundles
- [ ] No `dangerouslySetInnerHTML` without sanitization
- [ ] OAuth tokens stored securely (NextAuth.js session)
- [ ] Input validation on all API routes
- [ ] No hardcoded credentials anywhere
- [ ] `.env.local` is gitignored

### 8. AI Integration
- [ ] AI calls are server-side only (API routes)
- [ ] Manual trigger — no auto-analysis
- [ ] Privacy disclosure included in AI response flow
- [ ] Token usage tracked for cost control
- [ ] Responses include at least one actionable next step

### 9. Performance
- [ ] No unnecessary re-renders (proper dependency arrays)
- [ ] Images optimized with `next/image`
- [ ] No blocking API calls in render path
- [ ] Large data sets paginated or virtualized
- [ ] Bundle size reasonable — no unnecessary dependencies

### 10. Code Quality
- [ ] No dead code or commented-out blocks
- [ ] No TODO comments without a ticket reference
- [ ] Functions are focused — single responsibility
- [ ] No deeply nested conditionals (max 2-3 levels)
- [ ] Consistent naming conventions across the codebase

## Review Process

### Step 1: Identify Changed Files
```bash
git diff --name-only main...HEAD
```

### Step 2: Read Each File
For each changed file:
1. Read the full file
2. Check against the relevant checklist sections above
3. Note issues with severity

### Step 3: Categorize Issues

| Severity | Action | Examples |
|----------|--------|---------|
| **Blocker** | Must fix before merge | Security vuln, leaked secret, broken build |
| **Major** | Should fix before merge | `any` types, missing error handling, wrong patterns |
| **Minor** | Fix in follow-up | Naming improvements, minor refactors |
| **Nit** | Optional | Style preferences, alternative approaches |

### Step 4: Deliver Review

Format each finding as:
```
**[Severity]** file.ts:L42 — Description
Problem: What's wrong
Fix: What to do instead
```

### Step 5: Summary
End with:
- Total issues by severity
- Overall assessment (approve / request changes)
- Positive callouts — what was done well

## Common Issues to Watch For

| Issue | Why It Matters |
|-------|---------------|
| `useEffect` for data fetching | Must use React Query — no exceptions |
| `fetch()` in presentation layer | Presentation never calls APIs directly |
| Import from `server/` in client | Architecture violation — layers must not cross |
| Missing Zod validation | Server contracts must be validated at runtime |
| Business logic in route handler | Route handlers must be thin — delegate to service |
| Service calling `server/lib/` directly | Services must go through `repository/` layer |
| Repository returning raw API response | Must map to typed domain models |
| Raw `throw new Error()` in server | Use `AppError` subclasses with status codes |
| `any` type usage | Defeats TypeScript's purpose, hides bugs |
| Inline styles or `style={}` | Project uses Tailwind only |
| Hardcoded hex colors | Use Lunas CSS variables from design tokens |
| Client-side API keys | Security vulnerability |
| Missing loading/error states | Poor UX, blank screens |
| AI auto-trigger | Cost explosion risk |
| Missing input validation | Security + data integrity |
| `console.log` left in | Clean up before merge |
| Unused imports | Build warnings, code noise |

## Checklist Before Approving

- [ ] All Blocker issues resolved
- [ ] All Major issues resolved or tracked as tickets
- [ ] `npm run lint` passes
- [ ] `npm run build` passes
- [ ] No secrets in committed code
- [ ] New components have Storybook stories
