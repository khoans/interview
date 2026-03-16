**35.** Given the following table:
```sql
TABLE employee
    id INTEGER NOT NULL PRIMARY KEY
    managerId INTEGER REFERENCES employee(id)
    name VARCHAR(255) NOT NULL
```
Write a query to find all managers (employees who have at least one person reporting to them).

```SQL
select distinct m.id, m.name from employee m
inner join employee e on e.managerId = m.id 
```
