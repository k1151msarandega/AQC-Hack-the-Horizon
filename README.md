#AQC Hack the Horizon – Quantum Finance Challenge
Team 20: Schrödinger's Bulls

# **Hybrid Quantum–Classical Portfolio Optimisation**

### *AQC Hack the Horizon – Quantum Finance Challenge*

This repository contains a complete hybrid quantum–classical framework for constrained portfolio optimisation. The approach combines **QAOA** with a novel **K-aware H/O/S asset-partitioning strategy**, enabling quantum optimisation on a reduced and meaningful decision space while respecting real-world constraints.

---

## **Overview**

The challenge requires constructing a 25-asset portfolio from a universe of 50 assets, subject to:

* **Maximum 10 position changes (K ≤ 10)**
* **Sector concentration limits**
* **Transaction costs**
* **Binary buy/hold/sell decisions**

Direct optimisation is **K-infeasible** due to 36 assets currently held.
Our solution introduces a **K-aware H/O/S partitioning framework** that ensures feasibility *before* optimisation and reduces the quantum problem size from **50 to 26 qubits** — a **99.99999% reduction in search space**.

---

## **Key Features**

### **1. K-Aware H/O/S Partitioning**

Assets are divided into:

* **H (Must Hold)** – high-score, currently held assets
* **O (Optimise)** – the quantum decision set
* **S (Must Sell)** – low-score or illiquid assets

This ensures:

* K-feasibility
* Reduced quantum problem size
* Fair comparison across solvers

---

### **2. QAOA on the O-Subset**

* Full QUBO formulation including:

  * returns
  * covariance risk
  * transaction costs
  * K-penalties
  * cardinality penalties
* Converted to an Ising Hamiltonian
* Executed using Qiskit AerSimulator
* Classical COBYLA optimiser for variational parameters

---

### **3. Classical Baselines**

All methods optimise the *same* O-subset for fairness:

| Method    | Solver              |
| --------- | ------------------- |
| QAOA      | Qiskit + COBYLA     |
| Markowitz | MIQP (SCIP/ECOS_BB) |
| ILP       | GLPK_MI             |
| Greedy    | Score-based sorting |
| Random    | Uniform sampling    |

---

### **4. Bonus Challenges Implemented**

* **Liquidity filtering** (illiquid assets forced to S)
* **Minimum sector diversification** (≥1 asset per sector)
* **Real-time rebalancing triggers** (Sharpe, risk, sector, return, K)

---

## **Results Summary**

### **Performance**

* **QAOA Sharpe Ratio:** 2.614
* **Best Classical Sharpe:** 2.517
* **Improvement:** **+3.9%**

### **Constraints**

* **0 K-violations**
* **0 sector violations**
* **All methods feasible**
* QAOA selects 30 assets (soft cardinality), repaired post-processing

### **Runtime**

* QAOA: ~45 minutes (simulation)
* Classical: <1 second

---

## **Repository Structure**

```
├── data/                 # Asset returns, covariance, sectors, costs
├── src/
│   ├── partitioning.py   # H/O/S construction
│   ├── scoring.py        # Correlation-aware scoring
│   ├── classical.py      # Markowitz, ILP, greedy, random
│   ├── qaoa.py           # QUBO, Ising, QAOA circuit
│   ├── evaluation.py     # Sharpe, J(x), constraints
│   └── rebalancing.py    # Bonus trigger system
├── notebooks/
│   └── main_pipeline.ipynb
└── README.md
```

---

## **How to Run**

1. Install dependencies:

```
pip install -r requirements.txt
```

2. Open the main notebook:

```
notebooks/main_pipeline.ipynb
```

3. Run all cells to:

* load data
* construct H/O/S
* run classical solvers
* run QAOA
* evaluate and visualise results

---

## **Future Work**

* Execution on real quantum hardware with noise mitigation
* Dynamic time-series optimisation with adaptive H/O/S
* Multi-objective QUBO (Sharpe, CVaR, drawdown, ESG)
* Hierarchical quantum–classical decomposition for 500–5000 assets
* Integration with quantum machine learning for predictive alpha

---

## **Citation**

If you use this work, please cite:

> *Hybrid Quantum–Classical Portfolio Optimisation with K-Aware Asset Partitioning, AQC Hack the Horizon (2025).*

---

Final Presentation: https://youtu.be/KNLq0ZB4gw4
