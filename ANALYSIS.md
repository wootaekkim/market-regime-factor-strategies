# Results & Analysis

This document compares three variants:

1. **Regime-Specific Premia (Same Factors)** — *Strategy 1*  
2. **Regime-Specific Factor Choice — Ridge** — *Strategy 2 (original)*  
3. **Regime-Specific Factor Choice — OLS** — *Strategy 2 (revised)*  

All results summarized here are out-of-sample (OOS), 2017-01-31 → 2024-12-31.

> **Note:** Strategy 1 uses a cap-weighted PIT S&P 500 universe benchmark, while Strategy 2 variants use the S&P 500 index. Absolute metrics are directly comparable; benchmark-relative ones differ slightly due to this setup.

---

## Side-by-Side Summary

| Variant | Modeling Idea | Factor Choice | CAGR | Volatility | Sharpe | Sortino | MaxDD | IC Mean | IC IR |
|--------|---------------|---------------|------|------------|--------|---------|-------|---------|-------|
| **1) Premia (Same Factors, OLS)** | Same factor set across regimes; premia vary | Fixed | **17.70%** | 21.65% | **0.863** | **1.55** | **−20.26%** | 0.0181 | 0.0938 |
| **2) Factor Choice (Ridge)** | Different factors by regime; Ridge with CV | Varies | 13.21% | 21.31% | 0.691 | 1.05 | −30.15% | **0.0257** | **0.0949** |
| **3) Factor Choice (OLS)** | Different factors by regime; plain OLS | Varies | 12.44% | 21.14% | 0.662 | 1.01 | −30.61% | 0.0054 | 0.0487 |

Additional reported metrics:  
- **Strategy 1:** Alpha +3.76% (ann), Beta 1.02, TE 2.14%, IR 0.261, Hit ratio 0.632  
- **Strategy 2 (Ridge):** Alpha −3.14% (ann), Beta 1.20, TE 8.97%, IR −0.083, Hit ratio 0.611  

---

## Performance Charts

*(replace these with the actual PNGs from `/charts/` once committed)*

- **Cumulative Returns**  
  ![Cumulative Returns](/cumulative_returns.png)

- **Rolling Sharpe (36m)**  
  ![Rolling Sharpe](/rolling_sharpe.png)

- **Monthly Rank-IC**  
  ![Monthly IC](/monthly_ic.png)

---

## What the numbers say

### 1) Strategy 1 (Premia, Same Factors) is the clear winner
- **Best risk-adjusted outcome:** Highest Sharpe (0.86), **lowest drawdown** (−20%), and **highest CAGR** (~17.7%).  
- **Stable signal:** IC is modest (~0.018) but **translates cleanly into PnL**.  
- **Interpretation:** Holding the **same factor set** across regimes while letting **premia (coefficients) vary** captures state dependence without overfitting.

### 2) Strategy 2 (Factor Choice) underperforms — Ridge and OLS
- **Ridge version:** Higher **raw IC** (~0.026) but **worse portfolio** (lower Sharpe, deeper drawdowns, negative alpha).  
- **OLS version:** Removing Ridge regularization **does not fix** underperformance (CAGR ~12.4%, Sharpe 0.66, MaxDD ~−30.6%).  
- **Interpretation:** The core issue is **regime-dependent factor selection**, not the estimator. Swapping Ridge→OLS reduces complexity but doesn’t restore monetization.

---

## Why we tried OLS for Strategy 2

- **Hypothesis:** Ridge+CV with few regime-specific observations may **overfit**, inflating IC without monetization.  
- **Test:** Replace Ridge with **plain OLS**, keep all else the same.  
- **Outcome:** IC fell further, Sharpe did not improve.  
- **Conclusion:** The problem lies in **factor instability across regimes**, not the estimation method.

---

## Role of the HMM

- **More observables → higher IC.** Richer HMM state definitions improve regime detection.  
- **But different factors per regime = unstable PnL.** Switching factor sets with regime transitions increases **risk exposure variance**, leading to deeper drawdowns.  
- **Best compromise:** Use a **richer HMM** with a **fixed factor set**, letting only premia vary by regime. This balances predictive strength with portfolio stability.

---

## Conclusion

- **Strategy 1 (Same Factors, Premia Vary)** is the most robust: highest Sharpe, best CAGR, and shallowest drawdowns.  
- **Strategy 2 (Factor Choice)** demonstrates that **higher IC ≠ higher returns** when regime transitions disrupt portfolio exposures.  
- **OLS vs Ridge test** confirmed the **estimation method wasn’t the driver** — factor stability was.

---

## Future Work

1. **HMM Enhancements**  
   - More observables, more states, and **soft regime probabilities** to smooth routing.  

2. **Stable Factor Architecture**  
   - Fixed core set + small regime-dependent tweaks.  
   - Try **hierarchical shrinkage** rather than binary inclusion/exclusion.  

3. **Portfolio Overlays**  
   - Beta targeting, hedging, turnover constraints, and liquidity guards.  

4. **Robustness Checks**  
   - Subperiod performance, sector-neutral variants, transaction cost stress tests.  

---

**Takeaway:** Stability > flexibility.  
The path forward is a **same-factor, premia-varying regime model with richer HMM states** — maximizing predictive IC while keeping exposures robust.  
