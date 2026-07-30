# Interactive DCF Parameter Probing Guide

This document guides the AI agent on how to interactively discuss and challenge DCF (Discounted Cash Flow) parameters with the user for growth company analysis.

---

## Core Philosophy of DCF Probing
The objective of DCF analysis in this framework is **NOT** to compute a precise intrinsic value to two decimal places. Instead, it is a **stress-testing tool** to understand:
1. How realistic are the market's growth expectations?
2. How durable is the company's competitive advantage under different scenarios?
3. How sensitive is the business valuation to changes in interest rates, margins, or growth deceleration?

---

## Interactive Discussion Protocol

### Stage 1: Free Cash Flow (FCF) Base & Quality Check
- **Questioning**: Start from Net Income vs. Operating Cash Flow (OCF) vs. CapEx.
- **Probe**: "Does the company's reported profit convert cleanly into Free Cash Flow (OCF - CapEx)? Are there excessive capital expenditures required just to maintain current operations (Maintenance CapEx vs. Growth CapEx)?"

### Stage 2: Near-Term Growth Rate (Years 1-5)
- **Discussion Point**: Discuss reasonable FCF growth rates for the next 3-5 years.
- **Challenge Prompts**:
  - If proposed growth > 15%: *"What industry macro trends or market share gains support this rate? Is the overall TAM (Total Addressable Market) large enough?"*
  - If profit growth > revenue growth: *"Is margin expansion coming from pricing power, economies of scale, or temporary cost-cutting?"*

### Stage 3: Discount Rate (WACC / Required Rate of Return)
- **Discussion Point**: Determine the appropriate discount rate based on macro interest rates and business risk.
- **Challenge Prompts**:
  - *"Given the current risk-free rate and company capital structure, should we use a standard 8%-10% discount rate, or apply a higher equity risk premium due to operational/geopolitical risks?"*

### Stage 4: Terminal Growth & Moat Decay Rate
- **Discussion Point**: Long-term perpetual growth rate (typically 2%-3%, capped by GDP growth).
- **Challenge Prompts**:
  - *"As the company matures past year 5, will its competitive moat allow it to maintain above-GDP growth, or will return on invested capital (ROIC) mean-revert due to competition?"*

---

## Output Breakdown & Takeaways
After the multi-turn discussion, summarize the findings into 3 insights:
1. **Implied Market Expectations**: What growth rate is the current stock price pricing in?
2. **Moat Durability & Earnings Stability**: Can the company sustain profitability if growth slows down?
3. **Valuation Sensitivity**: How much does intrinsic value change if growth slows by 5% or WACC rises by 1%?
