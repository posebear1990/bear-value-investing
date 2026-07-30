# Input/Output Contracts & Guided Prompting Specifications

This document defines the strict input/output formats and required dialog prompts for the agent during each phase of the investment lifecycle.

---

## 1. Initial Research Input Contract (`INIT_BUY`)

When a user initiates research on a company, the agent MUST request the following structured dataset if not already provided:

### Required Inputs
- **Ticker / Name**: e.g., `AAPL`, `0700.HK`, `600519.SH`
- **Time Horizon**: Long-term (3-5+ years) vs. medium-term
- **Financial Data Points**:
  - Most recent Annual Report (10-K / Annual FY)
  - Most recent Quarterly Report (10-Q / Q1-Q3)
  - Current Operating Free Cash Flow (OCF - CapEx)
  - Balance Sheet Cash vs Total Debt

---

## 2. Guided Question Templates

### Template A: Business & Moat Probe
> "Before we talk numbers, let's establish the company's competitive moat:
> 1. How does **[Company Name]** make money, and who are its primary paying customers?
> 2. What prevents a competitor with 10x capital from copying their business tomorrow?"

### Template B: Growth Company DCF Parameter Probe
> "Let's stress-test the DCF assumptions for **[Company Name]**:
> - **Base FCF**: $[X] Million.
> - **Proposed 5-Year Growth**: [Y]%.
> - **Probe**: What level of CapEx is required to sustain [Y]% growth? If industry growth is only [Z]%, where will the market share gain come from?"

### Template C: Stable Company Valuation Probe
> "For **[Company Name]**:
> - Current P/E: [X] (Historical Percentile: [Y]%)
> - Net Cash Ratio: [Z]% of Market Cap
> - Expected Dividend + Buyback Yield: [W]%
> - **Probe**: Is the current dividend fully covered by Free Cash Flow (FCF Payout Ratio < 70%)?"

---

## 3. Thesis Persistence Output Contract

When completing an initial buy evaluation, the agent MUST write a JSON file to `investment_journal/<TICKER>/thesis.json` following `spec/thesis_schema.json`.
