# Bear Value Investing State Machine Specification

This document specifies the mandatory states, transition triggers, and agent actions for investment lifecycle management.

---

## Write-Guard Interception Protocol (写屏障保护规程) 🔒

To prevent the user (and the AI agent) from accidentally or unconsciously bypassing the value investing discipline, **THE AGENT IS STRICTLY FORBIDDEN FROM MUTATING `portfolio.json` OR THESIS CARDS DIRECTLY** based on casual remarks (e.g., *"I bought 100 more shares of X"*, *"Change my position of Y to Z"*).

### Mandatory Interception Rule:
Whenever the user requests or mentions a position size modification:
1. **HALT LEDGER MUTATION**: Do NOT modify `shares_count` or `portfolio.json` immediately.
2. **FORCE STATE ROUTING**: Route the request to `INIT_BUY`, `ACTION_ADD`, or `ACTION_SELL`.
3. **EXECUTE SOCRATIC PROBE**: Ask the user:
   > *"You are updating your position in [TICKER]. To maintain our investment discipline:
   > 1. What is your fundamental rationale and business logic for this change?
   > 2. Has there been a change in earnings, moat, or valuation safety margin?"*
4. **MUTATE ONLY UPON RATIONALE CONFIRMATION**: Update `portfolio.json` and append a transaction log ONLY after the user provides their fundamental logic.

---

## State Diagram & Transitions

```
[IDLE] 
  │
  ├── Trigger: User wants to buy new stock ─────────> [STATE: INIT_BUY]
  ├── Trigger: User wants to add position ──────────> [STATE: ACTION_ADD] (Intercept & Probe)
  ├── Trigger: User wants to sell / trim position ──> [STATE: ACTION_SELL] (Intercept & Probe)
  ├── Trigger: Periodic check / Routine review ─────> [STATE: ROUTINE_REVIEW]
  ├── Trigger: "Audit portfolio" / "持仓巡检" ───────> [STATE: AUDIT_PORTFOLIO]
  ├── Trigger: "Generate report" / "生成复盘报告" ───> [STATE: GENERATE_REPORT] ⚡️ NEW
  └── Trigger: "Refresh prices" / "更新股价" ────────> [STATE: REFRESH_PRICES]
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
4. **Exit Action**: Generate and save persistent state file: `../bear-investment-journal/thesis_cards/<TICKER>.json` and `<TICKER>.md`.

---

## State 2: `ACTION_ADD` (Position Addition Gatekeeper)

### Mandatory Pre-conditions
- Target `../bear-investment-journal/thesis_cards/<TICKER>.json` MUST exist.

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
- Target `../bear-investment-journal/thesis_cards/<TICKER>.json` MUST exist.

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

## State 4: `AUDIT_PORTFOLIO` (Active Portfolio Audit & Anti-Drift Inspection)

### Mandatory Pre-conditions
- Portfolio ledger `../bear-investment-journal/portfolio.json` MUST exist.
- Thesis cards in `../bear-investment-journal/thesis_cards/` available for audit.

### Execution Workflow
1. **Auto-Refresh Data**: Run `python3 ../bear-investment-journal/scripts/fetch_prices.py` to get live valuation and current weights.
2. **Audit 1: Concentration Boundary & Exception Check**:
   - Check all active positions against configured `concentration_limits`.
   - If position weight > `single_stock_overweight_cap` (e.g. >20%) AND missing `extreme_allocation_rationale`:
     - **Prompt Action**: *"Stock [TICKER] ([Weight]%) is in overweight territory without a recorded exception rationale. Please provide your justification and target reduction timeline."*
3. **Audit 2: Exit Condition Invalidation & Holding Drift Check**:
   - For every holding, compare current market developments/earnings against recorded `exit_conditions`.
   - If an exit condition HAS BEEN TRIGGERED (e.g. regulatory investigation, price target reached, profit margin collapse), but user still holds:
     - **Prompt Action (Mandatory Choice)**:
       > "⚠️ **[TICKER]** has triggered your recorded exit condition: **[Triggered Exit Condition]**. However, you still hold **[Weight]%**. 
       > Please select an action:
       > Option A: Execute sell/trim order according to pre-committed discipline.
       > Option B: Provide a NEW fundamental thesis explaining why the old exit condition is no longer applicable, and update your thesis card."
4. **Audit Summary**: Output a structured Audit Summary Table detailing positions needing user decision.

---

## State 5: `GENERATE_REPORT` (Periodic Review & Executive Report Export) ⚡️

### Execution Workflow
1. **Auto-Refresh Data**: Run `python3 ../bear-investment-journal/scripts/fetch_prices.py` to calculate exact current values, returns, and concentration levels.
2. **Assemble Portfolio Report**: Populate `../bear-investment-journal/templates/report_template.md` with:
   - Total portfolio value, cash reserves, and unrealized P&L.
   - 3-Tier Concentration Risk Matrix.
   - Active holdings performance table (cost, current price, return %).
   - Zero-position Watchlist Pipeline Pool evaluation.
   - Invalidation and exit trigger checklist.
3. **Export Report**: Save the final report to `../bear-investment-journal/history/YYYY-MM-DD-portfolio-report.md`.
4. **Display Summary**: Present the summary to the user in chat.
