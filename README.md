# Bear Value Investing Skill

A software-defined value investing analysis framework and pre-trade discipline skill for AI coding assistants and agents (Google Antigravity / AGY / Claude / Open Agent Standard).

---

## 🌟 Overview
`bear-value-investing` converts an AI agent into a strict investment partner and trading discipline gatekeeper. It enforces a specification-driven state machine (`INIT_BUY`, `ACTION_ADD`, `ACTION_SELL`, `ROUTINE_REVIEW`) to eliminate emotional trading, impulse purchases, and FOMO (Fear Of Missing Out).

---

## 📐 Specification Architecture

- ⚡️ **`SKILL.md`**: Main skill entry point and execution matrix.
- ⚙️ **`spec/state_machine.md`**: Lifecycle state transition rules and mandatory gatekeeper actions.
- 📋 **`spec/input_output_contracts.md`**: Guided questioning templates and financial input dataset standards.
- 💾 **`spec/thesis_schema.json`**: Strict JSON schema for persisting investment thesis cards.
- 📚 **`references/`**: Reference guides for DCF probing, stable yield calculations, and economic moat checklists:
  - [dcf_discussion_guide.md](references/dcf_discussion_guide.md): Growth stock interactive DCF parameter probing protocol.
  - [stable_company_valuation.md](references/stable_company_valuation.md): Stable company net-cash, valuation multiples, and shareholder yield.
  - [moat_and_risk_checklist.md](references/moat_and_risk_checklist.md): 5-pillar economic moat and cyclical risk checklist.
  - [anti_impulse_protocol.md](references/anti_impulse_protocol.md): Pre-trade cooling off protocol and cold-water questions.

---

## 🚀 How It Works

```
[User Request] ──> [Skill Entry (SKILL.md)] ──> [Check State Machine (spec/state_machine.md)]
                                                        │
                      ┌─────────────────────────────────┼─────────────────────────────────┐
                      ▼                                 ▼                                 ▼
              [STATE: INIT_BUY]                 [STATE: ACTION_ADD]              [STATE: ACTION_SELL]
              • Request Financials              • Read thesis.json               • Read thesis.json
              • Conduct 4-Step Analysis         • Present original thesis        • Check Exit Conditions
              • Socratic DCF Probing            • Ask: "What metric improved?"   • Check panic vs logic
              • Save thesis.json                • Approve/Reject Add             • Log transaction
```

---

## 🛠️ Usage
Install or link this skill directory into your `.agents/skills/` or `~/.gemini/config/skills/` directory.

Activate by asking your agent:
> *"Use bear-value-investing to evaluate AAPL stock."*
> *"I want to add to my 0700.HK position."*
