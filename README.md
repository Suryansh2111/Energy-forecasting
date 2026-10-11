# Weather-Driven Energy Production Forecasting

**Author:** Suryansh Mishra · [GitHub](https://github.com/Suryansh2111) · [LinkedIn](https://www.linkedin.com/in/suryansh-mishra-bb3879339/)

Course project, OPIM 5509 (Deep Learning), University of Connecticut.

## Problem

Grid operators commit power plants a day ahead. Better forecasts of tomorrow's total generation mean less reliance on expensive peaker plants and earlier warning of shortfalls. This project forecasts **every day of 2025's total electricity production in ISO New England**, using weather and past production and training only on data before January 1, 2025.

## Data

- Daily weather from one station: max/min temperature, dewpoint, precipitation, wind speed, direction and gust, relative humidity, "feels like" temperatures.
- Daily ISO-NE production by fuel source; the target is the total.
- Training data: 1,824 days before 2025. Test data: 297 days, January 1 - October 26, 2025.

## Approach

- **EDA:** trends and seasonality, production by source, weather vs production, collinearity among weather fields. ACF/PACF showed strong lag-1 dependence and a weekly cycle (lags 7, 14, 21).
- **Features:** lags of past production, week-of-year or month dummies, cyclical encodings, a time trend, weekend and US-holiday flags.
- **No leakage:** each day uses only information available before that day; scalers are fit on training data only.
- **Models:** linear regression feature ladder; Decision Tree, Random Forest, XGBoost; Keras dense networks, 1D CNN, LSTM, bidirectional LSTM and GRU (about 25 runs).
- **Explainability:** SHAP, permutation importance, partial dependence plots.

## Results (2025 test set)

| Model and features | Test MAE | Test MAPE |
| --- | --- | --- |
| Linear regression, weather only | 33,580 | 10.4% |
| + week-of-year, trend, weekend/holiday | 21,482 | 7.1% |
| + production lags 1-14 | 13,977 | 4.6% |
| Random Forest | 16,390 | 5.1% |
| XGBoost | 14,237 | **4.5%** |
| Best dense neural network (64/32, dropout) | 14,241 | **4.5%** |
| Best LSTM | 24,271 | 7.6% |
| GRU | 27,820 | 8.8% |

RMSE fell about 58%, from about 44,800 (weather-only regression) to about 18,800 (best neural network).

## Key findings

- Lag and calendar features did most of the work: test MAPE dropped from 10.4% to 4.6% with linear regression alone.
- XGBoost and a small dense network tied at 4.5% MAPE. LSTMs and GRUs did worse because 1,800 days is little data, and engineered lags already capture the time structure.
- Yesterday's production and temperature were the strongest drivers; the weekly lag captured the human weekday/weekend cycle.

## Next steps

Add lag 7 (the final model used lags 1-6), more weather stations, import/export data, prediction intervals with quantile regression, and hourly data for intraday forecasts.

## Files

- `Energy_Forecasting.ipynb`: assignment brief, full analysis and results

**Tools:** Python, pandas, scikit-learn, XGBoost, TensorFlow/Keras, statsmodels, SHAP
