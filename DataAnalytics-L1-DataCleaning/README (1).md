# Data Cleaning and Preparation

**AICTE Oasis Infobyte Internship — Data Analytics, Level 1, Task 3**

## Overview

This project takes a deliberately messy, real-world-style dataset of cafe sales transactions and
transforms it into a clean, verified, analysis-ready dataset using pandas and NumPy. Every cleaning
decision is investigated against the actual data and documented with its reasoning — no missing values,
duplicates, outliers, or dtype problems were assumed or invented.

## Objective

Demonstrate professional-level data cleaning by:
- Producing a full data-quality report before touching the data
- Investigating and handling missing values on a per-column basis (not one blanket method)
- Detecting duplicates, inconsistent formatting, invalid dates, wrong data types, and outliers
- Documenting every decision, and honestly recording when a "typical" data-quality problem was **not**
  found in this particular dataset
- Saving a new, cleaned CSV file without overwriting the original

## Dataset

**Cafe Sales — Dirty Data for Cleaning Training**
Source: Kaggle, `ahmedmohamed2003/cafe-sales-dirty-data-for-cleaning-training`

10,000 synthetic cafe sales transactions across 8 columns: `Transaction ID`, `Item`, `Quantity`,
`Price Per Unit`, `Total Spent`, `Payment Method`, `Location`, `Transaction Date`.

## Initial Data Quality Issues

- Every column was loaded as text, including the numeric and date columns
- Missing values existed both as blank cells **and** disguised as the literal strings `"ERROR"` and
  `"UNKNOWN"` — a hidden problem `isnull()` alone did not catch
- `Payment Method` (~32%) and `Location` (~40%) were missing/placeholder in a large share of rows, with no
  other column able to predict them
- `Quantity`, `Price Per Unit`, `Total Spent`, and `Item` were each missing/placeholder in roughly 5–10% of
  rows
- `Transaction Date` was missing/placeholder in ~4.6% of rows

Issues that were checked for but **not found** in this dataset (documented rather than fabricated):
- No exact duplicate rows or duplicate Transaction IDs
- No casing/whitespace inconsistencies in any categorical column
- No mixed date formats — all present dates were already `YYYY-MM-DD`
- No negative or zero quantities/prices, and no other impossible numeric values

## Cleaning Methods

The disguised placeholders `"ERROR"`/`"UNKNOWN"` were first standardised to real `NaN` across the whole
dataset so every subsequent pandas operation treated missingness consistently.

## Missing Data Handling

| Column | Missing | Strategy | Reason |
|---|---|---|---|
| `Total Spent` / `Price Per Unit` / `Quantity` | ~5% each | Calculated from the other two using the verified rule `Total = Quantity × Price` (0 mismatches across 8,544 fully-known rows) | Recovers the exact value with certainty rather than an estimate |
| `Item` | ~9.7% | Recovered from `Price Per Unit` only where the price uniquely identifies one product (4 of 6 price points); otherwise labelled `"Unknown Item"` | Two price points ($3.00, $4.00) are shared by two products each and cannot be distinguished |
| `Payment Method` / `Location` | ~32% / ~40% | Labelled `"Unknown"` | No other column reliably predicts these; mode imputation would fabricate certainty for a third to two-fifths of the data |
| `Transaction Date` | ~4.6% | Kept as `NaT` after conversion | No reliable column to reconstruct a transaction date from |

After recovery, only 38 `Quantity`, 38 `Price Per Unit`, 40 `Total Spent`, and 460 `Transaction Date` values
remained genuinely unrecoverable (rows where none of the supporting values were available), and were left as
missing rather than guessed.

## Duplicate Removal

None required. Zero exact duplicate rows and zero duplicate `Transaction ID` values were found in the raw
10,000-row dataset.

## Standardisation

Investigated and found unnecessary. Stripping whitespace and normalising case on `Item`, `Payment Method`,
and `Location` did not change the number of unique values in any of the three columns — every category was
already recorded consistently.

## Date Conversion

`Transaction Date` was converted to `datetime64` using `pd.to_datetime`. All present values were already in
a single `YYYY-MM-DD` format and parsed without a single new failure.

## Data Type Correction

| Column | Corrected dtype |
|---|---|
| `Transaction ID` | string (identifier, not a quantity) |
| `Item`, `Payment Method`, `Location` | string |
| `Quantity` | nullable integer (`Int64`) |
| `Price Per Unit`, `Total Spent` | float |
| `Transaction Date` | datetime64 |

## Outlier Detection

IQR method applied to `Quantity`, `Price Per Unit`, and `Total Spent`. `Quantity` and `Price Per Unit` had
zero outliers. `Total Spent` flagged 259 rows above the upper bound — all of them the value **$25.00**
(5 × $5.00 Salad, the maximum quantity and maximum price combination). These were inspected individually and
**retained**: they are legitimate high-value transactions, not data-entry errors, flagged only because the
underlying values are discrete rather than continuous.

## Before vs After Results

| Metric | Before | After |
|---|---|---|
| Rows | 10,000 | 10,000 |
| Columns | 8 | 8 |
| Missing Values (incl. disguised placeholders) | 10,082 | 576 |
| Duplicate Rows | 0 | 0 |
| Columns with Incorrect Data Type | 4 | 0 |

*(The 576 remaining "missing" values after cleaning are genuine `NaT`/`NaN` in `Transaction Date` — 460 —
plus the small number of `Quantity`/`Price Per Unit`/`Total Spent` rows with no supporting value to calculate
from — 116. `Payment Method`, `Location`, and `Item` are no longer counted as missing because they were
deliberately converted to the explicit label `"Unknown"`/`"Unknown Item"` rather than left blank.)*

## Final Dataset

10,000 rows × 8 columns, saved to `data/cleaned_dataset.csv`. Every column has an appropriate dtype, the
`Quantity × Price Per Unit = Total Spent` relationship holds throughout, there are no duplicate rows, and
every remaining gap is either a verified `NaT` or an explicit `"Unknown"` label rather than a silent defect.

## Technologies Used

- Python
- Jupyter Notebook
- pandas
- NumPy

## Project Structure

```text
DataAnalytics-L1-DataCleaning/
├── data/
│   ├── messy_dataset.csv
│   └── cleaned_dataset.csv
├── outputs/
│   └── screenshots/
├── Data_Cleaning.ipynb
├── README.md
└── requirements.txt
```

## Limitations

- `Payment Method` and `Location` remain unknown for roughly a third to two-fifths of transactions.
- `Item` could not be recovered where both `Item` and `Price Per Unit` were missing, or where the price
  ($3.00 or $4.00) was shared by two possible products.
- A small number of `Transaction Date` values remain missing with no reliable column to reconstruct them
  from.
- The recovery of `Quantity`/`Price Per Unit`/`Total Spent` assumes the `Total = Quantity × Price` rule held
  for the specific rows being recovered — verified on 8,544 fully-known rows with zero exceptions, but not
  independently checkable for the recovered rows themselves.
- This is a synthetic dataset built for cleaning practice; findings should not be generalised to a real cafe
  business.

## Conclusion

The original dataset's problems — hidden placeholder missing values, incorrect data types across every
column, and a handful of statistically flagged but legitimate high-value transactions — were each
investigated and resolved on their own terms rather than with a single blanket method. Values were recovered
by calculation wherever a verified relationship allowed it, and left as an honest, explicit "unknown" wherever
no such relationship existed. No rows were removed at any stage. The resulting dataset is fully typed,
duplicate-free, and internally consistent, and is ready for further analysis.
