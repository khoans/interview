**18.** Employee Salary Calculator — Decorator pattern. Base salary + bonus, tax, insurance, overtime. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## Employee Salary Calculator — Decorator Pattern

### Pattern: Decorator

**Why Decorator?** Salary components are stackable and vary per employee: some get overtime, some don't; bonus percentages differ; tax brackets vary. Decorator composes these dynamically without creating a subclass per combination.

### Text UML

```
┌───────────────────────────────┐
│    <<interface>>              │
│    SalaryCalculator           │
├───────────────────────────────┤
│ + calculate(): double         │
│ + getBreakdown(): String      │
└──────────┬────────────────────┘
           │
    ┌──────┴──────────────────────────┐
    │                                  │
┌───┴──────────────┐     ┌────────────┴──────────┐
│ BaseSalary       │     │ <<abstract>>          │
│                  │     │ SalaryDecorator       │
└──────────────────┘     ├───────────────────────┤
                         │ - base: SalaryCalc    │
                         └──────────┬────────────┘
                                    │
                  ┌─────────┬───────┼──────┬───────┐
                  │         │       │      │       │
              BonusDec  TaxDec  InsurDec OvertimeDec AllowanceDec
```

### Key Design Decisions

1. **Additive and subtractive decorators** — Bonus/overtime add; tax/insurance subtract. Same interface handles both
2. **getBreakdown()** — Audit trail showing each component's contribution. Essential for HR/payroll
3. **Order matters** — Tax is calculated on (base + bonus + overtime), not just base. Decorators must be stacked correctly
4. **Percentage-based and fixed-amount** decorators — Tax is percentage; transport allowance is fixed

### SOLID Principles Applied

- **SRP**: Each decorator handles one salary component
- **OCP**: Add new components (stock options, commission) without modifying existing decorators
- **LSP**: Decorated calculator IS-A SalaryCalculator — HR system treats all uniformly

### Java Code

