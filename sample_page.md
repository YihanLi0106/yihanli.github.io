## Fixed Income ETF Hedging

**Project description:** ASL Capital Inc. is expanding its focus to the fixed-income ETF market. This research investigates key factors influencing the price dynamics and performance of fixed-income ETFs. The study quantifies the effects of interest rate fluctuations and market volatility on these ETFs by employing hedging strategies with futures contracts.

---

### 1. Main Objectives

1. Analyze the behavior and key price drivers of selected fixed-income ETFs relevant to ASL Capital Inc.
2. Develop and implement hedging strategies using futures contracts tailored to these ETFs.
3. Select appropriate futures contracts and calculate optimal hedge ratios to minimize portfolio risk.
4. Evaluate the effectiveness of the hedging strategies on portfolio stability and performance.

---

### 2. Data Collection

I sourced historical price and returns data for selected ETFs and futures contracts from Bloomberg Terminal and Yahoo Finance's API (yfinance). 

**The ETFs analyzed included**:
- SPDR Bloomberg 1-3 Month T-Bill ETF
- iShares 7-10 Year Treasury Bond ETF
- iShares 20+ Year Treasury Bond ETF
- WisdomTree Floating Rate Treasury ETF
- iShares 0-3 Month Treasury Bond ETF

**The futures contracts considered were**:
- Five-Year U.S. Treasury Note Futures
- Ten-Year U.S. Treasury Note Futures
- Two-Year U.S. Treasury Note Futures
- Secured Overnight Financing Rate (SOFR) Futures
- Ultra T-Bond Futures
- U.S. Treasury Bond Futures
- Three-Month U.S. Treasury Bill Futures

---

### 3. Data Preprocessing

**Futures Data**:
Selected the futures contracts with the highest open interest for each trading day to ensure liquidity and relevance.

**ETF Data**:
Retrieved historical Net Asset Value (NAV) data for each ETF and fetched dividend data using \texttt{yfinance}.
Applied a backward adjustment method, similar to the one used by Yahoo Finance, to adjust NAVs for dividends and maintain consistency in the NAV series, and avoid artificial drops due to dividends.

**Correlation Analysis**:
We calculated the correlations between the adjusted NAV returns of each ETF and the future returns to identify the most effective hedging instrument for each ETF.

---

### 4. Strategies

#### 4.1. Duration Hedging
Duration hedging is a risk management technique used to mitigate the sensitivity of a portfolio to interest rate movements.

**Methodology**:  
- **Duration Data**: Historical modified durations (YAS_MOD_DUR) for each ETF were obtained from Bloomberg.  
- **Hedge Ratio Calculation**: The hedge ratio for each ETF-future pair was calculated using the formula:  
  \[
  \text{Hedge Ratio} = \frac{\text{ETF Duration}}{\text{Futures DV01}}
  \]
  where DV01 represents the dollar value change for a 1 basis point move in yield of the futures contract.  
- **Implementation**: ETFs with high correlations to specific futures contracts were hedged using those futures. If any ETFs showed low correlations with all futures, we used the SOFR futures.

**Results**:  
- **IEF** demonstrated a strong correlation with US futures, and **TLT** showed a strong correlation with WN futures, indicating effective hedging potential in both mid- to long-duration ETFs.  
- In contrast, **BIL**, **SGOV**, and **USFR** had low correlations with all Treasury futures and SOFR futures, resulting in limited hedging effectiveness when using SOFR futures.

<img src="images/dummy_thumbnail.jpg?raw=true"/>

*Figure 1: IEF Duration Hedge*

#### 4.2. A Two-Part Strategy Combining Duration Adjustment and Yield Spread Trading

**Prediction Model**:  
We employed a Bayesian-optimized Random Forest model to predict the next-day direction of selected Treasury yield rates and spreads. This model used a 3-year rolling window and technical indicators (MA, EMA, RSI, Bollinger Bands), achieving an accuracy of 81% to 86%. These predictive signals guided the strategy’s execution.

**Duration Adjustment Component**:  
- **Method**: Short futures when yield rates are predicted to rise and long futures when they are expected to fall.  
- **Formula**: Weights were optimized in rolling windows for enhanced performance.  

