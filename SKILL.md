---
name: ib-style-dcf & commercial credit risk analytics
description: Build, audit, and explain investment-banking-style (IB) unlevered DCF valuation models with standard assumptions, formatting conventions, and sensitivity/scenario analysis. Use when asked to value a company/business with a DCF, compute WACC, forecast unlevered free cash flow (UFCF), calculate terminal value (Gordon Growth and/or exit multiple), bridge enterprise value to equity value and per-share value, reconcile to market comps, or debug an existing DCF model/spreadsheet (Excel/Google Sheets/CSV) and its formulas. It can be used for multiple financial statement modelling, for investment banking research and also commercial credit risk analytics.
---

# IB-Style DCF

## Overview

Produce an IB-style unlevered DCF with (1) transparent assumptions, (2) auditable math, (3) clean outputs (valuation range + sensitivities), and (4) explicit bridges/reconciliations.

## Workflow Decision Tree

1. If the user provides a model/spreadsheet, start by auditing and repairing it before rebuilding.
2. If the user provides only historicals, build a new DCF from scratch.
3. If key inputs are missing, pause and ask targeted intake questions; do not invent company-specific numbers.

## Intake (Ask These First)

- Confirm the subject: company name, industry, geography, currency, reporting standard (US GAAP/IFRS).
- Confirm timeline and units: fiscal year-end, historical years available, forecast horizon (typical: 5 years), units (e.g., $ in millions).
- Confirm valuation goal: intrinsic value range, price target, fairness view, or “sanity-check an existing DCF.”
- Confirm capital structure items: net debt components, minority interest, investments, pensions, leases (if relevant), diluted shares.
- Confirm methodology preferences:
  - Terminal value: Gordon Growth, exit multiple, or both (typical: both).
  - Discounting: mid-year convention vs year-end (typical: mid-year).
  - FCF definition: UFCF to firm (standard) and exact line items to include/exclude.
  - Treatment of SBC, leases, restructuring, and other adjustments (only if user requests).

## Build The Model (IB Structure)

### 1) Set up model conventions

- Lock currency, units, and sign conventions (e.g., cash inflows positive; capex as a cash outflow; show costs as positive “expense” lines but handle correctly in cash flow).
- Separate **inputs/assumptions** from **calculations** from **outputs**.
- Keep formulas simple and traceable; avoid hardcoding inside formulas where possible.

### 2) Normalize historicals (if provided)

- Build a clean historical P&L view suitable for drivers (Revenue, COGS, OpEx, D&A, EBIT).
- Build working-capital and capex views at the level needed for forecasting.
- Call out non-recurring items and whether the user wants to normalize them.

### 3) Forecast operating drivers

- Forecast revenue using explicit drivers when possible (volume × price, subs × ARPU, etc.).
- Forecast margins and expenses with clear logic (e.g., % of revenue, per-unit, step functions).
- Forecast D&A, capex, and net working capital with consistent linkages (avoid circularities unless explicitly intended).

### 4) Net working capital (NWC): definitions + sign convention (do not skip)

Define NWC and the sign logic explicitly to avoid sign errors.

- Default operating definition (adjust only if the user specifies otherwise):
  - Current assets (operating): AR, inventory, other operating current assets.
  - Current liabilities (operating): AP, accrued expenses, deferred revenue/contract liabilities (if applicable), other operating current liabilities.
  - Exclude: cash and equivalents, short-term debt, current portion of long-term debt, and any non-operating items unless explicitly included.
- Formula:

  $$\text{NWC}_t = (\text{Current Assets} - \text{Current Liabilities})_t$$

  $$\Delta\text{NWC}_t = \text{NWC}_t - \text{NWC}_{t-1}$$

- Cash-flow rule (directionality):
  - Increase in operating current assets = **use of cash** ⇒ reduces UFCF (negative impact).
  - Increase in operating current liabilities = **source of cash** ⇒ increases UFCF (positive impact).
  - Therefore: subtract $\Delta\text{NWC}$ in UFCF (see UFCF formula below); a positive $\Delta\text{NWC}$ reduces UFCF.

### 5) Compute unlevered free cash flow (UFCF) with an explicit EBITDA → UFCF bridge

