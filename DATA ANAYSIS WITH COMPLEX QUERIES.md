# DATA ANAYSIS WITH COMPLEX QUERIES
 This report uses SQL techniques like **window functions**, **subqueries**, and **Common Table Expressions (CTEs)** to analyze trends and patterns in employee data, including salary trends,department analysis,etc.

 Consider 2 tables given below
## 1. Employees Table

| employee_id | name           | department | salary |   |   |   |
|-------------|----------------|------------|--------|---|---|---|
| 1           | Aditya Bhosle  | IT         | 120000 |   |   |   |
| 2           | Rutuja Rajpure | IT         | 110000 |   |   |   |
| 3           | Archana Gupta  | HR         | 95000  |   |   |   |
| 4           | Shashank Jhage | HR         | 92000  |   |   |   |
| 5           | Prasad Kate    | Sales      | 100000 |   |   |   |
| 6           | Khushi Oswal   | Sales      | 95000  |   |   |   |

## 2. Departments Table

| department_id | department_name |   |   |   |   |   |
|---------------|-----------------|---|---|---|---|---|
| 1             | IT              |   |   |   |   |   |
| 2             | HR              |   |   |   |   |   |
| 3             | Sales     


## 1. **Employee Salary Trends** (using Window Function )

We will analyze employee salaries and rank them within their departments using the `RANK()` window function to identify highest salary employee.

###  Query:
```sql
SELECT 
    employee_id,
    name,
    department,
    salary,
    RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS salary_rank
FROM employees;
```

### Explanation:
#### RANK() OVER partitions the employees by department and orders them by salary in descending order.This query helps identify the highest paid employee within each department.

### Output
| employee_id | name           | department | salary    | salary_rank |   |   |
|-------------|----------------|------------|-----------|-------------|---|---|
| 3           | Archana Gupta  | HR         | 95000.00  | 1           |   |   |
| 4           | Shashank Jhage | HR         | 92000.00  | 2           |   |   |
| 1           | Aditya Bhosle  | IT         | 120000.00 | 1           |   |   |
| 2           | Rutuja Rajpure | IT         | 110000.00 | 2           |   |   |
| 5           | Prasad Kate    | Sales      | 100000.00 | 1           |   |   |
| 6           | Khushi Oswal   | Sales      | 95000.00  | 2           |   |   |
|             |                |            |           |
## 2. Average Salary by Department (using Common Table Expressions)

 Next, we will calculate the average salary for each department and compare individual employee salaries to the department's average salary.
### Query:

 Calculate the average salary in each department and compare employee salaries
```sql
WITH department_avg_salary AS (
    SELECT department, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department
)
SELECT 
    e.employee_id,
    e.name,
    e.department,
    e.salary,
    d.avg_salary,
    CASE 
        WHEN e.salary > d.avg_salary THEN 'Above Average'
        WHEN e.salary < d.avg_salary THEN 'Below Average'
        ELSE 'Average'
    END AS salary_comparison
FROM employees e
JOIN department_avg_salary d ON e.department = d.department;
```
### Explanation:

 CTE (department_avg_salary) calculates the average salary for each department.The main query compares each employee's salary to the department's average salary and classifies them as "Above Average," "Below Average," or "Average."

### Output
| employee_id | name           | department | salary    | avg_salary    | salary_comparison |   |
|-------------|----------------|------------|-----------|---------------|-------------------|---|
| 1           | Aditya Bhosle  | IT         | 120000.00 | 115000.000000 | Above Average     |   |
| 2           | Rutuja Rajpure | IT         | 110000.00 | 115000.000000 | Below Average     |   |
| 3           | Archana Gupta  | HR         | 95000.00  | 93500.000000  | Above Average     |   |
| 4           | Shashank Jhage | HR         | 92000.00  | 93500.000000  | Below Average     |   |
| 5           | Prasad Kate    | Sales      | 100000.00 | 97500.000000  | Above Average     |   |
| 6           | Khushi Oswal   | Sales      | 95000.00  | 97500.000000  | Below Average 

## 3.Employees Earning Above Department Average (using a Subquery)
 Finds employees earning above their department’s average.

### Query
```sql
SELECT 
    e.employee_id, 
    e.name, 
    e.department, 
    e.salary
FROM Employees e
WHERE e.salary > (
    SELECT AVG(salary) 
    FROM Employees 
    WHERE department = e.department
);
```
### Explanation
 We use a subquery to calculate the average salary for each department, and then the main query selects employees who earn more than this average.

### Output
| employee_id | name          | department | salary |   |   |   |
|-------------|---------------|------------|--------|---|---|---|
| 1           | Aditya Bhosle | IT         | 120000 |   |   |   |
| 3           | Archana Gupta | HR         | 95000  |   |   |   |
| 5           | Prasad Kate   | Sales      | 100000 |   |   |   |
