# IB-Style DCF (Codex Skill)

A hardened, investment-banking-style **unlevered DCF** skill for Codex: explicit EBITDA→UFCF bridge, NWC sign conventions, WACC/beta mechanics (Hamada), terminal value guardrails, EV→equity bridge discipline, and sensitivity/scenario expectations.

[![GitHub stars](https://img.shields.io/github/stars/BillZhenZhang/Financial_Modeling_Skills?style=social)](https://github.com/BillZhenZhang/Financial_Modeling_Skills)
[![GitHub forks](https://img.shields.io/github/forks/BillZhenZhang/Financial_Modeling_Skills?style=social)](https://github.com/BillZhenZhang/Financial_Modeling_Skills/fork)
[![License](https://img.shields.io/github/license/BillZhenZhang/Financial_Modeling_Skills)](https://github.com/BillZhenZhang/Financial_Modeling_Skills/blob/HEAD/LICENSE)
[![Release](https://img.shields.io/github/v/release/BillZhenZhang/Financial_Modeling_Skills)](https://github.com/BillZhenZhang/Financial_Modeling_Skills/releases)


## What This Skill Does

This skill helps Codex **build or audit IB-style DCF models** with consistent guardrails and formulas:

- Builds **unlevered free cash flow (UFCF)** with an explicit **EBITDA → EBIT → NOPAT → UFCF** bridge (avoids missed/double-counted lines).
- Hardens **Net Working Capital (NWC)** treatment with explicit definitions and sign conventions (avoids sign errors).
- Builds **WACC** with practical input discipline (RFR, ERP, cost of debt) and **beta unlever/relever** using the **Hamada equation**.
- Enforces **discounting convention consistency** (mid-year vs year-end) and terminal value timing alignment.
- Enforces terminal-year **steady-state consistency** (“plowback rule” / CapEx vs D&A) and flags common TV pathologies.
- Produces IB-expected deliverables: **assumptions**, **valuation summary**, **EV→equity bridge**, and **2D sensitivities**.
- Runs a “model sanitization” pass to catch red flags (e.g., `g ≥ WACC`, TV concentration, circularity breaks).

## Who It’s For

- Investment banking / equity research / corporate finance analysts
- Anyone auditing or standardizing DCF logic and outputs
- People who want Codex to behave more like an IB analyst (clear assumptions, formulas, guardrails, and auditability)

## Installation

### Option A — Install via Codex skill installer (recommended)

On your machine with Codex installed, run:  

```
bash
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --repo <OWNER>/<REPO> \
  --path ib-style-dcf
```

### Option B — Manual install
1. Clone this repo
2. Copy the skill folder into your Codex skills directory:
```
bash
cp -R ib-style-dcf ~/.codex/skills/ib-style-dcf
Restart Codex
```

## How To Use
In Codex, invoke the skill by referencing $ib-style-dcf in your prompt.

**Example 1 — Build an IB-style DCF from inputs**
```
Use $ib-style-dcf to build an unlevered DCF.

Context:
- Currency: USD
- Units: $mm
- Forecast horizon: 5 years
- Discounting: mid-year
- Terminal value: Gordon Growth and Exit Multiple

Inputs:
- Historical income statement + D&A
- CapEx
- Working capital items (AR, inventory, AP, accrued, deferred revenue)
- Net debt definitions (cash, debt, other adjustments)
- Diluted shares / dilution schedule

Deliver:
- Key Assumptions
- UFCF bridge with explicit EBITDA→EBIT→NOPAT→UFCF math
- WACC build (show beta unlever/relever steps)
- Terminal value (both methods) + sanity checks
- EV→equity bridge and implied value per share
- Sensitivities: WACC × g and WACC × exit multiple
- Key risks / key drivers
```

**Example 2 — Audit an existing DCF**
```
Use $ib-style-dcf to audit this DCF for formula consistency and red flags.

I’m pasting:
- UFCF build
- WACC inputs
- terminal value page
- EV→equity bridge

Please identify:
1) what is wrong,
2) how to fix,
3) impact on EV / value per share.
```

## What Output To Expect
When used correctly, Codex should produce:

* A clean Key Assumptions section (tax rate type, WACC inputs, terminal assumptions, operating drivers)  
* A transparent UFCF bridge with correct signs (including ΔNWC)  
* A consistent discounting approach and properly timed terminal value discounting  
* A complete EV → equity → per-share bridge with clearly defined net debt + share count
* Sensitivity tables and a short interpretation of what drives the range
* A short sanitization report of any red flags discovered

## Validation (Optional)
The official skill validation script requires pyyaml:

```
python3 -m pip install --user pyyaml
python3 ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py ib-style-dcf

```
## Contributing
Contributions are welcome (issues and PRs). Great additions include:


* More edge-case handling (cyclicals, negative UFCF transitions, multi-segment)
* A clean Excel template (tabs/layout) consistent with the guardrails
* Sanitized example inputs/outputs to clarify “expected behavior”
Please do not include proprietary or confidential company data in issues or PRs.

## Citation
If this repo materially informs published work (research, teaching materials, internal methodology docs), please cite it.

## Suggested citation
Bill Zhen Zhang. (2026). Financial Modeling Skills (Codex) (Version X.Y.Z) [Software]. GitHub. https://github.com/BillZhenZhang/Financial_Modeling_Skills

## BibTeX
```
@software{zhen_zhang_financial_modeling_skills_2026,
  author  = {Bill Zhen Zhang},
  title   = {Financial Modeling Skills (Codex)},
  year    = {2026},
  version = {X.Y.Z},
  url     = {https://github.com/BillZhenZhang/Financial_Modeling_Skills},
}
```

## License
This project is licensed under the terms in LICENSE:
https://github.com/BillZhenZhang/Financial_Modeling_Skills/blob/HEAD/LICENSE

Disclaimer
This repository is for educational and workflow-standardization purposes only and does not constitute investment advice. Valuation is highly assumption-dependent.

