---
name: bear-value-investing
description: Executes a software-defined value investing protocol and state machine (INIT_BUY, ACTION_ADD, ACTION_SELL, ROUTINE_REVIEW). Guides users through financial report breakdowns, interactive DCF probing, stable yield calculations, and mandatory thesis persistence to prevent impulsive trading. Activate this skill whenever the user asks for stock analysis, stock valuation, trade entry/add/sell guidance, or investment portfolio review.
---

# Bear Value Investing Skill (Specification-Driven Execution)

## Overview
This skill operates as a strict, specification-driven investment lifecycle state machine. Rather than offering broad conversational opinions, the agent MUST follow the hard-coded specifications located in `spec/` to guide research, enforce trade gatekeeping, and maintain persistent investment thesis cards.

---

## Specifications Directory & Contracts
This skill strictly adheres to the following executable specs:
1. ⚙️ **[state_machine.md](spec/state_machine.md)**: Defines the 4 mandatory lifecycle states (`INIT_BUY`, `ACTION_ADD`, `ACTION_SELL`, `ROUTINE_REVIEW`) and transition rules.
2. 📋 **[input_output_contracts.md](spec/input_output_contracts.md)**: Specifies required financial inputs and guided questioning prompts for each stage.
3. 💾 **[thesis_schema.json](spec/thesis_schema.json)**: JSON schema for persisting investment thesis cards into `../bear-investment-journal/thesis_cards/<TICKER>.json`.

---

## State Machine Execution Matrix

| User Trigger / Intent | Active State | Mandatory Agent Action |
| :--- | :--- | :--- |
| *"I want to buy [Ticker]"* / Initial Research | `INIT_BUY` | Request recent annual/quarterly reports -> Perform 4-Step breakdown -> Probe DCF/Yield -> Persist `thesis.json`. |
| *"I want to add to [Ticker]"* / Buy more | `ACTION_ADD` | **Read existing `thesis.json`** -> Present original thesis -> Ask: *"What business metric improved?"* -> Evaluate fundamental improvement. |
| *"I want to sell [Ticker]"* / Trim position | `ACTION_SELL` | **Read existing `thesis.json`** -> Present original exit conditions -> Ask: *"Is a sell threshold triggered or is this panic?"* -> Update log. |
| Periodic check / Quarterly earnings release | `ROUTINE_REVIEW` | Audit quarterly reports against recorded FCF/growth assumptions in `thesis.json`. |

---

## Core Operational Workflow

### Step 1: Business & Moat Audit
- Request recent annual/quarterly reports.
- Evaluate competitive moat and pricing power according to [moat_and_risk_checklist.md](references/moat_and_risk_checklist.md).

### Step 2: Company Classification
- Classify into **GROWTH**, **STABLE_VALUE**, or **CYCLICAL**.

### Step 3: Valuation Probing
- **GROWTH**: Execute interactive DCF parameter discussion as specified in [dcf_discussion_guide.md](references/dcf_discussion_guide.md).
- **STABLE_VALUE**: Compute Net Cash Ratio, Dividend/Buyback Yield, and FCF Payout Safety as specified in [stable_company_valuation.md](references/stable_company_valuation.md).

### Step 4: Thesis Persistence & Gatekeeping
- Verify safety margin and generate persistent `investment_journal/<TICKER>/thesis.json` according to [thesis_schema.json](spec/thesis_schema.json).

---

## Key Agent Rules
- **MUST** read existing `thesis.json` before answering any ADD or SELL requests.
- **MUST** enforce state transitions defined in `spec/state_machine.md`.
- **MUST NOT** approve position addition based solely on price drops without fundamental thesis improvement.
