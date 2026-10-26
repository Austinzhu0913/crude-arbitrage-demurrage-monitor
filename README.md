# Physical Crude Arbitrage & Maritime Demurrage Sensitivity Monitor

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A desk-grade quantitative analytics framework for monitoring physical crude delivered CIF parity, maritime demurrage exposure, and forward curve roll risk on the Baltic TD3C route. 

The monitor evaluates how discharge bottlenecks, financing carry, and prompt market structure distort delivered CIF economics for Middle East crude flowing into China.

![Bloomberg Physical Desk Monitor](Crude.png)

---

## 1. Market Benchmarks & Bloomberg Terminal Metadata

All baseline parameters reflect official **Bloomberg Terminal** queries and **Baltic Exchange** fixtures:

| Parameter Category | Commercial Benchmark | Contract Ticker / Field | Settled Value | Quote Unit | Bloomberg Convention |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Shipping Route** | Baltic Dirty TD3C (MEG-China) | `TD3C Assessment` | 270,000 MT | VLCC Parcel | Baltic Fixture Standard |
| **Daily Demurrage** | Baltic VLCC Demurrage Rate | `TD3C Demurrage` | \$57,699 / day | USD / day | Contractual charterparty rate[cite: 7] |
| **Contractual Laytime** | Allowed Loading/Discharging | Standard SHINC | 72 running hours | 3.0 Days | Free time before demurrage |
| **Crude Prompt Benchmark** | ICE Brent Generic (M1) | `COZ6 Comdty` | \$116.72 / bbl | USD / bbl | `PX_LAST` (Dec 26 Delivery)[cite: 9, 10] |
| **Prompt Roll Spread** | ICE Brent M1-M2 Spread | `COZ6 - COF7` | -\$5.02 / bbl | USD / bbl | Steep prompt backwardation[cite: 9] |
| **Forward Curve Structure**| ICE Brent M1-M12 Spread | `COZ6 - COX7` | -\$28.87 / bbl | USD / bbl | Annual curve backwardation[cite: 9, 10] |
| **Dubai Cash Roll** | Dubai 1M vs 2M Spread | `DBL1 - DBL2` | -\$4.056 / bbl | USD / bbl | Terminal prompt spread[cite: 6] |

### Unit Conversion & Demurrage Mathematics
Demurrage accumulates strictly **after contractual laytime (72 hours)** is exhausted. The marginal cost per barrel is derived as:
$$\text{Demurrage Drag (\$/bbl/day)} = \frac{\text{Daily Demurrage Rate}}{\text{Parcel Volume}} = \frac{\$57,699}{2,000,000\text{ bbls}} \approx \$0.02885\text{ /bbl/day}$$

* **30-Day Port Congestion Impact**: $27 \text{ billable days} \times \$0.02885 \approx +\$0.779\text{ /bbl}$.

---

## 2. Desk Analytics Architecture

The dashboard is structured into three synchronized panels reflecting the full physical-to-financial risk transmission:

* **Panel 1: Physical Bottleneck & Demurrage Drag**: Tracks step-wise demurrage escalation across discrete port delay scenarios ($0\text{d}, 3\text{d}, 7\text{d}, 14\text{d}, 21\text{d}, 30\text{d}$).
* **Panel 2: Delivered CIF Cost Escalation**: Models delivered CIF cost inflation for Chinese coastal refiners, comparing **Hedged Quotation Periods (QP)** against **Unhedged Backwardation Exposure**.
* **Panel 3: ICE Brent Forward Curve Structure**: Visualizes the prompt-to-deferred discount across the 12-month forward curve ($-\$28.87\text{ /bbl}$ aggregate drop; prompt M1-M2 spread: $-\$5.02\text{ /bbl}$)[cite: 9, 10].

---

## 3. Physical Arbitrage Execution vs. Theoretical Spread

### Why "Paper Arbitrage" $\neq$ Executable Trading Margin
A common misconception in commodity quantitative modeling is assuming that a simple delivered differential guarantees executable arbitrage. A physical crude trading desk evaluates arbitrage viability via a complete netback balance:

$$\text{Arb Netback} = P_{\text{DES/CIF Destination}} - \left[ P_{\text{FOB Origin}} + \text{Flat Freight (WS)} + \text{Demurrage} + \text{Financing} + \text{Loss} + \text{Port/Canal Dues} \right]$$

### Key Institutional Realities:
1. **Pricing Windows (Quotation Periods, QP) & Hedging**:
   - Physical sales contracts rarely price on a single spot date; pricing typically averages Platts assessments across a 5-day window around bill of lading (B/L) or vessel arrival.
   - **Hedging Requirement**: Physical desks hedge floating flat price and roll risk using derivative swaps (e.g., Brent/Dubai EFS or prompt month swaps). Therefore, a 30-day delay does not automatically inflict a linear $\$0.167\text{/bbl/day}$ market erosion unless the position is left entirely unhedged across delivery months.
2. **Official Selling Price (OSP) Differentials**:
   - Middle Eastern grades (e.g., Arab Light, Upper Zakum) trade on monthly OSPs set by National Oil Companies (Aramco, ADNOC) as differentials to the Oman/Dubai benchmark, requiring basis risk adjustments.
3. **Floating Storage Arbitrage Criteria**:
   - Storing crude on a VLCC is economically justified **if and only if** the forward curve is in **Deep Contango** exceeding total carry costs:
   $$\text{Contango Spread (M2 - M1)} > \text{Daily Demurrage/Charter Rate} + \text{Cost of Capital (SOFR + Spread)} + \text{Insurance/Boil-off}$$
   - Under steep **Backwardation** ($-\$5.02\text{/bbl}$ M1-M2 spread)[cite: 9], time decay compounds with ship charter costs, strictly ruling out off-dock floating storage.

---

 ## 4. Sensitivity Analysis & Breakeven Conditions

A 30-day discharge delay introduces substantial balance-sheet drag:
* **Direct Demurrage Penalty**: +$0.779 /bbl
* **Financing Carry (at 5.5% APR over 51 total transit/berth days)**: +$0.897 /bbl
* **In-Transit Ocean Loss Allowance (0.2%)**: +$0.233 /bbl

**Breakeven Arbitrage Hurdle**: Destination refining netback must offer at least **+$4.41 /bbl premium over FOB origin pricing** (+$2.50 base freight + $1.91 delay and carry friction) to prevent structural margin erosion under prolonged congestion.
---

## 5. Project Structure & Quickstart

```text
crude-arbitrage-demurrage-monitor/
├── README.md        # Desk analytics methodology & limitations
├── requirements.txt # Pinned environment dependencies
├── crude.py         # Analytics engine, unit tests & visualizer
└── Crude.png        # High-resolution 3-panel monitor dashboard
```
Installation & Execution
```
```bash
# 1. Clone the repository
git clone [https://github.com/Austinzhu0913/crude-arbitrage-demurrage-monitor.git](https://github.com/Austinzhu0913/crude-arbitrage-demurrage-monitor.git)
cd crude-arbitrage-demurrage-monitor

# 2. Install dependencies
pip install -r requirements.txt

# 3. Run the engine and regenerate the monitor dashboard
python crude.py
```
