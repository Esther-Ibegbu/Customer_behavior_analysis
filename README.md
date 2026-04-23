# Customer_behavior_analysis
## Retail analysis using Python, SQL, and PowerBI

### Contents
1. overview
2. Tools used (and PowerBI dashbooard)
3. Results and insights
4. Recommendations
5. Conclusion

## Overview

This project presents an end-to-end data analytics workflow on a customer shopping behavior dataset. The project covers data loading, exploratory data analysis (EDA), data cleaning, SQL-based analysis, dashboard development in Power BI, and final reporting through documentation and presentation.

## Tools Used
Python (Pandas) – Data loading, cleaning, and transformation
MySQL – Data querying and aggregation
Power BI – Dashboard visualization
Microsoft PowerPoint – Presentation of insights
Microsoft Word / PDF – Reporting

## Project Steps
1. Data Loading (Python)
Imported dataset using Pandas:

```python
import pandas as pd
df = pd.read_csv(r"C:\Users\840G3\Downloads\customer_shopping_behavior.csv")
```

Checked for missing values:
```python
df.isnull().sum()
```

2. Data Cleaning & Transformation
Imputed missing review_rating using median per category:
```python
df['Review Rating'] = df.groupby('Category')['Review Rating'].transform(lambda x: x.fillna(x.median()))
```
3. Standardized column names:
```python
df.columns = df.columns.str.lower()
df.columns = df.columns.str.replace(' ', '_')
df = df.rename(columns={'purchase_amount_(usd)': 'purchase_amount'})
```

4. Created age group segmentation:
```python
labels = ['Young adult', 'Adult', 'Middle_age', 'Senior']
df['age_group'] = pd.qcut(df['age'], q=4, labels=labels)
```

5. Converted purchase frequency into numeric days:
```python
labels_mapping = {
    'Fortnightly': 14, 'Weekly': 7, 'Monthly': 30,
    'Bi-weekly': 14, 'Quarterly': 90,
    'Annually': 365, 'Every 3 months': 90
}
df['purchase_frequency_days'] = df['frequency_of_purchases'].map(labels_mapping)
```
Exported cleaned dataset to MySQL for further analysis.

## SQL Analysis (MySQL)
Key business questions were answered using SQL queries:

Revenue by Gender
```sql
SELECT gender, SUM(purchase_amount) AS revenue
FROM customer
GROUP BY gender;
```
High-Spending Customers Using Discounts
```sql
SELECT customer_id, purchase_amount
FROM customer
WHERE discount_applied = 'Yes'
AND purchase_amount >= (SELECT AVG(purchase_amount) FROM customer);
```
Top 5 Highest Rated Products
```sql
SELECT item_purchased, ROUND(AVG(review_rating), 2) AS average_product_rating
FROM customer
GROUP BY item_purchased
ORDER BY average_product_rating DESC
LIMIT 5;
```
Shipping Type vs Average Purchase
```sql
SELECT shipping_type, AVG(purchase_amount)
FROM customer
GROUP BY shipping_type
HAVING shipping_type IN ('Standard', 'Express');
```
Subscription Impact on Revenue
```sql
SELECT subscription_status,
COUNT(customer_id) AS total_customers,
ROUND(SUM(purchase_amount),2) AS revenue,
ROUND(AVG(purchase_amount),2) AS average_spend
FROM customer
GROUP BY subscription_status;
```
Customer Segmentation
```sql
WITH customer_type AS (
SELECT customer_id, previous_purchases,
CASE
WHEN previous_purchases = 1 THEN 'New'
WHEN previous_purchases BETWEEN 2 AND 10 THEN 'Returning'
WHEN previous_purchases >= 11 THEN 'Loyal'
END AS segment
FROM customer)
SELECT segment, COUNT(*) AS total_customers
FROM customer_type
GROUP BY segment;
```

## Dashboard (Power BI) [View Dashboard](https://github.com/Esther-Ibegbu/Customer_behavior_analysis/blob/26069485506753fe8b8e0018a2ed2d8c371b26be/Customerr%20Dashboard.png)
An interactive Power BI dashboard was developed to visualize insights.

Key Features:
KPI cards for total revenue, average spend, and average review rating
Revenue breakdown by gender, age group, and subscription status
Product performance and category analysis
Filters for categories and shipping type.

# 3. Results & Insights
The analysis revealed several key insights:

1. Customer Behavior: Young adults contribute more significantly to revenue.
2. Subscription Impact: Subscribed customers tend to generate higher total revenue.
3. Product Insights: A small number of products (Hats, blouses, jewelries and sunglasses) dominate both sales volume and ratings.
4. Discount Strategy: Discounts can drive higher spending when targeted correctly.
5. Customer Segmentation: Loyal customers form a smaller group but contribute disproportionately to revenue.

# 4. Recommendations
1. Target High-Value Age Segment (Young Adults): Develop targeted marketing campaigns tailored to their preferences (social media, mobile-first promotions, influencer marketing).
2. Expand and Incentivize Subscription Programs: Subscribed customers generate higher revenue, which makes subscriptions a key growth lever. Offer exclusive benefits (early access, special discounts, free shipping), target repeat customers with subscription offers, and introduce tiered subscription plans.
3. Leverage Top-Performing Products: Ensure consistent stock availability of high-performing items, feature them prominently in marketing campaigns and homepage displays.
4. Optimize Discount Strategy: Focus discounts on: high-value customers, bulk purchases, slow-moving inventory. Avoid over-discounting top-performing products. Test targeted promotions instead of blanket discounts, the goal is to ncrease revenue without eroding profit margins.

# 5. Conclusion
This project demonstrates a structured approach to data analytics, combining Python, SQL, and Power BI to extract meaningful insights from retail data.
