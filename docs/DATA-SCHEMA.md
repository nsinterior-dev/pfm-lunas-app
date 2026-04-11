# Translation Layer & Data Schema — Lunas

> v1.1 — April 2026. Source: [Notion — Translation Layer & Data Schema](https://www.notion.so/33f85edeff758188a801e88ce8333086)

## Core Principle

**Google Sheets IS the database for financial data. Firestore is glue only.**

Firestore does NOT store transactions, balances, or budgets. All actual financial data stays in the user's Google Sheet. Firestore stores only: sheet mappings, analysis history, user session, and recurring budget items.

---

## The Problem

Every user's spreadsheet looks different. Lunas cannot hardcode column names or row positions. Instead it uses a **two-phase approach:**

1. **Detect** — understand the structure of the sheet (once, on connect)
2. **Translate** — read/write using the saved mapping (every time after)

---

## Two Types of Sheets

| Type | Example | Structure | Approach |
|------|---------|-----------|----------|
| **Linear** | Debt per card, Savings daily log | One table, consistent columns | Row-by-row with header mapping |
| **Multi-section** | Monthly Budget | Multiple tables in one sheet, side-by-side | Formatting anchors + section mapping |

---

## Path A — Linear Translation

Used for: **Debt Tracker (per card sheets), Savings Tracker (bank + UITF)**

### Flow

```
Google Sheets API → 2D array
        ↓
Find header row (first bold or colored row)
        ↓
Map headers → Lunas fields (Claude assists once, saved to Firestore)
        ↓
Read rows linearly
Skip: empty rows, TOTAL rows (formula cells), title rows
        ↓
Normalized Lunas data model
```

### Tricky Parts

- **Installment parsing** — `11/24` → `{ current: 11, total: 24 }` vs `BNPL` → different type
- **Skip formula/total rows** — detect via Sheets API `includeGridData` render mode
- **Metadata rows** — Credit Limit, Remaining (above the table) — parsed separately

### Output Model

```typescript
{
  account: "BDO CC",
  credit_limit: 100000,
  transactions: [
    {
      date: "2025-11-11",
      category: "Japan",
      price: 2992.52,
      price_to_pay: 2992.52,
      installment: { type: "BNPL", current: null, total: null },
      statement_date: "2025-12-12",
      due_date: "2026-01-06"
    },
    {
      date: "2025-11-18",
      category: "Aircon",
      price: 58500,
      price_to_pay: 870.02,
      installment: { current: 11, total: 24 }
    }
  ]
}
```

---

## Path B — Multi-Section Translation

Used for: **Monthly Budget (multiple tables in one sheet)**

### Why It's Hard

The Monthly Budget sheet has side-by-side tables starting at the same row:

```
Row 23: [INCOME SUMMARY]  [EXPENSES SUMMARY]  [BILLS SUMMARY]  [CASH FLOW]
Row 43:                    [SAVINGS]           [DEBT PAYMENTS]
```

Standard libraries assume one table per row range. Won't work here.

### Steps

1. **Fetch values + formatting together** — `spreadsheets.get` with `includeGridData: true`
2. **Build formatting heat map** — For each cell: has value, is bold, has background color, is merged
3. **Detect section anchors** — Bold + colored background = section header anchor
4. **Claude classifies each anchor** — Short, cheap call with just the labels
5. **Build section boundaries** — Each section has its own independent col_range
6. **Column mapping per section** — Read the row after the section header
7. **Save mapping, reuse every month** — Jan–Dec tabs all use the same mapping

### Section Anchor Detection

```typescript
function findSectionAnchors(cellMap) {
  return cellMap.filter(cell =>
    cell.bold === true &&
    cell.bgColor !== null &&
    cell.bgColor !== '#ffffff' &&
    cell.value?.length > 0
  )
}
// Returns:
// { row: 23, col: 0,  value: "INCOME SUMMARY" }
// { row: 23, col: 4,  value: "EXPENSES SUMMARY" }
// { row: 23, col: 11, value: "BILLS SUMMARY" }
// { row: 23, col: 18, value: "CASH FLOW SUMMARY" }
// { row: 43, col: 11, value: "SAVINGS" }
// { row: 44, col: 11, value: "DEBT PAYMENTS" }
```

### Saved Mapping Shape

```typescript
interface MonthlyBudgetMapping {
  spreadsheet_id: string
  template_month: string          // "April 2026"
  sections: {
    type: "income" | "expenses" | "bills" | "savings" | "debts" | "summary"
    anchor_text: string
    anchor_bold: boolean
    anchor_col: number
    columns: Record<string, number>
  }[]
}
```

---

## Firestore Collections

| Collection | What | Why |
|------------|------|-----|
| `sheet_mappings` | Column maps, section anchors per sheet | Translation reference |
| `analysis_history` | Claude responses + timestamps | History view |
| `user_session` | Auth, connected spreadsheet ID | App state |
| `recurring_budget_items` | Fixed monthly items (apply to all) | Budget generation |

---

## Sheet-by-Sheet Reference

Based on Nicolle's actual spreadsheets.

### Debt Tracker

#### Total Debt Tracker (Linear — summary matrix)

| Column | Lunas Field | Notes |
|--------|-------------|-------|
| Due Month | `period` | Format: "Jan 2026" |
| BDO CC | `accounts[0].amount_due` | |
| Col C (checkbox) | `accounts[0].paid` | TRUE/FALSE |
| BPI CC | `accounts[1].amount_due` | |
| Col E (checkbox) | `accounts[1].paid` | |
| UB CC | `accounts[2].amount_due` | |
| Col G (checkbox) | `accounts[2].paid` | |
| SPayLater | `accounts[3].amount_due` | |
| Col I (checkbox) | `accounts[3].paid` | |
| Other Debts | `accounts[4].amount_due` | |
| TOTAL | `total_due` | Computed, skip on write |
| Paid | `total_paid` | Computed, skip on write |
| Running Balance | `running_balance` | Computed, skip on write |

Skip rows: row 1 (title), row 2 (empty), row 3 (section header), row 4 (empty), row 29 (GRAND TOTAL)

#### BDO CC / BPI CC / UB CC (Linear — transaction log + metadata + bill summary)

**Metadata block** (rows 1-5, right side):

| Cell | Lunas Field |
|------|-------------|
| Credit Limit value | `credit_limit` |
| Remaining value | `remaining` |
| Total Spent value | `total_spent` |
| Statement Cycle value | `statement_cycle` |
| Due Date value | `due_date_rule` |

**Transaction table** (header row 6):

| Column | Lunas Field | Notes |
|--------|-------------|-------|
| Date | `date` | |
| Category | `category` | |
| Description | `description` | |
| Price | `price` | |
| Price to Pay | `price_to_pay` | Monthly installment amount |
| Months | `installment_str` | Parse: "11/24" → {current:11, total:24} |
| Statement Date | `statement_date` | |
| Due Date | `due_date` | |
| Notes | `notes` | |

**Bill Summary block** (right side, rows 7+):

| Column | Lunas Field |
|--------|-------------|
| Statement Date | `bill.statement_date` |
| Amount Due | `bill.amount_due` |
| Due Date | `bill.due_date` |
| Paid (checkbox) | `bill.paid` |

#### SPayLater (Linear — simple payment schedule)

| Column | Lunas Field | Notes |
|--------|-------------|-------|
| Statement Month | `period` | |
| Amount Due | `amount_due` | |
| Due Date | `due_date` | |
| Paid | `paid` | TRUE/FALSE |
| Notes | `notes` | Flag: "ENDS" = last payment |

#### Other Debts (Linear)

Confirm structure on first connect. Likely: creditor name, balance, monthly payment, due date.

---

### Savings Tracker

#### Total Savings (Linear — summary per account, per year)

| Column | Lunas Field | Notes |
|--------|-------------|-------|
| Account name | `account_name` | RCBC, Globe ATRAM, etc. |
| Total (current) | `current_balance` | |
| 2025 Goal | `goal_2025` | |
| 2025 Achieved | `achieved_2025` | |
| 2025 % | `progress_2025` | Computed, skip on write |
| 2026 Goal | `goal_2026` | |
| 2026 Achieved | `achieved_2026` | |
| 2026 % | `progress_2026` | Computed, skip on write |

#### RCBC Savings / PayMaya Savings (Linear — daily transaction log)

**Metadata block:**

| Cell | Lunas Field |
|------|-------------|
| Goal value | `year_goal` |
| Progress % | Computed, skip |
| Total Incoming | `total_incoming` |

**Transaction table:**

| Column | Lunas Field | Notes |
|--------|-------------|-------|
| Date | `date` | |
| Current Savings | `running_balance` | Skip on write (computed) |
| Ingoing | `ingoing` | |
| Description (ingoing) | `ingoing_description` | |
| Outgoing | `outgoing` | |
| Description (outgoing) | `outgoing_description` | |
| Total | `total` | Computed, skip on write |

#### Globe ATRAM / Globe ATRAM 60 (Linear — UITF monthly contribution + live NAV)

Two sub-tables side by side:

**Left (peso contributions):**

| Column | Lunas Field | Notes |
|--------|-------------|-------|
| Month (JAN-DEC) | `month` | Row per month |
| 2024-2028 columns | `contributions[year]` | Peso amounts per year |
| Average Monthly Savings | Computed, skip | |
| Cumulative Savings | Computed, skip | |
| Dividend Payout rows | `dividends[year]` | Annual payout |

**Right (units):**

| Column | Lunas Field | Notes |
|--------|-------------|-------|
| Month rows | `units[year][month]` | Units purchased |
| Total Units | `total_units` | |
| Current Peso/unit | `nav_per_unit` | LIVE via IMPORTJSON — app must replicate |
| Total Value | `total_value` | Computed: units x NAV |

> **NAV live fetch**: Currently uses `=IMPORTJSON(...)` for live UITF pricing. Lunas needs a scheduled Cloud Function to replicate this. **Parked for MVP 2.**

#### PAG-IBIG MP2 (Linear — government fund)

Same structure as Globe ATRAM units table (no peso contribution split).

#### Travel Fund Savings (Linear — goal-based sub-fund)

Confirm structure on first connect. Likely same as RCBC format.

---

### Monthly Budget (Jan-Dec 2026 tabs)

**Type**: Multi-section — 6 tables inside one sheet, some side-by-side

| Section | Anchor Text | Col Range | Row Range (April) | Lunas Type |
|---------|-------------|-----------|-------------------|------------|
| Income Summary | INCOME SUMMARY | A-C | 24-33 | `income[]` |
| Expenses Summary | EXPENSES SUMMARY | E-I | 24-43 | `expenses[]` |
| Bills Summary | BILLS SUMMARY | L-Q | 24-33 | `bills[]` |
| Cash Flow Summary | CASH FLOW SUMMARY | S-U | 24-30 | `cash_flow` |
| Savings | SAVINGS | L-Q | 35-42 | `savings_allocations[]` |
| Debt Payments | DEBT PAYMENTS | L-Q | 44-55 | `debt_payments[]` |

**Column mappings per section:**

**Income:** `description`, `expected`, `actual`

**Expenses:** `category`, `description`, `payment_mode`, `budget`, `actual` (Remaining = computed, skip)

**Bills:** `category`, `description`, `payment_mode`, `due_date`, `amount_due`, `paid`

**Savings Allocations:** `category`, `description`, `payment_mode`, `goal`, `saved` (Remaining = computed, skip)

**Debt Payments:** `category`, `description`, `payment_mode`, `due_date`, `amount_due`, `paid`

**Cash Flow Summary:** `description`, `budget`, `actual`

**Top metadata** (rows 7-10): `income_received`, `actual_expenses`, `income_saved`, `remaining_income`

**Current Money in Banks block:** `bank_name`, `bank_budget`, `bank_left`

Month tab reuse: mapping saved once from template month (April 2026). All other month tabs use the same section anchors and column offsets.

---

### Out of Scope (MVP 1)

Sheets: Subscription To Check, I Want To Buy, Bucket List, Income Tracker, Salary Checker. Revisit in MVP 3.

---

## Confirmation Flow

Before Lunas reads or writes any sheet, user confirms:

```
Read confirmation:
"Lunas wants to read [BDO CC]
 We'll use this to show your transactions and payment history.
 [Allow read]  [Allow read + write]  [Skip]"

Write confirmation:
"Lunas will add a row to [April 2026]
 New expense: Grab | RCBC | ₱250
 → Row 31, columns E-I
 [Confirm]  [Cancel]"
```

---

## Full Architecture Flow

```
User connects Sheet
        ↓
App fetches all tabs + first 10 rows + formatting
        ↓
Linear sheets: Claude maps headers once
Budget sheets: Formatting anchors → Claude classifies sections
        ↓
User confirms mapping per sheet
        ↓
SheetMapping saved to Firestore
        ↓
All future reads use saved mapping
        ↓
Lunas internal model (normalized)
        ↓
Claude receives clean structured data
        ↓
Analysis + response
        ↓
Write-back uses mapping to find correct cell
        ↓
Google Sheet updated
```

---

## Google Sheets API — Response Reference

### Two Endpoints, Two Purposes

| Endpoint | When to Use | Returns |
|----------|-------------|---------|
| `spreadsheets.values.get` | Daily reads, all data fetches | Values only — fast, cheap |
| `spreadsheets.get` + `includeGridData: true` | Onboarding only, re-mapping | Values + formatting + formulas |

Never use `includeGridData` for regular data reads — it's heavy and slow.

### Endpoint 1 — `spreadsheets.values.get`

Used for all normal reads after mapping is saved.

```typescript
GET /v4/spreadsheets/{id}/values/{range}
// Example: /values/BDO CC!A1:I120
```

Response:
```json
{
  "range": "BDO CC!A1:I120",
  "majorDimension": "ROWS",
  "values": [
    ["BDO CREDIT CARD"],
    [],
    ["11/11/2025", "Japan", "12/12", "₱2,992.52", "₱2,992.52", "BNPL", "12/12/2025", "1/6/2026", ""],
    ["11/18/2025", "Aircon", "11/24", "₱58,500.00", "₱870.02", "24 mos", "12/12/2025", "1/6/2026", ""]
  ]
}
```

Key rules:
- Formula cells return their **computed result** — TOTAL row shows the number, not `=SUM(...)`
- Empty cells = empty string or missing from array entirely
- Trailing empty rows and columns are omitted
- No formatting info whatsoever

### Endpoint 2 — `spreadsheets.get` + `includeGridData`

Used for onboarding sheet detection only.

### Per-Cell Field Reference

| Field | What It Contains | When to Use |
|-------|------------------|-------------|
| `userEnteredValue.stringValue` | Raw text the user typed | String cells |
| `userEnteredValue.numberValue` | Raw number | Number cells |
| `userEnteredValue.boolValue` | true/false | Checkboxes |
| `userEnteredValue.formulaValue` | The formula string e.g. `=SUM(B6:B28)` | Detect formula/total rows — skip these |
| `effectiveValue.numberValue` | Computed result | Always use this for math, never parse formattedValue |
| `formattedValue` | Display string e.g. `"₱64,067.14"` | Display only, never parse for numbers |
| `userEnteredFormat.textFormat.bold` | true/false | Section anchor detection |
| `userEnteredFormat.backgroundColor` | `{red, green, blue}` as 0.0-1.0 floats | Section anchor detection |
| `{}` (empty object) | Empty cell | Handle in every read |

### Critical Gotchas

**Checkboxes come back as booleans:**
```json
{ "userEnteredValue": { "boolValue": true }, "formattedValue": "TRUE" }
```

**Formula/TOTAL rows — detect and skip using `formulaValue`:**
```typescript
const isFormulaRow = row.values.some(
  cell => cell.userEnteredValue?.formulaValue !== undefined
)
if (isFormulaRow) continue  // skip TOTAL rows
```

**Always use `effectiveValue.numberValue` for numbers — never parse `formattedValue`:**
```typescript
// WRONG
const amount = parseFloat(cell.formattedValue.replace('₱', '').replace(',', ''))

// RIGHT
const amount = cell.effectiveValue?.numberValue ?? 0
```

**Background color is RGB floats, not hex:**
```typescript
function isColored(bgColor) {
  if (!bgColor) return false
  const { red = 1, green = 1, blue = 1 } = bgColor
  return !(red > 0.95 && green > 0.95 && blue > 0.95)
}
```

**Empty cells are `{}` not null:**
```typescript
const value = cell.effectiveValue?.stringValue
            ?? cell.effectiveValue?.numberValue
            ?? null
```

### When Each Endpoint Is Used

| Action | Endpoint |
|--------|----------|
| First connect — detect sheet structure | `spreadsheets.get` + `includeGridData` |
| Daily dashboard read | `spreadsheets.values.get` |
| Add new row | `spreadsheets.values.append` |
| Update a cell | `spreadsheets.values.update` |
| User remaps their sheet | `spreadsheets.get` + `includeGridData` |

---

## Open Questions

| Question | Status |
|----------|--------|
| How to handle user adding new rows above the header? | To decide |
| NAV live fetch replication for ATRAM/MP2 | Parked — MVP 2 |
| What happens if user restructures their Sheet after mapping? | To decide |
| Include Travel Fund, I Want To Buy in MVP 1 or skip? | Parked — MVP 3 |
| Apply-to-all recurring budget items — how does write-back work for future months? | Next session |
