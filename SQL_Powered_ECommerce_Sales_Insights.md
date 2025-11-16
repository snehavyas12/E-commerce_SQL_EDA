# SQL-Powered E-Commerce Sales Insights: Trends, Correlations & Performance Metrics.

> An end to end SQL EDA uncovering patterns in pricing, customer sentiment, category performance, and seasonality across 12 months of e commerce data.

--- 

## Introduction

The E-Commerce Sales Dataset sourced from Kaggle is a comprehensive collection of transactional information that reflects the dynamics of an online retail marketplace over a 12-month period. It encompasses a wide range of product categories, pricing levels, customer reviews, and monthly sales figures. This dataset provides a valuable opportunity to explore consumer behavior, market trends, and product performance across different segments of an e-commerce ecosystem. Through its detailed structure containing product identifiers, prices, review metrics, and month-wise sales volumes it enables analysts to evaluate pricing effectiveness, seasonal patterns, and customer sentiment in relation to sales outcomes. 

---

## Problem Statement (one sentence)
This project analyzes one year of e-commerce sales data using SQL to uncover relationships between product attributes, customer feedback, and sales performance for data-driven business optimization. 

---

## Objectives

1. To examine the completeness of data by detecting duplicates, null values and outliers of the key variables. 
2. Doing descriptive analysis of the product and sales attributes
3. Evaluate the relationship between price, review score, and total sales units. Test hypotheses regarding whether higher-rated or lower-priced products sell more.
4. Identify “top performers” and “underperformers” across different metrics.
5. Provide actionable recommendations for pricing, product selection, and campaign planning based on data-driven evidence.

---

## Research Questions

1. What are the major product categories in the dataset, and which contribute most to total sales and revenue?
2. How does pricing influence the total units sold — is there evidence of price sensitivity?
3. Do products with higher customer review scores generate greater sales volume?
4. Are there visible seasonal patterns or monthly trends in total sales performance?
5. Which products or categories can be classified as consistent top performers across the year?

---

## Project Summary 

The purpose of this analysis is to perform an Exploratory Data Analysis (EDA) using SQL to extract meaningful insights directly from the dataset without external analytical tools. The workflow begins with basic data profiling—counting records, detecting missing values, and identifying duplicates—to ensure data quality. It then advances toward statistical exploration, identifying outliers, evaluating correlations (such as between price, review score, and sales), and summarizing category-level trends.
Subsequent analyses highlight seasonality, top-performing products, and category-wise revenue contributions, providing a clear picture of what drives sales success in the dataset. The ultimate objective is to translate raw data into business-ready insights that could support pricing optimization, product assortment decisions, and targeted marketing strategies.

---

## Dataset Overview

