
# 📘 SQL Reference Sheet

A concise MySQL syntax reference sheet for beginners and intermediate users. Perfect for quick lookups and practice.

## 📌 Table of Contents
- [SQL Execution Order](#sql-execution-order)
- [Basic Commands](#basic-commands)
- [Data Types](#data-types)
- [SELECT Queries](#select-queries)
- [Filtering & Sorting](#filtering--sorting)
- [INSERT, UPDATE, DELETE](#insert-update-delete)
- [Aggregate Functions](#aggregate-functions)
- [JOIN & Union](#join--union)
- [Subqueries](#subqueries)
- [CASE WHEN](#case-when)
- [CTE (Common Table Expressions)](#cte-common-table-expressions)
- [Window Functions](#window-functions)
- [Table Management](#table-management)
- [Functions](#functions)
- [Stored Procedures](#stored-procedures)
- [Views](#views)

## SQL Execution Order

SQL queries are executed in a specific order, and understanding the order is important for correct query construction. Below is the typical order of operations for a SQL query:

1. **FROM**: Determines the tables and joins
2. **ON**: Applies the join conditions
3. **WHERE**: Filters rows before grouping
4. **GROUP BY**: Groups rows based on column(s)
5. **HAVING**: Filters groups (after aggregation)
6. **SELECT**: Defines what data to return (including window functions)
7. **DISTINCT**: Removes duplicate rows (if used)
8. **ORDER BY**: Sorts the result set
9. **LIMIT / OFFSET**: Limits the number of rows returned

## Example: Correct Query Flow

```sql
SELECT name, AVG(salary) AS avg_salary
FROM employees
WHERE department = 'Sales'
GROUP BY name
HAVING AVG(salary) > 50000
ORDER BY avg_salary DESC
LIMIT 10;
```

## Data Types

| Type         | Description                  |
|--------------|------------------------------|
| `INT`        | Integer number               |
| `VARCHAR(n)` | Variable-length string       |
| `TEXT`       | Large text string            |
| `DATE`       | Date (YYYY-MM-DD)            |
| `DATETIME`   | Date and time                |
| `BOOLEAN`    | TRUE (1) or FALSE (0)        |
| `FLOAT`      | Floating-point number        |
| `DECIMAL(p,s)`| Fixed-point (e.g. prices)   |

##  Basic Commands

```sql
CREATE DATABASE db_name;
USE db_name;

CREATE DATABASE IF NOT EXISTS db_name;
USE db_name;

CREATE TABLE table_name (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    age INT
);

CREATE TABLE IF NOT EXISTS table_name (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    age INT
);
```

## SELECT Queries

```sql
SELECT * FROM users;
SELECT name, age FROM users;
SELECT name AS username FROM users;
```

## Filtering & Sorting

```sql
SELECT * FROM users WHERE age > 18;
SELECT * FROM users WHERE age > 18 AND gender = 'F';

-- Query products where the name contains 'apple' anywhere in the string
SELECT * FROM products WHERE name LIKE '%apple%';
-- Query users where the username starts with 'A'
SELECT * FROM users WHERE username LIKE 'A%';
-- Query products where the product name ends with 's'
SELECT * FROM products WHERE product_name LIKE '%s';
-- Query employees where the email has exactly one character before '@gmail.com'
SELECT * FROM employees WHERE email LIKE '_@gmail.com';

SELECT * FROM orders WHERE status IN ('shipped', 'delivered');
SELECT * FROM orders WHERE date BETWEEN '2023-01-01' AND '2023-12-31';
SELECT * FROM users ORDER BY age DESC;
SELECT * FROM users LIMIT 10 OFFSET 5;
```

## INSERT, UPDATE, DELETE

```sql
INSERT INTO users (name, age) VALUES ('Alice', 25);
UPDATE users SET age = 26 WHERE name = 'Alice';
DELETE FROM users WHERE age < 18;
```

## Aggregate Functions

```sql
SELECT COUNT(*) FROM users;
SELECT AVG(age) FROM users;
SELECT gender, COUNT(*) FROM users GROUP BY gender;
SELECT department, COUNT(*) FROM employees GROUP BY department HAVING COUNT(*) > 5;
```

## JOIN & Union

### Inner Join, Left Join, Right Join
```sql
SELECT a.name, b.salary 
FROM employees a 
INNER JOIN salaries b ON a.id = b.emp_id;

SELECT u.name, o.order_id 
FROM users u 
LEFT JOIN orders o ON u.id = o.user_id;

SELECT o.order_id, u.name 
FROM orders o 
RIGHT JOIN users u ON u.id = o.user_id;

```

### Full Outer Join

```sql
-- FULL OUTER JOIN simulation in MySQL
-- Combine LEFT JOIN and RIGHT JOIN using UNION to include all unmatched rows from both tables
SELECT A.id AS A_id, A.name AS A_name, B.id AS B_id, B.name AS B_name
FROM A
LEFT JOIN B ON A.id = B.id

UNION

SELECT A.id AS A_id, A.name AS A_name, B.id AS B_id, B.name AS B_name
FROM A
RIGHT JOIN B ON A.id = B.id;

-- FULL OUTER JOIN simulation using UNION ALL + filter to avoid duplicates
SELECT A.id AS A_id, A.name AS A_name, B.id AS B_id, B.name AS B_name
FROM A
LEFT JOIN B ON A.id = B.id

UNION ALL

SELECT A.id AS A_id, A.name AS A_name, B.id AS B_id, B.name AS B_name
FROM A
RIGHT JOIN B ON A.id = B.id
WHERE A.id IS NULL;  -- Only include rows that weren't matched in the LEFT JOIN
```

## Subqueries

```sql
SELECT name, 
       (SELECT COUNT(*) FROM orders o WHERE o.user_id = u.id) AS order_count 
FROM users u;

SELECT name 
FROM users 
WHERE id IN (SELECT user_id FROM orders WHERE total > 100);
```

## CASE WHEN

```sql
SELECT name,
       age,
       CASE 
           WHEN age < 18 THEN 'Minor'
           WHEN age BETWEEN 18 AND 64 THEN 'Adult'
           ELSE 'Senior'
       END AS age_group
FROM users;
```

## CTE (Common Table Expressions)

```sql
WITH recent_orders AS (
    SELECT * 
    FROM orders 
    WHERE order_date >= '2024-01-01'
)
SELECT o.id, o.total, u.name
FROM recent_orders o
JOIN users u ON o.user_id = u.id;
```

## Window Functions

```sql
SELECT name, department, salary,
       RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS dept_rank
FROM employees;
```

### Common Functions

| Function           | Description                               |
|--------------------|-------------------------------------------|
| `ROW_NUMBER()`     | Unique row number within partition        |
| `RANK()`           | Rank with gaps (e.g., 1, 1, 3...)         |
| `DENSE_RANK()`     | Rank without gaps (e.g., 1, 1, 2...)      |
| `NTILE(n)`         | Divides rows into n buckets               |
| `LAG(expr, offset)`| Access prior row’s value                 |
| `LEAD(expr, offset)`| Access following row’s value             |
| `FIRST_VALUE()`    | First value in the partition              |
| `LAST_VALUE()`     | Last value in the partition               |
| `SUM() OVER()`     | Running total                             |
| `AVG() OVER()`     | Running average                           |

### Example: Aggregate Function with Grouping

```sql
SELECT department, COUNT(*) AS num_employees, AVG(salary) AS avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 50000;
```

### Example: Running Total**Using LAG and LEAD

```sql
SELECT name, salary,
       LAG(salary) OVER (ORDER BY hire_date) AS previous_salary,
       LEAD(salary) OVER (ORDER BY hire_date) AS next_salary
FROM employees;
```

### Example: Sorting with Window Function

```sql
SELECT name, salary,
       RANK() OVER (ORDER BY salary DESC) AS salary_rank
FROM employees
ORDER BY salary DESC;
```

### Example: Moving Average with ROWS BETWEEN

```sql
SELECT order_date, total,
       AVG(total) OVER (ORDER BY order_date ROWS BETWEEN 30 PRECEDING AND CURRENT ROW) AS moving_avg
FROM orders;
```

## Table Management

```sql
-- Add a new column to a table
ALTER TABLE users ADD COLUMN email VARCHAR(100);

-- Modify the type of an existing column
ALTER TABLE users MODIFY COLUMN age INT;

-- Drop an existing column
ALTER TABLE users DROP COLUMN email;

-- Rename a column
ALTER TABLE users CHANGE age user_age INT;

-- Drop a table (if exists)
DROP TABLE IF EXISTS users;

-- Rename a table
RENAME TABLE old_table TO new_table;
```

## Functions

A function is a stored program that can return a single value. Functions are typically used in SELECT statements, WHERE clauses, or anywhere expressions are allowed. Functions in SQL are created and called differently from stored procedures.

### Example: Creating a Simple Function

```sql
CREATE FUNCTION calculate_discount(price DECIMAL(10,2), discount_rate DECIMAL(3,2))
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
    RETURN price * (1 - discount_rate);
END
```

### Example: Using the Function

```sql
SELECT name, price, calculate_discount(price, 0.10) AS discounted_price
FROM products;
```

## Stored Procedures

A stored procedure is a set of SQL statements that can be executed together. Stored procedures can have input parameters, output parameters, and return multiple values.

### Example: Creating a Stored Procedure

```sql
CREATE PROCEDURE GetEmployeeDetails(IN emp_id INT)
BEGIN
    SELECT name, department, salary
    FROM employees
    WHERE id = emp_id;
END
```

### Example: Calling the Stored Procedure

```sql
CREATE PROCEDURE GetEmployeeSalary(IN emp_id INT, OUT emp_salary DECIMAL(10,2))
BEGIN
    SELECT salary INTO emp_salary
    FROM employees
    WHERE id = emp_id;
END
```

### Example: Calling the Stored Procedure with Output Parameter

```sql
CALL GetEmployeeSalary(101, @salary);
SELECT @salary;
```

### Viewing Stored Procedures

```sql
# To view all stored procedures in the database
SHOW PROCEDURE STATUS;

# To view the code of a specific stored procedure
SHOW CREATE PROCEDURE GetEmployeeDetails;
```

### Dropping a Stored Procedure

```sql
DROP PROCEDURE IF EXISTS GetEmployeeDetails;
```

## Views

A view is a virtual table based on the result of a SELECT query. It doesn't store data but allows you to store complex queries for later reuse.

### Example: Creating a View

```sql
CREATE VIEW EmployeeSummary AS
SELECT department, COUNT(*) AS num_employees, AVG(salary) AS avg_salary
FROM employees
GROUP BY department;
```

### Example: Querying a View

```sql
SELECT * FROM EmployeeSummary;
```

### Example: Creating a View with a JOIN

```sql
CREATE VIEW DepartmentEmployee AS
SELECT e.name, e.department, d.manager
FROM employees e
JOIN departments d ON e.department = d.department_name;
```

### Example: Updating Data Through a View

```sql
UPDATE DepartmentEmployee
SET manager = 'New Manager'
WHERE name = 'John Doe';
```

### Dropping a View

```sql
DROP VIEW IF EXISTS EmployeeSummary;
```
