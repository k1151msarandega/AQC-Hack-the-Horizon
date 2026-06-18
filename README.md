# AQC Hack the Horizon – Quantum Finance Challenge

**Team 20: Schrödinger's Bulls**

## Hybrid Quantum–Classical Portfolio Optimisation

A complete hybrid quantum–classical framework for constrained portfolio optimisation that combines **QAOA** (Quantum Approximate Optimization Algorithm) with a novel **K-aware H/O/S asset-partitioning strategy** to solve real-world portfolio constraints efficiently.

---

## Terminology & Key Concepts

### **Core Terms**

| Term | Definition |
|------|-----------|
| **QAOA** | Quantum Approximate Optimization Algorithm – a hybrid quantum-classical algorithm that uses a quantum circuit to approximate solutions to optimization problems |
| **K** | The maximum number of position changes allowed in the portfolio (constraint: K ≤ 10) |
| **H/O/S Partitioning** | A framework that divides assets into three groups: **H**old, **O**ptimise, **S**ell (see below) |
| **H (Hold)** | Assets that must be retained in the portfolio (high-scoring, currently held assets) |
| **O (Optimise)** | Assets considered for optimization – the quantum decision set where buy/hold/sell decisions are made |
| **S (Sell)** | Assets that must be sold (low-scoring or illiquid assets) |
| **QUBO** | Quadratic Unconstrained Binary Optimization – a mathematical formulation used to express portfolio optimization as a quantum problem |
| **Ising Hamiltonian** | A quantum mechanical description of the optimization problem in a form quantum computers can process |
| **Sharpe Ratio** | A measure of risk-adjusted return: (Return - Risk-Free Rate) / Volatility. Higher is better |
| **Cardinality Constraint** | The requirement that exactly 25 assets must be in the portfolio |
| **Sector Concentration** | Rules ensuring the portfolio doesn't hold too many assets from a single market sector |

### **Optimization Components**

| Component | Definition |
|-----------|-----------|
| **Transaction Costs** | Fees and expenses incurred when buying or selling assets |
| **Covariance Risk** | A measure of how asset returns move together; used to calculate portfolio volatility |
| **K-Penalties** | Mathematical penalties added to the QUBO that enforce the K-feasibility constraint |
| **Cardinality Penalties** | Mathematical penalties that encourage the optimizer to select exactly 25 assets |
| **COBYLA** | Constrained Optimization BY Linear Approximation – a classical optimization algorithm used to tune quantum circuit parameters |

---

## Overview

### The Challenge

Construct a 25-asset portfolio from a universe of 50 assets, subject to:

* **Maximum 10 position changes (K ≤ 10)** – only 10 assets can be bought/sold from the current 36-asset portfolio
* **Sector concentration limits** – avoid overweighting any market sector
* **Transaction costs** – minimize buying and selling expenses
* **Binary buy/hold/sell decisions** – each asset is either in or out of the portfolio

### The Problem

Direct optimization is **K-infeasible**: with 36 assets currently held and only 10 changes allowed, we cannot optimize all 50 assets simultaneously.

### Our Solution

The **K-aware H/O/S partitioning framework** ensures feasibility *before* optimization and dramatically reduces the quantum problem size from **50 to 26 qubits** — reducing computational complexity by **99.99999%**.

---

## Key Features

### **1. K-Aware H/O/S Partitioning**

Assets are intelligently divided into three groups:

* **H (Must Hold)** – high-score assets currently held (stay in portfolio)
* **O (Optimise)** – candidate assets for optimization (quantum solver decides)
* **S (Must Sell)** – low-score or illiquid assets (removed from portfolio)

**Benefits:**
* Guarantees K-feasibility before quantum computation
* Reduces problem size for faster, more reliable quantum solutions
* Enables fair comparison across different optimization methods

---

### **2. QAOA on the O-Subset**

The quantum solver optimizes only the assets in the O-set with:

