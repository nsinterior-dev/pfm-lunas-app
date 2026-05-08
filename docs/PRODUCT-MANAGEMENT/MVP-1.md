# MVP 1 — Foundation

**Theme**: "Can I see and manage my data?"

**Goal**: A working app where the user can connect or create a spreadsheet and manage financial data cleanly.

**Sprints**: [Sprint 1](../EPIC-SPRINTS/SPRINT-1.md), [Sprint 2](../EPIC-SPRINTS/SPRINT-2.md), [Sprint 3](../EPIC-SPRINTS/SPRINT-3.md), [Sprint 4](../EPIC-SPRINTS/SPRINT-4.md)

## Features

- Google OAuth — authenticate with Google account
- Connect existing Google Sheet — user provides Sheet ID or picks from Drive
- Create new Google Sheet — scaffolded with Lunas-recommended structure
- Dashboard view — read and display data (debts, savings, expenses, income)
- Edit data — update cell values from the app
- Add rows — append new entries per category
- Add columns — extend the sheet structure
- Basic categorization — Debt, Savings, Needs, Wants, Investments
- Data source toggle — UI placeholder for switching between Google Sheet and Firestore

## Success Criteria

- User can connect their Sheet and see data in under 2 minutes
- Edits in app are reflected in Google Sheets and vice versa
- No data loss on any read/write operation

## Not Included

- AI analysis
- Multi-user
- Financial literacy content
- Charts/projections
