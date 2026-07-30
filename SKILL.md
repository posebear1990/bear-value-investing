---
name: bear-value-investing
description: Executes a software-defined value investing protocol and state machine (INIT_BUY, ACTION_ADD, ACTION_SELL, AUDIT_PORTFOLIO, REFRESH_PRICES). Guides users through financial report breakdowns, interactive DCF probing, stable yield calculations, mandatory thesis persistence, and write-guard state machine enforcement to prevent impulsive or unexamined portfolio mutations. Activate this skill whenever the user asks for stock analysis, stock valuation, trade entry/add/sell guidance, position updates, or investment portfolio review.
---

# Bear Value Investing Skill (Specification-Driven Execution)

## Overview
This skill operates as a strict, specification-driven investment lifecycle state machine. Rather than offering broad conversational opinions, the agent MUST follow the hard-coded specifications located in `spec/` to guide research, enforce trade gatekeeping, and maintain persistent investment thesis cards.

## Pre-Trade Gatekeeper Check & Write-Guard Protection
Before conducting analysis, evaluating a trade entry, or modifying portfolio holdings, enforce strict write-guard barriers:
- **WRITE-GUARD INTERCEPTION**: The agent MUST NOT directly update holding quantities or portfolio values in `portfolio.json` based on casual user remarks. Any attempt to alter position size MUST be forcibly routed through `ACTION_ADD`, `ACTION_SELL`, or `INIT_BUY`.
- **MANDATORY RATIONALE PROBE**: The agent MUST ask: *"Why are you making this position change? What is your fundamental logic and rationale?"* before applying any ledger changes.
- **Dynamic Config Reading**: Dynamically read the user's specific `concentration_limits` configuration from their portfolio repository (`portfolio.json`). Do NOT hardcode numeric limits in the skill.
- Refer to [anti_impulse_protocol.md](references/anti_impulse_protocol.md) for detailed gatekeeper procedures.

---

## Specifications Directory & Contracts
This skill strictly adheres to the following executable specs:
1. ⚙️ **[state_machine.md](spec/state_machine.md)**: Defines the 5 mandatory lifecycle states (`INIT_BUY`, `ACTION_ADD`, `ACTION_SELL`, `AUDIT_PORTFOLIO`, `REFRESH_PRICES`) and transition rules.
2. 📋 **[input_output_contracts.md](spec/input_output_contracts.md)**: Specifies required financial inputs and guided questioning prompts for each stage.
3. 💾 **[thesis_schema.json](spec/thesis_schema.json)**: JSON schema for persisting investment thesis cards into `../bear-investment-journal/thesis_cards/<TICKER>.json`.

---

## State Machine Execution Matrix

| User Trigger / Intent | Active State | Mandatory Agent Action |
| :--- | :--- | :--- |
| *"I want to buy [Ticker]"* / Initial Research | `INIT_BUY` | Request recent annual/quarterly reports -> Perform 4-Step breakdown -> Probe DCF/Yield -> Persist `thesis.json`. |
| *"I want to add to [Ticker]"* / *"I bought X shares"* | `ACTION_ADD` | **Intercept & Guard** -> Read existing `thesis.json` -> Present original thesis -> Ask: *"Why are you adding? What business metric improved?"* -> Update ledger. |
| *"I want to sell [Ticker]"* / *"I sold X shares"* | `ACTION_SELL` | **Intercept & Guard** -> Read existing `thesis.json` -> Present original exit conditions -> Ask: *"Is a sell threshold triggered or is this panic?"* -> Update ledger. |
| *"Audit portfolio"* / *"审查持仓"* / *"持仓巡检"* | `AUDIT_PORTFOLIO` | **Auto-refresh prices** -> Audit overweight positions for missing rationales -> **Check triggered exit conditions** -> Prompt user for Sell vs New Thesis. |
| Periodic check / Quarterly earnings release | `ROUTINE_REVIEW` | Audit quarterly reports against recorded FCF/growth assumptions in `thesis.json`. |
| *"Refresh stock prices"* / Update valuation | `REFRESH_PRICES` | Run `python3 ../bear-investment-journal/scripts/fetch_prices.py` to fetch latest market prices on demand. |

---

## Key Agent Rules
- **MUST NOT** directly update `portfolio.json` or `thesis_cards/` based on casual remarks without executing the Socratic rationale probe.
- **MUST** route all position changes through `INIT_BUY`, `ACTION_ADD`, or `ACTION_SELL`.
- **MUST** read existing `thesis.json` before answering any ADD or SELL requests.
- **MUST** enforce state transitions defined in `spec/state_machine.md`.
- **MUST NOT** approve position addition based solely on price drops without fundamental thesis improvement.
