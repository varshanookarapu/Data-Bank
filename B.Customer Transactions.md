
**Question 1:** What is the unique count and total amount for each transaction type?

```sql
SELECT txn_type, COUNT(txn_type) as unique_count , SUM(txn_amount) as total_amount FROM customer_transactions
GROUP BY txn_type
ORDER BY txn_type
```
<img width="1565" height="213" alt="image" src="https://github.com/user-attachments/assets/c4c5260c-e2af-4014-87da-6ae07a1e6588" />


```sql
WITH deposit AS(
SELECT customer_id,
(date_trunc('month',txn_date) + INTERVAL '1 month - 1 day' ) :: DATE as end_of_month,
SUM(txn_amount) as total_deposit FROM customer_transactions
WHERE txn_type='deposit'
GROUP BY customer_id ,txn_date
),

withdrawal AS(
SELECT customer_id,
(date_trunc('month',txn_date) + INTERVAL '1 month - 1 day' ) :: DATE as end_of_month,
SUM(txn_amount) as total_withdrawal FROM customer_transactions
WHERE txn_type='withdrawal'
GROUP BY customer_id ,txn_date
),

purchase AS(
SELECT customer_id,
(date_trunc('month',txn_date) + INTERVAL '1 month - 1 day' ) :: DATE as end_of_month,
SUM(txn_amount) as total_purchase FROM customer_transactions
WHERE txn_type='purchase'
GROUP BY customer_id ,txn_date
),

customer_accounting_details AS (
SELECT d.customer_id,d.end_of_month,total_deposit,
CASE WHEN  total_withdrawal IS NULL THEN 0 ELSE total_withdrawal END AS total_withdrawal,
CASE WHEN  total_purchase IS NULL THEN 0 ELSE total_purchase END AS total_purchase
FROM deposit d
LEFT JOIN  withdrawal w ON
d.customer_id = w.customer_id
LEFT JOIN  purchase p ON
d.customer_id=p.customer_id
  )

SELECT customer_id,end_of_month , SUM(total_deposit) - SUM(total_withdrawal)-SUM(total_purchase) AS closing_balance FROM
customer_accounting_details
WHERE customer_id=1
GROUP BY customer_id,end_of_month
```
customer_accounting_details
WHERE customer_id=1
``GROUP BY customer_id,end_of_month
