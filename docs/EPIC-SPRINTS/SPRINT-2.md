# Sprint 2 — Google Auth + Sheets Connect

**Dates**: Apr 28 – May 9, 2026
**MVP**: 1 — Foundation
**Goal**: User can sign in with Google and connect or create a Google Sheet.

---

## LUN-007 — Implement Google OAuth 2.0

- Install NextAuth.js
- Configure Google provider with Sheets API scope
- Store session + refresh token

**Acceptance**: Sign in with Google works, session persists

---

## LUN-008 — Build Connect Existing Sheet flow

- Input for Google Sheet URL or ID
- Validate Sheet is accessible with user's token
- Save Sheet ID to Firestore user doc

**Acceptance**: User can connect their Sheet and see confirmation

---

## LUN-009 — Build Create New Sheet flow

- Create new Google Sheet via Sheets API
- Scaffold with Lunas template structure (categories pre-built)
- Save Sheet ID to Firestore

**Acceptance**: New Sheet created in user's Drive with correct structure

---

## LUN-010 — Google Sheets API client

- Create /lib/sheets.ts
- Implement: getSheetData, updateCell, appendRow, addColumn

**Acceptance**: All four methods tested and working

---

## LUN-011 — Data source toggle UI (placeholder)

- UI toggle: "My Google Sheet" vs "App-managed"
- Google Sheet mode active, App-managed shows "Coming soon"

**Acceptance**: Toggle renders, correct mode is saved to Firestore
