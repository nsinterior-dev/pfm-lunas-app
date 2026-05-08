# Sprint 4 — MVP 1 Complete + Deploy

**Dates**: May 26 – Jun 6, 2026
**MVP**: 1 — Foundation
**Goal**: MVP 1 is polished, stable, and properly deployed on Cloud Run.

---

## LUN-018 — Basic categorization system

- Category tags: Debt, Savings, Needs, Wants, Investments
- Auto-detect from Sheet column headers
- Manual override per row

**Acceptance**: All rows correctly categorized

---

## LUN-019 — Summary cards on dashboard

- Total debt, total savings, monthly expenses
- Calculated from Sheet data

**Acceptance**: Numbers match Sheet data

---

## LUN-020 — MVP 1 QA pass

- Test all flows: connect Sheet, create Sheet, edit, add row, add column
- Fix all critical bugs

**Acceptance**: All MVP 1 flows work without errors

---

## LUN-021 — Production Cloud Run deployment

- Set up CI/CD via GitHub Actions -> Cloud Run
- Connect Secret Manager to production environment
- Custom domain or clean Cloud Run URL

**Acceptance**: Production app live and stable

---

## LUN-022 — README + /docs update

- Write README with setup instructions
- Copy finalized docs from Notion to /docs in repo

**Acceptance**: Repo is self-documenting