* **Complete QUBO formulation** including:
  * Expected returns
  * Covariance risk (volatility)
  * Transaction costs
  * K-penalties (enforce the 10-change limit)
  * Cardinality penalties (encourage 25 assets)

* **Quantum execution:**
  * Problem converted to Ising Hamiltonian (quantum form)
  * Executed using Qiskit AerSimulator
  * Classical COBYLA optimiser tunes quantum circuit parameters

---

### **3. Classical Baselines**

All methods optimize the *same* O-subset for fair comparison:

| Method | Solver | Purpose |
|--------|--------|---------|
| **QAOA** | Qiskit + COBYLA | Quantum variational approach |
| **Markowitz** | MIQP (SCIP/ECOS_BB) | Classical mean-variance optimization |
| **ILP** | GLPK_MI | Integer linear programming |
| **Greedy** | Score-based sorting | Simple heuristic |
| **Random** | Uniform sampling | Baseline comparison |

---

### **4. Bonus Challenges Implemented**

* **Liquidity filtering** – illiquid assets automatically forced to S (must sell)
* **Minimum sector diversification** – at least 1 asset required per market sector
* **Real-time rebalancing triggers** – automatic portfolio rebalancing based on Sharpe ratio, risk, sector changes, returns, or K-violations

---

## Results Summary

### **Performance**

* **QAOA Sharpe Ratio:** 2.614 (best risk-adjusted return)
* **Best Classical Sharpe:** 2.517
* **Improvement:** **+3.9%** better than classical methods

### **Constraints Satisfied**

* **0 K-violations** – all solutions respect the maximum 10 changes
* **0 sector violations** – diversification rules maintained
* **All methods feasible** – every optimizer produced valid portfolios
* QAOA selects 30 assets (soft cardinality), adjusted post-processing to reach 25

### **Computational Efficiency**

* **QAOA:** ~45 minutes (quantum simulation on classical computer)
* **Classical methods:** <1 second
* Trade-off: quantum provides better solution quality at higher computational cost

---

## Repository Structure

```
AQC-Hack-the-Horizon/
├── data/                    # Asset returns, covariance matrix, sector assignments, transaction costs
├── src/
│   ├── partitioning.py      # H/O/S construction and asset classification
│   ├── scoring.py           # Correlation-aware asset scoring
│   ├── classical.py         # Markowitz, ILP, greedy, random solvers
│   ├── qaoa.py              # QUBO formulation, Ising conversion, QAOA circuit
│   ├── evaluation.py        # Sharpe ratio, constraint checking, objective evaluation
│   └── rebalancing.py       # Bonus: Real-time rebalancing trigger system
├── notebooks/
│   └── main_pipeline.ipynb  # Complete workflow: data → partitioning → optimization → evaluation
└── README.md
```

---

## How to Run

### 1. Install Dependencies

```bash
pip install -r requirements.txt
```

### 2. Open the Main Notebook

```bash
jupyter notebook notebooks/main_pipeline.ipynb
```

### 3. Run All Cells

The notebook executes the complete pipeline:

* Load and prepare market data
* Construct H/O/S partition
* Score assets and identify candidates
* Run all classical solvers (Markowitz, ILP, greedy, random)
* Run QAOA on the quantum simulator
* Compare results and visualize portfolios

---

## Future Work

* **Quantum Hardware Execution** – run QAOA on real quantum processors with noise mitigation
* **Dynamic Time-Series Optimization** – adaptive H/O/S partitioning as market conditions change
* **Multi-Objective Optimization** – optimize for Sharpe ratio, CVaR (tail risk), drawdown, and ESG factors simultaneously
* **Scalability** – hierarchical quantum-classical decomposition for 500–5000 asset universes
* **Predictive Integration** – combine with quantum machine learning for alpha generation

---

## Citation

If you use this work in research or applications, please cite:

> *Hybrid Quantum–Classical Portfolio Optimisation with K-Aware Asset Partitioning. AQC Hack the Horizon Challenge (2025).*

---

## Final Presentation

Watch the presentation: https://youtu.be/KNLq0ZB4gw4
