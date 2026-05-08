---
name: ui-designer
description: Visual and UI design for Lunas (pfm-lunas-app). Use for translating Figma designs to code, making visual design decisions, layout composition, and refining shadcn/ui component variants with Tailwind CSS.
---

# Skill: UI Designer

> Invoke with `/ui-designer` for visual design decisions, component styling, layout composition, and design-to-code translation.

## When to Use

- Translating Figma designs to code
- Making visual design decisions (colors, spacing, typography)
- Composing layouts and component arrangements
- Creating or refining shadcn/ui component variants
- Reviewing visual consistency across screens

## Context

Read these before starting:
- `docs/DESIGN-TOKENS.md` — Brand colors (Teal/Purple), typography, and semantic tokens
- `docs/DESIGN.md` — Design system, principles, Figma workflow
- `docs/ARCHITECTURE.md` — Component structure
- `CLAUDE.md` — Project conventions

## Design System

- **Brand Colors**: Teal (Primary/Growth) and Purple (AI/Accent)
- **Component library**: shadcn/ui (Tailwind-native, Radix UI primitives)
- **Styling**: Tailwind CSS only — no inline styles, no CSS modules
- **Design tool**: Figma + shadcn/ui Figma Kit
- **Documentation**: Storybook

## Design Principles

1. **No judgment** — UI should feel supportive, not critical of financial habits
2. **Visual polish** — Prioritize clean, polished UI over feature quantity
3. **Plain language** — No jargon; financial terms get glossary links (MVP 3)
4. **Action-first** — Every screen guides the user toward a next step
5. **Responsive** — Desktop-first for MVP 1, scales down gracefully

## Workflow

### 1. Understand the Screen
- What is the user trying to do on this screen?
- What data is being displayed or collected?
- What actions are available?

### 2. Component Selection
- Check shadcn/ui for existing components first: Button, Card, Input, Table, Dialog, Badge, etc.
- Compose complex UI from shadcn/ui primitives — don't build from scratch
- If shadcn/ui doesn't cover it, extend with Tailwind

### 3. Layout Composition
- Use Tailwind's flexbox/grid utilities for layout
- Follow consistent spacing (Tailwind spacing scale: `p-4`, `gap-6`, etc.)
- Sidebar + content area pattern for dashboard (see LUN-012)
- Card-based sections for data categories

### 4. Visual Decisions
When making color, spacing, or typography choices, always reference `docs/DESIGN-TOKENS.md`.

**Colors** — Use Tailwind's semantic color system mapped in `tailwind.config.ts`:
- **Primary (Teal)**: `#1D9E75`. Used for main actions and growth.
- **Accent (Purple)**: `#7F77DD`. Used for AI features (Claude/Gemini).
- **Financial Semantics**:
    - Debt: Red (`#E24B4A`)
    - Savings: Teal (`#1D9E75`)
    - Investments: Purple (`#7F77DD`)
    - Wants: Amber (`#BA7517`)
    - Needs: Gray (`#888780`)
- **Neutral Backgrounds**: Warmer neutrals (`#F8F7F4`) to feel human.

Use Tailwind semantic tokens for general UI: `primary`, `destructive`, `muted`, `secondary`, `background`, `card`, `border`, `foreground`, `muted-foreground`.

**Typography** — Use Tailwind text utilities:
- Page titles: `text-2xl font-bold`
- Section headers: `text-lg font-semibold`
- Body: `text-sm` or `text-base`
- Muted/helper text: `text-sm text-muted-foreground`

**Spacing** — Consistent Tailwind scale:
- Section padding: `p-6`
- Card padding: `p-4`
- Element gaps: `gap-4` or `gap-6`
- Between sections: `space-y-6`

### 5. States
Every component must account for:
- **Default** — Normal state
- **Loading** — Skeleton or spinner
- **Empty** — Helpful message + action
- **Error** — Clear message + retry
- **Hover/Focus** — Accessible interaction feedback

## Tone Guidelines for Finance UI

| Scenario | Tone | Example |
|----------|------|---------|
| User has high debt | Supportive, not alarming | "Let's work on a plan" not "Warning: high debt!" |
| Empty dashboard | Encouraging | "Connect your sheet to get started" |
| Successful edit | Confirming | "Saved" (subtle toast) |
| Error | Helpful | "Couldn't reach Google Sheets. Try again?" |
| AI analysis | Clear, actionable | Always ends with a next step |

## Storybook Stories

For every new component, create a Storybook story showing:
- Default state
- With data
- Empty state
- Loading state
- Error state
- Responsive variants (if applicable)

## Checklist Before Done

- [ ] Uses shadcn/ui components where applicable
- [ ] All styling via Tailwind CSS
- [ ] Loading, empty, and error states designed
- [ ] Consistent spacing and typography
- [ ] Responsive layout (desktop-first)
- [ ] Storybook story created
- [ ] Tone is supportive, not judgmental
