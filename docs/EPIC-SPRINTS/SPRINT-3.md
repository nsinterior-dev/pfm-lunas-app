# Sprint 3 — Dashboard + CRUD

**Dates**: May 12 – May 23, 2026
**MVP**: 1 — Foundation
**Goal**: User can view, edit, add data from their Sheet inside the app.

---

## LUN-012 — Dashboard layout

- Main layout with sidebar + content area
- Sections: Debts, Savings, Needs, Wants, Investments
- Responsive, shadcn/ui components

**Acceptance**: Layout renders across screen sizes

---

## LUN-013 — Read and display Sheet data

- Fetch data from connected Sheet
- Display per category in table/card format
- Handle empty state

**Acceptance**: Real Sheet data visible in dashboard

---

## LUN-014 — Edit cell inline

- Click cell to edit
- Save on blur or Enter key
- Writes back to Google Sheet via API

**Acceptance**: Edit reflects in app AND in Google Sheet

---

## LUN-015 — Add new row

- "Add entry" button per category
- Form fields based on category columns
- Appends row to Sheet

**Acceptance**: New row appears in app and Sheet

---

## LUN-016 — Add new column

- "Add column" option per sheet section
- User names the column
- Column added to Sheet

**Acceptance**: New column visible in app and Sheet

---

## LUN-017 — Loading + error states

- Skeleton loaders for data fetch
- Error toast for failed API calls
- Retry button

**Acceptance**: No blank screens, user always knows what's happening
