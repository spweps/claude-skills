---
name: creating-financial-models
description: This skill provides an advanced financial modeling suite with DCF analysis, sensitivity testing, Monte Carlo simulations, and scenario planning for investment decisions
---

# Financial Modeling Suite

A comprehensive financial modeling toolkit for investment analysis, valuation, and risk assessment using industry-standard methodologies.

## Core Capabilities

### 1. Discounted Cash Flow (DCF) Analysis
- Build complete DCF models with multiple growth scenarios
- Calculate terminal values using perpetuity growth and exit multiple methods
- Determine weighted average cost of capital (WACC)
- Generate enterprise and equity valuations

### 2. Sensitivity Analysis
- Test key assumptions impact on valuation
- Create data tables for multiple variables
- Generate tornado charts for sensitivity ranking
- Identify critical value drivers

### 3. Monte Carlo Simulation
- Run thousands of probability-distribution scenarios
- Model uncertainty in key inputs
- Generate confidence intervals for valuations
- Calculate probability of achieving targets

### 4. Scenario Planning
- Build best/base/worst case scenarios
- Model different economic environments
- Test strategic alternatives
- Compare outcome probabilities

## Input Requirements

### For DCF Analysis
- Historical financial statements (3-5 years)
- Revenue growth assumptions
- Operating margin projections
- Capital expenditure forecasts
- Working capital requirements
- Terminal growth rate or exit multiple
- Discount rate components (risk-free rate, beta, market premium)

### For Sensitivity Analysis
- Base case model
- Variable ranges to test
- Key metrics to track

### For Monte Carlo Simulation
- Probability distributions for uncertain variables
- Correlation assumptions between variables
- Number of iterations (typically 1,000-10,000)

### For Scenario Planning
- Scenario definitions and assumptions
- Probability weights for scenarios
- Key performance indicators to track

## Output Formats

### DCF Model Output
- Complete financial projections
- Free cash flow calculations
- Terminal value computation
- Enterprise and equity value summary
- Valuation multiples implied
- Excel workbook with full model

### Sensitivity Analysis Output
- Sensitivity tables showing value ranges
- Tornado chart of key drivers
- Break-even analysis
- Charts showing relationships

### Monte Carlo Output
- Probability distribution of valuations
- Confidence intervals (e.g., 90%, 95%)
- Statistical summary (mean, median, std dev)
- Risk metrics (VaR, probability of loss)

### Scenario Planning Output
- Scenario comparison table
- Probability-weighted expected values
- Decision tree visualization
- Risk-return profiles

## Model Types Supported

1. **Corporate Valuation** — mature companies, growth companies, turnarounds
2. **Project Finance** — infrastructure, real estate, energy
3. **M&A Analysis** — acquisition valuations, synergy modeling, accretion/dilution
4. **LBO Models** — leveraged buyout analysis, IRR/MOIC returns, debt capacity

## Best Practices Applied

- Consistent formatting and structure; clear assumption documentation
- Separation of inputs, calculations, outputs
- Multiple valuation methods for triangulation
- Probability-weighted scenarios; stress testing; confidence intervals

## Example Usage

"Build a DCF model for this technology company using the attached financials"

"Run a Monte Carlo simulation on this acquisition model with 5,000 iterations"

"Create sensitivity analysis showing impact of growth rate and WACC on valuation"

"Develop three scenarios for this expansion project with probability weights"

## Scripts Included

- `scripts/dcf_model.py` — DCF valuation engine (CAPM WACC, FCF projection, terminal value, equity bridge, sensitivity)
- `scripts/sensitivity_analysis.py` — one-way, two-way, tornado, scenario, and breakeven analysis

## Security Audit

Source: `https://github.com/anthropics/claude-cookbooks` — Anthropic first-party repo. Audit: PASS.
Scripts use `numpy` and `pandas` only; no network calls; no file writes; deterministic math.

## Limitations

- Models are only as good as their assumptions
- Not a substitute for professional financial advice
- Industry benchmarks are general guidelines; adjust for sector context
