# Fraud Detection Using Machine Learning

**Task 3 — Fraud Detection**

## Objective

Build a complete ML pipeline to detect fraudulent credit card transactions in a highly imbalanced dataset,
comparing Logistic Regression and Random Forest, with SMOTE applied correctly (training data only, inside a
pipeline, to avoid data leakage) and evaluation focused on fraud-appropriate metrics rather than accuracy
alone. Every number below comes from actually running the notebook's pipeline.

## Dataset

- **Name:** Credit Card Fraud Detection dataset (Kaggle / ULB Machine Learning Group)
- **Source:** anonymised European cardholder transactions, September 2013
- **Raw size:** 284,807 transactions × 31 columns — **1,081 duplicate rows found and removed** (about 0.38%)
  → **283,726 transactions** used for analysis and modelling
- **Target variable:** `Class` (0 = legitimate, 1 = fraudulent)
- **Fraudulent transactions:** 473 (**0.167%** of the deduplicated dataset — roughly 1 in every 600
  transactions)
- **Features:** `Time` (seconds since the first transaction — not a clock time), `Amount`, and `V1`–`V28`
  (anonymised PCA components of the original transaction data)

## Technologies

```text
Python
Pandas
NumPy
Scikit-learn
imbalanced-learn
Matplotlib
Seaborn
Jupyter Notebook
```

## Models

```text
Logistic Regression
Random Forest
```

## Imbalance Handling

SMOTE (`sampling_strategy=0.1`, bringing the minority class to 10% of the majority class's training count)
applied **only inside each model's training pipeline** (`imblearn.pipeline.Pipeline`), fit exclusively on
`X_train`/`y_train`. The test set is never touched by SMOTE, so it remains representative of real,
unseen transaction data. A partial (0.1) rather than full 1:1 oversampling ratio was chosen deliberately —
full balancing would mean generating well over 200,000 synthetic fraud examples from just 378 real training
cases, which is a large amount of purely synthetic data relative to genuine examples.

## Evaluation Metrics

Accuracy is reported for reference only and **explicitly not treated as the primary metric** — with fraud at
0.167% of transactions, a model predicting every transaction as legitimate would already score above 99.8%
accuracy while catching zero fraud. Precision, recall, F1-score, and ROC-AUC (computed with respect to the
fraud class) are used instead, since each of them can actually distinguish a genuinely useful model from that
kind of default-majority shortcut.

## Workflow

```text
Data Loading → Data Quality Check → Class Imbalance Analysis → EDA
→ Train/Test Split (80/20, stratified) → SMOTE (training data only, inside pipeline)
→ Model Training (Logistic Regression, Random Forest) → Evaluation
→ ROC-AUC Analysis → Feature Importance → Model Comparison
→ Scalability Analysis → Conclusion
```

## Results

Stratified 80/20 split (`random_state=42`) — 226,980 training / 56,746 test transactions (378 / 95 fraud
cases respectively).

| Model | Accuracy | Precision | Recall | F1-Score | ROC-AUC |
|---|---|---|---|---|---|
| Logistic Regression | 0.9977 | 0.4000 | **0.8000** | 0.5333 | **0.9664** |
| **Random Forest** | **0.9995** | **0.9136** | 0.7789 | **0.8409** | 0.9411 |

**Confusion matrix counts (test set, 95 real fraud / 56,651 real legitimate):**

| Model | True Positives | False Negatives | False Positives |
|---|---|---|---|
| Logistic Regression | 76 | 19 | 114 |
| Random Forest | 74 | 21 | 7 |

## Key Findings

- Neither model wins on every metric: **Logistic Regression has the better recall and ROC-AUC**; **Random
  Forest has the far better precision and F1-score.** Logistic Regression's higher recall comes at the cost
  of 16x more false positives (114 vs. 7) for only 2 additional caught fraud cases.
- `V14`, `V10`, `V17`, and `V12` are the Random Forest's most important features by a wide margin; `V14` and
  `V10` also carry the largest-magnitude Logistic Regression coefficients — an independent point of agreement
  between two very differently structured models.
- `Amount` alone is not a clean separator between fraud and legitimate transactions — fraud has a *lower*
  median amount but *higher* mean than legitimate transactions, and `Amount` does not appear in the Random
  Forest's top 15 features.
- Fraud *rate* (not raw count) is notably elevated during low-traffic early-morning hours (~1.0–1.5% vs. the
  0.167% overall rate), though absolute fraud counts per hour remain small throughout.

## Deployment Recommendation

**Random Forest** is recommended, based on the trade-off analysis in the notebook: its far higher precision
and F1-score mean dramatically fewer false alarms (7 vs. 114 in this test set) for only a marginal recall
cost (2 fewer fraud cases caught) — a more operationally usable balance when false-positive volume carries a
real customer-experience and review-workload cost. A deployment scenario that weighted missed fraud far more
heavily than false-alarm cost could reasonably favour Logistic Regression's higher recall instead — the
right choice depends on the actual business cost of each error type, not a universal rule.

## Scalability

At a target of ~278 transactions/second (1 million/hour), neither model has been benchmarked at that
throughput in this notebook. Logistic Regression is lighter to store and serve (one coefficient per feature);
Random Forest (100 trees) is heavier but still generally fast at single-prediction inference. A real
deployment would need: horizontal scaling of stateless inference servers, a preprocessing pipeline that
matches training-time feature computation exactly, a tunable decision threshold (not fixed at 0.5) driven by
business cost trade-offs, and ongoing monitoring/retraining to handle concept drift as fraud patterns evolve.
Actual throughput must be verified via load-testing in a production-like environment before any capacity
claim is made.

## Limitations

- Extreme class imbalance — only 473 genuine fraud examples in the entire dataset to learn from.
- SMOTE's synthetic examples are interpolations between real fraud cases; they cannot introduce genuinely new
  fraud patterns beyond what the 378 real training examples already represented.
- Data covers only two specific days (September 2013) — fraud strategies evolve over time.
- `V1`–`V28` are anonymised PCA components with no traceable real-world meaning, limiting how concretely the
  feature-importance and coefficient findings can be explained.
- False positives (114 / 7) and false negatives (19 / 21) each represent a real cost — customer friction or
  financial loss respectively — not just numbers in a confusion matrix.
- Test-set performance reflects a stratified split of the same historical dataset, not independent,
  more-recent, or live production data.
- No throughput benchmarking was performed — the scalability discussion is a design analysis, not a measured
  result.

## Project Structure

```text
DataAnalytics-L1-FraudDetection/
├── data/
│   └── creditcard.csv
├── outputs/
│   └── screenshots/
├── Fraud_Detection.ipynb
├── README.md
└── requirements.txt
```
