# Banking Transactions Analysis (SQL + Power BI)

**Tools Used:** SQL (MySQL), Power BI  
**Project Type:** End-to-End Data Analytics Project  
**Role:** Data Analyst  
**Duration:** 1 Week  

---

## 📌 Project Overview

This project analyzes customer transactions from a simulated retail banking dataset. Using SQL for data processing and Power BI for visualization, the analysis delivers insights into customer behavior, revenue streams, and churn risk. The project mimics real-world business challenges that banks face to retain customers and maximize revenue.

---

## 🎯 Objectives

- Identify high-value customers and revenue-generating segments  
- Perform customer segmentation using RFM analysis  
- Evaluate performance of offers and product recommendations  
- Assess churn risk based on customer behavior  
- Visualize key metrics using interactive dashboards

---

## 🗃️ Dataset Overview

The primary table analyzed:

- **`transactions`**
  - `customer_id`, `customer_score`, `monthly_income`, `product_category`, `transaction_type`, `channel`, `fee_type`, `fee_amount`, `rfm_score`, `churn_risk`, `offer_accepted`, etc.

---

## 🧮 SQL Analysis Highlights

> ✅ SQL queries are included in the [`transactions_analysis.sql`](./transactions_analysis.sql) file.

### Key SQL Tasks:
- Customer segmentation by score, income, and behavior  
- Fee revenue aggregation by type (credit card, insurance, late fees)  
- RFM segmentation using `Recency`, `Frequency`, and `Monetary` values  
- Churn risk detection using custom logic  
- Channel and transaction type distribution  
- Offer uptake and product recommendation performance

---

## 📊 Power BI Dashboard

> 📂 Dashboard file: [`/powerbi/banking-dashboard.pbix`](./powerbi/banking-dashboard.pbix)  
> 🖼️ Screenshots: [`/visuals`](./visuals)

### Dashboard Pages:

1. **Customer Overview**  
   - Segment-wise distribution  
   - Income vs Score scatter  
   - Transaction channel usage

2. **Revenue Insights**  
   - Fee type comparison  
   - Avg. fee per customer  
   - City-wise contribution

3. **Churn & RFM**  
   - RFM segmentation  
   - High/medium/low churn risk  
   - Loyalty segment breakdown

4. **Offer Effectiveness**  
   - Offer acceptance by segment  
   - Recommended vs Accepted %  
   - Performance by product category

---

## 🔍 Key Insights

- **Score 4** customers generated the highest fee income  
- Over **60% of transactions** occurred via **online channel**  
- **Champions and Loyalists** make up 35% of customers  
- ~14% customers are at **high churn risk**  
- Offer success is highest among **Tier 2, high-income** segments