Show the bridge explicitly so no line items are missed and the tax logic is consistent.

**Core identities (use these exact formulas):**

$$\text{EBITDA} = \text{EBIT} + \text{D\\&A}$$

$$\text{EBIT} = \text{EBITDA} - \text{D\\&A}$$

$$\text{NOPAT} = \text{EBIT} \times (1 - \text{Tax Rate})$$

$$\text{UFCF} = \text{NOPAT} + \text{D\\&A} - \text{CapEx} - \Delta\text{NWC}$$

**Implementation checklist:**

- Start from EBIT (or EBITDA) and explicitly arrive at UFCF via NOPAT; do not “mix” after-tax and pre-tax lines.
- Use an explicit tax rate input and label it as **marginal** vs **effective**; default to marginal for valuation unless user specifies otherwise.
- Ensure D&A is added back only once (common error: adding back D&A after starting from EBITDA without subtracting it first).

### 6) Build WACC (hardened inputs + beta methodology)

**Risk-free rate (RFR):**

- Match currency and “duration” of cash flows; by default use the **10-year government bond** in the valuation currency (e.g., U.S. 10-year Treasury for USD).
- If valuing in a non-USD currency, use that currency’s sovereign curve (or swap curve if that is the user’s standard), not the U.S. 10-year.

**Equity risk premium (ERP):**

- Prefer user/firm-standard ERP if provided; otherwise ask for it or state a sourced assumption and treat it as a key sensitivity.
- Keep ERP consistent with the market/country of the risk-free rate and cash flows.
- Avoid double-counting country risk: if adding a country risk premium, do it once (either in ERP/CoE or via explicit cash-flow adjustments), not both.

**Beta (unlever/relever; Hamada):**

- Use trading comps to estimate $\beta_U$ (unlevered beta) when possible; avoid using a single raw levered beta without context.
- For each comp, compute (or accept) a levered beta $\beta_L$, then unlever and take a central tendency (median typical), then relever to the target capital structure.
- Hamada relevering relationship (use the marginal tax rate $t$):

  $$\beta_L = \beta_U \times \left[1 + (1 - t)\times\left(\frac{D}{E}\right)\right]$$

  Therefore, unlever as:

  $$\beta_U = \frac{\beta_L}{1 + (1 - t)\times\left(\frac{D}{E}\right)}$$

- Use market-value weights for $D/E$ if available; be explicit about the target $D/E$ used for the subject company.

**Cost of equity and cost of debt:**

- Compute cost of equity via CAPM using the selected RFR, ERP, and relevered $\beta_L$ (add size/company-specific premia only if explicitly justified).
- Set cost of debt pre-tax from observable market data when possible (YTM on the company’s debt, credit spreads, or a synthetic rating approach); apply the tax shield using the marginal tax rate.
- Show WACC weights (market-value preferred) and the final WACC clearly.

### 7) Discount UFCF to PV

Make timing explicit and stay consistent throughout the model.

- Choose **year-end** or **mid-year** convention and state it in the assumptions table (mid-year is typical in IB models).
- Discount factors (define $t=1,2,\ldots,N$ as forecast years):
  - Year-end: $$\text{DF}_t = \frac{1}{(1+\text{WACC})^{t}}$$
  - Mid-year: $$\text{DF}_t = \frac{1}{(1+\text{WACC})^{t-0.5}}$$
- Present:
  - $$\text{PV}(\text{UFCF}_t)=\text{UFCF}_t\times \text{DF}_t$$
  - PV of terminal value as a separate line item.
- Terminal value timing guardrail (common failure mode):
  - If terminal value is modeled at **end of year $N$**, discount it using the **end-of-year** timing for $N$ (do not silently discount TV using a mid-year factor unless you explicitly define TV as a mid-year value).

### 8) Terminal value + terminal-state consistency (“plowback” rule)

Provide both when requested (or propose both by default):

- **Gordon Growth**: pick a long-term growth rate consistent with inflation/GDP/industry; ensure `g < WACC`.
- **Exit Multiple**: define the multiple (e.g., EV/EBITDA), the base metric year, and apply to terminal-year (or forward) metric consistently.
- Reconcile/compare the implied perpetuity growth from the exit multiple (and vice versa) if useful for sanity.

