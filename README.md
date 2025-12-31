# sabraza-DS.v2.5.3.3.5

## Project description
The primary goal of this project is to analyze 15 years of daily data for major tech stocks (AAPL, AMZN, GOOGL, META, MSFT) to determine if statistical models can effectively predict future returns or the timing of extreme market events.

Dataset has `Close`, `Open`, `High`, `Low`, `Volume` columns with inside separation by stock ticket.

**Project structure:**

1. Exploratory Data Analysis (EDA):

    *  Processed and cleaned over 16,000 days of multi-index trading data.

    * Conducted rigorous statistical tests (Augmented Dickey-Fuller) to confirm stationarity in daily returns.

    * Identified structural anomalies, such as the "zero-return" impact of weekends and the specific IPO timeline of META (Facebook).

2. Time-Series Forecasting (The "Random Walk" Test):

    * Developed a baseline AutoRegressive (AR) model to predict daily returns.

    * Challenged the baseline with complex ARIMA(1,1,1) models to capture moving average patterns.

    **Finding: Complex models failed to outperform the simple baseline (RMSE ≈ 2%), providing strong empirical evidence that daily price movements for these large-cap stocks follow a Random Walk and are resistant to purely technical forecasting.**

3. Survival Analysis (Predicting Opportunity):

    * Pivoted the problem to "Time-to-Event" modeling. Defined the target event as a single-day price increase of ≥5%.

    * Engineered "Duration" (days waiting for a jump) and "Censoring" variables to transform the time-series dataset into a survival dataset.

    * Applied the Cox Proportional Hazards Model to quantify the impact of market volatility on waiting times.

    **Finding: Discovered a strong positive relationship (β≈51.07), proving that a 1% increase in daily volatility makes a major price jump ~66% more likely to occur immediately.**

4. Stock Ranking System:

    * Built a deployment-ready ranking algorithm that processes current market data to generate a "Risk Score" (Likelihood of Jump).

    * The system ranks stocks in real-time, identifying which assets are statistically primed for a breakout based on their current volatility state.

## Installation

This project uses `uv` for package management. To install the dependencies, first create a `requirements.txt` file from `pyproject.toml` and then run:

```bash
uv pip install -r requirements.txt
```

The required packages are:
- `matplotlib>=3.10.7`
- `pandas>=2.3.3`
- `scikit-learn>=1.7.2`
- `seaborn>=0.13.2`
- `statsmodels>=0.14.5`
- `yfinance>=0.2.66`

## Get Data
Data is available from Yahoo Finance and will be downloaded using `yfinance` library. The notebook `m3s3.ipynb` contains the code to download and save the data.

```python
import yfinance as yf
tickers = ["AAPL", "AMZN", "GOOGL", "META","MSFT"]
stocks_data = yf.download(tickers, start="2010-01-01", end="2025-12-01")
```

## Inference and Models
The project explores two main modeling approaches:

### Time Series Forecasting
- **Objective:** Predict the next day's stock price.
- **Models:**
    - Autoregressive (AR) model as a baseline.
    - ARIMA(1,1,1) model.
- **Conclusion:** The models are effectively defaulting to predicting the historical mean return (close to zero) rather than capturing daily directional movements. The identical performance of simple vs. complex models suggests that daily returns for these large-cap stocks exhibit Random Walk behavior.

### Survival Analysis
- **Objective:** Predict the timing of significant price jumps (>=5%).
- **Model:** Cox Proportional Hazards (Cox PH) model.
- **Conclusion:** A strong positive relationship was found between volatility and the likelihood of a price jump. A 1% increase in daily volatility makes a major price jump ~66% more likely to occur immediately.

## Stock Ranking System
A stock ranking system was developed based on the survival analysis. It generates a "Risk Score" to rank stocks based on their likelihood of a price jump.

## Conclusions
- Daily stock returns for the analyzed tech stocks follow a Random Walk, making them difficult to predict with simple time-series models.
- Survival analysis, specifically the Cox PH model, is effective in predicting the timing of extreme market events based on volatility.
- The project provides a framework for a stock ranking system to identify potential investment opportunities.

## Suggestions for Improvement
- **Macro-Economic Features:** Incorporate external features like Interest Rates or the VIX index.
- **Non-Linear Survival Models:** Use models like Random Survival Forests to capture more complex relationships.
- **Regime-Switching Models:** Employ Markov Switching AR models to account for different market conditions (bull vs. bear markets).
