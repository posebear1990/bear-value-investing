# Bear Value Investing State Machine Specification

This document specifies the mandatory states, transition triggers, and agent actions for investment lifecycle management.

---

## State Diagram & Transitions

```
[IDLE] 
  │
  ├── Trigger: User wants to buy new stock ─────────> [STATE: INIT_BUY]
  ├── Trigger: User wants to add position ──────────> [STATE: ACTION_ADD]
  ├── Trigger: User wants to sell / trim position ──> [STATE: ACTION_SELL]
  └── Trigger: Quarterly review / routine check ───> [STATE: ROUTINE_REVIEW]
```

---

## State 1: `INIT_BUY` (Initial Buy Evaluation)

### Mandatory Pre-conditions
- Stock ticker or company name specified.
- Recent quarterly (Q) and annual (FY) financial reports available or fetched.

### Execution Workflow
1. **Agent Action**: Request/fetch recent financial statements if missing.
2. **Agent Action**: Execute Step 1 (Moat), Step 2 (Classification), Step 3 (Valuation/DCF), Step 4 (Risk/Safety).
3. **Agent Action**: Present initial finding & prompt Socratic parameters.
4. **Exit Action**: Generate and save persistent state file: `investment_journal/<TICKER>/thesis.json` and `thesis.md`.

---

## State 2: `ACTION_ADD` (Position Addition Gatekeeper)

### Mandatory Pre-conditions
- Target `investment_journal/<TICKER>/thesis.json` MUST exist.

### Execution Workflow
1. **Agent Action**: Intercept request. Read existing `thesis.json`.
2. **Agent Prompt (Mandatory Question)**:
   > "You are proposing to ADD to **[TICKER]**. Here were your original core investment theses recorded on **[DATE]**:
   > 1. [Thesis 1]
   > 2. [Thesis 2]
   >
   > What specific fundamental metrics or earnings data have improved to justify adding to this position? Is this fundamental-driven or price-movement driven?"
3. **Agent Evaluation**: Compare user's rationale against initial thesis.
   - If fundamental logic improved: Approve position expansion (e.g. increase tranche from 20% to 50%). Update `thesis.json`.
   - If purely price driven without thesis improvement: Trigger Warning and enforce cooling-off period.

---

## State 3: `ACTION_SELL` (Position Sale / Trim Gatekeeper)

### Mandatory Pre-conditions
- Target `investment_journal/<TICKER>/thesis.json` MUST exist.

### Execution Workflow
1. **Agent Action**: Intercept request. Read existing `thesis.json`.
2. **Agent Prompt (Mandatory Question)**:
   > "You are proposing to SELL/TRIM **[TICKER]**. Here were your recorded exit conditions:
   > 1. [Exit Condition 1]
   > 2. [Exit Condition 2]
   >
   > Has the core business moat deteriorated, or has the stock valuation exceeded your sell threshold? Or are you reacting to market panic?"
3. **Agent Evaluation**: Log sell rationale in thesis history.

---

## State 4: `ROUTINE_REVIEW` (Periodic Thesis Audit)

### Execution Workflow
1. Read all active `thesis.json` files in `investment_journal/`.
2. Compare original revenue/FCF growth assumptions against newly released quarterly reports.
3. Flag any thesis drift or thesis invalidation.
