**30.** Given the following tables:
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
Write a query to list all employees with their department name and project names they are working on.

    ┌────┬───────────────────────────┬───────┬─────┐
    │ #  │ Category                  │ Count │ %   │
    ├────┼───────────────────────────┼───────┼─────┤
    │ 1  │ 🗄️ Database/SQL           │ 14    │ 27% │
    │ 2  │ 🍃 Spring Framework       │ 10    │ 19% │
    │ 3  │ ☕ Java Core              │ 8     │ 15% │
    │ 4  │ 🏗️ System Architecture    │ 6     │ 12% │
    │ 5  │ 🚀 DevOps/Deployment      │ 5     │ 10% │
    │ 6  │ 💬 Behavioral/Soft Skills │ 4     │ 8%  │
    │ 7  │ 📐 Design Patterns        │ 2     │ 4%  │
    │ 8  │ 📊 Data Structures        │ 2     │ 4%  │
    │ 9  │ 🔄 Agile/Scrum            │ 2     │ 4%  │
    │ 10 │ ✅ Testing                │ 2     │ 4%  │
    │ 11 │ 🎨 Frontend               │ 2     │ 4%  │
    │ 12 │ 🔒 Security               │ 1     │ 2%  │
    │ 13 │ 🧮 Math/Logic             │ 1     │ 2%  │
    └────┴───────────────────────────┴───────┴─────┘