* **Rows**: **1000**
* **Columns**: product_id, product_name, category, price, review_score, review_count, sales_month_1 … sales_month_12
* **Time Span**: 12 months (aggregated as month_1 … month_12)
* **Source**: Kaggle (https://www.kaggle.com/datasets/fahmidachowdhury/e-commerce-sales-analysis?utm_source=chatgpt.com)

---

## Exploratory Analysis

### 1. Count total number of rows

SELECT COUNT(*) AS total_rows
FROM ecommerce_sales;

![alt text](image.png)

#### Insights: 
There are total 1000 records. The datset is medium sized, perfect for exploratory analysis

### 2. Data Profiling

SELECT column_name, data_type
FROM information_schema.columns
WHERE table_name = 'ecommerce_sales'

![alt text](image-1.png)

#### Insights:
In the schema we have 18 columns, where product_id is the primary key, price, review_scor, review_count and 12 columns are for 12 month sales, which contains numeric datatype, are the measures of the table. The dimensions are prodcut name and product category. 

### 3. Distinct products and categories

SELECT 
   COUNT(DISTINCT product_id) AS total_products,
   COUNT(DISTINCT category) AS total_categories
FROM ecommerce_sales

![alt text](image-2.png)

-- list of categories
SELECT DISTINCT category 
FROM ecommerce_sales;

![alt text](image-16.png)

#### Insights:
We have 1000 distinct product_id meaning there aren't any duplicates in the dataset. For basic profiling we have 7 categories in the dataset as Books, Clothing, Electronics, Sports, Toys, Health, Home & Kitchen.

### 4. Overview of prices 

SELECT 
   MIN(price) AS price_min, 
   MAX(price) AS price_max, 
   AVG(price) AS price_avg 
FROM ecommerce_sales;

![alt text](image-3.png)

### 5. Overview of review scores 

SELECT 
   MIN(review_score) AS score_min, 
   MAX(review_score) AS score_max, 
   AVG(revciew_score) AS score_avg 
FROM ecommerce_sales;

![alt text](image-5.png)

### 6. Finding duplicates on product_id

SELECT product_id, COUNT(*) AS cnt
FROM ecommerce_sales
GROUP BY product_id
HAVING COUNT(*) > 1
ORDER BY cnt DSC; 

![alt text](image-4.png)

### 7. Handling nulls

SELECT
   SUM(CASE WHEN product_id IS NULL THEN 1 ELSE 0 END) AS product_id_nulls,
   SUM(CASE WHEN product_name IS NULL THEN 1 ELSE 0 END) AS product_name_nulls,
   SUM(CASE WHEN category IS NULL THEN 1 ELSE 0 END) AS category_nulls,
   SUM(CASE WHEN price IS NULL THEN 1 ELSE 0 END) AS price_nulls,
   SUM(CASE WHEN review_score IS NULL THEN 1 ELSE 0 END) AS review_score_nulls,
   SUM(CASE WHEN review_count IS NULL THEN 1 ELSE 0 END) AS review_count_nulls,
   SUM(CASE WHEN sales_month_1 IS NULL THEN 1 ELSE 0 END) AS m1_nulls,
   SUM(CASE WHEN sales_month_2 IS NULL THEN 1 ELSE 0 END) AS m2_nulls,
   SUM(CASE WHEN sales_month_3 IS NULL THEN 1 ELSE 0 END) AS m3_nulls,
   SUM(CASE WHEN sales_month_4 IS NULL THEN 1 ELSE 0 END) AS m4_nulls,
   SUM(CASE WHEN sales_month_5 IS NULL THEN 1 ELSE 0 END) AS m5_nulls,
   SUM(CASE WHEN sales_month_6 IS NULL THEN 1 ELSE 0 END) AS m6_nulls,
   SUM(CASE WHEN sales_month_7 IS NULL THEN 1 ELSE 0 END) AS m7_nulls,
   SUM(CASE WHEN sales_month_8 IS NULL THEN 1 ELSE 0 END) AS m8_nulls,
   SUM(CASE WHEN sales_month_9 IS NULL THEN 1 ELSE 0 END) AS m9_nulls,
   SUM(CASE WHEN sales_month_10 IS NULL THEN 1 ELSE 0 END) AS m10_nulls,
   SUM(CASE WHEN sales_month_11 IS NULL THEN 1 ELSE 0 END) AS m11_nulls,
   SUM(CASE WHEN sales_month_12 IS NULL THEN 1 ELSE 0 END) AS m12_nulls
FROM ecommerce_sales;

![alt text](image-6.png)

### 8. Detecting outliers 

with q AS (
   SELECT 
      percentile_cont(0.25) WITHIN GROUP (ORDER BY price) AS q1,
      percentile_cont(0.75) WITHIN GROUP (ORDER BY price) AS q3
   FROM ecommerce_sales         
)
SELECT e*
FROM ecommerce_sales, q
WHERE price < (q1 - 1.5 * (q3 - q1)) AND price > (q3 + 1.5 * (q3 - q1))
ORDER BY price

![alt text](image-7.png)

### 9. Top 5 most expensive products

SELECT product_id, category, price
FROM ecommerce_sales
ORDER BY price DESC
LIMIT 5;

![alt text](image-8.png)

### 10. Average review score per category
SELECT category, ROUND(AVG(review_score), 2) AS avg_score
FROM ecommerce_sales
GROUP BY category
ORDER BY avg_score DESC;

![alt text](image-9.png)

### 11. Months total for seasonality overview
SELECT 
   SUM(COALESCE(sales_month_1,0))  AS month_1_units,
   SUM(COALESCE(sales_month_2,0))  AS month_2_units,
   SUM(COALESCE(sales_month_3,0))  AS month_3_units,
   SUM(COALESCE(sales_month_4,0))  AS month_4_units,
   SUM(COALESCE(sales_month_5,0))  AS month_5_units,
   SUM(COALESCE(sales_month_6,0))  AS month_6_units,
   SUM(COALESCE(sales_month_7,0))  AS month_7_units,
   SUM(COALESCE(sales_month_8,0))  AS month_8_units,
   SUM(COALESCE(sales_month_9,0))  AS month_9_units,
   SUM(COALESCE(sales_month_10,0)) AS month_10_units,
   SUM(COALESCE(sales_month_11,0)) AS month_11_units,
   SUM(COALESCE(sales_month_12,0)) AS month_12_units
FROM ecommerce_sales;

![alt text](image-10.png)

#### Insights:
For seasonality, the range of total sales extends from 487194 to 514798. The month 5 i.e. May has the lowest total for thear, whereas October shows the highest total. On an average, for each month, around 501659.34 was the total sales. This shows steady sales, with some insignificant fluctuations.

### 12. Total revenue per product -- top sellers
WITH metrics AS (
   SELECT
      product_id, product_name, category, price, review_score, review_count,
      (COALESCE(sales_month_1,0)+COALESCE(sales_month_2,0)+COALESCE(sales_month_3,0)+
      COALESCE(sales_month_4,0)+COALESCE(sales_month_5,0)+COALESCE(sales_month_6,0)+
      COALESCE(sales_month_7,0)+COALESCE(sales_month_8,0)+COALESCE(sales_month_9,0)+
      COALESCE(sales_month_10,0)+COALESCE(sales_month_11,0)+COALESCE(sales_month_12,0)) AS total_units
   FROM ecommerce_sales
)

SELECT total_units * price AS total_revenue
FROM metrics
ORDER BY total_revenue DESC
LIMIT 10;  

![alt text](image-17.png)

#### Insights:
The books top the revenue, also in top 20, books appear 6 times followed by Health which appears 5 times and toys for 4 times. These products identify the top sellers, and help busniesses make decisions to promote them more. Furthermore, the categories than don't appear on the top sellers can be managed likewise. 

### 13. Total units per category 
with total_units AS (
   SELECT
      product_id, product_name, category, price, review_score, review_count,
      (COALESCE(sales_month_1,0)+COALESCE(sales_month_2,0)+COALESCE(sales_month_3,0)+
      COALESCE(sales_month_4,0)+COALESCE(sales_month_5,0)+COALESCE(sales_month_6,0)+
      COALESCE(sales_month_7,0)+COALESCE(sales_month_8,0)+COALESCE(sales_month_9,0)+
      COALESCE(sales_month_10,0)+COALESCE(sales_month_11,0)+COALESCE(sales_month_12,0)) AS total_units
   FROM ecommerce_sales
)
SELECT tu.category, SUM(total_units) AS units_per_category
FROM total_units tu
GROUP BY tu.category;

![alt text](image-12.png)

#### Insights:
In quantity, Electronics top the table. But as we just saw, Electroincs don't shine in revenue. Electronics are followed by sports and Home & Furniture, all not the top sellings. In such case, these products need to be promoted at lower prices to increase the sale. 

### 14. Do highly reviewed products sell more? 
SELECT review_score, 
   ROUND(AVG(sales_month_1 + sales_month_2 + sales_month_3 + sales_month_4 + sales_month_5 + sales_month_6 + sales_month_7 + sales_month_8 + sales_month_9 + sales_month_10 + sales_month_11 + sales_month_12), 2) AS avg_sales
FROM ecommerce_sales
GROUP BY review_score
ORDER BY avg_sales DSC;

![alt text](image-18.png)

#### Insights:
The review score of 2.1 shows the highest average sells (6373.50), whereas the review_score of 4.6 show lowest average sales of 5457.58. Overall, as we can see, ratings do not significantly affect the sales in our dataset, which means, there can be underrated gems in our inventory which can't find customers. 

### 15. Correlation of price with units
with price prod AS (
   SELECT 
      product_id, 
      price, 
      (COALESCE(sales_month_1,0)+COALESCE(sales_month_2,0)+COALESCE(sales_month_3,0)+
      COALESCE(sales_month_4,0)+COALESCE(sales_month_5,0)+COALESCE(sales_month_6,0)+
      COALESCE(sales_month_7,0)+COALESCE(sales_month_8,0)+COALESCE(sales_month_9,0)+
      COALESCE(sales_month_10,0)+COALESCE(sales_month_11,0)+COALESCE(sales_month_12,0)) AS total_units
   FROM ecommerce_table
)
SELECT corr(price, total_units) AS corr_price_units FROM price_prod;

![alt text](image-13.png)

#### Insights:
As expected, price is negatively correlated with sales. The costly products don't find as much customers. But the correlation though negative is not that strong. the coefficient -0.016 is not very significant, the reason can be in the variety of products, like Health, Books, Kitchen. 

### 16. Correlation of review score and units 
WITH review_prod AS (
  SELECT product_id, product_name, review_score, category,
         (COALESCE(sales_month_1,0)+COALESCE(sales_month_2,0)+COALESCE(sales_month_3,0)+
          COALESCE(sales_month_4,0)+COALESCE(sales_month_5,0)+COALESCE(sales_month_6,0)+
          COALESCE(sales_month_7,0)+COALESCE(sales_month_8,0)+COALESCE(sales_month_9,0)+
          COALESCE(sales_month_10,0)+COALESCE(sales_month_11,0)+COALESCE(sales_month_12,0)) AS total_units
  FROM ecommerce_sales
)
SELECT corr(review_score, total_units) AS corr_review_units 
FROM review_prod;

![alt text](image-14.png)

#### insights:
As seen in our Bi-Variate analysis, review score and sales do not share strong relationships. This is evident from our correlation coefficient. The main aim to perfoem this analysis was to see the direction of the correlation, here, we see it is negative, which is unusual. Before the business takes in policy decision on this, more data is needed. 

### 17. Lowest selling products with highest ratings
SELECT * 
FROM review_prod 
WHERE review_score >= 4.5 
ORDER BY total_units ASC 
LIMIT 10;

![alt text](image-19.png)

#### Insights:
From this output, it is safe to say that the 'Clothing' category is the most underrated category on the business. among lowet 10 selling products with ratings above 4.5, clothing appears 5 times. The marketing team can use this insight to promote their clothing more. 

### 18. Winners per category 
WITH totals AS (
   SELECT 
      product_id, 
      product_name, 
      category,
      (COALESCE(s1,0)+COALESCE(s2,0)+COALESCE(s3,0)+COALESCE(s4,0)+COALESCE(s5,0)+COALESCE(s6,0)+
      COALESCE(s7,0)+COALESCE(s8,0)+COALESCE(s9,0)+COALESCE(s10,0)+COALESCE(s11,0)+COALESCE(s12,0)) AS total_units

   FROM (
      SELECT product_id, product_name, category,
      sales_month_1 s1, sales_month_2 s2, sales_month_3 s3, sales_month_4 s4,
      sales_month_5 s5, sales_month_6 s6, sales_month_7 s7, sales_month_8 s8,
      sales_month_9 s9, sales_month_10 s10, sales_month_11 s11, sales_month_12 s12
    FROM ecommerce_sales
   ) x
),
ranked AS (
  SELECT *, ROW_NUMBER() OVER (PARTITION BY category ORDER BY total_units DESC) AS rnk
  FROM totals
)
SELECT * 
FROM ranked 
WHERE 1 
ORDER BY category, rnk;

![alt text](image-15.png)

#### Insights:
The top products in each category is product 853 (Books), 286 (Clothing), 224 (Electronics), 734 (Health), 822 (Home & Kitchen), 905 (Sports), 197 (Toys). The reason we do this analysis is to comepare winners per category with their total sales. Like in Electronics the totla sale of the winner is 9151, which is much higher than Home & Kitchen with 8337. this comparitive analysis help us locate the under perfoemers and make informed policy decision regarding them. 

## Conclusion
We did Exploratory Data Analysis on an ecommerce compay dataset. After basic profiling, we carried out some statistical analysis on the variables to extract some insights. We further explored data to find out the top and last products in terms of revenue, sales and ratings. We identified certain gaps which can be filled with policy making. Furthermore, we found some inconsistencies in our results, which pave way for further analysis and business insights. 


