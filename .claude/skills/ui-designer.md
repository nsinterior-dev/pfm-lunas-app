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
- `docs/DESIGN.md` — Design system, principles, Figma workflow
- `docs/ARCHITECTURE.md` — Component structure
- `CLAUDE.md` — Project conventions

## Design System

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
When making color, spacing, or typography choices:

**Colors** — Use Tailwind's semantic color system via `tailwind.config.ts`:
- Primary actions: `primary`
- Destructive actions: `destructive`
- Muted/secondary: `muted`, `secondary`
- Backgrounds: `background`, `card`
- Borders: `border`
- Text: `foreground`, `muted-foreground`

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
