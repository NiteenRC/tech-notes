## 🧠 SQL Query: Find Topper(s) for Each Subject (Without Using RANK)

**Table:** `student`

### 🎯 Objective:
Find the student(s) with the highest marks in each subject. Return their `name`, `subject`, and `marks`.

---

### ✅ SQL Query:

```sql
SELECT s.name, s.subject, s.marks
FROM student s
JOIN (
    SELECT subject, MAX(marks) AS max_marks
    FROM student
    GROUP BY subject
) AS toppers
ON s.subject = toppers.subject AND s.marks = toppers.max_marks;
````

---

### 🔍 Explanation:

* The subquery gets the **maximum marks for each subject**.
* The outer query **joins** this result with the original table.
* It **filters** to only those students whose marks match the subject's maximum.

---

### 🧪 Example Input:

| name  | subject | marks |
| ----- | ------- | ----- |
| Alice | Math    | 95    |
| Bob   | Math    | 95    |
| Carol | Math    | 90    |
| Dave  | Physics | 89    |
| Eve   | Physics | 92    |

---

### ✅ Output:

| name  | subject | marks |
| ----- | ------- | ----- |
| Alice | Math    | 95    |
| Bob   | Math    | 95    |
| Eve   | Physics | 92    |

```