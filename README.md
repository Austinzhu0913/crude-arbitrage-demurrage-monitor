# Physical Crude Delivered Economics & Demurrage Sensitivity Prototype

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

An independent quantitative research prototype analyzing landed CIF delivered parity, maritime demurrage sensitivity, and term structure roll exposures for Middle East crude flowing into China (Baltic TD3C route). 

The model evaluates how discharge bottlenecks, carry financing, and forward market structure distort delivered costs relative to theoretical headline spreads.

![Physical Desk Monitor](Crude.png)

---

## 1. Baseline Assumptions & Benchmark Metadata

The model utilizes historical settlement data and charterparty conventions as fixed simulation parameters. Figures are grounded in terminal settlement snapshots and industry guidelines:

| Parameter Category | Industry Benchmark | Contract / Field | Baseline Value | Units | Source & Nature of Data |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Shipping Route** | Baltic Dirty Tanker TD3C | MEG to China | 270,000 MT | Parcel Size | Standard VLCC cargo (~2.0M bbls) |
| **Demurrage Rate** | Baltic VLCC Assessment | TD3C Daily Assessment | \$57,699 | USD / day | Static historical charterparty rate[cite: 7] |
| **Contractual Laytime**| Allowed Loading/Discharge | Standard SHINC Terms | 72.0 | Hours (3 days)| Free time before demurrage begins |
| **Crude Prompt Price** | ICE Brent Futures (M1) | `COZ6 Comdty` | \$116.72 | USD / bbl | Settlement snapshot (Dec 26 delivery)[cite: 9] |
| **Prompt Roll Spread** | ICE Brent M1-M2 Spread | `COZ6 - COF7` | -\$5.02 | USD / bbl | Backwardation snapshot[cite: 9] |
| **12-Month Curve** | ICE Brent M1-M12 Spread | `COZ6 - COX7` | -\$28.87 | USD / bbl | Full forward curve structure[cite: 9] |
| **Dubai Cash Roll** | Dubai 1M vs 2M Spread | `DBL1 - DBL2` | -\$4.056 | USD / bbl | Cash market spread snapshot[cite: 6] |

*Note on Data Ingestion*: This repository is an offline simulation prototype. Input variables are statically calibrated from representative terminal snapshots rather than a live, automated Bloomberg API feed.

---

## 2. Mathematical Framework & Demurrage Mechanics

### Per-Barrel Demurrage Conversion
Demurrage triggers strictly after the allowed laytime ($72\text{ hours} = 3.0\text{ days}$) is exceeded:
$$\text{Marginal Demurrage (\$/bbl/day)} = \frac{\text{Daily Demurrage Rate}}{\text{Parcel Volume}} = \frac{\$57,699}{2,000,000\text{ bbls}} \approx \$0.02885\text{ /bbl/day}$$

* Under a **30-day port queue** (27 billable demurrage days):
  $$\text{Demurrage Drag} = 27\text{ days} \times \$0.02885\text{ /bbl/day} \approx +\$0.779\text{ /bbl}$$

---

## 3. Modular CIF Landed Cost & Sensitivity Analysis

The model evaluates a hypothetical 30-day congestion scenario (21-day sea voyage + 30-day waiting queue = 51 total financing days) based on standardized desk rules of thumb:

* **Fixed Logistics & Port Charges**:
  * Baseline Ocean Freight (Baltic TD3C baseline): `+$2.250 /bbl`
  * Coastal Port Dues, Pilotage & Towage: `+$0.180 /bbl`
* **Operational Demurrage Drag**:
  * Contractual Demurrage (27 billable days @ $57,699/day): `+$0.779 /bbl`
* **Financing, Loss & Cargo Administration**:
  * Working Capital Cost (5.5% APR on $116.72/bbl FOB over 51 days): `+$0.897 /bbl`
  * Documentary Letter of Credit (DLC) & Issuance (0.20% flat): `+$0.233 /bbl`
  * Contractual Ocean Loss Allowance (0.20%) & Marine Insurance (0.03%): `+$0.268 /bbl`
  * Independent Cargo Surveyor & Laboratory Assay (SGS/Saybolt): `+$0.015 /bbl`

### Estimated Breakeven Parity Hurdle
$$\text{Landed Hurdle}_{\text{Hedged}} \approx \$2.43\text{ (Freight/Port)} + \$0.78\text{ (Demurrage)} + \$1.41\text{ (Carry/Admin)} = \mathbf{+\$4.62\text{ /bbl}}$$

Under these assumptions, the destination refining netback requires an estimated premium of at least **+$4.62 /bbl over FOB origin pricing** to achieve economic breakeven, assuming Quotation Periods (QP) are fully matched and hedged.

---

## 4. Physical Trading Realities & Model Boundaries

1. **Quotation Periods (QP) & Calendar Roll Exposure**:
   - Physical sales contracts are typically indexed to specific pricing windows (e.g., bill of lading date $+/-$ days or delivery month averages).
   - If a cargo is priced on arrival and delayed into a deferred delivery month without executing timespread hedges (e.g., Brent Calendar Swaps), the position faces front-month backwardation roll exposure (M1-M2: -$5.02 /bbl)[cite: 9]. The actual economic impact depends on the specific QP contract clause and dynamic market movements over the holding period, rather than a deterministic linear decay.
2. **Floating Storage Feasibility**:
   - In steep backwardation, floating storage is generally economically unfavorable because deferred barrels trade at a steep discount to prompt barrels, while ship charter and financing costs continue to accumulate.
   - However, floating storage decisions also depend on asset opportunity costs, optionality in sales contracts, blending economics, and port access constraints.
3. **Basis & Quality Differentials**:
   - Arbitrage viability also incorporates Official Selling Price (OSP) differentials set by National Oil Companies (Aramco, ADNOC) against Oman/Dubai, as well as API gravity and sulfur yield adjustments at destination refineries.

---

## 5. Project Structure & Quickstart

```text
crude-arbitrage-demurrage-monitor/
├── README.md        # Research documentation & model parameters
├── requirements.txt # Pinned environment dependencies
├── crude.py         # Simulation engine & 3-panel monitor renderer
└── Crude.png        # Generated 3-panel dashboard