```java
import java.util.ArrayList;
import java.util.List;

// --- Salary Calculator Interface ---
public interface SalaryCalculator {
    double calculate();
    String getBreakdown();
}

// --- Base Salary ---
public class BaseSalary implements SalaryCalculator {
    private final String employeeName;
    private final double monthlySalary;

    public BaseSalary(String employeeName, double monthlySalary) {
        this.employeeName = employeeName;
        this.monthlySalary = monthlySalary;
    }

    @Override
    public double calculate() {
        return monthlySalary;
    }

    @Override
    public String getBreakdown() {
        return String.format("Employee: %s\n  Base Salary:    +$%.2f", employeeName, monthlySalary);
    }
}

// --- Abstract Decorator ---
public abstract class SalaryDecorator implements SalaryCalculator {
    protected final SalaryCalculator base;

    public SalaryDecorator(SalaryCalculator base) {
        this.base = base;
    }
}

// --- Bonus Decorator (percentage of base) ---
public class BonusDecorator extends SalaryDecorator {
    private final double bonusPercentage;
    private final String reason;

    public BonusDecorator(SalaryCalculator base, double bonusPercentage, String reason) {
        super(base);
        this.bonusPercentage = bonusPercentage;
        this.reason = reason;
    }

    @Override
    public double calculate() {
        double baseSalary = base.calculate();
        return baseSalary + (baseSalary * bonusPercentage / 100);
    }

    @Override
    public String getBreakdown() {
        double bonusAmount = base.calculate() * bonusPercentage / 100;
        return base.getBreakdown() + String.format(
            "\n  Bonus (%s %.0f%%): +$%.2f", reason, bonusPercentage, bonusAmount);
    }
}

// --- Tax Decorator (progressive brackets) ---
public class TaxDecorator extends SalaryDecorator {
    private final double taxRate;

    public TaxDecorator(SalaryCalculator base, double taxRate) {
        super(base);
        this.taxRate = taxRate;
    }

    @Override
    public double calculate() {
        double gross = base.calculate();
        return gross - (gross * taxRate / 100);
    }

    @Override
    public String getBreakdown() {
        double taxAmount = base.calculate() * taxRate / 100;
        return base.getBreakdown() + String.format(
            "\n  Tax (%.0f%%):          -$%.2f", taxRate, taxAmount);
    }
}

// --- Insurance Decorator (fixed rate) ---
public class InsuranceDecorator extends SalaryDecorator {
    private final double insuranceRate;
    private final String type; // "health", "dental", "life"

    public InsuranceDecorator(SalaryCalculator base, double insuranceRate, String type) {
        super(base);
        this.insuranceRate = insuranceRate;
        this.type = type;
    }

    @Override
    public double calculate() {
        double current = base.calculate();
        return current - (current * insuranceRate / 100);
    }

    @Override
    public String getBreakdown() {
        double amount = base.calculate() * insuranceRate / 100;
        return base.getBreakdown() + String.format(
            "\n  %s Insurance (%.1f%%): -$%.2f", type, insuranceRate, amount);
    }
}

// --- Overtime Decorator (hourly rate) ---
public class OvertimeDecorator extends SalaryDecorator {
    private final double hoursWorked;
    private final double hourlyRate;

    public OvertimeDecorator(SalaryCalculator base, double hoursWorked, double hourlyRate) {
        super(base);
        this.hoursWorked = hoursWorked;
        this.hourlyRate = hourlyRate;
    }

    @Override
    public double calculate() {
        return base.calculate() + (hoursWorked * hourlyRate);
    }

    @Override
    public String getBreakdown() {
        double overtimeAmount = hoursWorked * hourlyRate;
        return base.getBreakdown() + String.format(
            "\n  Overtime (%.0fh x $%.2f): +$%.2f", hoursWorked, hourlyRate, overtimeAmount);
    }
}

// --- Fixed Allowance Decorator ---
public class AllowanceDecorator extends SalaryDecorator {
    private final double amount;
    private final String type; // "transport", "meal", "phone"

    public AllowanceDecorator(SalaryCalculator base, double amount, String type) {
        super(base);
        this.amount = amount;
        this.type = type;
    }

    @Override
    public double calculate() {
        return base.calculate() + amount;
    }

    @Override
    public String getBreakdown() {
        return base.getBreakdown() + String.format(
            "\n  %s Allowance:   +$%.2f", type, amount);
    }
}
```

### Usage

```java
// Senior developer: Base + Performance Bonus + Overtime + Transport - Tax - Health Insurance
SalaryCalculator salary = new BaseSalary("John Doe", 5000.00);
salary = new BonusDecorator(salary, 15, "Performance");      // +15% bonus
salary = new OvertimeDecorator(salary, 20, 45.00);           // +20h overtime
salary = new AllowanceDecorator(salary, 200, "Transport");   // +$200 transport
salary = new TaxDecorator(salary, 22);                       // -22% tax
salary = new InsuranceDecorator(salary, 3.5, "Health");      // -3.5% health insurance

System.out.println(salary.getBreakdown());
System.out.printf("\n  ═══════════════════════════\n  NET PAY:          $%.2f%n", salary.calculate());

// Output:
// Employee: John Doe
//   Base Salary:    +$5000.00
//   Bonus (Performance 15%): +$750.00
//   Overtime (20h x $45.00): +$900.00
//   Transport Allowance:   +$200.00
//   Tax (22%):          -$1507.00
//   Health Insurance (3.5%): -$186.90
//
//   ═══════════════════════════
//   NET PAY:          $5156.10
```

### Interview Talking Points

- **Order-sensitive stacking**: Tax should be calculated AFTER bonuses/overtime are added. Stacking order = business rule
- **Audit trail**: `getBreakdown()` produces a line-item payslip — essential for compliance and employee transparency
- **vs Strategy**: Strategy would swap the entire calculation algorithm. Decorator lets you compose individual components
- **Production**: In payroll systems, salary components are stored as rules in DB and applied dynamically — similar to decorators loaded at runtime
