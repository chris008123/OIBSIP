# Exploratory Data Analysis of Retail Sales

## AICTE Oasis Infobyte Internship — Data Analytics

**Level 1 — Task 1**

## Project Overview

This project performs Exploratory Data Analysis (EDA) on a retail sales dataset as part of the AICTE Oasis Infobyte Data Analytics Internship.

The objective is to explore the dataset, identify sales trends, understand customer demographics, analyze product-category performance, examine relationships between numerical variables, and generate actionable business insights.

## Objectives

* Understand the structure and quality of the retail sales dataset.
* Calculate descriptive statistics for numerical variables.
* Analyze monthly and quarterly sales trends.
* Examine customer demographics based on age and gender.
* Analyze revenue and quantity sold across product categories.
* Identify relationships between numerical variables using correlation analysis.
* Create visualizations to communicate important findings.
* Develop actionable business recommendations based on the analysis.

## Dataset

The dataset contains **1,000 retail transactions** and includes the following variables:

| Column           | Description                            |
| ---------------- | -------------------------------------- |
| Transaction ID   | Unique identifier for each transaction |
| Date             | Date of the transaction                |
| Customer ID      | Identifier for the customer            |
| Gender           | Customer gender                        |
| Age              | Customer age                           |
| Product Category | Category of the purchased product      |
| Quantity         | Number of units purchased              |
| Price per Unit   | Price of one unit                      |
| Total Amount     | Total transaction value                |

## Tools and Technologies

* Python
* Pandas
* NumPy
* Matplotlib
* Seaborn
* Jupyter Notebook

## Analysis Performed

### 1. Data Inspection

The dataset was inspected for:

* Dataset dimensions
* Data types
* Missing values
* General data quality

The dataset contains **1,000 rows and 9 columns**, with no missing values in the available data.

### 2. Descriptive Statistics

The analysis calculated:

* Mean
* Median
* Mode
* Standard deviation

Key findings include an average customer age of approximately **41 years** and an average transaction quantity of approximately **2.5 items**.

### 3. Sales Trends

Monthly and quarterly sales trends were analyzed to identify periods of stronger and weaker sales performance.

The highest monthly sales occurred in **May 2023**, while **Q4 2023** recorded the highest quarterly sales.

### 4. Customer Demographics

Customer transactions were analyzed by age group and gender.

The **45–54** age group recorded the highest number of transactions, while the gender distribution was almost evenly balanced:

* Female: 510 transactions
* Male: 490 transactions

### 5. Product Category Analysis

Revenue and quantity sold were analyzed across product categories.

**Electronics** generated the highest total revenue at **156,905**, while **Clothing** recorded the highest quantity sold at **894 units**.

This demonstrates that the category with the highest number of units sold does not necessarily generate the highest revenue.

### 6. Correlation Analysis

A correlation matrix and heatmap were used to examine relationships between numerical variables.

The strongest relationship was between **Price per Unit and Total Amount**, with a correlation of approximately **0.852**.

Quantity had a moderate positive correlation with Total Amount of approximately **0.374**.

Age showed very weak relationships with the main purchasing variables.

### 7. Additional Analysis

Average transaction value was compared by gender.

The results were almost identical:

* Female: approximately **456.55**
* Male: approximately **455.43**

This suggests that gender does not have a meaningful effect on average transaction value in this dataset.

## Key Business Recommendations

1. **Prioritize high-value Electronics products** because Electronics generated the highest total revenue.

2. **Use seasonal sales patterns for planning** by preparing inventory and promotional campaigns ahead of historically stronger sales periods.

3. **Analyze pricing carefully** because Price per Unit has the strongest relationship with Total Amount.

4. **Avoid relying heavily on gender-based targeting** because average transaction values for male and female customers are almost identical.

5. **Encourage larger purchases** through product bundles, cross-selling, and quantity-based promotions.

## Dataset Limitations

The dataset contains product categories but does not contain individual product names. Therefore, a genuine **Top 10 best-selling product** ranking could not be calculated without inventing product-level information.

The analysis therefore focuses on product-category performance using revenue and quantity sold.

The dataset also contains only part of **January 2024**, so the January 2024 and Q1 2024 figures should not be interpreted as complete-period performance.

## Project Structure

```text
DataAnalytics-L1-EDARetailSales/
│
├── data/
│   └── retail_sales.csv
│
├── outputs/
│   └── screenshots/
│
├── EDA_Retail_Sales.ipynb
├── README.md
└── requirements.txt
```

## Conclusion

The analysis demonstrates how exploratory data analysis can be used to identify sales patterns, understand customer behavior, evaluate product-category performance, and support business decision-making.

The findings suggest that product pricing, category performance, transaction quantity, and seasonal sales patterns are more useful indicators of transaction value than customer age or gender in this dataset.
