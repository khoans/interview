**20.** Organization Hierarchy — Composite pattern. Employees and departments, calculate total salary/headcount. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## Organization Hierarchy — Composite Pattern

### Pattern: Composite

**Why Composite?** An org chart is a tree: departments contain teams, teams contain employees, departments can contain sub-departments. `getTotalSalary()` and `getHeadcount()` must work uniformly on both individual employees and entire departments.

### Text UML

```
┌──────────────────────────────┐
│      <<interface>>           │
│      OrganizationUnit        │
├──────────────────────────────┤
│ + getName(): String          │
│ + getTotalSalary(): double   │
│ + getHeadcount(): int        │
│ + print(indent): void        │
└──────────┬───────────────────┘
           │
    ┌──────┴──────────────┐
    │                      │
┌───┴──────────┐  ┌───────┴──────────┐
│  Employee    │  │  Department      │
│  (Leaf)      │  │  (Composite)     │
├──────────────┤  ├──────────────────┤
│ - name       │  │ - name           │
│ - position   │  │ - units: List    │
│ - salary     │  ├──────────────────┤
└──────────────┘  │ + add(unit)      │
                  │ + remove(unit)   │
                  └──────────────────┘
```

### Key Design Decisions

1. **OrganizationUnit interface** — Both Employee and Department share the same contract
2. **Recursive aggregation** — `getTotalSalary()` on Department sums all children recursively
3. **Headcount** — Employee returns 1; Department returns sum of all children's headcount
4. **Pretty-print org chart** — Indented tree view for visualization

### SOLID Principles Applied

- **SRP**: Employee holds individual data; Department manages hierarchy
- **OCP**: Add new org unit types (Contractor, Consultant) by implementing interface
- **LSP**: Department and Employee are interchangeable where OrganizationUnit is expected

### Java Code

```java
import java.util.*;

// --- Organization Unit Interface ---
public interface OrganizationUnit {
    String getName();
    double getTotalSalary();
    int getHeadcount();
    void print(String indent);
}

// --- Employee (Leaf) ---
public class Employee implements OrganizationUnit {
    private final String name;
    private final String position;
    private final double salary;

    public Employee(String name, String position, double salary) {
        this.name = name;
        this.position = position;
        this.salary = salary;
    }

    @Override
    public String getName() { return name; }

    @Override
    public double getTotalSalary() { return salary; }

    @Override
    public int getHeadcount() { return 1; }

    @Override
    public void print(String indent) {
        System.out.printf("%s- %s (%s) $%.0f%n", indent, name, position, salary);
    }

    public String getPosition() { return position; }
    public double getSalary() { return salary; }
}

// --- Department (Composite) ---
public class Department implements OrganizationUnit {
    private final String name;
    private final List<OrganizationUnit> units;

    public Department(String name) {
        this.name = name;
        this.units = new ArrayList<>();
    }

    public void add(OrganizationUnit unit) {
        units.add(unit);
    }

    public void remove(OrganizationUnit unit) {
        units.remove(unit);
    }

    public List<OrganizationUnit> getUnits() {
        return Collections.unmodifiableList(units);
    }

    @Override
    public String getName() { return name; }

    @Override
    public double getTotalSalary() {
        return units.stream().mapToDouble(OrganizationUnit::getTotalSalary).sum();
    }

    @Override
    public int getHeadcount() {
        return units.stream().mapToInt(OrganizationUnit::getHeadcount).sum();
    }

    @Override
    public void print(String indent) {
        System.out.printf("%s[%s] (headcount: %d, salary: $%.0f)%n",
            indent, name, getHeadcount(), getTotalSalary());
        for (OrganizationUnit unit : units) {
            unit.print(indent + "  ");
        }
    }

    // Find all employees in this department (recursively)
    public List<Employee> findAllEmployees() {
        List<Employee> employees = new ArrayList<>();
        for (OrganizationUnit unit : units) {
            if (unit instanceof Employee) {
                employees.add((Employee) unit);
            } else if (unit instanceof Department) {
                employees.addAll(((Department) unit).findAllEmployees());
            }
        }
        return employees;
    }

    // Find department by name (recursive)
    public Optional<Department> findDepartment(String deptName) {
        if (this.name.equals(deptName)) return Optional.of(this);
        for (OrganizationUnit unit : units) {
            if (unit instanceof Department) {
                Optional<Department> found = ((Department) unit).findDepartment(deptName);
                if (found.isPresent()) return found;
            }
        }
        return Optional.empty();
    }
}
```

### Usage

```java
// Build organization hierarchy
Department company = new Department("Acme Corp");

// Engineering department
Department engineering = new Department("Engineering");
Department backend = new Department("Backend Team");
backend.add(new Employee("Alice", "Senior Developer", 120000));
backend.add(new Employee("Bob", "Developer", 95000));
backend.add(new Employee("Charlie", "Junior Developer", 70000));

Department frontend = new Department("Frontend Team");
frontend.add(new Employee("Diana", "Senior Developer", 115000));
frontend.add(new Employee("Eve", "Developer", 90000));

engineering.add(backend);
engineering.add(frontend);
engineering.add(new Employee("Frank", "Engineering Manager", 150000));

// Marketing department
Department marketing = new Department("Marketing");
marketing.add(new Employee("Grace", "Marketing Director", 130000));
marketing.add(new Employee("Hank", "Content Writer", 65000));

company.add(engineering);
company.add(marketing);
company.add(new Employee("Ivan", "CEO", 200000));

// Uniform operations on any level
System.out.println("Company headcount: " + company.getHeadcount());  // 9
System.out.println("Engineering salary: $" + engineering.getTotalSalary());  // $640,000
System.out.println("Backend headcount: " + backend.getHeadcount());  // 3

// Print org chart
company.print("");
// [Acme Corp] (headcount: 9, salary: $1035000)
//   [Engineering] (headcount: 6, salary: $640000)
//     [Backend Team] (headcount: 3, salary: $285000)
//       - Alice (Senior Developer) $120000
//       - Bob (Developer) $95000
//       - Charlie (Junior Developer) $70000
//     [Frontend Team] (headcount: 2, salary: $205000)
//       - Diana (Senior Developer) $115000
//       - Eve (Developer) $90000
//     - Frank (Engineering Manager) $150000
//   [Marketing] (headcount: 2, salary: $195000)
//     - Grace (Marketing Director) $130000
//     - Hank (Content Writer) $65000
//   - Ivan (CEO) $200000
```

### Interview Talking Points

- **Classic Composite**: Org charts are THE textbook example — tree structure with uniform operations on nodes and leaves
- **Real-world complexity**: In production, departments have managers, budget caps, cost centers — but the Composite structure stays the same
- **Recursive aggregation**: `getTotalSalary()` and `getHeadcount()` demonstrate the power — works at any level of the hierarchy
- **Performance**: For very deep trees, consider caching aggregated values and invalidating on changes (observer pattern to notify parent on child changes)