**Terminal steady state requirement (do not skip):**

- Check the terminal year is not “broken” by forcing a steady-state reinvestment profile:
  - D&A should be roughly in the same neighborhood as CapEx in the terminal year (steady-state maintenance assumption), unless the user explicitly models a different steady-state (e.g., long-run growth requires sustained growth CapEx).
  - Working capital intensity should stabilize (avoid one-off terminal-year swings that mechanically inflate/deflate UFCF).
- If using Gordon Growth, ensure the reinvestment implied by growth is directionally consistent with margins/ROIC assumptions; flag inconsistencies.
  - Useful identity (if ROIC is available): $$g \approx \text{ROIC}\times \text{Reinvestment Rate}$$ (so $\text{Reinvestment Rate}\approx g/\text{ROIC}$).

### 9) Enterprise value → equity value bridge

Do not hand-wave this step: explicitly define each bridge component and the share count used.

- Enterprise value: $$\text{EV}=\sum_{t=1}^{N}\text{PV}(\text{UFCF}_t) + \text{PV}(\text{TV})$$
- Net debt (default): total debt (ST + LT + revolver drawn) minus cash & equivalents.
  - Be explicit about what “cash” means (operating cash vs restricted; whether to net out non-operating investments).
  - Do not mix “gross debt” and “net debt” definitions across comps/DCF.
- Other claims / adjustments (include only if applicable and defined):
  - Preferred equity, minority interest, pension deficits, lease-related debt-like items, non-controlling interests.
  - Non-operating assets (equity investments, excess cash) as add-backs if they are truly non-operating and valued separately.
- Equity value:

  $$\text{Equity Value}=\text{EV}-\text{Net Debt}-\text{Other Claims}+\text{Non-operating Assets}$$

- Per-share value (diluted):
  - Use diluted shares consistent with the equity-bridge date (and currency) and state the method (basic vs fully diluted).
  - If options/RSUs/convertibles matter, ask for the diluted share schedule (or the treasury stock method inputs) rather than guessing.

### 10) Sensitivities and scenarios (IB expectation)

- At minimum: 2D table of **WACC × terminal growth** (Gordon) and/or **WACC × exit multiple** (multiple method).
- Optionally: operating sensitivities (revenue CAGR, margin, capex intensity) if that is the core debate.
- Report valuation ranges and identify key drivers.

## Output Requirements (Default)

Deliver in a consistent, memo-like structure with clear tables (IB expectation):

- **Key Assumptions**: tax rate (marginal/effective), WACC build (RFR/ERP/beta/leverage/CoD), terminal assumptions, key operating drivers.
- **Operating Forecast**: revenue drivers + margins (high level; enough to audit).
- **UFCF Bridge**: explicit EBITDA→EBIT→NOPAT→UFCF math and a clear $\Delta\text{NWC}$ line.
- **Valuation Summary**: PV(UFCF), PV(TV), EV, equity bridge items, equity value, diluted shares, implied value/share (with ranges).
- **Sensitivities**: 2D tables and short interpretation (what drives the range).
- If the user supplied a spreadsheet: list each fix with “what was wrong → what changed → impact.”

## Model Sanitization (Common Red Flags)

Before delivering outputs, actively search for these failure modes and fix or flag them:

- Circular reference breaks or hidden circularity (especially around interest, revolver, and taxes) that produce unstable results.
- Mixed units ($ vs $mm), mixed periods (FY vs LTM), or inconsistent year labels (FY2026E meaning different end dates).
- Sign errors in CapEx, $\Delta\text{NWC}$, or non-cash addbacks that invert cash flow direction.
- Double-counting D&A (starting from EBITDA but still adding back D&A without subtracting it first).
- Terminal growth violation: `g ≥ WACC` in Gordon Growth.
- Terminal value concentration that is implausibly high (rule of thumb: flag if terminal value PV > 80% of total EV and explain why).
- Discounting mismatch (mid-year vs year-end) between cash flows and terminal value.
- Tax-rate mismatch (using historical effective rate when a marginal valuation tax rate is intended).

## Quality Checks (Do Before Finalizing)

