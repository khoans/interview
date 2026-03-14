**26.** Given the following tables:
```sql
TABLE departments
    id INTEGER NOT NULL PRIMARY KEY
    name VARCHAR(255) NOT NULL
    budget DECIMAL(12, 2)

TABLE employees
    id INTEGER NOT NULL PRIMARY KEY
    name VARCHAR(255) NOT NULL
    department_id INTEGER REFERENCES departments(id)

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

```SQL
select e.* from employees e 
join employee_projects ep on ep.employee_id = e.id
join projects p on ep.project_id = p.id
where p.name = 'Website Redesign'
```
