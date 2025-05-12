## 🗂️ Table: Employee

| ID | EMAIL         | SALARY |
|----|---------------|--------|
| 1  | ABC@GMAIL.COM | 200    |
| 2  | ABC@GMAIL.COM | 300    |
| 3  | CDE@GMAIL.COM | 400    |

---

## ✅ 1. Get the second highest salary

### 🔍 Query

```sql
SELECT SALARY
FROM Employee
ORDER BY SALARY DESC
LIMIT 1 OFFSET 1;  -- or LIMIT 1, 1 in MySQL
````

### 📋 Output

| SALARY |
|--------|
| 300    |

---

## 🗂️ Table: Student

| ID | EMAIL                                 |
|----|---------------------------------------|
| 1  | [ABC@GMAIL.COM](mailto:ABC@GMAIL.COM) |
| 2  | [ABC@GMAIL.COM](mailto:ABC@GMAIL.COM) |
| 3  | [CDE@GMAIL.COM](mailto:CDE@GMAIL.COM) |

---

## ✅ 2. Get duplicate emails from Student table

### 🔍 Query

```sql
SELECT EMAIL
FROM Student
GROUP BY EMAIL
HAVING COUNT(EMAIL) > 1;
```

### 📋 Output

| EMAIL                                 |
|---------------------------------------|
| [ABC@GMAIL.COM](mailto:ABC@GMAIL.COM) |

---

```

Let me know if you'd like this saved as a `.md` file or want to add variations like **dense_rank()** or **window functions** for other SQL flavors.
```
