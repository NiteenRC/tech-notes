# Hibernate Relationships with Table Structures

---

## 🔗 One-to-One

### Example: `User` ↔ `Profile`

#### `User.java`

```java

@Entity
public class User {
    @Id
    @GeneratedValue
    private Long id;

    private String name;

    @OneToOne(mappedBy = "user", cascade = CascadeType.ALL)
    private Profile profile;
}
```

#### `Profile.java`

```java
@Entity
public class Profile {
    @Id @GeneratedValue
    private Long id;

    private String bio;

    @OneToOne
    @JoinColumn(name = "user_id")
    private User user;
}
```

#### Generated Tables

- `user`
    - `id` (PK)
    - `name`

- `profile`
    - `id` (PK)
    - `bio`
    - `user_id` (FK → `user.id`)

---

## 🔗 One-to-Many / Many-to-One

### Example: `Department` → `Employee`

#### `Department.java`

```java

@Entity
public class Department {
    @Id
    @GeneratedValue
    private Long id;

    private String name;

    @OneToMany(mappedBy = "department", cascade = CascadeType.ALL)
    private List<Employee> employees = new ArrayList<>();
}
```

#### `Employee.java`

```java

@Entity
public class Employee {
    @Id
    @GeneratedValue
    private Long id;

    private String name;

    @ManyToOne
    @JoinColumn(name = "department_id")
    private Department department;
}
```

#### Generated Tables

- `department`
    - `id` (PK)
    - `name`

- `employee`
    - `id` (PK)
    - `name`
    - `department_id` (FK → `department.id`)

---

## 🔗 Many-to-Many

### Example: `Student` ↔ `Course`

#### `Student.java`

```java

@Entity
public class Student {
    @Id
    @GeneratedValue
    private Long id;

    private String name;

    @ManyToMany
    @JoinTable(
            name = "student_course",
            joinColumns = @JoinColumn(name = "student_id"),
            inverseJoinColumns = @JoinColumn(name = "course_id")
    )
    private Set<Course> courses = new HashSet<>();
}
```

#### `Course.java`

```java

@Entity
public class Course {
    @Id
    @GeneratedValue
    private Long id;

    private String title;

    @ManyToMany(mappedBy = "courses")
    private Set<Student> students = new HashSet<>();
}
```

#### Generated Tables

- `student`
    - `id` (PK)
    - `name`

- `course`
    - `id` (PK)
    - `title`

- `student_course` (Join Table)
    - `student_id` (FK → `student.id`)
    - `course_id` (FK → `course.id`)
    - Composite PK: `student_id + course_id`

---

## Summary

| Relationship | Description                      | Tables Involved                       | Foreign Key Location           |
|--------------|----------------------------------|---------------------------------------|--------------------------------|
| One-to-One   | One record in each entity        | `user`, `profile`                     | `user_id` in `profile`         |
| One-to-Many  | One dept, many employees         | `department`, `employee`              | `department_id` in `employee`  |
| Many-to-One  | Many employees to one department | `employee`, `department`              | `department_id` in `employee`  |
| Many-to-Many | Students can take many courses   | `student`, `course`, `student_course` | In join table `student_course` |