# Customer Segmentation Analysis

**AICTE Oasis Infobyte Internship — Data Analytics, Level 1, Task 2**

## Overview

This project segments the customers of a UK-based online gift retailer into distinct behavioural groups using RFM (Recency, Frequency, Monetary) analysis and K-Means clustering, then translates those segments into specific, targeted marketing recommendations.

## Objective

Apply clustering algorithms to an e-commerce transaction dataset to identify meaningful customer segments based on purchasing behaviour, and recommend a marketing strategy tailored to each segment.

## Dataset

The classic **UCI Online Retail dataset** — 541,909 transaction-line rows across 8 columns (`InvoiceNo`, `StockCode`, `Description`, `Quantity`, `InvoiceDate`, `UnitPrice`, `CustomerID`, `Country`), covering December 2010 – December 2011 for a non-store online retailer.

## Data Cleaning

- Removed 135,080 rows (24.9%) with missing `CustomerID`, since transactions can't be attributed to a customer without one.
- Removed 5,225 exact duplicate rows.
- Removed 40 rows with `UnitPrice <= 0` (bookkeeping/adjustment entries, not real purchases or returns).
- **Cancelled invoices (returns) were deliberately kept, not removed** — flagged with an `IsCancellation` column and used as a genuine "returns" signal in the RFM features, rather than being discarded.
- Result: 401,564 clean transaction rows, of which 8,872 (2.2%) are cancellations, across 4,371 unique customers.

## RFM Analysis

- **Recency** — days since the customer's most recent transaction (purchase or return), relative to a reference date one day after the last transaction in the dataset.
- **Frequency** — count of unique *purchase* invoices only (cancellations excluded), so it reflects genuine buying behaviour.
- **Monetary** — net spend per customer (`Quantity × UnitPrice`, summed across all transactions including returns), so returns reduce a customer's total rather than being ignored. This means Monetary can be negative for customers who returned more than they bought.

Because Frequency and Monetary were both strongly right-skewed (skewness 12.08 and 21.70 respectively), both were log-transformed before scaling. Since Monetary can be negative, a **signed log transform** (`sign(x) × log1p(|x|)`) was used instead of the standard `log1p`. All features were then standardised with `StandardScaler`.

## Clustering Method

K-Means clustering was applied to the standardised Recency, log-Frequency, and signed-log-Monetary features.

## Elbow Method

Inertia was calculated for K = 2 through 10. The reduction rate slowed sharply after **K=5** (dropping from a 23.5% reduction at K=4→5 to 13.5% at K=5→6), identifying K=5 as the clearest elbow.

## Cluster Profiles

| Segment | Avg Recency (days) | Avg Frequency (orders) | Avg Monetary (£) | Customers | % of Total |
|---|---|---|---|---|---|
| High-Value Loyal Customers | 16.4 | 17.2 | 9,894.70 | 494 | 11.3% |
| Promising Regular Customers | 39.8 | 4.6 | 1,697.40 | 1,390 | 31.8% |
| New / Low-Engagement Customers | 58.1 | 1.5 | 451.10 | 1,500 | 34.3% |
| At-Risk / Dormant Customers | 256.8 | 1.4 | 394.20 | 933 | 21.3% |
| Net-Negative Returners | 227.5 | 0.4 | -245.00 | 54 | 1.2% |

## Key Insights

- 4,371 customers were segmented into 5 behaviourally distinct groups.
- **High-Value Loyal Customers** (11.3%) drive disproportionate revenue — ~17 orders and ~£9,895 net spend on average.
- **New / Low-Engagement Customers** (34.3%) is the single largest segment, representing the biggest growth opportunity.
- **At-Risk / Dormant Customers** (21.3%) haven't purchased in roughly 8.5 months on average.
- A small but genuine **Net-Negative Returners** segment (1.2%, 54 customers) has negative average net spend — a finding only visible because returns were netted into Monetary rather than discarded.

## Marketing Recommendations

See Section 18 of the notebook for the full, segment-specific recommendations. In brief: VIP treatment and premium cross-sell for High-Value Loyal Customers; loyalty incentives and threshold offers for Promising Regular Customers; low-friction onboarding follow-up for New/Low-Engagement Customers; time-limited win-back campaigns for At-Risk/Dormant Customers; and manual review (not blanket marketing spend) for the small Net-Negative Returners group.

## Technologies Used

- Python
- Jupyter Notebook
- pandas, NumPy
- scikit-learn (KMeans, StandardScaler)
- matplotlib, seaborn
- SciPy (skewness calculation)

## Project Structure

```
DataAnalytics-L1-CustomerSegmentation/
├── data/
│   └── Online Retail.csv
├── outputs/
│   └── screenshots/
├── Customer_Segmentation_Analysis.ipynb
├── README.md
└── requirements.txt
```

## Limitations

- The dataset covers a specific ~13-month historical period for a single retailer; findings may not generalise to other periods or businesses.
- Monetary reflects net spend *observed within this dataset's window only* — it is a defensible proxy, not a true predictive customer lifetime value.
- K-Means requires choosing K in advance; the Elbow Method gives a defensible choice (K=5), not a provably "correct" one.
- Results are sensitive to preprocessing choices — specifically, netting returns into Monetary, excluding cancellations from Frequency, and using a signed-log transform. Different reasonable choices could produce different segments.
- The dataset is dominated by UK transactions, so findings may not generalise across other geographic markets.
- The Net-Negative Returners segment is very small (54 customers) and may partly reflect account/bookkeeping quirks (e.g. returns of purchases made before the dataset window) rather than a stable behavioural customer type.

## Conclusion

This project applied RFM analysis and K-Means clustering to segment real e-commerce transaction data into five behaviourally distinct, interpretable customer groups, and converted those groups into differentiated, actionable marketing strategies grounded in the customers' actual purchasing behaviour rather than assumed archetypes.
