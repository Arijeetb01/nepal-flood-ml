readme = """# Nepal Flood & Weather: River Discharge Prediction

Predicts daily river discharge (`river_discharge_m3s`) at 10 Nepali river stations from weather data, using linear regression and XGBoost.

**Dataset:** Nepal Flood & Weather Dataset 2023-2026 (Kaggle). The CSV is not included in this repo; download it from Kaggle and place it next to the notebooks.

## Notebooks
| Notebook | What it does |
|---|---|
| `nepal_flood_linear_regression.ipynb` | Linear regression baseline |
| `nepal_flood_xgboost.ipynb` | XGBoost, compared side by side with linear regression |

## How to run
1. Download the CSV from Kaggle into this folder.
2. `pip install -r requirements.txt`
3. Open a notebook and choose **Kernel > Restart & Run All**.

## Data issue found
All 2023 rows (3,650) have their weather columns shifted by one position relative to the header. For example, `temperature_mean_c` holds soil moisture. The notebooks realign them automatically.

## Method
- **Target:** log(1 + discharge), because flows range from about 0 to 3,000+ m3/s.
- **Features:** weather variables, rolling rainfall (3/7/14/30 days), lagged rainfall and soil moisture, seasonality (sine/cosine of day of year), and one-hot station.
- **Split:** chronological. Train before 2025-09-01; test 2025-09-01 to 2026-08-31 (a full monsoon season).
- **Model A:** weather + station only.
- **Model B:** Model A plus yesterday's discharge.

## Results (test year)
| Model | R2 (log) | R2 (m3/s) | MAE | RMSE |
|---|---|---|---|---|
| Linear, Model A | 0.890 | 0.336 | 12.2 | 53.2 |
| Linear, Model B | 0.992 | 0.956 | 2.17 | 13.6 |
| XGBoost, Model A | _fill in_ | _fill in_ | _fill in_ | _fill in_ |
| XGBoost, Model B | _fill in_ | _fill in_ | _fill in_ | _fill in_ |

## Notes and limitations
- Model B's high score comes mostly from river persistence (today's flow is close to yesterday's), not weather skill. It only applies if yesterday's gauge reading is available.
- Rain, precipitation and soil-moisture columns are strongly correlated, so individual linear coefficients should not be read as physical effects.
- Weather-only performance is uneven across stations; Bhada Bridge is the hardest.
- No flood/no-flood label is in this file, so this predicts discharge, not flood events.
