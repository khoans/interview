**40.** Given the following table:
```sql
TABLE employees
    id INTEGER NOT NULL PRIMARY KEY
    name VARCHAR(255) NOT NULL
    salary DECIMAL(10, 2)
    department_id INTEGER
```
Write a query to find employees who work in the same department as 'John Smith'.

```SQL
select * from employees e 
where department_id = (select department_id from employees where name = 'John Smith')
```
