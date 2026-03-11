
**Question 1:** What is the unique count and total amount for each transaction type?

```sql
SELECT txn_type, COUNT(txn_type) as unique_count , SUM(txn_amount) as total_amount FROM customer_transactions
GROUP BY txn_type
ORDER BY txn_type
```
<img width="1565" height="213" alt="image" src="https://github.com/user-attachments/assets/c4c5260c-e2af-4014-87da-6ae07a1e6588" />
