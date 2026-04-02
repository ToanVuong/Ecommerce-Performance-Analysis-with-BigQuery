# 🛒 Ecommerce Performance Analysis with BigQuery

**Website Traffic, User Behavior & Ecommerce Funnel Analysis**  
**Dataset:** Google Analytics Sample (BigQuery)

**Author:** Vuong Minh Toan  
**Tools:** Google BigQuery (Standard SQL)

***

## 📌 Project Overview

This project analyzes **ecommerce performance** using the **Google Analytics public sample dataset** on **BigQuery**.

The goal is to answer practical business questions related to:

*   Website traffic & engagement
*   Revenue performance by acquisition channel
*   Purchaser vs non‑purchaser behavior
*   Product co‑purchase patterns
*   Ecommerce funnel effectiveness

The analysis is implemented through **08 SQL queries**, each targeting a specific business insight.

***

## 🎯 Business Questions

*   How did traffic, pageviews, and transactions perform in Q1 2017?
*   Which traffic sources had the highest bounce rates?
*   Which channels generated the most revenue?
*   How do purchasers behave differently from non‑purchasers?
*   What is the average number of transactions per user?
*   How much revenue is generated per visit?
*   Which products are frequently bought together?
*   Where are the main drop‑offs in the ecommerce funnel?

***

## 📂 Dataset Description

*   **Source:** Google Analytics Sample Dataset (BigQuery Public Data)
*   **Tables used:**
    *   `ga_sessions_2017*`
    *   `ga_sessions_201707*`
*   **Domain:** Ecommerce & Digital Analytics

### Data Characteristics

*   Session‑level data
*   Nested and repeated records
*   Product‑level ecommerce events

To work with product-level data, the project extensively uses:

```sql
UNNEST(hits)
UNNEST(hits.product)
```

***

## 🧩 Key Fields Used

| Field                              | Description                |
| ---------------------------------- | -------------------------- |
| `fullVisitorId`                    | Unique visitor identifier  |
| `date`                             | Session date (`YYYYMMDD`)  |
| `totals.visits`                    | Session count              |
| `totals.pageviews`                 | Pageviews per session      |
| `totals.bounces`                   | Bounce indicator           |
| `totals.transactions`              | Number of transactions     |
| `trafficSource.source`             | Traffic acquisition source |
| `hits.eCommerceAction.action_type` | Ecommerce action type      |
| `hits.product.v2ProductName`       | Product name               |
| `hits.product.productRevenue`      | Product revenue (micros)   |
| `hits.product.productQuantity`     | Purchased quantity         |

***

## ✅ Query Overview

| #  | Analysis Topic        | Key Output                      |
| -- | --------------------- | ------------------------------- |
| 01 | Monthly performance   | Visits, pageviews, transactions |
| 02 | Bounce rate analysis  | Bounce rate by traffic source   |
| 03 | Revenue by channel    | Revenue by source (Month/Week)  |
| 04 | User behavior         | Purchaser vs non‑purchaser      |
| 05 | Transaction frequency | Avg transactions per user       |
| 06 | Monetization          | Avg revenue per session         |
| 07 | Cross‑sell analysis   | Also‑bought products            |
| 08 | Funnel analysis       | View → Cart → Purchase          |

***

## ⚙️ Analysis Approach

1.  **Understand data structure**
    *   Review schema
    *   Identify nested fields

2.  **Translate business questions into SQL**
    *   Use CTEs and aggregations
    *   Apply time‑based grouping

3.  **Handle nested ecommerce data**
    *   Use `UNNEST`
    *   Filter valid purchase events

4.  **Calculate ecommerce KPIs**
    *   Revenue, bounce rate, conversion
    *   User‑level averages

5.  **Interpret results for business insights**

***

## 🔎 Key Insights

*   High traffic volume does not always imply high revenue
*   Revenue contribution varies significantly by acquisition source
*   Purchasers show deeper engagement than non‑purchasers
*   Product co‑purchase patterns suggest bundling opportunities
*   Funnel analysis identifies clear conversion drop‑off points

***

## ✅ Recommendations

