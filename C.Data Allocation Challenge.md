

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
