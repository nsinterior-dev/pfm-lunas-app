# MVP 2 — AI Analysis

**Theme**: "Can the app understand my situation?"

**Goal**: Integrate Claude and Gemini for on-demand financial analysis.

**Sprints**: [Sprint 5](../EPIC-SPRINTS/SPRINT-5.md) (+ continuation in Q3)

## Features

- Claude integration — deep reasoning, long-context financial analysis
- Gemini integration — faster, Google-ecosystem-native
- Model selector — user chooses, or we route by task
- Analyze button — manual trigger only (cost control)
- Prompt suggestions (pre-built, one-tap):
  - "Why is my savings not growing?"
  - "How do I pay off my debts faster?"
  - "I earn well but I'm always broke — what's wrong?"
  - "What should I prioritize this month?"
  - "How long until I'm debt-free at this rate?"
  - "Where is most of my money going?"
- Custom prompt input
- Action-first responses — AI always ends with a concrete next step
- Privacy disclosure UI — clear messaging on what data is sent

## AI Routing (Tentative)

| Task | Model |
|------|-------|
| Quick calculations / summaries | Gemini |
| Deep debt strategy / behavioral analysis | Claude |
| User override | Always honored |

## Success Criteria

- AI response in under 10 seconds for typical data sizes
- Every response includes at least one actionable next step
- Meaningful analysis achievable in one tap
