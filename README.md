# 🛒 Ecommerce Performance Analysis with BigQuery

**📊 Website Traffic, Engagement, Revenue & Product Funnel Analysis | Google Analytics | BigQuery SQL**

**👤 Author:** Vuong Minh Toan
**📅 Date:** [YYYY-MM-DD]
**🛠️ Tools Used:** Google BigQuery SQL, Google Analytics Sample Dataset

---

## 📑 Table of Contents

1. [Background & Overview](#-background--overview)
2. [Dataset Description & Data Structure](#-dataset-description--data-structure)
3. [Main Process](#️-main-process)
4. [Final Conclusion & Recommendations](#-final-conclusion--recommendations)

---

## 📌 Background & Overview

### 🎯 Objective

This project uses **BigQuery SQL** to analyze ecommerce website performance based on the **Google Analytics sample dataset**.

The analysis focuses on the following business questions:

* How did website traffic and transactions perform across Q1 2017?
* Which traffic sources had the highest bounce rate in July 2017?
* Which acquisition channels generated the most revenue?
* How do purchasers and non-purchasers differ in behavior?
* What is the average number of transactions per user?
* How much revenue is generated per visit?
* Which products are frequently purchased together?
* How effective is the ecommerce funnel?

### 📖 Project Overview

This is a task-based SQL project built on top of the **Google Analytics ecommerce dataset in BigQuery**, covering key metrics such as:

* Visits & Pageviews
* Transactions
* Bounce rate
* Revenue by traffic source
* Purchaser vs non-purchaser behavior
* Cross-sell patterns
* Ecommerce funnel conversion

### 👥 Target Audience

* Data Analysts
* Business Analysts
* Ecommerce / Growth teams
* Marketing teams
* Decision-makers

---

## 📂 Dataset Description & Data Structure

### 📌 Data Source

* Source: Google Analytics Sample Dataset (BigQuery)
* Tables:

  * `ga_sessions_2017*`
  * `ga_sessions_201707*`
  * `ga_sessions_*`
* Domain: Ecommerce analytics

### 📊 Data Structure

The dataset contains **session-level data with nested structures**.

To extract product-level data, the project uses:

* `UNNEST(hits)`
* `UNNEST(hits.product)`

### 🔑 Key Fields

| Field Name | Data Type | Description |
|---|---|---|
| `fullVisitorId` | STRING | The unique visitor ID. |
| `date` | STRING | The date of the session in `YYYYMMDD` format. |
| `totals` | RECORD | Aggregate values across the session. |
| `totals.bounces` | INTEGER | Total bounces (for convenience). For a bounced session, the value is `1`, otherwise it is `NULL`. |
| `totals.hits` | INTEGER | Total number of hits within the session. |
| `totals.pageviews` | INTEGER | Total number of pageviews within the session. |
| `totals.visits` | INTEGER | The number of sessions (for convenience). This value is `1` for sessions with interaction events. The value is `NULL` if there are no interaction events in the session. |
| `totals.transactions` | INTEGER | Total number of ecommerce transactions within the session. |
| `trafficSource.source` | STRING | The source of the traffic source. Could be the name of the search engine, the referring hostname, or a value of the `utm_source` URL parameter. |
| `hits` | RECORD | Populated for any and all types of hits. Typically a repeated record (multiple hits per session). |
| `hits.eCommerceAction` | RECORD | Ecommerce hits that occurred during the session. Repeated field with an entry for each collected hit. |
| `hits.eCommerceAction.action_type` | STRING | Action type: `1`=Product list click, `2`=Product detail view, `3`=Add to cart, `4`=Remove from cart, `5`=Checkout, `6`=Purchase, `7`=Refund, `8`=Checkout options, `0`=Unknown. Usually applies to all products in a hit, except when `hits.product.isImpression = TRUE` (product impression in list view). |
| `hits.product` | RECORD | Populated for each hit containing Enhanced Ecommerce PRODUCT data. |
| `hits.product.productQuantity` | INTEGER | The quantity of the product purchased. |
| `hits.product.productRevenue` | INTEGER | Product revenue in micros (`value * 10^6`; e.g., `2.40` → `2400000`). |
| `hits.product.productSKU` | STRING | Product SKU. |
| `hits.product.v2ProductName` | STRING | Product name. |
| `device.deviceCategory` | STRING | Device type (`Mobile`, `Tablet`, `Desktop`). |
---

## ⚙️ Main Process

1. 🔍 **Data Understanding**

   * Analyze schema
   * Identify key fields

2. 🧠 **SQL Design**

   * Translate business questions into SQL
   * Use aggregation, CTEs, filtering

3. 🔗 **Nested Data Handling**

   * Apply `UNNEST`
   * Filter valid purchase data

4. 📊 **Metric Calculation**

   * Visits, revenue, conversion
   * Behavior comparison

5. 💡 **Business Insights**

   * Interpret results
   * Generate recommendations

---

Dưới đây là **README.md** hoàn chỉnh, được chuẩn hoá để bạn dùng trực tiếp cho GitHub / portfolio BigQuery – đúng tinh thần *SQL Final Project*.

***

# BigQuery SQL Final Project

**Google Analytics Sample Dataset**

## 📌 Overview

This project is part of the **SQL Coaching Module – Final Project**.  
The objective is to analyze user behavior and e‑commerce performance using **Google Analytics public sample data** in **BigQuery**.

The project includes **08 analytical SQL queries**, covering traffic analysis, conversion metrics, revenue analysis, cohort funneling, and product-level insights.

***

## 🧩 Dataset Information

*   **Dataset**:  
    `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`

*   **Description**:  
    Public Google Analytics session-level data, including:
    *   User sessions
    *   Traffic sources
    *   Pageviews
    *   Transactions
    *   Product-level eCommerce actions

***

## 📐 References

*   **Google Analytics Table Schema**:  
    <https://support.google.com/analytics/answer/3437719?hl=en>

*   **BigQuery Standard SQL – Format Elements**:  
    <https://cloud.google.com/bigquery/docs/reference/standard-sql/format-elements>

***

## ✅ Queries Summary

| #  | Query Name                       | Key Output                      |
| -- | -------------------------------- | ------------------------------- |
| 01 | Monthly totals (Jan–Mar 2017)    | Visits, pageviews, transactions |
| 02 | Bounce rate by source (Jul 2017) | Bounce rate by traffic source   |
| 03 | Revenue by source (Month & Week) | Revenue (million)               |
| 04 | Avg pageviews per user           | Purchaser vs non‑purchaser      |
| 05 | Avg transactions per user        | Purchasing users                |
| 06 | Avg revenue per session          | Purchasers only                 |
| 07 | Also‑bought products             | Co‑purchase analysis            |
| 08 | Funnel cohort analysis           | View → Cart → Purchase          |

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

## 🚀 Skills Demonstrated

*   BigQuery Standard SQL
*   Google Analytics data modeling
*   UNNEST & nested field handling
*   Funnel & cohort analysis
*   E‑commerce KPI analytics

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

## 📎 Repository Structure

```bash
project-folder/
│
├── sql/
│   └── ecommerce_performance_analysis.sql
├── images/
│   ├── schema.png
│   ├── query_1_result.png
│   ├── query_2_result.png
│   ├── query_3_result.png
│   ├── query_4_result.png
│   ├── query_5_result.png
│   ├── query_6_result.png
│   ├── query_7_result.png
│   └── query_8_result.png
├── README.md
└── notes/
```

