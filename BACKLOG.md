# Product Backlog: Family Budget Tracker

This document tracks prioritized feature requests, functional improvements, and technical enhancements for the Family Budget Tracker.

---

## Priority Matrix

| Priority | Feature | Category | Target Scope |
| :---: | :--- | :--- | :--- |
| **P1** | **Savings Goals & Sinking Funds Tracker** | Savings & Wealth | New Goals engine, cumulative target tracking, progress deadlines |
| **P2** | **Family Split & "Who Owes What" Settlement Engine** | Multi-Member | Shared expense calculations, partner debt balances, 1-click settle-up |
| **P2** | **Budget Cycle Rollover & Carryover Management** | Cycles | Payday surplus/deficit carryforward, cycle-over-cycle variance |
| **P3** | **Real-Time Category Budget Caps & Envelope Warnings** | Transactions | In-modal remaining envelope warnings, overspend alerts |
| **P3** | **Live FX Rate Sync & JSON Backup Restore** | Settings & Data | Automated currency rate updates, full database restore from export |

---

## Detailed Specifications

### 🎯 [P1] Savings Goals & Sinking Funds Tracker
* **Context & Gap Analysis**:
  * *What exists*: The system currently has `type: 'Savings'` with category-level per-cycle targets via `budgetTargetBase` (e.g. save \$500/cycle to Emergency Fund in [`initialCategories`](file:///home/antigravity/TeymurovFuad/family-budget-tracker/src/data/initialData.ts#L38-L42)).
  * *What is missing*: There is no cumulative goal tracking engine with total target amounts, target dates, cumulative balance tracking, or completion projections.
* **Requirements**:
  1. **Goal Entity**: Define `SavingsGoal` model:
     - `id`, `name`, `targetAmount`, `currentAmount`, `currency`, `targetDate`, `category`, `color`, `icon`.
  2. **Cumulative Balance Aggregation**:
     - Automatically aggregate all historical transactions tagged with `type: 'Savings'` and matching category to compute current accumulated total.
     - Support direct manual initial balance baseline (for existing savings before using the app).
  3. **Visual Progress & Projections**:
     - Progress bars (% funded) and milestone badges.
     - Required monthly/cycle contribution rate to achieve the goal by `targetDate`.
     - Status indicators: *On Track*, *Behind*, *Completed*.
  4. **Allocation from Budget Cycles**:
     - Ability to allocate unaccounted surplus directly into a savings goal at the end of a cycle.

---

### ⚖️ [P2] Family Split & "Who Owes What" Settlement Engine
* **Context & Gap Analysis**:
  * *What exists*: Transactions support member tagging (`Fuad`, `Partner`, `Shared`).
  * *What is missing*: Automated settlement calculation showing who paid for shared household items and net balance between members.
* **Requirements**:
  1. Configurable split ratios for shared expenses (default: 50/50, or proportional to income from salary cycles).
  2. Net settlement summary card: `[Member A paid for Shared] - [Member B paid for Shared] = Net Owed`.
  3. "Settle Up" action to create an equalizing transaction that clears the balance.

---

### 🔄 [P2] Budget Cycle Rollover & Carryover Management
* **Context & Gap Analysis**:
  * *What exists*: [`CyclesView.tsx`](file:///home/antigravity/TeymurovFuad/family-budget-tracker/src/components/CyclesView.tsx) calculates unaccounted liquidity per cycle (`salaryAmount - expenses - savings`).
  * *What is missing*: When a new salary cycle starts, the prior surplus/deficit is neither rolled into savings nor forwarded to the new cycle.
* **Requirements**:
  1. Option to roll surplus into a designated Savings Goal or emergency fund.
  2. Option to carry forward positive or negative buffer into the next cycle's starting balance.
  3. Cycle-over-cycle variance reports (comparing current cycle to previous 3 cycles).

---

### ⚠️ [P3] Real-Time Category Budget Caps & Envelope Warnings
* **Context & Gap Analysis**:
  * *What exists*: Category targets exist, and [`SummaryView.tsx`](file:///home/antigravity/TeymurovFuad/family-budget-tracker/src/components/SummaryView.tsx) shows historical percentage spent.
  * *What is missing*: No live check or warning during transaction entry.
* **Requirements**:
  1. When selecting a category in [`AddTransactionModal.tsx`](file:///home/antigravity/TeymurovFuad/family-budget-tracker/src/components/AddTransactionModal.tsx), show remaining cycle budget (e.g. `Groceries: $140.50 remaining of $800`).
  2. Warning prompt if the entered amount will exceed 100% of the envelope budget.

---

### 🌐 [P3] Live FX Rate Sync & JSON Backup Restore
* **Context & Gap Analysis**:
  * *What exists*: Static exchange rates in [`server.ts`](file:///home/antigravity/TeymurovFuad/family-budget-tracker/server.ts#L182-L190); `/api/export` for JSON downloads.
  * *What is missing*: Live rate updates and a restore UI.
* **Requirements**:
  1. 1-click "Fetch Live Exchange Rates" button in [`SettingsView.tsx`](file:///home/antigravity/TeymurovFuad/family-budget-tracker/src/components/SettingsView.tsx) using public rates API for EUR, GBP, AZN, and PLN.
  2. "Restore from Backup" file uploader in Settings to restore all database collections.
