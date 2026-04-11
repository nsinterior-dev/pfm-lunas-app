# Design Tokens — Colors & Typography

> Status: Decided — April 2026
> Source: [Notion — Design System](https://www.notion.so/33f85edeff758182bcfacf5824c59096)
> Figma: [Lunas Design File](https://www.figma.com/design/gynStkNiTn5wc1yvQwoJxO/Lunas?node-id=0-1)

## Design Principles

- **Trustworthy** — not alarming, not clinical
- **Manageable** — clear hierarchy, nothing overwhelming
- **Modern** — distinct from typical fintech blue
- **Both modes** — light and dark via system preference

---

## Brand Colors

### Primary — Teal

Not blue (every bank uses it), not green (too obvious for money). Teal bridges trust + growth. Distinct in fintech.

| Stop | Hex | Usage |
|------|-----|-------|
| 50 | `#E1F5EE` | Backgrounds, tags |
| 200 | `#5DCAA5` | Hover states (dark mode) |
| 400 | `#1D9E75` | Primary action, CTA buttons |
| 600 | `#0F6E56` | Hover (light mode) |
| 800 | `#085041` | Text on light teal backgrounds |

### Accent — Purple / Indigo

Pairs with teal without competing. Used specifically for AI features (Claude analysis, prompt suggestions) — gives the AI layer its own visual identity.

| Stop | Hex | Usage |
|------|-----|-------|
| 50 | `#EEEDFE` | AI feature backgrounds |
| 200 | `#AFA9EC` | Subtle AI highlights |
| 400 | `#7F77DD` | AI accent, secondary actions |
| 600 | `#534AB7` | AI borders |
| 800 | `#3C3489` | AI text on light backgrounds |

---

## Financial Category Semantics

| Category | Color | Light bg | Action | Reason |
|----------|-------|----------|--------|--------|
| Debt | Red | `#FCEBEB` | `#E24B4A` | Universal danger signal |
| Savings | Teal | `#E1F5EE` | `#1D9E75` | Same as brand — intentional |
| Investments | Purple | `#EEEDFE` | `#7F77DD` | Premium, growth |
| Wants | Amber | `#FAEEDA` | `#BA7517` | Caution / discretionary |
| Needs | Gray | `#F1EFE8` | `#888780` | Neutral, essential |

---

## Neutral Backgrounds

Slightly warm whites and blacks — not pure `#fff` or `#000`. Warmer neutrals feel human, not clinical.

| Token | Light | Dark |
|-------|-------|------|
| Page background | `#F8F7F4` | `#111210` |
| Surface / card | `#FFFFFF` | `#1A1A16` |
| Border | `#D3D1C7` | `#2C2C2A` |
| Text primary | `#0E0E0C` | `#F1EFE8` |
| Text secondary | `#5F5E5A` | `#888780` |
| Text muted | `#888780` | `#B4B2A9` |

---

## shadcn/ui CSS Variable Mapping

```css
/* Light mode */
:root {
  --background: #F8F7F4;
  --foreground: #0E0E0C;
  --card: #FFFFFF;
  --primary: #1D9E75;
  --primary-foreground: #E1F5EE;
  --secondary: #EEEDFE;
  --secondary-foreground: #3C3489;
  --muted: #F1EFE8;
  --muted-foreground: #888780;
  --accent: #7F77DD;
  --accent-foreground: #EEEDFE;
  --border: #D3D1C7;
  --destructive: #E24B4A;
  --destructive-foreground: #FCEBEB;
  --ring: #1D9E75;
}

/* Dark mode */
.dark {
  --background: #111210;
  --foreground: #F1EFE8;
  --card: #1A1A16;
  --primary: #5DCAA5;
  --primary-foreground: #04342C;
  --secondary: #26215C;
  --secondary-foreground: #CECBF6;
  --muted: #2C2C2A;
  --muted-foreground: #B4B2A9;
  --accent: #7F77DD;
  --accent-foreground: #EEEDFE;
  --border: #2C2C2A;
  --destructive: #F09595;
  --destructive-foreground: #501313;
  --ring: #5DCAA5;
}
```

---

## Typography

| Role | Font | Size | Weight |
|------|------|------|--------|
| Display / hero | Inter or system-ui | 32-40px | 500 |
| Heading H1 | Inter | 24px | 500 |
| Heading H2 | Inter | 18px | 500 |
| Body | Inter | 15px | 400 |
| Label / caption | Inter | 12-13px | 400-500 |
| Mono (code, hex) | JetBrains Mono | 13px | 400 |

> Typography to be finalized in Figma. Inter is the working assumption.
