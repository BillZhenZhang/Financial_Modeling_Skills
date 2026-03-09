# IB-Style DCF (Codex Skill)

A hardened, investment-banking-style **unlevered DCF** skill for Codex: explicit EBITDA→UFCF bridge, NWC sign conventions, WACC/beta mechanics (Hamada), terminal value guardrails, EV→equity bridge discipline, and sensitivity/scenario expectations.

> Replace `<OWNER>` and `<REPO>` in the badge links below.

[![GitHub stars](https://img.shields.io/github/stars/<BillZhenZhang>/<Financial_Modeling_Skills>?style=social)](https://github.com/<BillZhenZhang>/<Financial_Modeling_Skills>)
[![GitHub forks](https://img.shields.io/github/forks/<BillZhenZhang>/<Financial_Modeling_Skills>?style=social)](https://github.com/<BillZhenZhang>/<REPO>/fork)
[![License](https://img.shields.io/github/license/<BillZhenZhang>/<Financial_Modeling_Skills>)](https://github.com/<BillZhenZhang>/<Financial_Modeling_Skills>/blob/main/LICENSE)
[![Release](https://img.shields.io/github/v/release/<BillZhenZhang>/<Financial_Modeling_Skills>)](https://github.com/<BillZhenZhang>/<Financial_Modeling_Skills>/releases)

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

```bash
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --repo <OWNER>/<REPO> \
  --path ib-style-dcf

