# Physical Crude Arbitrage & Maritime Demurrage Sensitivity Monitor

![Bloomberg Physical Desk Monitor](bloomberg_crude_monitor.png)

A desk-grade quantitative analytics framework for monitoring physical crude arbitrage economics and maritime demurrage sensitivity on the Baltic TD3C route. The monitor is designed to quantify how discharge bottlenecks, prompt market decay, and freight timing distort delivered CIF parity for Middle East crude flowing into China.

---

### Current Market Construct (Bloomberg Terminal Actuals)
* **Shipping Benchmark**: Baltic Dirty Tanker TD3C (MEG to China | 270,000 MT / 2.0M bbls VLCC)
* **Daily Demurrage Assessment**: **$57,699 / day** (Translates to ~$0.02885/bbl/day marginal freight drag)
* **Contractual Laytime Baseline**: 72 running hours (3.0 days allowed laytime SHINC/SHEX)
* **Paper Term Structure**: ICE Brent Generic Futures forward curve (CO1 to CO12) in severe Backwardation ($116.72/bbl down to $87.85/bbl; 12-month spread: -$28.87/bbl; prompt M1-M2 spread: -$5.02/bbl)

---

## Executive Summary

The physical crude value chain for a VLCC fixture from the Middle East Gulf (MEG) to China is structurally sensitive to voyage timing, berth congestion, and clearance bottlenecks. In a steep **Backwardation** market regime, prompt barrels are priced at a substantial premium over deferred barrels. 

This market structure introduces a severe compounding penalty:
1. **Direct Freight Drag**: Accumulation of contractual demurrage penalties payable to the shipowner.
2. **Prompt Market Decay**: Unhedged physical cargo values suffer steep mark-to-market depreciation for every day delivery is delayed.

This system resolves a fundamental physical trading dilemma:
> **"Does the headline arbitrage spread justify execution once dynamic demurrage penalties and rapid prompt curve decay are accounted for?"**

---

## Desk Analytics Architecture

The dashboard is structured into three synchronized panels reflecting the full physical-to-financial risk transmission:

### Panel 1: Physical Bottleneck & Demurrage Drag
* **Operational Logic**: Port waiting times are treated as dynamic balance-sheet costs. Demurrage penalties trigger strictly after the contractual 72-hour (3.0-day) free laytime allowance.
* **Quantification**: On a 2.0M bbl VLCC, demurrage accumulates at **+$0.02885/bbl/day**, reaching **+$0.779/bbl** at 30 days of congestion.

### Panel 2: Delivered CIF Cost Escalation
* **Refinery Economics**: Models landed cost inflation for Chinese coastal refiners from a baseline CIF delivered parity of **$119.22/bbl** (M1 base + $2.50/bbl baseline freight/insurance).
* **Margin Impact**: A 30-day discharge bottleneck drives delivered parity to **$120.00/bbl**, directly squeezing refinery margins and landed netbacks.

### Panel 3: ICE Brent Forward Curve & Term Structure Penalty
* **Paper Term Structure**: Ingests the full 12-month ICE Brent futures curve (COZ6 through COX7), demonstrating a steep aggregate drop of **-$28.87/bbl**.
* **Decay Comparison**: With a prompt M1-M2 spread penalty of **-$5.02/bbl** (approx. -$0.167/bbl/day), prompt time decay outweighs freight demurrage by nearly 6:1, strictly ruling out off-dock floating storage and demanding expedited discharge.

---

## Project Structure

```text
crude-arbitrage-demurrage-monitor/
├── README.md                     # Desk analytics documentation
├── requirements.txt              # Pinned environment dependencies
├── crude_arbitrage_monitor.py    # Quantification engine & Bloomberg UI renderer
└── bloomberg_crude_monitor.png   # High-resolution 3-panel monitor dashboard

```

---

## Quickstart

### Prerequisites

* Python 3.9+
* `matplotlib>=3.7.0`, `numpy>=1.24.0`

### Installation & Execution

```bash
# 1. Clone the repository
git clone [https://github.com/Austinzhu0913/crude-arbitrage-demurrage-monitor.git](https://github.com/Austinzhu0913/crude-arbitrage-demurrage-monitor.git)
cd crude-arbitrage-demurrage-monitor

# 2. Install dependencies
pip install -r requirements.txt

# 3. Run the desk engine and regenerate the Bloomberg monitor
python crude_arbitrage_monitor.py

```

---

## Commercial Takeaways

* **Demurrage is secondary; term structure decay dominates**: In severe backwardation, while freight demurrage burns ~$0.0288/bbl/day, prompt paper erosion destroys margin at >$0.16/bbl/day.
* **Strict Laytime Management**: Physical trading operations must prioritize berth pre-clearance and tight laytime terms over floating storage optionality.