\[
\text{Spread Ret} = \sum_{i=1}^{3} \text{Weight}_i \times \text{Signal}_i \times (\text{Ret}_{\text{short-term},i} - \text{Ret}_{\text{long-term},i})
\]  

**Yield Spread Trading Component**:  
- **Method**: Long short-term futures and short long-term futures when spreads were predicted to widen. Short short-term futures and long long-term futures when spreads were forecasted to narrow.  
- **Formula**: The hedge ratio was calculated using DV01, and the Adjustment Ratio (AR) was dynamically scaled in rolling windows.  

\[
\text{Adjust Ret} = \text{Ret}_{\text{ETF}} + \text{Signal}_D \times \text{Hedge Ratio} \times \text{AR} \times \text{Ret}_{\text{Future}}
\]  

**Portfolio Optimization**:  
- The strategy’s components were integrated and optimized in a 20-day rolling window to **maximize the Sharpe ratio**.  
- **Combined Formula**:  

\[
\text{Portfolio Ret}_{\text{AR, weight}} = \text{Adjust Return} + \frac{1}{1 - \sum_{i=1}^{3} \text{Weight}_i} \times \text{Spread Ret}
\]  

**Benchmark**:  
Performance was measured against the Bloomberg US Treasury Total Return Unhedged USD Index (LUATTRUU).  


#### 4.3. Adaptive Beta Hedge Strategy

**Beta Hedge Formula**:  

\[
R_S = \alpha + \beta R_H + \epsilon
\]  

Where:  
- \( R_S \): Return of the asset you are trying to hedge  
- \( \alpha \): Intercept  
- \( \beta \): Hedge ratio, representing the sensitivity of the asset’s returns to the returns of the hedging instrument  
- \( R_H \): Return of the hedging instrument  
- \( \epsilon \): Error term  


**Hedge Ratio Calculation**:  
The hedge ratio \( H \) can be calculated as:  

\[
H = -\hat{\beta} \times \frac{V_S}{V_H}
\]  

Where:  
- \( \hat{\beta} \): Estimated regression coefficient  
- \( V_S \): Value of the position being hedged  
- \( V_H \): Value of one unit of the hedging instrument  


**Methodology**:  
We employ various regression techniques, including Linear Regression, Lasso Regression, Ridge Regression, and LightGBM (Gradient Boosting Machine). Additionally, to ensure the stability of the beta coefficient over time, we utilize a sliding window approach with rolling regressions and time-varying betas. This frequent updating reflects the latest market conditions and interactions between variables, ensuring that the beta estimates remain relevant and accurate. This involves setting different window lengths, such as 15 days, 30 days, 45 days, and 60 days, to capture temporal variations in the beta coefficient effectively.


**Results**:  
The optimized portfolio strategy significantly outperformed ETF holdings, particularly for mid- to long-duration ETFs like **IEF** and **TLT**, demonstrating enhanced cumulative returns. The information ratio improvements (+1.16 for **IEF** and +0.995 for **TLT**) confirm stronger risk-adjusted performance.

**Information Ratio for Optimized Portfolio vs. Holding ETF**:

| ETF  | BIL | SGOV | IEF | TLT |
|------|-----|------|-----|-----|
| Optimized Portfolio | 0.95 | 0.97 | 0.88 | 0.38 |
| Holding ETF         | 0.86 | 0.89 | -0.29 | -0.61 |
| **Change**          | **+0.08** | **+0.08** | **+1.16** | **+0.995** |

*Table 1: Information Ratio for Optimized Portfolio vs. Holding ETF*

---

**Accumulated Return Comparison for ETFs**:  
The following charts show the cumulative returns for BIL, SGOV, IEF, and TLT:

<img src="images/dummy_thumbnail.jpg?raw=true"/>

*Figure 2: Accumulated Return Comparison for ETFs*

---

### 5. Conclusions

Our analysis attempts to address the complexities inherent in managing various fixed-income ETFs, where we observed that lower-duration ETFs are difficult to hedge. However, for mid- to long-duration ETFs such as **IEF** and **TLT**, we were able to optimize returns and mitigate associated risks. The project underscored the importance of tailoring risk management techniques to the unique characteristics of each ETF. These insights offer valuable perspectives on the nuanced dynamics of fixed-income investments.

