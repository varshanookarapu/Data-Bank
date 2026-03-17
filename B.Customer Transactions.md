
**Question 1:** What is the unique count and total amount for each transaction type?

```sql
SELECT txn_type, COUNT(txn_type) as unique_count , SUM(txn_amount) as total_amount FROM customer_transactions
GROUP BY txn_type
ORDER BY txn_type
```
<img width="1565" height="213" alt="image" src="https://github.com/user-attachments/assets/c4c5260c-e2af-4014-87da-6ae07a1e6588" />

---

**Question 2:** What is the average total historical deposit counts and  average amounts for all customers
```sql
WITH customer_deposits AS 
(
SELECT customer_id, COUNT(txn_type) AS deposit_count, SUM (txn_amount) as deposit_amount
FROM customer_transactions
WHERE txn_type = 'deposit'
GROUP BY customer_id
)

SELECT  ROUND(AVG(deposit_count)) as average_deposit_Count , ROUND(AVG(deposit_amount)) as avg_deposit_amount FROM customer_deposits
```
<img width="1492" height="155" alt="image" src="https://github.com/user-attachments/assets/069107a3-b8b6-4e5a-a48d-c8a0801b6f8a" />

---

**Question 3:** For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?

```sql
WITH customer_activity_counts AS ( 
SELECT customer_id, EXTRACT(MONTH from txn_date) as month,to_char(txn_date,'month') as month_name,
     COUNT (CASE WHEN txn_type='deposit' THEN 1 END ) AS deposit_count,
     COUNT (CASE WHEN txn_type='purchase' THEN 1 END ) AS purchase_count,
     COUNT (CASE WHEN txn_type='withdrawal' THEN 1 END ) AS withdrawal_count
FROM customer_transactions
GROUP BY customer_id ,EXTRACT(MONTH from txn_date) , to_char(txn_date,'month')
ORDER BY customer_id ,EXTRACT(MONTH from txn_date) , to_char(txn_date,'month')
)


SELECT month,month_name, COUNT(customer_id) FROM customer_activity_counts 
WHERE deposit_count > 1 AND ( purchase_count >= 1  OR withdrawal_count >=1   )
GROUP BY month ,month_name
ORDER BY month
```

<img width="1669" height="318" alt="image" src="https://github.com/user-attachments/assets/7f441706-792b-4f1e-9552-b09b8e8ee67a" />


---


**Question 4:** What is the closing balance for each customer at the end of the month? 

```sql
WITH customer_amounts AS
(
SELECT customer_id, txn_date,   
SUM (CASE WHEN txn_type = 'deposit' THEN txn_amount END ) as total_deposit,
SUM (CASE WHEN txn_type = 'withdrawal' THEN txn_amount END ) as total_withdrawal,
SUM (CASE WHEN txn_type = 'purchase' THEN txn_amount END ) as total_purchase
FROM customer_transactions 
GROUP BY customer_id ,txn_date 
),

--(date_trunc('month',txn_date) + INTERVAL '1 month - 1 day ' ) :: DATE as end_of_month ,

customer_amounts_2 AS
(
SELECT customer_id,txn_date,
CASE WHEN total_deposit IS NULL THEN 0 ELSE total_deposit END  as total_deposit,  
CASE WHEN total_purchase IS NULL THEN 0 ELSE total_purchase END  as total_purchase,
CASE WHEN total_withdrawal IS NULL THEN 0 ELSE total_withdrawal END  as total_withdrawal
FROM customer_amounts
)

SELECT customer_id, (date_trunc('month',txn_date) + INTERVAL '1 month - 1 day ' ) :: DATE as end_of_month, SUM(total_deposit-total_purchase-total_withdrawal) AS closing_balance
FROM customer_amounts_2
WHERE customer_id IN ( 1,2,3)
GROUP BY customer_id,(date_trunc('month',txn_date) + INTERVAL '1 month - 1 day ' ) :: DATE 
ORDER BY customer_id
```
