# 50 SQL Practice Questions (Easy - Medium)

## Questions 1-10: Basic SELECT and Filtering

**1.** Given the following table:
```sql
TABLE employees
    id INTEGER NOT NULL PRIMARY KEY
    name VARCHAR(255) NOT NULL
    salary DECIMAL(10, 2)
    department_id INTEGER
```
Write a query to find all employees with salary greater than 5000.

**2.** Given the same `employees` table, write a query to find all employees whose name starts with 'A'.

**3.** Write a query to find all employees whose salary is between 3000 and 7000 (inclusive).

**4.** Write a query to find all employees who do not have a department_id assigned (NULL).

**5.** Write a query to find all employees whose name contains the letter 'o'.

**6.** Write a query to return distinct department_ids from the employees table.

**7.** Write a query to find all employees ordered by salary in descending order.

**8.** Write a query to find the top 5 highest paid employees.

**9.** Write a query to find all employees whose department_id is either 1, 3, or 5.

**10.** Write a query to find all employees whose name ends with 'son'.

---

## Questions 11-20: Aggregation and GROUP BY

**11.** Write a query to count the total number of employees.

**12.** Write a query to find the average salary of all employees.

**13.** Write a query to find the total sum of all salaries.

**14.** Write a query to find the minimum and maximum salary.

**15.** Write a query to count the number of employees in each department.

**16.** Write a query to find the average salary per department.

**17.** Write a query to find departments that have more than 5 employees.

**18.** Write a query to find the department with the highest average salary.

**19.** Write a query to count how many employees have NULL department_id.

**20.** Write a query to find the total salary budget per department, ordered by total budget descending.

---

## Questions 21-30: JOINs

**21.** Given the following tables:
```sql
TABLE departments
    id INTEGER NOT NULL PRIMARY KEY
    name VARCHAR(255) NOT NULL
    budget DECIMAL(12, 2)

TABLE employees
    id INTEGER NOT NULL PRIMARY KEY
    name VARCHAR(255) NOT NULL
    department_id INTEGER REFERENCES departments(id)
```
Write a query to list all employees with their department names.

**22.** Write a query to list all departments with their employees (include departments with no employees).

**23.** Write a query to find all employees who work in the 'IT' department.

**24.** Write a query to find departments that have no employees.

**25.** Write a query to count the number of employees in each department, including departments with zero employees.

**26.** Given an additional table:
```sql
TABLE projects
    id INTEGER NOT NULL PRIMARY KEY
    name VARCHAR(255) NOT NULL
    department_id INTEGER REFERENCES departments(id)

TABLE employee_projects
    employee_id INTEGER REFERENCES employees(id)
    project_id INTEGER REFERENCES projects(id)
    PRIMARY KEY (employee_id, project_id)
```
Write a query to find all employees working on the 'Website Redesign' project.

**27.** Write a query to find all projects that have no employees assigned.

**28.** Write a query to count how many projects each employee is working on.

**29.** Write a query to find employees who work on more than 2 projects.

**30.** Write a query to list all employees with their department name and project names they are working on.

---

## Questions 31-40: Subqueries and Self-Joins

**31.** Using the `employees` table from question 1, write a query to find employees who earn more than the average salary.

**32.** Write a query to find the employee with the highest salary in each department.

**33.** Write a query to find departments where the average salary is above the company-wide average salary.

**34.** Given the table from question 48:
```sql
TABLE employee
    id INTEGER NOT NULL PRIMARY KEY
    managerId INTEGER REFERENCES employee(id)
    name VARCHAR(255) NOT NULL
```
Write a query to list all employees with their manager's name.

**35.** Write a query to find all managers (employees who have at least one person reporting to them).

**36.** Write a query to find employees who earn more than their managers.

**37.** Write a query to find the second highest salary in the company.

**38.** Write a query to find all employees who have the same salary as at least one other employee.

**39.** Write a query to find departments that have employees with salary above 10000.

**40.** Write a query to find employees who work in the same department as 'John Smith'.

---

## Questions 41-50: Advanced Filtering and Data Manipulation

**41.** Write a query to update all employees' salary by 10% for those in department_id = 1.

**42.** Write a query to delete all employees who do not have a department_id assigned.

**43.** Write a query to insert a new employee with name 'Jane Doe', salary 6000, department_id 3.

**44.** Write a query to find duplicate names in the employees table (names that appear more than once).

**45.** Write a query to find employees whose salary is NULL.

**46.** Write a query using CASE statement to categorize employees as 'Low' (salary < 4000), 'Medium' (4000-8000), 'High' (> 8000).

**47.** Write a query to find the 3rd highest salary without using LIMIT/OFFSET.

**48.** Write a query to find employees who joined in the last 6 months (assume there's a `hire_date DATE` column).

**49.** Write a query to find consecutive employees (by id) who work in the same department.

**50.** Write a query to pivot the data: show each department as a column with the count of employees (assume departments: 'IT', 'HR', 'Sales').

---

## Answer Template

Create a separate file `sql-practice-answers.md` with your answers following this format:

```markdown
## Question 1
```sql
SELECT * FROM employees WHERE salary > 5000;
```

## Question 2
```sql
SELECT * FROM employees WHERE name LIKE 'A%';
```
...
```

---

## Tips for Interview Preparation

1. **Understand the basics**: Make sure you're comfortable with SELECT, WHERE, ORDER BY, LIMIT
2. **Master JOINs**: INNER, LEFT, RIGHT, FULL OUTER joins
3. **Aggregation**: GROUP BY, HAVING, aggregate functions (COUNT, SUM, AVG, MIN, MAX)
4. **Subqueries**: Know when to use subqueries vs JOINs
5. **NULL handling**: Understand how NULL behaves in comparisons
6. **Practice writing**: Write queries by hand, not just running them
7. **Explain your thought**: In interviews, explain your approach before writing

Good luck with your interview preparation!
