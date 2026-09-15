# Wine Quality Prediction

**Task 2 — Wine Quality Prediction**

## Objective

Predict wine-quality categories from physicochemical measurements and compare three classification
algorithms — Random Forest, SGD Classifier, and SVC — across the complete ML workflow from data cleaning
through model interpretation and deployment recommendation. Every number below comes from actually running
the notebook.

## Dataset

- **Source:** UCI Machine Learning Repository — Wine Quality dataset (Cortez et al., 2009)
- **Type:** Red wine (`winequality.csv`) — stated explicitly since a separate white-wine version also exists
- **Target variable:** `quality` (original tasters' score, grouped into `Low`/`Medium`/`High` — see below)
- **Major features:** `fixed acidity`, `volatile acidity`, `citric acid`, `residual sugar`, `chlorides`,
  `free sulfur dioxide`, `total sulfur dioxide`, `density`, `pH`, `sulphates`, `alcohol`
- **Raw size:** 1,599 samples × 12 columns, no missing values, **240 duplicate rows removed** (15% of the
  data) → **1,359 samples** used for modelling

## Technologies

```text
Python
Pandas
NumPy
Scikit-learn
Matplotlib
Seaborn
Jupyter Notebook
```

## Machine Learning Models

```text
Random Forest Classifier
SGD Classifier
Support Vector Classifier (SVC)
```

## Workflow

```text
Data Loading → Data Inspection → Data Cleaning → EDA → Class Distribution Analysis
→ Feature Engineering (quality grouping) → Train/Test Split (80/20, stratified)
→ Feature Scaling (SGD/SVC only, inside pipelines) → Model Training
→ Evaluation → Model Comparison → Feature Importance → Conclusion
```

## Class Imbalance & Target Grouping

The original 6-class `quality` target (scores 3–8) is **severely imbalanced**: scores 5 and 6 make up over
80% of the data, while scores 3 and 8 combined make up under 2% (10 and 17 wines respectively after dedup).
Keeping all 6 classes would leave some test folds with only 2–3 examples — unreliable to evaluate.

**Grouped into three classes** using boundaries drawn from the natural break in the score histogram:

```text
Low    : quality <= 4   (63 wines,  4.64%)
Medium : quality 5–6    (1,112 wines, 81.82%)
High   : quality >= 7   (184 wines, 13.54%)
```

Still imbalanced, but every class now has enough examples to train and evaluate meaningfully.

## Results

Stratified 80/20 split (`random_state=42`) — 1,087 training / 272 test wines.

| Model | Accuracy | Precision (weighted) | Recall (weighted) | F1 (weighted) | F1 (macro) |
|---|---|---|---|---|---|
| Random Forest | 0.8199 | 0.7899 | 0.8199 | 0.7969 | **0.5065** |
| SGD | 0.8051 | 0.8069 | 0.8051 | 0.7862 | 0.4833 |
| **SVC** | **0.8456** | 0.8109 | **0.8456** | **0.7974** | 0.4321 |

**SVC has the best accuracy and weighted F1 — but the worst macro F1**, because it never once correctly
identifies a `Low`-quality wine in the test set (0/13). Random Forest trades a little accuracy for the most
balanced performance across all three classes.

## Key Findings

- `alcohol`, `volatile acidity`, and `sulphates` are consistently the strongest predictors of quality — both
  by correlation (0.48, −0.40, 0.25 respectively) and by Random Forest feature importance (0.152, 0.117,
  0.113), reinforcing each other via two independent methods.
- Every model's biggest weakness is the `Low` class: Random Forest catches 2/13, SGD 1/13, SVC 0/13 in the
  test set — a direct, measured consequence of having only 63 `Low` wines in the whole dataset.
- The dominant confusion for every model is minority classes (`Low`/`High`) being pulled toward `Medium`,
  not confused with each other directly.
- SGD shows an interesting precision/recall split on `Low`: 1.00 precision but 0.08 recall — when it predicts
  `Low` it's usually right, but it almost never predicts `Low` at all.

## Conclusion

**Random Forest is the recommended model**, selected on macro F1 (0.5065 — the highest of the three) and
more consistent behaviour across all quality groups, not on accuracy alone. SVC's higher headline accuracy
(0.8456) is achieved by leaning almost entirely on the majority `Medium` class while essentially abandoning
the `Low` class — a meaningful practical weakness given the project's goal of identifying low- and
high-quality wines, not just maximising overall correct-guess rate.

This is not a claim that Random Forest is production-ready. Before deployment: more `Low`/`High` training
examples, hyperparameter tuning aimed specifically at minority-class recall (e.g. `class_weight="balanced"`),
out-of-sample validation beyond this single dataset, and a context-specific decision on acceptable per-class
error rates would all be required.

## Limitations

- Severe class imbalance persists even after grouping — `Low`+`High` are under 20% of the data combined.
- Only 63 `Low`-quality wines total (50 train / 13 test) — too few for strong conclusions about real-world
  reliability on genuinely low-quality wine.
- `quality` is a subjective average of human tasters' ratings, not a directly measured physical quantity.
- Only 11 physicochemical variables are available; known real-world quality factors (grape variety, vintage,
  winemaking process) are not recorded in this dataset.
- Red wine only — findings should not be assumed to transfer to white wine without separate testing.
- The 240 removed duplicate rows are assumed to be repeated records rather than coincidentally identical
  distinct samples — reasonable given how many measurements would have to coincide by chance, but not
  absolutely certain.
- Test-set performance is a stratified split of the same dataset/time period, not an independent, truly
  out-of-sample validation.

## Project Structure

```text
DataAnalytics-L1-WineQualityPrediction/
├── data/
│   └── winequality.csv
├── outputs/
│   └── screenshots/
├── Wine_Quality_Prediction.ipynb
├── README.md
└── requirements.txt
```
