# 📦 SQL Query: Customers with 2+ Orders in Same Week and > $1000 Total

Find customers who made **two or more orders** within the **same calendar week**  
**AND** whose total **weekly order amount exceeded $1000**.

---

## 🗂️ Tables

### orders

| id | customer_id | order_date | total_amount |
|----|-------------|------------|--------------|
| 1  | 101         | 2025-04-01 | 600.00       |
| 2  | 101         | 2025-04-03 | 500.00       |
| 3  | 102         | 2025-04-02 | 1000.00      |
| 4  | 103         | 2025-04-04 | 300.00       |
| 5  | 101         | 2025-04-10 | 800.00       |

### customer

| customer_id | customerName |
|-------------|--------------|
| 101         | Alice        |
| 102         | Bob          |
| 103         | Charlie      |

---

## ✅ SQL Query

```sql
SELECT c.customerName
FROM orders o
JOIN customer c ON c.customer_id = o.customer_id
GROUP BY c.customerName, c.customer_id, YEAR(o.order_date), WEEK(o.order_date)
HAVING COUNT(o.id) >= 2 AND SUM(o.total_amount) > 1000;
````

---

## 📋 Expected Output

| customerName |
|--------------|
| Alice        |

> Alice made 2 orders in the same week totaling \$1100 → included.