# market-regime-factor-strategies
Regime-switching factor strategies with HMM: fixed vs regime-dependent factor sets
# 📘 HMM Regime Factor Strategies

This repository explores **regime-specific factor investing strategies** using **Hidden Markov Models (HMMs)** for market regime detection.  
We design and compare two distinct approaches to modeling factor returns across regimes.

---

## 🔹 Data Collection & Feature Engineering

Both strategies share the same **data foundation**:

- **Universe**: S&P 500 constituents (2010–present).  
- **Features**: Fundamental + market-based factors (e.g., valuation, profitability, growth, momentum, volatility, liquidity).  
- **Processing**:
  - Monthly cross-sectional panel.
  - Z-scoring within each month.
  - Median imputation for missing values.
  - Optional winsorization (default off).
- **Targets**: Forward 1-month excess returns.

> Regimes are identified using **HMM on market-wide returns/volatility**, providing a dynamic market-state classification.

---

## ⚙️ Strategy 1: Regime-Specific **Premia Model** (Same Factors)

This strategy assumes that the **same factor set drives returns**, but **their premia (coefficients)** vary by regime.  

### Methodology
1. **HMM Regime Detection**  
   - Market classified into discrete regimes (e.g., bull, bear, high vol).  
2. **Factor Model (OLS)**  
   - For each regime, fit an **OLS cross-sectional regression**:  
     \[
     r_{i,t+1} = \beta^{(regime)} X_{i,t} + \epsilon_{i,t}
     \]  
   - Factors = fixed set across regimes (value, quality, momentum, risk, etc.).  
3. **Score Construction**  
   - Predicted returns = regime-specific factor exposures × fixed factors.  
   - Each stock gets a regime-adjusted expected return score.  
4. **Portfolio Construction**  
   - Rank by score, select top *K* (e.g., 50).  
   - Weight proportional to scores.  
   - Rebalance monthly.

### Intuition
- Factors are **always relevant**, but their strength changes with regime.  
- Example: Value may work better in recovery regimes, momentum in trending bull regimes.

---

## ⚙️ Strategy 2: Regime-Specific **Factor Choice Model**

This strategy assumes that **different factors matter in different regimes**.  
Rather than keeping the same set, the model **selects which factors to use** in each regime.  

### Methodology
1. **HMM Regime Detection**  
   - Same as Strategy 1.  
2. **Factor Selection per Regime**  
   - Each regime has its **own subset of factors** chosen based on predictive strength.  
   - Two modeling variants were tested:
     - **Ridge Regression** (with L2 regularization) to stabilize factor selection.  
     - **OLS Regression** (plain linear model) as a simpler alternative.  
3. **Score Construction**  
   - Predicted returns built using **only the regime’s chosen factors**.  
   - Emphasis on flexibility: different regimes may drop or add signals.  
4. **Portfolio Construction**  
   - Same top-*K* selection and proportional weighting as Strategy 1.  

### Intuition
- In turbulent regimes, risk/volatility factors may dominate.  
- In stable growth regimes, profitability and momentum may be stronger.  
- Gives flexibility to adapt to **state-dependent factor relevance**.

---

## 🆚 Key Difference Between the Two

| Aspect | Strategy 1: Regime-Specific Premia | Strategy 2: Regime-Specific Factor Choice |
|--------|------------------------------------|-------------------------------------------|
| Factor Set | Fixed across regimes | Varies by regime |
| Model | OLS regression | Ridge or OLS regression |
| Flexibility | Low (same signals, changing weights) | High (different signals per regime) |
| Risk | More stable, less prone to overfitting | Higher risk of overfitting, but more adaptive |

---

## 🚀 Next Step
Performance comparisons for these two strategies are documented in a **separate Results & Insights README** (see `/analysis/README.md`).  

---
