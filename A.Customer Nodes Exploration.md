# Data Bank

https://8weeksqlchallenge.com/case-study-4/

## A. Customer Nodes Exploration


**Question 1:** How many unique nodes are there on the Data Bank system?

---

## SQL Code

```sql
SELECT  DISTINCT node_id AS unique_nodes  FROM customer_nodes ORDER BY node_id;
```

<img width="789" height="301" alt="image" src="https://github.com/user-attachments/assets/df2e4936-5ede-45b8-9fcf-019618efe90a" />

---


**Question 2:** What is the number of nodes per region?

---

## SQL Code

```sql
SELECT cn.region_id,region_name, COUNT(node_id) as node_count FROM customer_nodes cn LEFT JOIN regions r 
ON cn.region_id = r.region_id
GROUP BY  cn.region_id,region_name
ORDER BY cn.region_id;
```
<img width="1364" height="316" alt="image" src="https://github.com/user-attachments/assets/0455b4a7-95f8-4c16-8397-327730d152f7" />

---

**Question 3:** How many customers are allocated to each region?

---

## SQL Code

```sql
SELECT cn.region_id,region_name, COUNT(DISTINCT customer_id) as customer_count FROM customer_nodes cn LEFT JOIN regions r 
ON cn.region_id = r.region_id
GROUP BY  cn.region_id,region_name
ORDER BY cn.region_id
```
<img width="1361" height="315" alt="image" src="https://github.com/user-attachments/assets/de13d849-2170-42c1-98f7-6b70a117b0ee" />

---

**Question 4:** How many days on average are customers reallocated to a different node?

---

## SQL Code

```sql
```

---

**Question 5:** What is the median, 80th and 95th percentile for this same reallocation days metric for each region?

---

## SQL Code

```sql
```

---