*   Optimize or re‑target high‑bounce traffic sources
*   Invest more in high‑revenue acquisition channels
*   Use remarketing for high‑intent non‑purchasers
*   Apply product bundling based on co‑purchase patterns
*   Improve UX at key funnel drop‑off stages

***

## 🚀 Skills Demonstrated

*   BigQuery Standard SQL
*   Google Analytics data modeling
*   Nested & repeated data handling (`UNNEST`)
*   Ecommerce KPI analysis
*   Funnel & cohort analysis

***

## 🧪 Query Details

### **Query 01 – Monthly Totals (Jan–Mar 2017)**

**Output**

*   `month`
*   `visits`
*   `pageviews`
*   `transactions`

```sql
SELECT
  FORMAT_DATE('%Y%m', PARSE_DATE('%Y%m%d', date)) AS month,
  SUM(totals.visits) AS visits,
  SUM(totals.pageviews) AS pageviews,
  SUM(totals.transactions) AS transactions
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
WHERE _TABLE_SUFFIX BETWEEN '0101' AND '0331'
GROUP BY 1
ORDER BY 1;
```

***

### **Query 02 – Bounce Rate by Source (July 2017)**

**Output**

*   `source`
*   `total_visits`
*   `total_bounces`
*   `bounce_rate`

```sql
SELECT
  trafficSource.source AS source,
  SUM(totals.visits) AS total_visits,
  SUM(totals.bounces) AS total_bounces,
  (SUM(totals.bounces) / SUM(totals.visits)) * 100.00 AS bounce_rate
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`
GROUP BY source
ORDER BY total_visits DESC;
```

***

### **Query 03 – Revenue by Source (Month & Week, June 2017)**

**Output**

*   `time_type` (Month / Week)
*   `time`
*   `source`
*   `revenue` (in millions)

```sql
SELECT
  'Month' AS time_type,
  FORMAT_DATE('%Y%m', PARSE_DATE('%Y%m%d', date)) AS time,
  trafficSource.source AS source,
  SUM(product.productRevenue) / 1000000 AS revenue
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201706*`,
UNNEST(hits) AS hits,
UNNEST(hits.product) AS product
WHERE product.productRevenue IS NOT NULL
GROUP BY time, source

UNION ALL

SELECT
  'Week' AS time_type,
  FORMAT_DATE('%G%V', PARSE_DATE('%Y%m%d', date)) AS time,
  trafficSource.source AS source,
  SUM(product.productRevenue) / 1000000 AS revenue
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201706*`,
UNNEST(hits) AS hits,
UNNEST(hits.product) AS product
WHERE product.productRevenue IS NOT NULL
GROUP BY time, source

ORDER BY time_type, time, revenue DESC;
```

***

### **Query 04 – Avg Pageviews per User (Purchaser vs Non‑purchaser)**

**Period**: June–July 2017  
**Output**

*   `month`
*   `avg_pageviews_purchase`
*   `avg_pageviews_non_purchase`

```sql
WITH purchaser_data AS (
  SELECT
    FORMAT_DATE('%Y%m', PARSE_DATE('%Y%m%d', date)) AS month,
    SUM(totals.pageviews) / COUNT(DISTINCT fullVisitorId) AS avg_pageviews_purchase
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`,
  UNNEST(hits) AS hits,
  UNNEST(hits.product) AS product
  WHERE _TABLE_SUFFIX BETWEEN '0601' AND '0731'
    AND totals.transactions >= 1
    AND product.productRevenue IS NOT NULL
  GROUP BY month
),
non_purchaser_data AS (
  SELECT
    FORMAT_DATE('%Y%m', PARSE_DATE('%Y%m%d', date)) AS month,
    SUM(totals.pageviews) / COUNT(DISTINCT fullVisitorId) AS avg_pageviews_non_purchase
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`,
  UNNEST(hits) AS hits,
  UNNEST(hits.product) AS product
  WHERE _TABLE_SUFFIX BETWEEN '0601' AND '0731'
    AND totals.transactions IS NULL
    AND product.productRevenue IS NULL
  GROUP BY month
)
SELECT
  month,
  avg_pageviews_purchase,
  avg_pageviews_non_purchase
