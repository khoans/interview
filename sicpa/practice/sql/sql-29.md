**29.** Given the following tables:
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
Write a query to find employees who work on more than 2 projects.
