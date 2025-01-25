# Nominal Price Illusion

**Project description:**  
This research explores the psychological bias termed *Nominal Price Illusion*, where investors perceive low-priced stocks as having more upside potential compared to high-priced stocks. The study investigates how this bias affects investor behavior, asset pricing, and corporate actions such as stock splits. By employing robust statistical methodologies, including risk-neutral skewness derived from options, we highlight the implications of this behavioral bias in financial markets.

<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/3.2.2/es5/tex-mml-chtml.js">
</script>

---

## 1. Research Objectives

1. Identify the impact of nominal price illusion on investor expectations and stock skewness.
2. Evaluate the implications of nominal price illusion on option pricing and trading behavior.
3. Analyze stock splits to isolate the effects of nominal price changes on investor perceptions.

---

## 2. Methodology

### 2.1 Skewness Measures

To evaluate investor skewness expectations, we rely on two primary metrics:

1. **Risk-Neutral Skewness (RNSkew):**  
   Derived from Bakshi, Kapadia, and Madan (2003), RNSkew captures investor expectations of asymmetry in return distributions implied by option prices. The formula is::

   $$
   RNSkew_{i,t}(\tau) = \frac{\mathbb{E}^Q_t \big[(R(t,\tau) - \mathbb{E}^Q_t[R(t,\tau)])^3\big]}{\big[\mathbb{E}^Q_t \big[(R(t,\tau) - \mathbb{E}^Q_t[R(t,\tau)])^2\big]\big]^{3/2}}
   $$

   where \(R(t,\tau)\) represents the return over time \(\tau\), and \(\mathbb{E}^Q_t\) is the expectation under the risk-neutral measure.

2. **Physical Skewness (Skew):**  
   Calculated using daily return data over a one-year horizon, Skew serves as an ex-post measure of realized return asymmetry.

### 2.2 Option-Implied Metrics

We extract higher-order moments (variance \(V\), skewness \(W\), and kurtosis \(X\)) from option prices using integrals over call and put prices:

$$
V_{i,t}(t, \tau) = \int_0^{S(t)} \frac{2(1 + \ln[K/S(t)])}{K^2} P(t, \tau; K) dK + \int_{S(t)}^\infty \frac{2(1 - \ln[K/S(t)])}{K^2} C(t, \tau; K) dK
$$

$$
W_{i,t}(t, \tau) = \int_0^{S(t)} \frac{6 \ln[K/S(t)] + 3(\ln[K/S(t)])^2}{K^2} P(t, \tau; K) dK - \int_{S(t)}^\infty \frac{6 \ln[K/S(t)] - 3(\ln[K/S(t)])^2}{K^2} C(t, \tau; K) dK
$$

$$
X_{i,t}(t, \tau) = \int_0^{S(t)} \frac{12(\ln[K/S(t)])^2 + 4(\ln[K/S(t)])^3}{K^2} P(t, \tau; K) dK + \int_{S(t)}^\infty \frac{12(\ln[K/S(t)])^2 - 4(\ln[K/S(t)])^3}{K^2} C(t, \tau; K) dK
$$

Where:
- \(C\) and \(P\) are call and put prices.
- \(S(t)\): Stock price.
- \(K\): Strike price.

---

### 2.3 Fama-MacBeth Regressions / Cross-Sectional Regressions

To isolate the effect of nominal prices on skewness, we employ monthly Fama-MacBeth regressions:

$$
DV_{i,t} = \alpha + \beta_1 \ln(\text{Price}_{i,t-1}) + \beta_2 \text{Controls}_{i,t-1} + \epsilon_{i,t}
$$

Dependent variables (\(DV_{i,t}\)) include \(RNSkew\), \(Skew\), and their differences. Control variables account for factors such as size, leverage, past returns, and volatility.

---


### 2.4 Portfolio Analysis

We construct delta-hedged and non-delta-hedged option portfolios based on stock price quintiles to evaluate asset pricing implications. Returns are computed using the formula:

$$
R_{call} = \frac{(c_T - c_0) - \Delta_0(S_T - S_0 + \sum_{t=1}^{T} D_t e^{r(T-t)})}{|\Delta_0 S_0 - c_0|}
$$

Where:
- \(c_T\) and \(c_0\): Option prices at maturity and initiation.
- \(\Delta_0\): Delta at initiation.
- \(S_T\) and \(S_0\): Stock prices at maturity and initiation.
- \(D_t\): Dividends.
- \(r\): Risk-free rate.

---

## 3. Results and Key Findings

1. **Nominal Price Illusion:**
   Investors assign greater importance to nominal stock prices when forming skewness expectations, resulting in a systematic overestimation of the upside potential of low-priced stocks.

2. **Behavior in the Options Market:**
   Low-priced stocks exhibit higher call-to-put volume and open interest ratios, indicating greater investor optimism and a preference for lottery-like stocks.

3. **Stock Splits:**
   Post-split skewness expectations increase significantly despite a decline in realized physical skewness, demonstrating the strong influence of nominal price changes on investor perceptions.

<img src="images/IEF vs US.png"/>

*Figure:  RNSkew around stock splits. *

5. **Asset Pricing Implications:**
   Low-priced stock options, particularly out-of-the-money calls, are overvalued relative to high-priced stock options. A strategy exploiting this mispricing yields abnormal returns.

---

## 4. Conclusions

Our findings reveal a pervasive nominal price illusion among investors, which impacts their skewness expectations, trading behavior, and pricing in the options market. This bias provides insights into behavioral finance and has significant implications for firms, investors, and asset pricing theories. Future research may further explore how this bias interacts with other behavioral phenomena in financial markets.

This study highlights the importance of understanding the psychological drivers behind investor decision-making and the resulting distortions in market prices.

