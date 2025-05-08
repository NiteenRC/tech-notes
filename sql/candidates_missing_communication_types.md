## 📝 Problem Statement

Write a query to **find candidates** who are:
- **Created after March 27, 2025**, and
- **Have not subscribed to all three specific communication types**: **2, 3, and 4**.

> ✅ If a candidate is **missing even one** of these types, they should be selected.  
> ❌ If a candidate has **all three (2, 3, and 4)**, they should **NOT** be selected.

---

## 🗂️ Table: Candidate

| candidate_id | name    | create_date           |
|--------------|---------|------------------------|
| 1            | Alice   | 2025-03-28 10:00:00    |
| 2            | Bob     | 2025-03-29 11:00:00    |
| 3            | Charlie | 2025-04-01 09:30:00    |
| 4            | David   | 2025-04-02 14:45:00    |

---

## 🗂️ Table: Subscription

| subscription_id | candidate_id | communication_types_id |
|------------------|--------------|--------------------------|
| 101              | 1            | 2                        |
| 102              | 1            | 3                        |
| 103              | 2            | 2                        |
| 104              | 2            | 3                        |
| 105              | 2            | 4                        |
| 106              | 3            | 2                        |

---

## ✅ Goal:

Find candidates created **after March 27, 2025**, who have **not subscribed to all three** specific communication types (2, 3, and 4).  
Even if one is missing, include them.

---

## 🧠 SQL Query

```sql
SELECT c.name
FROM Candidate c
LEFT JOIN Subscription s 
  ON c.candidate_id = s.candidate_id 
     AND s.communication_types_id IN (2, 3, 4)
WHERE c.create_date > '2025-03-27'
GROUP BY c.candidate_id, c.name
HAVING COUNT(DISTINCT s.communication_types_id) < 3;
````

---

## 📋 Expected Output

| name    |
| ------- |
| Alice   |
| Charlie |
| David   |

> 🔸 Bob is excluded because he has all 3 communication types (2, 3, 4).