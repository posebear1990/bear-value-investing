# Pre-Trade Anti-Impulse Protocol & Gatekeeper System

This document outlines the emotional circuit-breaker and pre-trade discipline protocol to prevent emotional, impulsive, or FOMO-driven trading.

---

## 1. Trigger Conditions for Gatekeeper Mode
The AI agent must automatically trigger **Gatekeeper Mode** if any of the following are detected:
- The user expresses urgency (e.g., *"Quick, should I buy now?"*, *"It's surging, I don't want to miss out!"*).
- The target stock has experienced a price spike (>5%-8%) on the current trading day.
- The user intends to place a market order without completing Steps 1-3 of the Value Investing Workflow.

---

## 2. The 3 Cold-Water Probing Questions
Before approving any purchase, the agent MUST ask the user these three questions:

1. **FOMO vs. Margin of Safety Check**:
   > *"Are you buying because the stock price has fallen into a comfortable margin of safety, or because the recent price momentum makes you afraid of missing out (FOMO)?"*
2. **20% Downside Stress Test**:
   > *"If macro noise causes this stock to drop another 20% tomorrow, do you have concrete business fundamentals and cash flow metrics to hold/add comfortably, or will you panic sell?"*
3. **Position Sizing & Tranche Discipline**:
   > *"Is this a initial tranche (e.g., 20%-30% test position), or are you trying to go all-in at once? Are you breaking the rule of scaling in gradually?"*

---

## 3. The Investment Thesis Card Template
Once analysis is approved, generate a compact **Investment Thesis Card** and save it under the `investment_journal/` directory before executing the trade.

```markdown
# 📌 Investment Thesis Card - [Stock Ticker / Name]
- **Date**: YYYY-MM-DD
- **Entry Price**: [Current Price]
- **Category**: [Growth / Stable Value]
- **Core Thesis**:
  1. [Primary valuation rationale, e.g., Net Cash Ratio > 20%, FCF yield 8%]
  2. [Moat or competitive advantage driver]
- **Planned Position Size**: [e.g., 20% Initial Tranche]
- **Exit / Selling Rules**:
  - Sell ONLY if the core business moat deteriorates or valuation becomes excessively overvalued.
  - DO NOT sell due to short-term market noise or macro sentiment panic.
```
