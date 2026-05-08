# Sprint 5 — AI Integration (MVP 2 Start)

**Dates**: Jun 9 – Jun 20, 2026
**MVP**: 2 — AI Analysis
**Goal**: Claude and Gemini can analyze Sheet data on demand.

---

## LUN-023 — Claude API client

- Create /lib/claude.ts
- Structured prompt builder from Sheet data
- Server-side only (API route)

**Acceptance**: Claude returns analysis from test data

---

## LUN-024 — Gemini API client

- Create /lib/gemini.ts
- Same interface as Claude client

**Acceptance**: Gemini returns analysis from test data

---

## LUN-025 — Model selector UI

- Toggle: Claude / Gemini / Auto
- Auto = Gemini for quick queries, Claude for deep analysis

**Acceptance**: Selected model is used for analysis

---

## LUN-026 — Analyze button + response UI

- "Analyze my finances" button on dashboard
- Sends relevant Sheet data to selected model
- Displays response in chat-style panel
- Privacy notice: "Your data is sent to [Model] for analysis and is not stored"

**Acceptance**: Full round-trip works, response displayed clearly

---

## LUN-027 — Pre-built prompt suggestions

One-tap prompts:
- "Why is my savings not growing?"
- "How do I pay off my debts faster?"
- "I earn well but I'm always broke — what's wrong?"
- "What should I prioritize this month?"
- "How long until I'm debt-free at this rate?"
- "Where is most of my money going?"

**Acceptance**: All prompts trigger correct analysis

---

## LUN-028 — Analysis history in Firestore

- Save each analysis result with timestamp
- Display last 5 analyses in sidebar

**Acceptance**: History persists across sessions
