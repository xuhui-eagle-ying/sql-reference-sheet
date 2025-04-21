
# 📘 SQL Reference Sheet

A concise SQL syntax reference sheet for beginners and intermediate users. Perfect for quick lookups and practice.

## 📌 Table of Contents
- [🔹 Basic Commands](#-basic-commands)
- [🔹 Data Types](#-data-types)
- [🔹 SELECT Queries](#-select-queries)
- [🔹 Filtering & Sorting](#-filtering--sorting)
- [🔹 INSERT, UPDATE, DELETE](#-insert-update-delete)
- [🔹 Aggregate Functions](#-aggregate-functions)
- [🔹 JOINs](#-joins)
- [🔹 Subqueries](#-subqueries)
- [🔹 Table Management](#-table-management)

## 🔹 Basic Commands

```sql
CREATE DATABASE db_name;
USE db_name;
CREATE TABLE table_name (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    age INT
);
```

## 🔹 Data Types

| Type        | Description            |
|-------------|------------------------|
| `INT`       | Integer number         |
| `VARCHAR(n)`| Variable-length string |
| `TEXT`      | Large string           |
| `DATE`      | Date (YYYY-MM-DD)      |
| `BOOLEAN`   | TRUE or FALSE          |
| `FLOAT`     | Floating-point number  |

## 🔹 SELECT Queries

```sql
SELECT * FROM users;
SELECT name, age FROM users;
SELECT name AS username FROM users;
```

## 🔹 Filtering & Sorting

```sql
SELECT * FROM users WHERE age > 18;
SELECT * FROM users WHERE age > 18 AND gender = 'F';
SELECT * FROM products WHERE name LIKE '%apple%';
SELECT * FROM orders WHERE status IN ('shipped', 'delivered');
SELECT * FROM orders WHERE date BETWEEN '2023-01-01' AND '2023-12-31';
SELECT * FROM users ORDER BY age DESC;
SELECT * FROM users LIMIT 10 OFFSET 5;
```

## 🔹 INSERT, UPDATE, DELETE

```sql
INSERT INTO users (name, age) VALUES ('Alice', 25);
UPDATE users SET age = 26 WHERE name = 'Alice';
DELETE FROM users WHERE age < 18;
```

## 🔹 Aggregate Functions

```sql
SELECT COUNT(*) FROM users;
SELECT AVG(age) FROM users;
SELECT gender, COUNT(*) FROM users GROUP BY gender;
SELECT department, COUNT(*) FROM employees GROUP BY department HAVING COUNT(*) > 5;
```

## 🔹 JOINs

```sql
SELECT a.name, b.salary FROM employees a INNER JOIN salaries b ON a.id = b.emp_id;
SELECT u.name, o.order_id FROM users u LEFT JOIN orders o ON u.id = o.user_id;
SELECT o.order_id, u.name FROM orders o RIGHT JOIN users u ON u.id = o.user_id;
```

## 🔹 Subqueries

```sql
SELECT name, (SELECT COUNT(*) FROM orders o WHERE o.user_id = u.id) AS order_count FROM users u;
SELECT name FROM users WHERE id IN (SELECT user_id FROM orders WHERE total > 100);
```

## 🔹 Table Management

```sql
ALTER TABLE users ADD COLUMN email VARCHAR(100);
ALTER TABLE users MODIFY COLUMN age INT;
ALTER TABLE users DROP COLUMN email;
DROP TABLE users;
```
