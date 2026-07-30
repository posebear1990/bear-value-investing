# Pre-Trade Anti-Impulse Protocol & Dynamic Concentration Limits

This document outlines the emotional circuit-breaker, pre-trade discipline protocol, and dynamic position concentration risk limits.

---

## 1. Dynamic Position Concentration Limits (动态仓位集中度红线规程)

To ensure the skill is generic and applicable across different investor risk profiles, **concentration limit values MUST NOT be hardcoded in the skill**. Instead, the AI agent MUST dynamically read the user's specific risk boundaries from the `concentration_limits` configuration object inside the user's portfolio repository (`portfolio.json`).

### Standard Configuration Schema (`portfolio.json`)
The skill expects the user's portfolio repository to define the following numeric thresholds:

```json
"concentration_limits": {
  "single_stock_normal_cap": 10.0,
  "single_stock_overweight_cap": 20.0,
  "single_stock_extreme_cap": 30.0,
  "cash_and_broad_etf_normal_min": 5.0,
  "cash_and_broad_etf_extreme_min": 0.0
}
```

### 3-Tier Risk Boundary Protocol
1. **Normal Range (常规区间)**: Stock position weight $\le$ `single_stock_normal_cap`.
2. **Overweight Range (超配区间)**: `single_stock_normal_cap` < Weight $\le$ `single_stock_overweight_cap`.
3. **Extreme Range (极限警告线)**: Weight > `single_stock_overweight_cap` (approaching or exceeding `single_stock_extreme_cap`).

### Extreme State Protocol (极限状态硬性机制)
Whenever a proposed buy or market shift pushes a position above `single_stock_overweight_cap` OR reduces combined cash & broad-market ETFs below `cash_and_broad_etf_normal_min`:
1. **Mandatory Interception**: The agent MUST flag a **Concentration Limit Exception Alert**.
2. **Explicit Rationale Requirement**: The user MUST provide a formal, documented justification for why this extreme allocation exists.
3. **Temporary Exception Notice**: The agent MUST record in the stock's `thesis.json` that:
   > *"This extreme position ([Current Weight]%) exceeds the configured overweight threshold ([Threshold]%). It is recorded as a TEMPORARY EXCEPTION, not a permanent condition."*
   The thesis MUST include a concrete reduction timeline or target trigger.

---

## 2. Trigger Conditions for Gatekeeper Mode
The AI agent must automatically trigger **Gatekeeper Mode** if any of the following are detected:
- Proposed trade pushes a single stock above the configured `single_stock_normal_cap` or `single_stock_overweight_cap`.
- The user expresses urgency or FOMO (e.g., *"Quick, buy now!"*, *"It's surging!"*).
- The stock experienced a >5% intraday price spike.

---

## 3. The 3 Cold-Water Probing Questions
1. **FOMO vs. Margin of Safety Check**:
   > *"Are you buying because the stock price has fallen into a comfortable margin of safety, or because recent momentum makes you afraid of missing out?"*
2. **20% Downside Stress Test**:
   > *"If macro noise causes this stock to drop another 20% tomorrow, do you have concrete business fundamentals and cash flow metrics to hold/add comfortably, or will you panic sell?"*
3. **Dynamic Concentration & Tranche Check**:
   > *"Adding to this position pushes it to [X]%. This approaches your configured extreme cap ([Extreme Cap]%). What is your explicit timeline and trigger for reducing it back below your normal cap ([Normal Cap]%)?"*
