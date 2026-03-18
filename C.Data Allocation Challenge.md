
## running customer balance column that includes the impact each transaction


```sql
SELECT customer_id,txn_type,txn_date, txn_amount ,
SUM
(CASE WHEN txn_type = 'deposit' THEN txn_amount ELSE -txn_amount END ) 
OVER ( PARTITION BY customer_id ORDER BY txn_date ) 
AS running_balance FROM customer_transactions
ORDER BY customer_id, txn_date
```
<img width="1742" height="818" alt="image" src="https://github.com/user-attachments/assets/a4a89a27-ed68-4098-ab07-a25fd2cbff04" />

---

## customer balance at the end of each month
```sql

WITH balance AS
(
SELECT customer_id,txn_date, EXTRACT ( 'month' FROM txn_date ) as txn_month ,
SUM ( CASE WHEN txn_type = 'deposit' THEN txn_amount ELSE -txn_amount END )  AS balance
FROM customer_transactions 
GROUP BY customer_id,txn_date
),

running_total AS
(
 SELECT * , 
 SUM(balance) OVER(PARTITION BY customer_id ORDER BY txn_date) :: NUMERIC AS running_balance,
 ROW_NUMBER() OVER(PARTITION BY customer_id,txn_month ORDER BY txn_date DESC) as row_num
 FROM balance 
)  



-- closing balance at the end of every month 

SELECT customer_id, txn_month , (date_trunc('month',txn_date) + INTERVAL '1 month - 1 day ' ) :: DATE as end_of_month ,running_balance as customer_balance_at_eom
FROM running_total WHERE row_num =1
ORDER BY customer_id,txn_month

```

<img width="1645" height="882" alt="image" src="https://github.com/user-attachments/assets/6702305c-50ec-48c2-97ce-7bf7b0492bc1" />

---

## minimum, average and maximum values of the running balance for each customer

```sql

WITH CTE AS 
(
SELECT customer_id,txn_type,txn_date, txn_amount ,
SUM
(CASE WHEN txn_type = 'deposit' THEN txn_amount ELSE -txn_amount END ) 
OVER ( PARTITION BY customer_id ORDER BY txn_date ) 
AS running_balance FROM customer_transactions
ORDER BY customer_id, txn_date
)


SELECT customer_id , MIN(running_balance) as min_running_balance, MAX(running_balance) as max_running_balance , ROUND(AVG(running_balance),2)  as  avg_running_balance
FROM CTE 
GROUP BY customer_id
ORDER BY customer_id
```

<img width="1668" height="845" alt="image" src="https://github.com/user-attachments/assets/9986d6f2-a113-4a41-8019-dec2f22aa4f2" />

