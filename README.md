# Surface Temperature Analysis and Forecasting in Bologna

Time-series analysis and forecasting of daily 2 m air temperature (`t2m`) in Bologna, Italy, using ERA5 reanalysis data (1952-2022).

## Project Summary

This project analyzes long-term climate behavior in Bologna and builds forecasting models for daily mean temperature.

- Source data: ERA5 hourly `t2m` at Bologna grid point (44.5 N, 11.25 E)
- Time coverage: 1952-01-01 to 2022-12-31
- Raw volume: 622,392 hourly observations
- Modeling target: daily mean temperature (`temp_mean`)

The workflow combines exploratory climate analysis, time-series feature engineering, and model comparison across statistical and machine learning methods.

## Key Findings

- Warming trend: `+0.157 deg C per decade` (about `+1.10 deg C` over 1952-2022)
- Warmest month: July (about 24.6 deg C daily mean)
- Coldest month: January (about 3.9 deg C daily mean)
- Extreme events trend:
  - Hot days (>95th percentile, 32.4 deg C): `+1.6 days/decade`
  - Cold days (<5th percentile, -2.0 deg C): `-1.8 days/decade`
- Best forecasting model on test set (2020-2022): **Gradient Boosting**

## Model Performance (Test Set: 2020-2022)

| Model | MAE | RMSE | MAPE |
|---|---:|---:|---:|
| Gradient Boosting | 1.101 | 1.431 | 27.801 |
| Random Forest | 1.140 | 1.475 | 29.504 |
| SARIMA | 1.156 | 1.524 | 26.638 |
| Persistence | 1.174 | 1.538 | 25.324 |
| Seasonal Naive | 2.758 | 3.457 | 78.262 |

## Repository Contents

- `forecast_t2m.ipynb`: Full pipeline (EDA, feature engineering, model training, diagnostics, and forecasting)
- `ERA5_t2m_hourly_Bologna_1952-2022_rd.nc`: Input dataset (NetCDF)
- `README.md`: Project documentation

## Data

The notebook expects this ERA5 NetCDF file:

- `ERA5_t2m_hourly_Bologna_1952-2022_rd.nc`

In `forecast_t2m.ipynb`, the current path is:

```python
DATA_PATH = '~/Projects/Aiproject/ERA5_t2m_hourly_Bologna_1952-2022_rd.nc'
```

Update this path to your local file location before running.

ERA5 access:
- ECMWF Copernicus Climate Data Store: https://cds.climate.copernicus.eu/
- ERA5 documentation: https://confluence.ecmwf.int/display/CKB/ERA5

## Method Overview

1. Data preprocessing
- Kelvin to Celsius conversion
- Hourly to daily aggregation (`mean`, `min`, `max`, and `temp_range`)

2. Feature engineering (16 features total)
- Calendar features: `day_of_year`, `month`, `year`
- Cyclical encoding: `sin_day`, `cos_day`
- Lag features: `lag_1`, `lag_2`, `lag_3`, `lag_7`, `lag_14`, `lag_30`, `lag_365`
- Rolling stats: `rolling_mean_7`, `rolling_std_7`, `rolling_mean_30`

3. Time-based split
- Train: 1952-12-31 to 2017-12-31 (23,742 days)
- Validation: 2018-01-01 to 2019-12-31 (730 days)
- Test: 2020-01-01 to 2022-12-31 (1,096 days)

4. Models compared
- Baselines: Persistence, Seasonal Naive
- Statistical: SARIMA
- ML: Random Forest, Gradient Boosting

5. Evaluation metrics
- MAE, RMSE, MAPE

## Forecasting Output

The notebook includes recursive future forecasting with the best model (Gradient Boosting):

- 7-day forecast starting 2023-01-01
- 30-day forecast summary (January 2023 profile)

Example 7-day output from the notebook:

| Date | Forecasted temp_mean |
|---|---:|
| 2023-01-01 | 8.16 |
| 2023-01-02 | 7.05 |
| 2023-01-03 | 6.41 |
| 2023-01-04 | 5.89 |
| 2023-01-05 | 5.81 |
| 2023-01-06 | 5.97 |
| 2023-01-07 | 6.17 |

## Reproduce Locally

### 1) Create environment

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 2) Install dependencies

```bash
pip install numpy pandas xarray matplotlib scipy scikit-learn statsmodels jupyter netCDF4
```

### 3) Run notebook

```bash
jupyter notebook forecast_t2m.ipynb
```

## Suggested Next Improvements

- Add multivariate predictors (humidity, precipitation, pressure, wind)
- Quantify forecast uncertainty (prediction intervals)
- Test deep learning baselines (LSTM/Transformers)
- Package notebook code into a reusable Python module or CLI

## Author

Mohamed Mostafa Fathy Abdelgaber
