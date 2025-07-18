# 📈 Stock Market Prediction Using Stochastic Process

This project explores the idea that **stock market price movements follow a first-order Markov Chain**, and implements **Hidden Markov Models (HMM)** to model and analyze the behavior of stock prices over time.

---

## 🧠 Objective

To **test and validate** whether daily stock price movements—categorized into states like **Up**, **Down**, and **Stagnant**—can be modeled as a **first-order Markov Process**, where the next day's state depends only on the current day's state.

---

## 🔧 Methodology

### 📊 Observable Inputs
For each trading day:
- **Opening Price**
- **Closing Price**
- **High Price**
- **Low Price**

Using these, we define movement patterns and categorize them into 3 states:
- **Up**
- **Down**
- **Stagnant**

### 🧮 Difference Patterns

We designed **four models** based on different ways of calculating price movement:

| Model | Difference Pattern | Range Type |
|-------|--------------------|------------|
| **Model 1** | `Close[n] - Close[n-1]` | Static |
| **Model 2** | `Close[n] - Open[n]`    | Static |
| **Model 3** | `Close[n] - Close[n-1]` | Dynamic |
| **Model 4** | `Close[n] - Open[n]`    | Dynamic |

#### 🔹 Static Range
Based on 1.5×mean of difference values:
- Down: Below –threshold  
- Stagnant: Between –threshold and +threshold  
- Up: Above +threshold

#### 🔹 Dynamic Range
`|Difference/Open| * 0.5` used to dynamically adjust thresholds.

---

## 🔁 Transition Matrix Construction

For each model, we computed a **3x3 transition probability matrix**, capturing the likelihood of moving between the states (e.g., Up → Down, Down → Stagnant, etc.) over time.

---

## 🧪 Hypothesis Testing: First-Order Markov Chain

We test the null hypothesis:
> **"The sequence of price movements follows a first-order Markov Chain."**

### Chi-Squared Test

We compute **Chi-Squared statistics** for each model across:
- **6-month timeframe**
- **10-year timeframe**

### 📌 Observations

- **Short-term (6 months):**  
  Mixed results. Some models conform, others do not. Suggests that **short-term market data may not follow Markovian behavior consistently**.

- **Long-term (10 years):**  
  High probabilities (~99%) across all models, strongly suggesting that **stock prices over longer durations tend to follow a Markov process**.

- **Model 3 (Close[n] – Close[n–1], Dynamic Range):**  
  Most consistent across both timeframes. Probabilities always above 90%, making it the **best candidate** for Markov-based modeling.

---

## 🔍 Key Takeaways

- Stock market movement **can be reasonably modeled** using a **Hidden Markov Model**, particularly over longer durations.
- There is evidence of **underlying hidden states** that influence stock price behavior.
- While not perfectly predictive, HMM provides **trend indicators** useful for decision-making.
- Compared to LSTM, HMMs offer **lower complexity** and better **feature extraction** for time series like stock prices.


