# Storybook — Component Library

> Source: [Notion — Storybook](https://www.notion.so/33f85edeff7581869dc9c520ded81844)
> Storybook is the source of truth for all UI components in Lunas.
> Design in Figma -> Build in code -> Document in Storybook -> Use in app.

## Why Storybook

- Components built and tested **in isolation** — no full app needed
- Visual documentation for every state (loading, empty, error, filled)
- Pairs directly with shadcn/ui — each component gets a story
- Future-proofs MVP 4 onboarding — new devs learn from Storybook
- Catches design regressions — token changes show in every story

## Setup

```bash
npx storybook@latest init
npm run storybook        # http://localhost:6006
```

### Required Addons

| Addon | Purpose |
|-------|---------|
| `@storybook/addon-a11y` | Accessibility checks per component |
| `@storybook/addon-themes` | Toggle light / dark mode |
| `@storybook/addon-docs` | Auto-generates docs from props + JSDoc |
| `@chromatic-com/storybook` | Visual regression testing (later, optional) |

---

## Component Plan

### Phase 1 — Base (Sprint 1, LUN-002 + LUN-003)

shadcn/ui primitives. Each gets a story with all variants.

| Component | States | Notes |
|-----------|--------|-------|
| `Button` | default, hover, loading, disabled | Primary (teal), secondary (purple), ghost |
| `Input` | empty, filled, error, disabled | With label + helper text |
| `Card` | default, with header, with footer | Used everywhere in dashboard |
| `Badge` | debt, savings, investments, wants, needs | Each category color |
| `Skeleton` | single line, card, table row | Loading states |
| `Dialog` | open, with form, confirm/cancel | Add entry, edit cell |
| `Table` | empty, with data, loading | Core of the dashboard |

### Phase 2 — Feature Components (Sprint 3, MVP 1)

Custom components built on shadcn primitives.

| Component | Feature | States |
|-----------|---------|--------|
| `CategoryBadge` | Dashboard | All 5 categories |
| `DataRow` | Dashboard | View, edit, saving, error |
| `SummaryCard` | Dashboard | With value, loading, zero state |
| `SheetConnector` | Sheets | Idle, connecting, connected, error |
| `DataSourceToggle` | Sheets | Google Sheet active, App-managed (disabled) |

### Phase 3 — AI Components (Sprint 5, MVP 2)

| Component | Feature | States |
|-----------|---------|--------|
| `AnalysisPanel` | AI | Empty, loading, result, error |
| `PromptChip` | AI | Default, hover, selected |
| `ModelSelector` | AI | Claude, Gemini, Auto |
| `AnalysisHistoryItem` | AI | With timestamp, model badge |

---

## Story Convention

```typescript
import type { Meta, StoryObj } from '@storybook/react'
import { Button } from '@/components/ui/button'

const meta: Meta<typeof Button> = {
  title: 'Base/Button',
  component: Button,
  tags: ['autodocs'],
  argTypes: {
    variant: {
      control: 'select',
      options: ['default', 'secondary', 'ghost', 'destructive'],
    },
  },
}

export default meta
type Story = StoryObj<typeof Button>

export const Primary: Story = {
  args: { children: 'Analyze finances', variant: 'default' },
}

export const Loading: Story = {
  args: { children: 'Analyzing...', disabled: true },
}

export const Destructive: Story = {
  args: { children: 'Remove debt entry', variant: 'destructive' },
}
```

### Title Naming

```
Base/Button
Base/Input
Base/Card
Feature/CategoryBadge
Feature/SummaryCard
Feature/SheetConnector
AI/AnalysisPanel
AI/PromptChip
AI/ModelSelector
```

---

## Design Token Stories

Storybook also documents design tokens — not just components.

Create a `Design Tokens` section with stories for:
- **Colors** — all palette swatches (primary, accent, semantic, neutrals)
- **Typography** — all type scales rendered
- **Spacing** — spacing scale visualized

---

## Light / Dark Mode

Using `@storybook/addon-themes`, every story renders in both modes.

```typescript
// .storybook/preview.ts
import { withThemeByClassName } from '@storybook/addon-themes'

export const decorators = [
  withThemeByClassName({
    themes: { light: 'light', dark: 'dark' },
    defaultTheme: 'light',
  }),
]
```

**Rule**: No component ships unless it passes visual review in both light and dark mode in Storybook.

---

## File Structure

```
stories/
├── base/
│   ├── Button.stories.tsx
│   ├── Input.stories.tsx
│   ├── Card.stories.tsx
│   ├── Badge.stories.tsx
│   └── ...
├── feature/
│   ├── CategoryBadge.stories.tsx
│   ├── SummaryCard.stories.tsx
│   └── ...
├── ai/
│   ├── AnalysisPanel.stories.tsx
│   ├── PromptChip.stories.tsx
│   └── ...
└── tokens/
    ├── Colors.stories.tsx
    ├── Typography.stories.tsx
    └── Spacing.stories.tsx
```

## Workflow

```
Figma design ready
    -> Build component in isolation (Storybook)
    -> Write stories for all states
    -> Check light + dark mode
    -> Check a11y tab — no critical violations
    -> Integrate into feature presentation/ layer
    -> PR -> merge
```
