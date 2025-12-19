 Paytm Wallet SQL Business Analysis Project

 Project Overview

 This project is an end-to-end SQL-based business analysis of Paytm wallet transactions.
 The main aim is to analyze customer behavior, transactions, merchants, fraud patterns, recharges, and payment failures     using SQL and convert data into business insights.

 Business Objectives

1. Understand wallet transaction trends.
2. Segment customers based on spending behavior.
3. Analyze merchant performance.
4. Detect suspicious or fraudulent transactions.
5. Study recharge and subscription usage.
6. Identify reasons for payment failures.

Tools & Technologies Used

1. SQL (MySQL)
2. GitHub (Project hosting & documentation)
3. SQL Window Functions
4. Aggregate Functions (SUM, COUNT, AVG)

 Database Tables Used

 1️⃣ Customers Table

| Customer      | Description               |
| ----------- | ------------------------- |
| user_id     | Unique customer ID        |
| signup_date | Date when user registered |


2️⃣ Wallet Transactions Table

| Transaction | Description                      |
| ----------- | -------------------------------- |
| txn_id      | Transaction ID                   |
| user_id     | Customer ID                      |
| merchant_id | Merchant ID                      |
| txn_date    | Transaction date                 |
| amount      | Transaction amount               |
| txn_type    | Recharge, Shopping, Bill Payment |
| status      | Success or Failed                |

 3️⃣ Merchants Table

| Merchant      | Description   |
| ------------- | ------------- |
| merchant_id   | Merchant ID   |
| merchant_name | Merchant name |


Analysis Performed

 1️⃣ Wallet Transaction Analysis

1. Total transactions per day
2. Successful vs failed transactions
3. Total revenue from successful payments

SQL Used:

SELECT txn_date, COUNT(*) AS total_transactions
FROM wallet_transactions
GROUP BY txn_date;

SELECT SUM(amount) AS total_revenue
FROM wallet_transactions
WHERE status = 'Success';

2️⃣ Customer Segmentation

Customers are grouped into High, Medium, and Low Value based on:

1. Total amount spent
2. Number of transactions

SQL Used:

SELECT user_id,
       SUM(amount) AS total_spent,
       COUNT(txn_id) AS total_transactions,
       CASE
         WHEN SUM(amount) > 3000 THEN 'High Value'
         WHEN SUM(amount) BETWEEN 1000 AND 3000 THEN 'Medium Value'
         ELSE 'Low Value'
       END AS customer_segment
FROM wallet_transactions
WHERE status = 'Success'
GROUP BY user_id;

 3️⃣ Merchant Performance Analysis

1. Identify top merchants by revenue
2. Understand transaction volume per merchant

SQL Used:

SELECT m.merchant_name,
       COUNT(w.txn_id) AS total_transactions,
       SUM(w.amount) AS total_revenue
FROM wallet_transactions w
JOIN merchants m
ON w.merchant_id = m.merchant_id
WHERE w.status = 'Success'
GROUP BY m.merchant_name
ORDER BY total_revenue DESC;

 4️⃣ Fraud Detection Analysis

1. Detect very high-value transactions
2. Identify users with unusually frequent transactions
3. Detect sudden jumps in transaction amount

SQL Used:

SELECT *
FROM wallet_transactions
WHERE amount > 4000;

SELECT user_id, COUNT(txn_id) AS txn_count
FROM wallet_transactions
GROUP BY user_id
HAVING COUNT(txn_id) > 2;

SELECT user_id, txn_date, amount,
       LAG(amount) OVER(PARTITION BY user_id ORDER BY txn_date) AS previous_amount
FROM wallet_transactions;

 5️⃣ Recharge & Subscription Analysis

1. Number of recharges per user
2. Average recharge amount
3. Most common transaction types

SQL Used:

SELECT user_id,
       COUNT(*) AS recharge_count,
       AVG(amount) AS avg_recharge
FROM wallet_transactions
WHERE txn_type = 'Recharge'
AND status = 'Success'
GROUP BY user_id;

SELECT txn_type, COUNT(*) AS txn_count
FROM wallet_transactions
GROUP BY txn_type
ORDER BY txn_count DESC;

 6️⃣ Payment Failure Analysis

1. Failed transactions by transaction type
2. Overall payment failure rate

SQL Used:

SELECT txn_type, COUNT(*) AS failed_count
FROM wallet_transactions
WHERE status = 'Failed'
GROUP BY txn_type;

SELECT 
COUNT(CASE WHEN status = 'Failed' THEN 1 END) * 100.0 / COUNT(*) 
AS failure_rate
FROM wallet_transactions;

 
 Key Insights

1. A small group of customers generates most revenue
2. Recharge transactions are the most frequent
3. Some merchants contribute higher revenue than others
4. Failed transactions occur more in specific transaction types
5. High-value and frequent transactions need fraud monitoring


 Business Recommendations

1. Reward high-value customers with loyalty offers
2. Encourage low-value customers with cashback offers
3. Support top merchants to increase transaction volume
4. Improve systems for high-failure transaction categories
5. Monitor suspicious transaction patterns








