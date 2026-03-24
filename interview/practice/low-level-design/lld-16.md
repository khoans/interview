**16.** Coffee Shop Ordering — Decorator pattern. Base drinks + unlimited add-ons with price/description. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## Coffee Shop Ordering — Decorator Pattern

### Pattern: Decorator

**Why Decorator?** Combinatorial explosion problem — 4 base drinks x 6 add-ons = 24+ subclasses with inheritance. Decorator lets you stack unlimited add-ons dynamically at runtime with open/closed principle.

### Text UML

```
┌──────────────────────────┐
│    <<interface>>         │
│    Beverage              │
├──────────────────────────┤
│ + getDescription(): String│
│ + getCost(): double      │
│ + getSize(): Size        │
└──────────┬───────────────┘
           │
    ┌──────┴──────────────────────────┐
    │                                  │
┌───┴──────────┐          ┌───────────┴────────────┐
│ BaseBeverage │          │ <<abstract>>           │
│ (concrete)   │          │ BeverageDecorator      │
├──────────────┤          ├────────────────────────┤
│ Espresso     │          │ - beverage: Beverage   │
│ HouseBlend   │          └──────────┬─────────────┘
│ DarkRoast    │                     │
│ GreenTea     │              ┌──────┼──────┬─────────┐
└──────────────┘              │      │      │         │
                          Milk  Mocha  Whip   Caramel
                                              Vanilla
```

### Key Design Decisions

1. **Interface-based Beverage** — Both base drinks and decorators share the same type
2. **Abstract decorator base** — `BeverageDecorator` holds reference to wrapped `Beverage` and delegates
3. **Size-aware pricing** — Add-on cost varies by size (Tall/Grande/Venti)
4. **Unlimited stacking** — `new Mocha(new Mocha(new Espresso()))` = double mocha espresso

### SOLID Principles Applied

- **SRP**: Each decorator handles one add-on's cost/description
- **OCP**: Add new toppings without modifying existing classes
- **LSP**: Decorated beverage IS-A Beverage — works everywhere a Beverage is expected
- **DIP**: All code depends on `Beverage` interface

### Java Code

```java
// --- Size Enum ---
public enum Size {
    TALL(1.0), GRANDE(1.25), VENTI(1.5);

    private final double priceMultiplier;
    Size(double multiplier) { this.priceMultiplier = multiplier; }
    public double getMultiplier() { return priceMultiplier; }
}

// --- Beverage Interface ---
public interface Beverage {
    String getDescription();
    double getCost();
    Size getSize();
}

// ===================== Base Beverages =====================

public class Espresso implements Beverage {
    private final Size size;

    public Espresso(Size size) { this.size = size; }

    @Override
    public String getDescription() { return size + " Espresso"; }
    @Override
    public double getCost() { return 1.99 * size.getMultiplier(); }
    @Override
    public Size getSize() { return size; }
}

public class HouseBlend implements Beverage {
    private final Size size;

    public HouseBlend(Size size) { this.size = size; }

    @Override
    public String getDescription() { return size + " House Blend"; }
    @Override
    public double getCost() { return 0.89 * size.getMultiplier(); }
    @Override
    public Size getSize() { return size; }
}

public class DarkRoast implements Beverage {
    private final Size size;

    public DarkRoast(Size size) { this.size = size; }

    @Override
    public String getDescription() { return size + " Dark Roast"; }
    @Override
    public double getCost() { return 1.49 * size.getMultiplier(); }
    @Override
    public Size getSize() { return size; }
}

public class GreenTea implements Beverage {
    private final Size size;

    public GreenTea(Size size) { this.size = size; }

    @Override
    public String getDescription() { return size + " Green Tea"; }
    @Override
    public double getCost() { return 1.29 * size.getMultiplier(); }
    @Override
    public Size getSize() { return size; }
}

// ===================== Abstract Decorator =====================

public abstract class BeverageDecorator implements Beverage {
    protected final Beverage beverage;

    public BeverageDecorator(Beverage beverage) {
        this.beverage = beverage;
    }

    @Override
    public Size getSize() {
        return beverage.getSize();
    }
}

// ===================== Concrete Decorators =====================

public class MilkDecorator extends BeverageDecorator {
    public MilkDecorator(Beverage beverage) { super(beverage); }

    @Override
    public String getDescription() { return beverage.getDescription() + ", Milk"; }

    @Override
    public double getCost() { return beverage.getCost() + 0.30 * getSize().getMultiplier(); }
}

public class MochaDecorator extends BeverageDecorator {
    public MochaDecorator(Beverage beverage) { super(beverage); }

    @Override
    public String getDescription() { return beverage.getDescription() + ", Mocha"; }

    @Override
    public double getCost() { return beverage.getCost() + 0.50 * getSize().getMultiplier(); }
}

public class WhipDecorator extends BeverageDecorator {
    public WhipDecorator(Beverage beverage) { super(beverage); }

    @Override
    public String getDescription() { return beverage.getDescription() + ", Whip"; }

    @Override
    public double getCost() { return beverage.getCost() + 0.40 * getSize().getMultiplier(); }
}

public class CaramelDecorator extends BeverageDecorator {
    public CaramelDecorator(Beverage beverage) { super(beverage); }

    @Override
    public String getDescription() { return beverage.getDescription() + ", Caramel"; }

    @Override
    public double getCost() { return beverage.getCost() + 0.60 * getSize().getMultiplier(); }
}

public class VanillaDecorator extends BeverageDecorator {
    public VanillaDecorator(Beverage beverage) { super(beverage); }

    @Override
    public String getDescription() { return beverage.getDescription() + ", Vanilla"; }

    @Override
    public double getCost() { return beverage.getCost() + 0.45 * getSize().getMultiplier(); }
}
```

### Usage

```java
// Grande Dark Roast with double Mocha, Whip, and Caramel
Beverage order = new DarkRoast(Size.GRANDE);
order = new MochaDecorator(order);
order = new MochaDecorator(order);   // double mocha
order = new WhipDecorator(order);
order = new CaramelDecorator(order);

System.out.println(order.getDescription());
// GRANDE Dark Roast, Mocha, Mocha, Whip, Caramel
System.out.printf("$%.2f%n", order.getCost());
// $4.36 (1.49*1.25 + 0.50*1.25*2 + 0.40*1.25 + 0.60*1.25)

// Simple Tall Espresso with Milk
Beverage simple = new MilkDecorator(new Espresso(Size.TALL));
System.out.println(simple.getDescription() + " — $" + String.format("%.2f", simple.getCost()));
// TALL Espresso, Milk — $2.29
```

### Interview Talking Points

- **Classic Head First Design Patterns example** — interviewers expect this exact pattern
- **Java I/O uses same pattern**: `new BufferedReader(new InputStreamReader(new FileInputStream(file)))` — decorators wrapping decorators
- **vs Inheritance**: 4 drinks x 6 toppings x 3 sizes = 72 classes with inheritance. Decorator: 4 + 6 = 10 classes
- **Production**: In Spring, `HandlerInterceptor` and `Filter` chains follow the same wrapping principle
