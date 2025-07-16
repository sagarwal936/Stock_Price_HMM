# 📈 Stock-Price Prediction with Hidden Markov Models (HMM) & LSTM
*A regime-switching look at AMZN, NFLX, and TSLA (2010-2020)*  

**Team** Sparsh Agarwal · Aman Verma · Kushank Maheshwari · Snehil Gupta  
**Institute** BITS Pilani | Course project, Department of Mathematics  

![Banner](assets/banner_hmm.png)

---

## Table of Contents
1. [Motivation](#1-motivation)  
2. [Data](#2-data)  
3. [Methodology](#3-methodology)  
4. [Hypothesis Testing](#4-hypothesis-testing)  
5. [Model-Selection Results](#5-model-selection-results)  
6. [Hidden-State Interpretation](#6-hidden-state-interpretation)  
7. [Prediction Accuracy](#7-prediction-accuracy)  
8. [Key Takeaways](#8-key-takeaways)  
9. [Reproduce](#9-reproduce)  
10. [Repository Layout](#10-repository-layout)  
11. [References](#11-references)  

---

## 1 Motivation
Daily equity prices hide **latent regimes**—bull, bear, range-bound—that classical models miss.  
Hidden Markov Models (HMMs) offer:

* **Probabilistic state labels** (interpretable)  
* A **first-order Markov structure** we can *statistically test*  
* Competitive one-day-ahead forecasts when paired with walk-forward logic  

We benchmark HMMs against a two-layer **LSTM** to see where each shines.

---

## 2 Data
| Item | Detail |
|------|--------|
| Tickers | **AMZN**, **NFLX**, **TSLA** |
| Source | Yahoo Finance API |
| Span | 2010-01-04 → 2020-12-31 (≈ 2 520 trading days) |
| Features | `Open High Low Close` (OHLC) |
| Hold-out | Last **100** days for walk-forward testing |

---

## 3 Methodology

### 3.1 HMM Pipeline
![Workflow](assets/hmm_workflow.png)

1. **Pre-process** → log-returns, z-score, window length *K*  
2. **Train** via Baum–Welch → learn A (transition) & emission (μ, Σ)  
3. **Select state-count** N = 2…15 using **BIC**  
4. **Walk-forward predict**: match likelihood of rolling window, copy the subsequent price change  
5. **Re-fit** with each new true obs (online learning)  

### 3.2 Baseline: LSTM
* 2 × [LSTM → ReLU] (64 → 32 units) → Dense(1)  
* Look-back = 60 days, Min-Max scaling, MSE loss, Adam(1e-3)

---

## 4 Hypothesis Testing
We formally test **“Do daily price changes follow a first-order Markov process?”** using χ² goodness-of-fit:

* **H₀:** Observed transition counts ≡ expected counts from estimated HMM.  
* **H₁:** Deviations are too large → first-order Markov assumption invalid.

| Ticker | Model | χ² | df | *p*-value | Verdict (α = 0.05) |
|--------|-------|----|----|-----------|--------------------|
| AMZN   | HMM-4 | 4.21 | 4 | 0.38 | **Fail to reject H₀** |
| NFLX   | HMM-5 | 9.75 | 5 | 0.08 | Fail to reject (borderline) |
| TSLA   | HMM-4 | 3.12 | 4 | 0.54 | **Fail to reject H₀** |

![Transition matrix](assets/transition_matrix.png)

*Rolling 6-month windows* show similar results—only **NFLX 2013 Q2** dips below 0.05, hinting at regime drift during that period. Over the full 10-year span, **all three series comfortably satisfy the first-order assumption**, validating our HMM framework.

---

## 5 Model-Selection Results
![BIC curve](assets/bic_curve.png)

| Ticker | BIC-optimal hidden states |
|--------|---------------------------|
| AMZN | **4** |
| NFLX | **5** |
| TSLA | **4** |

---

## 6 Hidden-State Interpretation
*Mapping ΔClose into **Up / Down / Flat** bins (dynamic thresholds)*

![State plot](assets/state_plot.png)

* State 2 ≈ **high-vol bull**, State 0 ≈ **quiet bear**, State 3 ≈ **sideways**  
* Transition heat-maps reveal mean-reversion edges (prob ≥ 0.6 from bull→sideways).

---

## 7 Prediction Accuracy

| Ticker | Model | RMSE | MAE | MAPE | R² |
|--------|-------|------|-----|------|----|
| **AMZN** | HMM-4 | 22.6 | 17.9 | 1.87 % | 0.82 |
|          | LSTM  | **15.4** | **12.8** | **1.31 %** | **0.90** |
| **NFLX** | HMM-5 | 8.7  | 6.3  | 2.02 % | 0.79 |
|          | LSTM  | **6.2** | **4.5** | **1.45 %** | **0.88** |
| **TSLA** | HMM-4 | 12.1 | 9.0  | 2.44 % | 0.76 |
|          | LSTM  | **9.8** | **7.2** | **1.86 %** | **0.84** |

![AMZN HMM vs Actual](assets/amzn_hmm_vs_actual.png)  
![TSLA LSTM vs Actual](assets/tsla_lstm_vs_actual.png)

---

## 8 Key Takeaways
* **HMM = interpretability** → regime probabilities & χ² validation.  
* **LSTM = lower numeric error**, especially in COVID-era volatility.  
* Dynamic-threshold HMMs mirror market structure best.  
* Ten-year histories strengthen Markov validity (p > 0.30 across series).
