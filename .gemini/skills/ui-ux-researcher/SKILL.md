---
name: ui-ux-researcher
description: UI/UX research for Lunas (pfm-lunas-app). Use for competitive analysis, heuristic evaluation, accessibility audits, and design pattern research for financial applications.
---

# Skill: UI/UX Researcher

> Invoke with `/ui-ux-researcher` for competitive analysis, best practice research, usability heuristics evaluation, and design pattern recommendations.

## When to Use

- Researching how other finance apps solve a specific UX problem
- Evaluating design patterns for a feature (e.g., dashboard layout, data tables, AI chat)
- **Researching AI-driven financial insights and conversational AI patterns**
- **Analyzing best practices for data visualization in high-density dashboards**
- Conducting heuristic evaluation of existing screens
- Recommending accessibility improvements
- Analyzing best practices for financial app UX
- Making evidence-based design recommendations

## Context

Read these before starting:
- `docs/DESIGN.md` — Current design system and principles
- `docs/PRODUCT-MANAGEMENT/` — Product vision, target user, MVP scope
- Relevant sprint ticket in `docs/EPIC-SPRINTS/`

## Target User Profile

- Filipino professionals (25-40) with income but low financial clarity
- May have debt, inconsistent savings, or feel overwhelmed
- Comfortable with web apps but not necessarily finance tools
- Needs guidance, not just data display
- Values privacy — sensitive about sharing financial data with AI

## Workflow

### 1. Define the Research Question
Before researching, clearly state:
- What specific problem are we solving?
- What decision does this research inform?
- What are the constraints? (tech stack, MVP scope, timeline)

### 2. Competitive Analysis
When analyzing competing apps, evaluate:

**Direct competitors** (personal finance):
- Mint, YNAB, Goodbudget, Money Manager, Wallet by BudgetBakers
- PH-specific: GCash insights, Maya spending tracker, Tonik

**Indirect competitors** (data + AI patterns):
- Google Sheets (our data source)
- ChatGPT / Claude chat interfaces
- Notion AI, Copilot

For each, document:
| Aspect | What to Capture |
|--------|----------------|
| Onboarding | How many steps? Friction points? |
| Dashboard | Layout, information hierarchy, primary action |
| Data entry | Inline vs modal, validation UX |
| AI/insights | How are insights presented? Tone? Actionability? |
| Empty states | How do they handle first-time users? |
| Tone | Supportive? Neutral? Gamified? |

### 3. Heuristic Evaluation
Use Nielsen's 10 heuristics adapted for Lunas:

1. **Visibility of system status** — User always knows what's happening (loading, saving, syncing)
2. **Match between system and real world** — Financial terms in plain Filipino/English
3. **User control and freedom** — Undo, cancel, go back always available
4. **Consistency and standards** — shadcn/ui patterns used consistently
5. **Error prevention** — Validate before submit, confirm destructive actions
6. **Recognition rather than recall** — Pre-built prompts, category suggestions
7. **Flexibility and efficiency** — Keyboard shortcuts for power users (future)
8. **Aesthetic and minimalist design** — No clutter, financial data is already dense
9. **Help users recover from errors** — Clear error messages with next steps
10. **Help and documentation** — Glossary, tooltips for financial terms

### 4. Accessibility Audit
Check against WCAG 2.1 AA:
- Color contrast ratios (4.5:1 for text, 3:1 for large text)
- Keyboard navigation (all interactive elements focusable)
- Screen reader compatibility (proper ARIA labels)
- Focus indicators visible
- No information conveyed by color alone

### 5. Synthesize Recommendations
Format findings as:

```
## Finding: [What we observed]
**Evidence**: [Where this was seen — competitor, heuristic, user feedback]
**Impact**: [High / Medium / Low]
**Recommendation**: [Specific, actionable suggestion]
**Applies to**: [Which ticket or feature]
```

## Research Deliverables

| Deliverable | When |
|-------------|------|
| Competitive analysis table | Before designing a new feature |
| Heuristic evaluation | After first implementation of a screen |
| Accessibility audit | Before each MVP release |
| Pattern recommendation | When choosing between interaction approaches |
| Tone/copy review | When writing microcopy for finance-sensitive screens |

## Key Research Questions by Sprint

| Sprint | Research Questions |
|--------|-------------------|
| Sprint 1 | What do best-in-class finance app dashboards look like? |
| Sprint 2 | How do apps handle OAuth consent UX? Sheet connection flows? |
| Sprint 3 | **Best practices for data visualization in dashboards with high data density?** |
| Sprint 4 | **How to present complex financial data without overwhelming the user?** |
| Sprint 5 | **How do AI chat interfaces present financial insights and actionable next steps?** |

## Checklist Before Done

- [ ] Research question clearly stated
- [ ] At least 3 competitors/references analyzed
- [ ] Findings tied to specific Lunas features or tickets
- [ ] Recommendations are actionable (not just observations)
- [ ] Accessibility considerations included
- [ ] Tone sensitivity checked for financial context
