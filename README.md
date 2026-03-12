0# Pairs_trading_strategy

# PEP-KO Pairs Trading Strategy

A quantitative pairs trading model for **PepsiCo (PEP)** and **Coca-Cola (KO)** using Monte Carlo simulation, Cholesky correlation, and Ornstein-Uhlenbeck mean-reversion modeling.

---

## Overview

Pairs trading is a market-neutral strategy that exploits the historically stable price relationship between two correlated assets. When the spread between them diverges, the strategy bets on reversion to the mean — going **long the underperformer** and **short the outperformer**.

This project models the PEP–KO spread across three frameworks:

1. **Independent Monte Carlo** — bootstrapped return simulation for each stock
2. **Correlated Monte Carlo** — Cholesky decomposition to preserve historical correlation
3. **Ornstein-Uhlenbeck (OU) Process** — continuous-time mean-reversion model for the spread

Finally, a **portfolio-level simulation** tests the long PEP / short KO strategy over a 7-day trading window.
---

## Getting Started

### 1. Clone the repo

```bash
git clone https://github.com/your-username/pep-ko-pairs-trading.git
cd pep-ko-pairs-trading
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Run the model

```bash
python pepsi_coke_pairs_trading.py
```

---

## What It Does

### Data
- Downloads daily closing prices for **KO** and **PEP** from Yahoo Finance (`2022-07-14` to `2023-10-09`)
- Computes the raw spread: `Spread = PEP - KO`

### Analysis Pipeline

| Step | Description |
|------|-------------|
| **Price Trends** | Plots KO, PEP, and their spread over time |
| **Distribution Analysis** | Histograms + Q-Q plots of daily changes |
| **Independent MC** | 1,000 simulated price paths per asset using bootstrapped returns |
| **Correlated MC** | Cholesky-decorrelated noise to preserve ~0.77 historical correlation |
| **Spread Forecast** | Forward-projected spread from last observed price |
| **Detrending** | Removes linear trend from spread for stationarity analysis |
| **OU Process** | Mean-reverting simulation of the detrended spread |
| **Portfolio Sim** | 1,000 simulations of a 7-day long PEP / short KO trade |

### Portfolio Simulation Logic
- Starting capital: **$100,000**
- Each day, the portfolio is rebalanced: **50% short KO, 50% long PEP**
- Cash earns interest at **5% annually** (252 trading days)
- Outputs: mean final value, standard deviation, 5th/95th percentile (VaR)

---

## Key Methods

### Cholesky Decomposition
Used to generate correlated random noise for PEP and KO:

```
C = corrcoef(PEP, KO)
L = cholesky(C)
[z_pep, z_ko] = [w_pep, w_ko] @ L
```

### Ornstein-Uhlenbeck SDE
Discretized form used to model mean reversion:

```
X[t] = X[t-1] + μ - a·X[t-1] + σ·Z[t]
```

Where `a` is the mean-reversion speed (default: `0.005`).

---

