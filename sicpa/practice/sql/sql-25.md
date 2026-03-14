**25.** Given the following tables:
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
Write a query to count the number of employees in each department, including departments with zero employees.

```SQL
select d.id, d.name, count(*) from departments d 
left join employees e on e.department_id = d.id
group by d.id, d.name
```
