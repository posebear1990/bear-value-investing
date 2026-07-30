# Pre-Trade Anti-Impulse Protocol & Position Concentration Limits

This document outlines the emotional circuit-breaker, pre-trade discipline protocol, and strict position concentration risk limits.

---

## 1. Position Concentration Limits (仓位集中度红线)

To prevent over-concentration risk and portfolio fragility, the agent MUST enforce the following concentration boundaries:

### Single Stock Limits (个股集中度)
- **Normal Cap (常规上限)**: **10%** of total portfolio.
- **Overweight Cap (超配上限)**: **20%** of total portfolio.
- **Extreme Cap (极限上限)**: **30%** of total portfolio.

### Cash + Broad-Market ETF Limits (现金与宽基防守底仓)
- **Normal Minimum (通常最低线)**: **5%** (Combined Cash + Broad-market ETFs like A50/S&P/CSI300).
- **Extreme Minimum (极限最低线)**: **0%**.

### Extreme State Protocol (极限状态硬性机制)
Whenever a stock position exceeds **20%** (approaching the 30% extreme cap) OR combined cash+broad-market ETF drops below **5%**:
1. **Mandatory Interception**: The agent MUST flag an **Extreme Concentration Alert**.
2. **Explicit Rationale Requirement**: The user MUST provide a formal, documented justification for why this extreme allocation exists.
3. **Temporary Exception Notice**: The agent MUST record in the stock's `thesis.json` that *"This extreme position is a temporary exception, NOT a permanent condition"*, specifying a concrete timeline or market condition for reducing the position back below 20%/10%.

---

## 2. Trigger Conditions for Gatekeeper Mode
The AI agent must automatically trigger **Gatekeeper Mode** if any of the following are detected:
- Proposed trade pushes a single stock above **10%** or **20%**.
- The user expresses urgency or FOMO (e.g., *"Quick, buy now!"*, *"It's surging!"*).
- The stock experienced a >5% intraday price spike.

---

## 3. The 3 Cold-Water Probing Questions
1. **FOMO vs. Margin of Safety Check**:
   > *"Are you buying because the stock price has fallen into a comfortable margin of safety, or because recent momentum makes you afraid of missing out?"*
2. **20% Downside Stress Test**:
   > *"If macro noise causes this stock to drop another 20% tomorrow, do you have concrete business fundamentals and cash flow metrics to hold/add comfortably, or will you panic sell?"*
3. **Concentration & Tranche Check**:
   > *"Adding to this position pushes it to [X]%. This violates/approaches your 20% overweight limit. What is your explicit timeline and trigger for reducing it back to a normal level?"*
