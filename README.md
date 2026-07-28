# Seasonal-Target-Revenue-Analysis
# Target Corporation Quarterly Revenue Forecasting

A seasonal time series regression project that models and forecasts Target Corporation's quarterly revenue using an ordinary least squares (OLS) model with a linear time trend and seasonal indicator variables. Built in Python with pandas, statsmodels, and matplotlib.

## Overview

Retail revenue is highly seasonal, with a large spike every holiday quarter. This project quantifies that pattern for Target Corporation, separating the underlying growth trend from the recurring seasonal swings, and then uses the fitted relationship to forecast the next four quarters with prediction intervals.

The analysis is framed as a client deliverable: a revenue outlook prepared for a wealth management audience evaluating Target as a holding.

## Data

| Field | Detail |
|-------|--------|
| Source | Compustat quarterly fundamentals (`qSales_2024.csv`) |
| Company | Target Corporation (ticker TGT, gvkey 3813) |
| Target variable | `saleq`, quarterly revenue in USD millions |
| Coverage | 2000 Q4 through 2023 Q4 |
| Observations | 93 quarters |

## Approach

1. Load the dataset, parse dates, and filter to Target (`tic == 'TGT'`).
2. Plot raw quarterly revenue to inspect the trend and seasonality.
3. Group revenue by fiscal quarter to confirm the seasonal ordering (Q4 highest, Q1 lowest of the growth-adjusted quarters).
4. Engineer features:
   - `time`: a sequential index (1 to 93) capturing the long-term trend.
   - `q4_dummy`: holiday quarter indicator (fiscal Q4, roughly Nov to Jan).
   - `q1_dummy`: post-holiday quarter indicator (fiscal Q1, roughly Feb to Apr).
   - Fiscal Q2 and Q3 act as the baseline (reference) category.
5. Fit an OLS regression of revenue on the trend and the two seasonal dummies.
6. Validate visually by overlaying predicted revenue on actual revenue.
7. Build synthetic future rows for the next four quarters and generate point forecasts with 80 percent prediction intervals.

## Model

Fitted equation (revenue in USD millions):

```
revenue = 8,490.5 + 168.8 * time + 4,675.9 * q4_dummy - 386.3 * q1_dummy
```

| Term | Coefficient | Std error | t | p-value | Interpretation |
|------|-------------|-----------|-------|---------|----------------|
| Intercept | 8,490.5 | 413.5 | 20.53 | 0.000 | Baseline revenue at the start of the series |
| time | 168.8 | 6.79 | 24.87 | 0.000 | About 169 million added revenue per quarter |
| q4_dummy | 4,675.9 | 442.3 | 10.57 | 0.000 | Holiday quarter lift over baseline |
| q1_dummy | -386.3 | 448.7 | -0.86 | 0.392 | Not statistically different from baseline |

Model fit: R-squared 0.894, adjusted R-squared 0.891, F-statistic 251.3 (p = 2.55e-43).

## Key Findings

- **Steady growth.** Target's revenue rises by roughly 169 million dollars per quarter over the sample, and the trend is highly significant.
- **Strong holiday effect.** The holiday quarter adds about 4,676 million dollars above the baseline quarters, the single largest seasonal driver and highly significant.
- **No meaningful post-holiday dip.** The Q1 indicator is small and not statistically significant (p = 0.39), meaning the post-holiday quarter is not reliably different from the Q2 and Q3 baseline once the trend is accounted for.
- **High explanatory power.** The three predictors together explain about 89 percent of the variation in quarterly revenue.

## Forecast

Point forecasts with 80 percent prediction intervals for the four quarters following the sample:

| Quarter ending | Fiscal quarter | Forecast (USD M) | 80% interval |
|----------------|----------------|------------------|--------------|
| 2024-04-30 | Q1 | 23,970 | 21,616 to 26,325 |
| 2024-07-31 | Q2 | 24,525 | 22,196 to 26,855 |
| 2024-10-31 | Q3 | 24,694 | 22,363 to 27,025 |
| 2025-01-31 | Q4 | 29,539 | 27,183 to 31,894 |

The holiday quarter forecast again stands out, consistent with the historical seasonal pattern.

## Limitations and Next Steps

- **Residual autocorrelation.** The Durbin-Watson statistic is 0.152, well below 2, indicating strong positive autocorrelation in the residuals. This is common in trending time series and means the reported standard errors are likely understated. Reasonable extensions include adding autoregressive terms, using Newey-West robust standard errors, or modeling growth on a log scale.
- **Constant linear trend.** The model assumes a fixed dollar increase per quarter. Revenue appears to accelerate after 2020, which a linear trend does not fully capture.
- **Structural shifts.** The model does not account for one-off events (for example the pandemic-era demand jump) that a longer-horizon forecast should treat with care.

## Tech Stack

Python, pandas, numpy, statsmodels, matplotlib.

## How to Run

```bash
pip install numpy pandas matplotlib statsmodels
```

Place `qSales_2024.csv` in the working directory and run the notebook top to bottom. It loads the data, fits the model, and produces the plots and forecast table.

## Author

Abaan Iqbal
Waterloo, ON
abaaniqbal128@gmail.com
