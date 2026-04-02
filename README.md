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

## 📊 Key Analysis Tasks

### 1️⃣ Monthly Performance (Q1 2017)

* Measure visits, pageviews, transactions
* 📈 Identify trends across months

---

### 2️⃣ Bounce Rate by Traffic Source

* Evaluate traffic quality
* ⚠️ Identify high-bounce channels

---

### 3️⃣ Revenue by Source (Month & Week)

* Analyze revenue contribution
* 💰 Detect high-performing channels

---

### 4️⃣ Purchaser vs Non-Purchaser Behavior

* Compare engagement levels
* 👤 Support segmentation

---

### 5️⃣ Transactions per User

* Measure purchase frequency
* 🔁 Identify repeat buyers

---

### 6️⃣ Revenue per Visit

* Evaluate monetization efficiency
* 📊 Benchmark conversion value

---

### 7️⃣ Product Cross-Sell Analysis

* Identify co-purchased products
* 🛒 Support bundling strategy

---

### 8️⃣ Ecommerce Funnel Analysis

* Track: View → Cart → Purchase
* 📉 Identify drop-off points

---

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

