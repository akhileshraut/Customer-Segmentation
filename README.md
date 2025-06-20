# Customer Segmentation - Banking (SQL + Power BI)

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

### SQL: Customer Segmentation

```sql
USE BANKING;
SELECT * FROM transactions;

/*1. Most Transaction-Active Segments*/

SELECT CustomerSegment, COUNT(*) AS TransactionCount
FROM transactions
GROUP BY CustomerSegment
ORDER BY TransactionCount DESC;

/*2. Popular Products by Segment*/

SELECT CustomerSegment, ProductCategory, COUNT(*) AS UsageCount
FROM transactions
GROUP BY CustomerSegment, ProductCategory
ORDER BY CustomerSegment, UsageCount DESC;

/*3. Credit Score vs. Fee Correlation*/

 SELECT CustomerID, CustomerScore,
       SUM(CreditCardFees + InsuranceFees + LatePaymentAmount) AS TotalFees,
       COUNT(*) AS TransactionCount
FROM transactions
GROUP BY CustomerID, CustomerScore
ORDER BY CustomerScore DESC;

/*4. Transaction Types by Volume & Value*/

SELECT TransactionType,
       COUNT(*) AS TransactionCount,
       ROUND(SUM(Amount),2) AS TotalAmount
FROM transactions
GROUP BY TransactionType
ORDER BY TransactionCount DESC;

/*5. Branch Performance by City*/

SELECT BranchCity,
       COUNT(*) AS TransactionCount,
       ROUND(SUM(Amount),2) AS TotalTransactionValue
FROM transactions
GROUP BY BranchCity
ORDER BY TotalTransactionValue DESC;

/*6. Channel Usage*/

SELECT Channel,
       COUNT(*) AS UsageCount,
       ROUND(SUM(Amount),2) AS TotalValue
FROM transactions
GROUP BY Channel
ORDER BY UsageCount DESC;

/*7. Fee Revenue Breakdown*/

SELECT
  SUM(CreditCardFees) AS CreditCardFeeRevenue,
  ROUND(SUM(InsuranceFees),2) AS InsuranceFeeRevenue,
  SUM(LatePaymentAmount) AS LateFeeRevenue
FROM transactions;

/*8. Monthly Transaction Trends*/

SELECT
  DATE_FORMAT(TransactionDate, '%Y-%m') AS Month,
  COUNT(*) AS TransactionCount,
  ROUND(SUM(Amount),2) AS TotalTransactionValue
FROM transactions
GROUP BY DATE_FORMAT(TransactionDate, '%Y-%m')
ORDER BY Month;

/*9. Recommended vs. Actual Product Usage*/

SELECT RecommendedOffer, ProductCategory, COUNT(*) AS MatchCount
FROM transactions
GROUP BY RecommendedOffer, ProductCategory
ORDER BY MatchCount DESC;

/*10. Segment or Channel Trends Over Time*/

SELECT
  DATE_FORMAT(TransactionDate, '%Y-%m') AS Month,
  CustomerSegment,
  Channel,
  COUNT(*) AS Transactions
FROM transactions
GROUP BY Month, CustomerSegment, Channel
ORDER BY Month;

/* 11. Credit Score vs. Fees & Frequency*/

SELECT 
  CASE 
    WHEN CustomerScore >= 750 THEN 'Excellent'
    WHEN CustomerScore >= 650 THEN 'Good'
    WHEN CustomerScore >= 550 THEN 'Average'
    ELSE 'Poor'
  END AS CreditTier,
  COUNT(*) AS Transactions,
  ROUND(AVG(CreditCardFees + InsuranceFees + LatePaymentAmount), 2) AS AvgFees
FROM transactions
GROUP BY CreditTier
ORDER BY AvgFees DESC;

/* 12. Avg Transaction Amount per Credit Tier*/

SELECT 
  CASE 
    WHEN CustomerScore >= 750 THEN 'Excellent'
    WHEN CustomerScore >= 650 THEN 'Good'
    WHEN CustomerScore >= 550 THEN 'Average'
    ELSE 'Poor'
  END AS CreditTier,
  COUNT(*) AS Transactions,
  ROUND(AVG(Amount), 2) AS AvgTransactionAmount
FROM transactions
GROUP BY CreditTier
ORDER BY AvgTransactionAmount DESC;

/* 13. Fee Revenue per Unique Customer*/

SELECT 
  CASE 
    WHEN CustomerScore >= 750 THEN 'Excellent'
    WHEN CustomerScore >= 650 THEN 'Good'
    WHEN CustomerScore >= 550 THEN 'Average'
    ELSE 'Poor'
  END AS CreditTier,
  COUNT(DISTINCT CustomerID) AS UniqueCustomers,
  ROUND(SUM(CreditCardFees + InsuranceFees + LatePaymentAmount), 2) AS TotalFees,
  ROUND(SUM(CreditCardFees + InsuranceFees + LatePaymentAmount) / COUNT(DISTINCT CustomerID), 2) AS FeePerCustomer
FROM transactions
GROUP BY CreditTier
ORDER BY FeePerCustomer DESC;

/* 14 ALL*/

SELECT 
  CASE 
    WHEN CustomerScore >= 750 THEN 'Excellent'
    WHEN CustomerScore >= 650 THEN 'Good'
    WHEN CustomerScore >= 550 THEN 'Average'
    ELSE 'Poor'
  END AS CreditTier,
  COUNT(*) AS Transactions,
  ROUND(AVG(Amount), 2) AS AvgTransaction,
  ROUND(SUM(LatePaymentAmount), 2) AS TotalLateFees,
  ROUND(SUM(CreditCardFees + InsuranceFees + LatePaymentAmount), 2) AS TotalFees,
  ROUND(AVG(MonthlyIncome), 2) AS AvgIncome
FROM transactions
GROUP BY CreditTier
ORDER BY TotalFees DESC;

/* 15. Are Recommendations Working?*/

SELECT 
  RecommendedOffer,
  COUNT(*) AS OfferCount,
  ROUND(SUM(Amount), 2) AS AssociatedValue
FROM transactions
GROUP BY RecommendedOffer
ORDER BY OfferCount DESC
LIMIT 10;

/* 16 Count of Recommended Offers by Customer Segment*/

SELECT 
  CustomerSegment,
  RecommendedOffer,
  COUNT(*) AS RecommendationCount
FROM transactions
GROUP BY CustomerSegment, RecommendedOffer
ORDER BY CustomerSegment, RecommendationCount DESC;

/* 17 Average Monthly Income per Recommended Offer*/

SELECT 
  RecommendedOffer,
  ROUND(AVG(MonthlyIncome), 2) AS AvgIncome
FROM transactions
GROUP BY RecommendedOffer
ORDER BY AvgIncome DESC;

/* 18 Income Distribution by Segment*/

SELECT 
  CustomerSegment,
  ROUND(AVG(MonthlyIncome), 2) AS AvgIncome,
  MIN(MonthlyIncome) AS MinIncome,
  MAX(MonthlyIncome) AS MaxIncome
FROM transactions
GROUP BY CustomerSegment
ORDER BY AvgIncome DESC;

/* 19 Offer Penetration Rate by Segment*/

SELECT 
  CustomerSegment,
  COUNT(DISTINCT RecommendedOffer) AS UniqueOffers,
  COUNT(*) AS TotalTransactions,
  ROUND(COUNT(DISTINCT RecommendedOffer)*100.0 / COUNT(*), 2) AS OfferDiversityRate
FROM transactions
GROUP BY CustomerSegment;

/* 20 Segment Customers by Income & Score*/

SELECT 
  CustomerID,
  CustomerSegment,
  MonthlyIncome,
  CASE 
    WHEN CustomerScore >= 750 THEN 'Excellent'
    WHEN CustomerScore >= 650 THEN 'Good'
    WHEN CustomerScore >= 550 THEN 'Average'
    ELSE 'Poor'
  END AS CreditTier
FROM transactions
GROUP BY CustomerID, CustomerSegment, MonthlyIncome, CustomerScore;

/* 21 Analyze Behavior – Volume & Fees */

SELECT 
  CustomerSegment,
  COUNT(*) AS TotalTransactions,
  ROUND(AVG(Amount), 2) AS AvgTransactionValue,
  ROUND(AVG(CreditCardFees + InsuranceFees + LatePaymentAmount), 2) AS AvgFees
FROM transactions
GROUP BY CustomerSegment;

/* 22 Churn Risk Indicator */

SELECT 
  CustomerID,
  MAX(TransactionDate) AS LastTransactionDate,
  ROUND(AVG(CustomerScore), 1) AS AvgScore,
  SUM(LatePaymentAmount) AS TotalLateFees,
  CASE 
    WHEN DATEDIFF(CURDATE(), MAX(TransactionDate)) > 90 AND AVG(CustomerScore) < 600 AND SUM(LatePaymentAmount) > 20 THEN 'High Risk of Churn'
    ELSE 'Likely Retained'
  END AS ChurnStatus
FROM transactions
GROUP BY CustomerID
ORDER BY LastTransactionDate desc;

/* 23 count of Churn Risk Indicator */

SELECT 
  ChurnStatus,
  COUNT(*) AS CustomerCount
FROM (
  SELECT 
    CustomerID,
    MAX(TransactionDate) AS LastTransactionDate,
    ROUND(AVG(CustomerScore), 1) AS AvgScore,
    SUM(LatePaymentAmount) AS TotalLateFees,
    CASE 
      WHEN DATEDIFF(CURDATE(), MAX(TransactionDate)) > 90 
           AND AVG(CustomerScore) < 600 
           AND SUM(LatePaymentAmount) > 20 
        THEN 'High Risk of Churn'
      ELSE 'Likely Retained'
    END AS ChurnStatus
  FROM transactions
  GROUP BY CustomerID
) AS ChurnAnalysis
GROUP BY ChurnStatus;

 /* 24 1.RFM */
 
SELECT 
  CustomerID,
  DATEDIFF(CURDATE(), MAX(TransactionDate)) AS Recency,
  COUNT(*) AS Frequency,
  ROUND(SUM(Amount), 2) AS Monetary
FROM transactions
GROUP BY CustomerID
ORDER BY Monetary DESC;

/* 25 2.RFM */

WITH RFM_Base AS (
  SELECT 
    CustomerID,
    DATEDIFF(CURDATE(), MAX(TransactionDate)) AS Recency,
    COUNT(*) AS Frequency,
    ROUND(SUM(Amount), 2) AS Monetary
  FROM transactions
  GROUP BY CustomerID
),
RFM_Scored AS (
  SELECT 
    CustomerID,
    Recency,
    Frequency,
    Monetary,
    
    -- R score (lower recency = better = 5)
    NTILE(5) OVER (ORDER BY Recency ASC) AS R_Score,

    -- F score (higher frequency = better = 5)
    NTILE(5) OVER (ORDER BY Frequency DESC) AS F_Score,

    -- M score (higher monetary = better = 5)
    NTILE(5) OVER (ORDER BY Monetary DESC) AS M_Score

  FROM RFM_Base
)
SELECT 
  CustomerID,
  Recency, Frequency, Monetary,
  R_Score, F_Score, M_Score,
  CONCAT(R_Score, F_Score, M_Score) AS RFM_Segment
FROM RFM_Scored
ORDER BY RFM_Segment DESC;


/* 26 3. labeled RFM */

WITH RFM_Base AS (
  SELECT 
    CustomerID,
    DATEDIFF(CURDATE(), MAX(TransactionDate)) AS Recency,
    COUNT(*) AS Frequency,
    ROUND(SUM(Amount), 2) AS Monetary
  FROM transactions
  GROUP BY CustomerID
),
RFM_Scored AS (
  SELECT 
    CustomerID,
    Recency,
    Frequency,
    Monetary,

    NTILE(5) OVER (ORDER BY Recency ASC) AS R_Score,
    NTILE(5) OVER (ORDER BY Frequency DESC) AS F_Score,
    NTILE(5) OVER (ORDER BY Monetary DESC) AS M_Score
  FROM RFM_Base
),
RFM_Labeled AS (
  SELECT 
    CustomerID,
    Recency, Frequency, Monetary,
    R_Score, F_Score, M_Score,
    CONCAT(R_Score, F_Score, M_Score) AS RFM_Segment,

    CASE
      WHEN R_Score = 5 AND F_Score = 5 AND M_Score = 5 THEN 'Champions'
      WHEN R_Score = 4 AND F_Score = 5 AND M_Score BETWEEN 4 AND 5 THEN 'Loyal Customers'
      WHEN R_Score = 3 AND F_Score BETWEEN 4 AND 5 AND M_Score >= 3 THEN 'Potential Loyalist'
      WHEN R_Score = 1 AND F_Score = 1 AND M_Score = 1 THEN 'Lost Customers'
      WHEN R_Score = 2 AND F_Score <= 2 THEN 'About to Sleep'
      WHEN R_Score = 1 THEN 'At Risk'
      WHEN F_Score = 5 AND R_Score < 5 THEN 'Need Attention'
      WHEN M_Score = 5 AND R_Score BETWEEN 3 AND 5 THEN 'Big Spenders'
      WHEN R_Score = 5 AND F_Score <= 2 THEN 'Promising'
      WHEN R_Score = 5 AND F_Score = 1 THEN 'New Customers'
      ELSE 'Others'
    END AS Segment_Label
  FROM RFM_Scored
)
SELECT * FROM RFM_Labeled
ORDER BY Segment_LABEL, RFM_Segment DESC;

/* 27 4. labeld RFM COUNT */

WITH RFM_Base AS (
  SELECT 
    CustomerID,
    DATEDIFF(CURDATE(), MAX(TransactionDate)) AS Recency,
    COUNT(*) AS Frequency,
    ROUND(SUM(Amount), 2) AS Monetary
  FROM transactions
  GROUP BY CustomerID
),
RFM_Scored AS (
  SELECT 
    CustomerID,
    Recency,
    Frequency,
    Monetary,
    NTILE(5) OVER (ORDER BY Recency ASC) AS R_Score,
    NTILE(5) OVER (ORDER BY Frequency DESC) AS F_Score,
    NTILE(5) OVER (ORDER BY Monetary DESC) AS M_Score
  FROM RFM_Base
),
RFM_Labeled AS (
  SELECT 
    CustomerID,
    CONCAT(R_Score, F_Score, M_Score) AS RFM_Segment,
    CASE
      WHEN R_Score = 5 AND F_Score = 5 AND M_Score = 5 THEN 'Champions'
      WHEN R_Score = 4 AND F_Score = 5 AND M_Score BETWEEN 4 AND 5 THEN 'Loyal Customers'
      WHEN R_Score = 3 AND F_Score BETWEEN 4 AND 5 AND M_Score >= 3 THEN 'Potential Loyalist'
      WHEN R_Score = 1 AND F_Score = 1 AND M_Score = 1 THEN 'Lost Customers'
      WHEN R_Score = 2 AND F_Score <= 2 THEN 'About to Sleep'
      WHEN R_Score = 1 THEN 'At Risk'
      WHEN F_Score = 5 AND R_Score < 5 THEN 'Need Attention'
      WHEN M_Score = 5 AND R_Score BETWEEN 3 AND 5 THEN 'Big Spenders'
      WHEN R_Score = 5 AND F_Score <= 2 THEN 'Promising'
      WHEN R_Score = 5 AND F_Score = 1 THEN 'New Customers'
      ELSE 'Others'
    END AS Segment_Label
  FROM RFM_Scored
)

SELECT 
  Segment_Label,
  COUNT(*) AS CustomerCount
FROM RFM_Labeled
GROUP BY Segment_Label
ORDER BY CustomerCount DESC;
```


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

1. **Customer Segmentation | Retail Banking**  
   _RFM & Behavioral Insights_  
   - Score vs Income matrix  
   - Segment & Tier distribution  
   - Transaction channel & product analysis

2. **Customer Value Segments | Retail Banking**  
   _Financial Behavior Breakdown_  
   - Fee revenue by type, tier, and channel  
   - Churn risk distribution  
   - Top contributing customer groups

3. **RFM & Segmentation Explorer**  
   - RFM heatmap: Champions, Loyalists, At Risk  
   - Offer & recommendation acceptance by RFM tiers  
   - Cross-section of churn risk with income and tier
---

## 🔍 Key Insights

- **Score 4** customers generated the highest fee income  
- Over **60% of transactions** occurred via **online channel**  
- **Champions and Loyalists** make up 35% of customers  
- ~14% customers are at **high churn risk**  
- Offer success is highest among **Tier 2, high-income** segments