FROM purchaser_data
FULL JOIN non_purchaser_data USING (month)
ORDER BY month;
```

***

### **Query 05 – Avg Transactions per Purchasing User (July 2017)**

```sql
SELECT
  FORMAT_DATE('%Y%m', PARSE_DATE('%Y%m%d', date)) AS month,
  SUM(totals.transactions) / COUNT(DISTINCT fullVisitorId) AS avg_transactions_per_user
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`,
UNNEST(hits) AS hits,
UNNEST(hits.product) AS product
WHERE totals.transactions >= 1
  AND product.productRevenue IS NOT NULL
GROUP BY month;
```

***

### **Query 06 – Avg Revenue per Session (Purchasers Only, July 2017)**

```sql
SELECT
  FORMAT_DATE('%Y%m', PARSE_DATE('%Y%m%d', date)) AS month,
  (SUM(product.productRevenue) / 1000000) / SUM(totals.visits) AS avg_spend_per_session
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`,
UNNEST(hits) AS hits,
UNNEST(hits.product) AS product
WHERE totals.transactions IS NOT NULL
  AND product.productRevenue IS NOT NULL
GROUP BY month;
```

***

### **Query 07 – Also‑Bought Products Analysis**

**Target product**: *YouTube Men's Vintage Henley*  
**Period**: July 2017

```sql
WITH buyer_list AS (
  SELECT DISTINCT fullVisitorId
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`,
  UNNEST(hits) AS hits,
  UNNEST(hits.product) AS product
  WHERE product.v2ProductName = "YouTube Men's Vintage Henley"
    AND totals.transactions >= 1
    AND product.productRevenue IS NOT NULL
)
SELECT
  product.v2ProductName AS other_purchased_products,
  SUM(product.productQuantity) AS quantity
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`,
UNNEST(hits) AS hits,
UNNEST(hits.product) AS product
JOIN buyer_list USING (fullVisitorId)
WHERE product.v2ProductName != "YouTube Men's Vintage Henley"
  AND totals.transactions >= 1
  AND product.productRevenue IS NOT NULL
GROUP BY other_purchased_products
ORDER BY quantity DESC;
```

***

### **Query 08 – Funnel Cohort Analysis (Product Level)**

**Funnel**:  
`View → Add to Cart → Purchase`  
**Period**: Jan–Mar 2017

```sql
WITH product_data AS (
  SELECT
    FORMAT_DATE('%Y%m', PARSE_DATE('%Y%m%d', date)) AS month,
    product.v2ProductName AS product_name,
    COUNTIF(hits.eCommerceAction.action_type = '2') AS num_product_view,
    COUNTIF(hits.eCommerceAction.action_type = '3') AS num_add_to_cart,
    COUNTIF(hits.eCommerceAction.action_type = '6' AND product.productRevenue IS NOT NULL) AS num_purchase
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`,
  UNNEST(hits) AS hits,
  UNNEST(hits.product) AS product
  WHERE _TABLE_SUFFIX BETWEEN '0101' AND '0331'
    AND hits.eCommerceAction.action_type IN ('2','3','6')
  GROUP BY month, product_name
)
SELECT
  month,
  product_name,
  num_product_view,
  num_add_to_cart,
  num_purchase,
  ROUND(num_add_to_cart / NULLIF(num_product_view, 0) * 100, 2) AS add_to_cart_rate,
  ROUND(num_purchase / NULLIF(num_product_view, 0) * 100, 2) AS purchase_rate
FROM product_data
ORDER BY month, product_name;
```
***
## 🔎 Final Conclusion & Recommendations

### 📍 Key Takeaways

* Traffic quality matters as much as volume
* Revenue varies significantly by acquisition channel
* Purchasers show deeper engagement behavior
* Cross-sell patterns reveal bundling opportunities
* Funnel analysis highlights conversion bottlenecks

### ✅ Recommendations

* Optimize high-bounce traffic sources
* Invest in high-revenue channels
* Build remarketing strategies
* Apply product bundling
* Improve conversion funnel

---

## 🛠️ Tools & Techniques

* BigQuery SQL
* Google Analytics dataset
* CTEs & Aggregations
* `UNNEST` for nested data
* Funnel & revenue analysis

---



