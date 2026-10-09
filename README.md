# Task 2 — Electricity Demand Forecasting

**InternSpark Business Analytics Internship | Task 2: Sales Forecasting Model**

## Project overview
The assignment links to an hourly electricity-consumption dataset. Since the target data measures electricity demand in megawatts (MW), this project forecasts **daily average electricity demand**, rather than product sales. The selected series is `PJME_hourly.csv` from the provided archive.

## Deliverables
- `Sales_Forecasting_Model_Task_2.ipynb` — notebook with data preparation, exploratory chart, chronological validation, model comparison, 90-day forecast, and business interpretation. Outputs are embedded where execution was supported.
- `PJME_hourly_source.csv` — source series extracted from the supplied archive.
- `cleaned_daily_demand.csv` — prepared daily average demand time series.
- `model_evaluation_metrics.csv` — MAE, MAPE, and RMSE for both evaluated approaches.
- `90_day_demand_forecast.csv` — daily base forecast, approximate 90% prediction interval, and illustrative low/high scenarios.
- `Sales_Forecasting_Report.pdf` — report with executive summary, methodology, charts, evaluation, actions, and limitations.
- `data_quality_and_assumptions.csv` — concise log of key data-quality decisions and assumptions.
- `visualizations/` — PNG charts used in the report.

## Methodology
1. Parse timestamps and numeric demand values; remove invalid rows.
2. Average readings that share the same timestamp.
3. Reindex the observations to a continuous hourly timeline and count missing timestamps.
4. Aggregate hourly readings to daily average demand; interpolate missing daily observations to maintain a regular series.
5. Reserve the final 365 days for a chronological holdout test, avoiding random splits that could leak future information.
6. Fit an additive Holt–Winters ETS model with a 7-day seasonal cycle.
7. Compare it with a weekly seasonal-naive benchmark using MAE, MAPE, and RMSE.
8. Refit ETS to the full prepared series and generate a 90-day forecast.
9. Estimate an approximate 90% interval by resampling validation residuals. Create illustrative low/high planning scenarios at -5%/+5% of the base forecast.

## Validation results
| Model | MAE (MW) | MAPE | RMSE (MW) |
|---|---:|---:|---:|
| ETS additive trend + weekly seasonality | 5,577.3 | 19.23% | 6,321.8 |
| Weekly seasonal-naive benchmark | 4,838.8 | 16.18% | 5,781.5 |

**Interpretation:** On this particular holdout period, the weekly seasonal-naive benchmark outperformed ETS on the three reported error metrics. This is an important result, not something to hide: the simplest benchmark should remain a serious candidate. The notebook includes both so the model choice is evidence-based.

## Forecast period
- Historical data ends: **2018-08-03**
- Forecast dates: **2018-08-04 to 2018-11-01**
- Horizon: **90 days**

## Business recommendations
- Use the base forecast as a planning reference and review the upper prediction interval when thinking about reserve capacity.
- Stress-test operational plans using the ±5% low/high scenarios; these are sensitivity assumptions, not probabilities.
- Monitor errors as new observations arrive; keep the seasonal-naive benchmark if it continues to perform better.
- For stronger production forecasting, add external drivers such as temperature, holidays, and calendar effects, then revalidate chronologically.
- Maintain a log of missing timestamps and interpolated observations so estimated values are not mistaken for actual measurements.

## Assumptions and limitations
- `PJME_MW` is electricity demand in MW. The assignment title says sales forecasting, but the provided dataset supports demand forecasting.
- Duplicate timestamps are averaged. Daily demand is the average of available hourly readings.
- Missing daily values are time-interpolated; this is a preprocessing assumption and can smooth genuine peaks or dips.
- The ETS model includes weekly seasonality but not explicit annual seasonality, weather, holidays, or structural breaks.
- The residual-bootstrap prediction interval is approximate and depends on the holdout residuals being informative about future errors.
- The ±5% low/high scenarios are illustrative planning sensitivities, not separately trained models or confidence bounds.
- The dataset ends in **2018**. Results are for an academic historical exercise and should not be described as a current operational forecast.

## Running the notebook
Keep `PJME_hourly_source.csv` in the same directory as the notebook. Dependencies: `pandas`, `numpy`, `matplotlib`, and `statsmodels`. In a fresh Google Colab session, upload the source CSV and update `DATA_PATH` if needed.
