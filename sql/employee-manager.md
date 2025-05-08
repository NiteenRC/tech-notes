
## 🗂️ Table: Employee

| id | name | managerId |
|----|------|-----------|
| 1  | lmn  | null      |
| 2  | ggg  | 1         |
| 3  | abc  | 1         |
| 4  | pqr  | 2         |
| 5  | xyz  | 4         |

---

## ✅ 1. Get employees who are not managers

### 🔍 Query
```sql
SELECT name
FROM Employee
WHERE id NOT IN (
    SELECT DISTINCT managerId
    FROM Employee
    WHERE managerId IS NOT NULL
);
````

### 📋 Output

| name |
| ---- |
| abc  |
| xyz  |

---

## ✅ 2. Get all employees along with their manager names

### 🔍 Query

```sql
SELECT e1.name AS employee_name, e2.name AS manager_name
FROM Employee e1
JOIN Employee e2 ON e1.managerId = e2.id;
```

### 📋 Output

| employee\_name | manager\_name |
| -------------- | ------------- |
| ggg            | lmn           |
| abc            | lmn           |
| pqr            | ggg           |
| xyz            | pqr           |

---