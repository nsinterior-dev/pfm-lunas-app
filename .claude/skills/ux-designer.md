# Skill: UX Designer

> Invoke with `/ux-designer` for user flow design, interaction patterns, information architecture, and usability decisions.

## When to Use

- Designing user flows (e.g., onboarding, connect Sheet, analyze finances)
- Making interaction decisions (e.g., inline edit vs modal, toast vs alert)
- Structuring information architecture (navigation, dashboard sections)
- Defining error handling UX and edge case behaviors
- Writing UI copy and microcopy

## Context

Read these before starting:
- `docs/DESIGN.md` — Design principles
- `docs/PRODUCT-MANAGEMENT/` — MVP features and success criteria
- Relevant sprint ticket in `docs/EPIC-SPRINTS/`

## Target User

- Filipino professionals with income but low financial clarity
- May have debt, inconsistent savings, or feel overwhelmed
- Not financially illiterate — just need structure and guidance
- Using this on desktop (MVP 1), potentially mobile later

## UX Principles

1. **No judgment** — Never make the user feel bad about their financial situation
2. **Action-first** — Every screen should answer "What do I do next?"
3. **Progressive disclosure** — Show what's needed now, reveal complexity gradually
4. **Reduce cognitive load** — Financial data is already stressful; simplify the interface
5. **One primary action per screen** — Clear hierarchy of what to do

## Workflow

### 1. Map the User Flow
Before any design or code, map the complete user journey:
```
Entry point → Steps → Decision points → Success state → Edge cases
```

Example (Connect Sheet flow):
```
Dashboard (empty) → "Connect Sheet" CTA → Input Sheet URL → Validate → Success → Dashboard (with data)
                                        → Invalid URL → Error message → Retry
                                        → No permission → Explain scopes → Retry
```

### 2. Define Interaction Patterns

| Pattern | When to Use | Lunas Convention |
|---------|-------------|-----------------|
| Inline edit | Quick single-value changes | Cell editing in dashboard (LUN-014) |
| Modal/Dialog | Multi-field forms, confirmations | Add row (LUN-015), add column (LUN-016) |
| Toast | Success/error feedback | After save, after API error |
| Skeleton loader | Data fetching | Dashboard load, Sheet data fetch |
| Empty state | No data yet | First-time user, empty category |
| Bottom sheet | Mobile secondary actions | Future (MVP 4+) |

### 3. Write Microcopy
All UI text follows these rules:
- **Plain language** — No financial jargon without explanation
- **Active voice** — "Connect your sheet" not "Sheet can be connected"
- **Supportive tone** — "Let's get started" not "You haven't set up anything"
- **Short** — Labels under 3 words, descriptions under 15 words
- **Actionable** — Buttons say what they do: "Connect Sheet", "Analyze", "Add Entry"

### 4. Handle Edge Cases

For every flow, define:
- **Happy path** — Everything works
- **Empty state** — No data, first-time user
- **Error state** — API fails, invalid input, network issue
- **Permission denied** — OAuth scope issues
- **Slow response** — AI taking > 5 seconds
- **Partial data** — Some Sheet columns missing or malformed

### 5. Navigation & Information Architecture

MVP 1 Dashboard structure:
```
Sidebar:
├── Dashboard (home)
├── Debts
├── Savings
├── Needs
├── Wants
├── Investments
└── Settings

Top bar:
├── Sheet name / connection status
├── Data source toggle
└── User avatar / sign out
```

## UX Patterns for Finance

| Scenario | Pattern | Reasoning |
|----------|---------|-----------|
| Showing debt totals | Card with trend indicator | Quick glance, no digging |
| Editing a cell | Click → inline input → blur to save | Fast, minimal friction |
| AI analysis | Button → loading → chat panel | Manual trigger, clear feedback |
| First-time user | Empty state → single CTA | Don't overwhelm with options |
| Destructive action | Confirmation dialog | Prevent accidental deletes |

## Output Format

When designing a flow, deliver:

1. **User flow diagram** (ASCII or description)
2. **Screen list** with primary action per screen
3. **Microcopy** for key UI elements (buttons, headers, empty states, errors)
4. **Edge cases** and how each is handled
5. **Open questions** for the user to decide

## Checklist Before Done

- [ ] User flow mapped (happy path + edge cases)
- [ ] Primary action per screen identified
- [ ] Empty, loading, and error states defined
- [ ] Microcopy written (buttons, headers, feedback messages)
- [ ] Tone is supportive and non-judgmental
- [ ] No dead ends — every state has a next step
- [ ] Interaction patterns consistent with project conventions