- Verify signs and units (no silent $ vs $mm errors).
- Tax consistency: use the **marginal** tax rate for “tax on EBIT” unless the user explicitly requests an effective-rate approach.
- Confirm `g < WACC` for perpetuity growth (Gordon Growth).
- Confirm terminal value contribution is reasonable; flag if it dominates (and why).
- Check NWC logic directionally (growth usually consumes cash unless business model differs).
- SBC treatment: state explicitly whether SBC is treated as a real operating cost (typical IB default) vs a non-cash add-back with a corresponding dilution/share-count treatment; do not mix treatments.
- Confirm bridge items (net debt, shares) match the user’s definitions/sources.
- Implied multiples: compute the implied EV/EBITDA (or EV/EBIT) multiple from the Gordon Growth terminal value and sanity-check vs market reality.
- Discounting consistency: ensure the timing convention for UFCF discounting matches the terminal value timing; flag any mid-year/year-end mixing.
- Provide a brief “audit trail” so the model can be rebuilt.

## Scope & Edge Cases (When To Slow Down)

- Financial institutions (banks/insurers): standard unlevered UFCF/WACC DCF is often inappropriate; ask before proceeding and consider dividend/excess-return approaches instead.
- Early-stage or negative/volatile UFCF: extend the explicit forecast until cash flows stabilize; rely more on scenario analysis and be cautious with terminal value dominance.
- Cyclicals/commodities: consider mid-cycle margins and multi-scenario cases; avoid extrapolating peak/trough economics into perpetuity.

## Communication Style (IB-Style)

- Be explicit about assumptions and their sources (user-provided vs placeholder vs estimated).
- Avoid false precision; show ranges.
- Keep a professional, memo-like tone in summaries: “Key Assumptions”, “Valuation Summary”, “Sensitivities”, “Key Risks”.

## Technical Appendix (Formula Guardrails)

Use this section as the final backstop when auditing or rebuilding the model.

**EBITDA / EBIT / NOPAT / UFCF:**

Assume $\text{OpEx}$ excludes D\&A unless explicitly defined otherwise.

$$\text{EBIT} = \text{Revenue} - \text{COGS} - \text{OpEx} - \text{D\\&A}$$

$$\text{EBITDA} = \text{EBIT} + \text{D\\&A}$$

$$\text{EBIT} = \text{EBITDA} - \text{D\\&A}$$

$$\text{NOPAT} = \text{EBIT} \times (1 - \text{Tax Rate})$$

$$\text{UFCF} = \text{NOPAT} + \text{D\\&A} - \text{CapEx} - \Delta\text{NWC}$$

**WACC + CAPM (core):**

$$\text{Cost of Equity} = \text{RFR} + \beta_L\times\text{ERP}$$

$$\text{WACC} = \left(\frac{E}{D+E}\right)\times \text{CoE} + \left(\frac{D}{D+E}\right)\times \text{CoD}\times(1-t)$$

**Discount factors (repeat):**

Year-end: $$\text{DF}_t = \frac{1}{(1+\text{WACC})^{t}}$$

Mid-year: $$\text{DF}_t = \frac{1}{(1+\text{WACC})^{t-0.5}}$$

If terminal value is as-of **end of year $N$**, discount it consistently:

$$\text{PV}(\text{TV}_N)=\frac{\text{TV}_N}{(1+\text{WACC})^{N}}$$

**Terminal value (Gordon Growth) + implied multiple sanity:**

$$\text{TV}_t = \frac{\text{UFCF}_{t+1}}{\text{WACC} - g} = \frac{\text{UFCF}_t \times (1 + g)}{\text{WACC} - g}$$

$$\text{Implied EV/EBITDA}_t = \frac{\text{TV}_t}{\text{EBITDA}_t}$$

**Net working capital deltas (repeat):**

$$\Delta\text{NWC}_t = (\text{Current Assets} - \text{Current Liabilities})_t - (\text{Current Assets} - \text{Current Liabilities})_{t-1}$$

**Beta relevering (Hamada; repeat):**

$$\beta_L = \beta_U \times \left[1 + (1 - t)\times\left(\frac{D}{E}\right)\right]$$
