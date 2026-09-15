# Unveiling the Android App Market — Google Play Store Analysis

## Objective

An end-to-end analysis of the Google Play Store ecosystem — app metadata and user reviews — to understand category saturation, ratings, pricing, installs, estimated revenue, and user sentiment, and to produce actionable insights for someone planning to launch a new Android application.

## Datasets

1. **Google Play Store Apps** (`googleplaystore.csv`) — metadata for ~10,800 Android apps: category, rating, size, installs, price, type, content rating, genres, and version info.
2. **Google Play Store User Reviews** (`googleplaystore_user_reviews.csv`) — ~64,000 user reviews linked to apps by name, including pre-existing sentiment labels (re-analyzed independently in this project using VADER).

**Source:** the well-known "Google Play Store Apps" dataset originally published on Kaggle (lava18/google-play-store-apps). Loaded here via a verified GitHub-hosted copy, since the analysis environment used to build this project could not reach Kaggle directly.

## Technologies

```
Python
Pandas
NumPy
Matplotlib
Seaborn
NLTK (VADER)
Plotly
Jupyter Notebook
```

## Data Cleaning

- Dropped 1 structurally corrupted row (`Category` missing, shifting all later columns — caught by checking for non-numeric-looking values in a text column).
- Removed 1,181 duplicate `App` entries, keeping the version with the highest `Reviews` count.
- Removed 1 row with an invalid `Type` value.
- Converted `Reviews`, `Installs`, `Price` to numeric; converted `Size` to a new `Size_MB` column, explicitly preserving `"Varies with device"` as missing rather than zero.
- Left `Rating` missingness (1,463 rows) as missing rather than imputing, after confirming it correlates with low review counts (genuinely under-reviewed apps) rather than being random.
- In the Reviews dataset: removed 26,868 rows with no review text and 7,735 exact duplicate reviews, leaving 29,692 usable reviews for sentiment analysis.

## Analysis Performed

- Category distribution and market saturation
- Rating distribution and average rating by category (30-app minimum per category)
- App size vs. installs correlation
- Free vs. paid app distribution
- Paid app price analysis
- Estimated revenue proxy (`Price × Installs`) and revenue by category
- VADER-based sentiment analysis on user reviews
- Sentiment distribution and sentiment by app category (30-review minimum per category)
- Interactive Plotly visualization (Ratings vs. Installs, bubble-sized by review count)

## Key Findings

- **FAMILY** (1,875 apps) and **GAME** (945) are by far the most saturated categories; **BEAUTY** (53), **COMICS** (56), and **PARENTING** (60) are the least.
- Ratings cluster between 4.0–4.5 stars (mean 4.17, median 4.3); **EVENTS, ART_AND_DESIGN, and EDUCATION** have the highest category-average ratings, **DATING** the lowest.
- App size correlates only weakly with installs (Pearson r ≈ 0.13 raw, ≈ 0.30 with installs log-scaled) — size alone is not a strong install driver.
- **92.2% of apps are free**, 7.8% paid; paid app prices cluster tightly around a median of $2.99, with a handful of $400 outliers.
- The estimated revenue proxy is led by **FAMILY (~$115.7M), LIFESTYLE (~$57.6M), and GAME (~$40.7M)**, driven mainly by install volume rather than price.
- User sentiment is **67.8% positive, 19.7% negative, 12.5% neutral**; **COMICS, AUTO_AND_VEHICLES, and HEALTH_AND_FITNESS** show the strongest sentiment, **SOCIAL, VIDEO_PLAYERS, and NEWS_AND_MAGAZINES** the weakest.

## Developer Recommendations

1. **Category choice is a real trade-off between competition and audience size.** FAMILY/GAME offer scale but heavy competition; smaller categories like COMICS combine low saturation with the strongest user sentiment in the dataset.
2. **Free-with-monetization is the dominant, default model** (92.2% of apps) — and the revenue analysis shows install volume, not price point, is what actually drives the estimated revenue proxy.
3. **Category and content strategy affect sentiment more than raw app size does** — size shows only a weak relationship with installs, while sentiment varies meaningfully (0.17–0.54 average compound score) by category, likely reflecting how much a category's content invites user disagreement.

## Limitations

- Historical snapshot (apps mostly last updated 2017–2018), not a live view of today's Play Store.
- `Installs` is a rounded lower-bound range, not an exact count.
- Revenue is an **estimated gross proxy only** (`Price × Installs`) — it ignores Google's revenue share, refunds, regional pricing, subscriptions, and in-app purchases.
- Reviews reflect only users who chose to write one — sentiment skews positive partly because satisfied users are more likely to leave a review at all.
- VADER is not equivalent to human judgment and can misread sarcasm or nuanced/mixed reviews.
- Category-level comparisons used minimum sample-size thresholds (30 apps/reviews) specifically to avoid small-sample categories producing misleading extremes.
- Correlation (e.g. size vs. installs) never implies causation anywhere in this analysis.

## Conclusion

This project took two real, messy Play Store datasets through a full analytical pipeline — cleaning, exploration, correlation analysis, revenue estimation, and independent sentiment analysis — to surface genuine, data-grounded patterns in the Android app market: which categories are crowded versus open, how monetization actually splits in practice, and where user sentiment is strongest and weakest. The three developer recommendations above are offered as a starting point for launch decisions, with the stated limitations kept firmly in view.

## Project Structure

```
DataAnalytics-L1-GooglePlayStoreAnalysis/
├── data/
│   ├── googleplaystore.csv
│   └── googleplaystore_user_reviews.csv
├── outputs/
│   └── screenshots/
├── Google_Play_Store_Analysis.ipynb
├── README.md
└── requirements.txt
```

**Note:** the interactive Plotly visualization (Step 18 in the notebook) is saved as `outputs/screenshots/12_interactive_visualization.html` rather than a static PNG, since a screenshot would lose the hover/zoom/legend-filtering interactivity that's the whole point of that chart. It's fully interactive when opened in the notebook itself.
