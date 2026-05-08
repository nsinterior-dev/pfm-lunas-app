# Design System — Lunas

> Source: [Notion](https://www.notion.so/33f85edeff758163805ec368386dd03e)
> Figma: [Lunas Design File](https://www.figma.com/design/gynStkNiTn5wc1yvQwoJxO/Lunas?node-id=0-1)

## Related Docs

- **[DESIGN-TOKENS.md](DESIGN-TOKENS.md)** — Colors, CSS variables, typography, category semantics
- **[STORYBOOK.md](STORYBOOK.md)** — Component library plan, story conventions, addons

## Design Tools

| Tool | Purpose |
|------|---------|
| Figma | Design source of truth — all screens designed here before code |
| shadcn/ui Figma Kit | Pre-built component library matching shadcn/ui code components |
| Storybook | Component documentation and isolated testing |

## Design-First Workflow

```
Figma design ready
  -> Build component in isolation (Storybook)
  -> Write stories for all states
  -> Check light + dark mode
  -> Check a11y — no critical violations
  -> Integrate into feature presentation/ layer
  -> PR -> merge
```

## Design Principles

1. **Trustworthy** — not alarming, not clinical
2. **Manageable** — clear hierarchy, nothing overwhelming
3. **Modern** — distinct from typical fintech blue
4. **No judgment** — UI should feel supportive, not critical of financial habits
5. **Visual polish** — Prioritize clean, polished UI over feature quantity
6. **Plain language** — No jargon; financial terms get glossary links (MVP 3)
7. **Action-first** — Every screen guides the user toward a next step
8. **Responsive** — Desktop-first for MVP 1, scales down gracefully
9. **Both modes** — Light and dark via system preference

## Component Library: shadcn/ui

Components live in `/components/ui/`. You own the source code.

### Why shadcn/ui
- **Own the code** — No dependency lock-in
- **Tailwind-native** — Consistent with styling approach
- **Figma kit** — Design-to-code parity
- **Accessible** — Built on Radix UI primitives

### Base Components (Sprint 1)
Button, Card, Input, Table, Dialog, Badge, Skeleton

## Styling

- **Tailwind CSS** for all styling — no inline styles, no CSS modules
- Follow shadcn/ui conventions for component variants
- CSS variables for design tokens (see [DESIGN-TOKENS.md](DESIGN-TOKENS.md))
- **No component ships unless it passes visual review in both light and dark mode**

## Brand Identity

- **Primary**: Teal (`#1D9E75`) — trust + growth, distinct in fintech
- **Accent**: Purple (`#7F77DD`) — AI features get their own visual identity
- **Neutrals**: Warm whites/blacks — human, not clinical
- **Font**: Inter (body) + JetBrains Mono (code/numbers)

See [DESIGN-TOKENS.md](DESIGN-TOKENS.md) for the full color palette, CSS variables, and category semantics.
