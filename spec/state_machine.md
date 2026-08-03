# Bear Value Investing State Machine Specification

This document specifies the mandatory states, transition triggers, and agent actions for investment lifecycle management.

---

## Write-Guard Interception Protocol (写屏障保护规程) 🔒

To prevent the user (and the AI agent) from accidentally or unconsciously bypassing the value investing discipline, **THE AGENT IS STRICTLY FORBIDDEN FROM MUTATING `portfolio.json` OR THESIS CARDS DIRECTLY** based on casual remarks (e.g., *"I bought 100 more shares of X"*, *"Change my position of Y to Z"*).

### Mandatory Interception Rule:
Whenever the user requests or mentions a position size modification:
1. **HALT LEDGER MUTATION**: Do NOT modify `shares_count` or `portfolio.json` immediately.
2. **FORCE STATE ROUTING**: Route the request to `INIT_BUY`, `ACTION_ADD`, or `ACTION_SELL`.
3. **CHECK THESIS EXISTENCE & EXPLICITLY NOTIFY**:
   - Check if `../bear-investment-journal/thesis_cards/<TICKER>.json` exists.
   - **If NO prior thesis card exists**: The agent MUST explicitly state to the user first:
     > *"⚠️ **Notice**: No prior investment thesis card exists for [TICKER] in the system. I will initialize a new thesis card while processing this position update."*
   - **If a thesis card exists**: Read and present the original historical thesis points and exit conditions.
4. **EXECUTE SOCRATIC PROBE & ALIGNMENT**:
   - Evaluate if the transaction aligns with pre-stored thesis (`ALIGNMENT_MATCH`) or drifts (`ALIGNMENT_DRIFT`).
5. **ATOMIC MUTATION 4-STEP PROTOCOL (原子化落盘 4 步曲)**: ⚡️ MANDATORY
   Upon user confirmation, the agent MUST execute the following 4 steps atomically:
   - **Step 1**: Update `shares_count` in `../bear-investment-journal/portfolio.json`.
   - **Step 2**: Update/append `history_log` in `../bear-investment-journal/thesis_cards/<TICKER>.json`.
   - **Step 3**: **MANDATORY**: Append a transaction log entry into `../bear-investment-journal/history/transactions.json`.
   - **Step 4**: Run `python3 ../bear-investment-journal/scripts/fetch_prices.py` to recalculate valuation.

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
  ├── Trigger: "Generate report" / "生成复盘报告" ───> [STATE: GENERATE_REPORT]
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

### Execution Workflow
1. **Agent Action**: Intercept request. Read existing `thesis.json` or state missing status.
2. **Agent Evaluation & Probe**: Verify thesis alignment.
3. **Atomic Execution**: Execute **Atomic Mutation 4-Step Protocol** (update portfolio, thesis card, append `transactions.json`, and run `fetch_prices.py`).

---

## State 3: `ACTION_SELL` (Position Sale / Trim Gatekeeper)

### Execution Workflow
1. **Agent Action**: Intercept request. Read existing `thesis.json` (or state missing notice).
2. **Agent Evaluation & Probe**: Verify sell rationale against exit conditions.
3. **Atomic Execution**: Execute **Atomic Mutation 4-Step Protocol** (update portfolio, thesis card, append `transactions.json`, and run `fetch_prices.py`).

---

## State 4: `AUDIT_PORTFOLIO` (Active Portfolio Audit & Anti-Drift Inspection)

### Execution Workflow
1. **Auto-Refresh Data**: Run `python3 ../bear-investment-journal/scripts/fetch_prices.py` to get live valuation and current weights.
2. **Audit 1: Concentration Boundary & Exception Check**:
   - Check all active positions against configured `concentration_limits`.
3. **Audit 2: Exit Condition Invalidation & Holding Drift Check**:
   - Compare current market developments against recorded `exit_conditions`.
4. **Audit Summary**: Output a structured Audit Summary Table detailing positions needing user decision.

---

## State 5: `GENERATE_REPORT` (Periodic Review & Executive Report Export)

### Execution Workflow
1. **Auto-Refresh Data**: Run `python3 ../bear-investment-journal/scripts/fetch_prices.py`.
2. **Assemble Portfolio Report**: Populate `../bear-investment-journal/templates/report_template.md`.
3. **Export Report**: Save to `../bear-investment-journal/history/YYYY-MM-DD-portfolio-report.md`.
