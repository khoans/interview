**29.** Design a Shipping Cost Calculator using the Strategy pattern. Support FedEx, UPS, USPS, DHL with different pricing rules.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Shipping Cost Calculator — Strategy Pattern

**Pattern:** Strategy (Behavioral)
**Why Strategy?** Each carrier has unique pricing rules (weight tiers, zone multipliers, surcharges). Strategy encapsulates each carrier's logic independently.

**SOLID Principles Applied:**
- **SRP:** Each carrier strategy owns its pricing formula
- **OCP:** Add new carriers (e.g., Amazon Logistics) without modifying calculator
- **DIP:** ShippingCalculator depends on ShippingStrategy interface

### Text UML Diagram

```
┌──────────────────┐      ┌────────────────────┐
│ShippingCalculator│─────→│  <<interface>>      │
│──────────────────│      │ ShippingStrategy    │
│ -strategy        │      │────────────────────│
│ +calculate()     │      │ +calculateCost()    │
│ +setStrategy()   │      │ +getEstimatedDays() │
│ +findCheapest()  │      │ +getCarrierName()   │
└──────────────────┘      └────────┬───────────┘
                                   │ implements
                    ┌──────┬───────┼───────┬──────┐
                    │      │       │       │      │
                 FedEx   UPS    USPS    DHL    (new)
```

### Key Design Decisions

1. **Package as value object** — Immutable shipping details passed to strategies
2. **Zone-based pricing** — Distance zones affect cost; each carrier has different zone multipliers
3. **findCheapest()** — Calculator can compare all strategies and pick the best one
4. **Estimated days** — Each carrier provides delivery time estimate alongside cost

### Java Implementation

```java
import java.util.*;

// ============================================================
// VALUE OBJECTS
// ============================================================

class Package {
    private final double weightLbs;
    private final double lengthIn;
    private final double widthIn;
    private final double heightIn;
    private final String fromZip;
    private final String toZip;

    public Package(double weightLbs, double lengthIn, double widthIn,
                   double heightIn, String fromZip, String toZip) {
        this.weightLbs = weightLbs;
        this.lengthIn = lengthIn;
        this.widthIn = widthIn;
        this.heightIn = heightIn;
        this.fromZip = fromZip;
        this.toZip = toZip;
    }

    public double getWeightLbs() { return weightLbs; }
    public double getLengthIn() { return lengthIn; }
    public double getWidthIn() { return widthIn; }
    public double getHeightIn() { return heightIn; }
    public String getFromZip() { return fromZip; }
    public String getToZip() { return toZip; }

    /** Dimensional weight = (L x W x H) / divisor */
    public double getDimensionalWeight(int divisor) {
        return (lengthIn * widthIn * heightIn) / divisor;
    }

    /** Billable weight = max(actual, dimensional) */
    public double getBillableWeight(int dimDivisor) {
        return Math.max(weightLbs, getDimensionalWeight(dimDivisor));
    }
}

class ShippingQuote {
    private final String carrier;
    private final double cost;
    private final int estimatedDays;

    public ShippingQuote(String carrier, double cost, int estimatedDays) {
        this.carrier = carrier;
        this.cost = cost;
        this.estimatedDays = estimatedDays;
    }

    public String getCarrier() { return carrier; }
    public double getCost() { return cost; }
    public int getEstimatedDays() { return estimatedDays; }

    @Override
    public String toString() {
        return String.format("%s: $%.2f (%d days)", carrier, cost, estimatedDays);
    }
}

// ============================================================
// STRATEGY INTERFACE
// ============================================================

interface ShippingStrategy {
    ShippingQuote calculateCost(Package pkg);
    String getCarrierName();
}

// ============================================================
// CONCRETE STRATEGIES
// ============================================================

class FedExStrategy implements ShippingStrategy {
    private static final double BASE_RATE = 5.99;
    private static final double PER_LB_RATE = 0.80;
    private static final int DIM_DIVISOR = 139; // FedEx divisor
    private static final double FUEL_SURCHARGE = 1.08; // 8% fuel surcharge

    @Override
    public ShippingQuote calculateCost(Package pkg) {
        double billableWeight = pkg.getBillableWeight(DIM_DIVISOR);
        double cost = (BASE_RATE + (billableWeight * PER_LB_RATE)) * FUEL_SURCHARGE;

        // Zone surcharge (simplified: based on zip distance)
        int zone = estimateZone(pkg.getFromZip(), pkg.getToZip());
        cost *= (1 + zone * 0.05);

        return new ShippingQuote(getCarrierName(), cost, 2 + zone);
    }

    @Override
    public String getCarrierName() { return "FedEx"; }

    private int estimateZone(String from, String to) {
        int diff = Math.abs(Integer.parseInt(from.substring(0, 1))
                - Integer.parseInt(to.substring(0, 1)));
        return Math.min(diff, 8);
    }
}

class UPSStrategy implements ShippingStrategy {
    private static final double BASE_RATE = 6.49;
    private static final double PER_LB_RATE = 0.75;
    private static final int DIM_DIVISOR = 139;

    @Override
    public ShippingQuote calculateCost(Package pkg) {
        double billableWeight = pkg.getBillableWeight(DIM_DIVISOR);
        double cost = BASE_RATE + (billableWeight * PER_LB_RATE);

        // UPS weight tier discounts
        if (billableWeight > 50) cost *= 0.90; // 10% discount for heavy
        if (billableWeight > 100) cost *= 0.85; // additional 15% off

        int zone = estimateZone(pkg.getFromZip(), pkg.getToZip());
        cost *= (1 + zone * 0.04);

        return new ShippingQuote(getCarrierName(), cost, 3 + zone);
    }

    @Override
    public String getCarrierName() { return "UPS"; }

    private int estimateZone(String from, String to) {
        int diff = Math.abs(Integer.parseInt(from.substring(0, 1))
                - Integer.parseInt(to.substring(0, 1)));
        return Math.min(diff, 8);
    }
}

class USPSStrategy implements ShippingStrategy {
    private static final double FLAT_RATE_SMALL = 8.95;
    private static final double FLAT_RATE_MEDIUM = 15.50;
    private static final double FLAT_RATE_LARGE = 21.90;
    private static final double MAX_WEIGHT = 70.0;

    @Override
    public ShippingQuote calculateCost(Package pkg) {
        if (pkg.getWeightLbs() > MAX_WEIGHT) {
            throw new IllegalArgumentException("USPS max weight is 70 lbs");
        }

        // USPS flat rate based on box size
        double volume = pkg.getLengthIn() * pkg.getWidthIn() * pkg.getHeightIn();
        double cost;
        if (volume <= 500) {
            cost = FLAT_RATE_SMALL;
        } else if (volume <= 2000) {
            cost = FLAT_RATE_MEDIUM;
        } else {
            cost = FLAT_RATE_LARGE;
        }

        return new ShippingQuote(getCarrierName(), cost, 5); // USPS flat rate = any zone
    }

    @Override
    public String getCarrierName() { return "USPS"; }
}

class DHLStrategy implements ShippingStrategy {
    private static final double BASE_RATE = 12.99;
    private static final double PER_LB_RATE = 1.20;
    private static final int DIM_DIVISOR = 139;

    @Override
    public ShippingQuote calculateCost(Package pkg) {
        double billableWeight = pkg.getBillableWeight(DIM_DIVISOR);
        double cost = BASE_RATE + (billableWeight * PER_LB_RATE);

        // DHL international surcharge simulation
        cost *= 1.15; // 15% international handling

        return new ShippingQuote(getCarrierName(), cost, 7);
    }

    @Override
    public String getCarrierName() { return "DHL Express"; }
}

// ============================================================
// CONTEXT — calculator with comparison support
// ============================================================

class ShippingCalculator {
    private ShippingStrategy strategy;
    private final List<ShippingStrategy> allStrategies;

    public ShippingCalculator() {
        this.allStrategies = new ArrayList<>();
        allStrategies.add(new FedExStrategy());
        allStrategies.add(new UPSStrategy());
        allStrategies.add(new USPSStrategy());
        allStrategies.add(new DHLStrategy());
        this.strategy = allStrategies.get(0); // default
    }

    public void setStrategy(ShippingStrategy strategy) {
        this.strategy = strategy;
    }

    public ShippingQuote calculate(Package pkg) {
        return strategy.calculateCost(pkg);
    }

    /** Compare all carriers and return sorted quotes */
    public List<ShippingQuote> getAllQuotes(Package pkg) {
        List<ShippingQuote> quotes = new ArrayList<>();
        for (ShippingStrategy s : allStrategies) {
            try {
                quotes.add(s.calculateCost(pkg));
            } catch (IllegalArgumentException e) {
                System.out.println(s.getCarrierName() + ": " + e.getMessage());
            }
        }
        quotes.sort(Comparator.comparingDouble(ShippingQuote::getCost));
        return quotes;
    }

    /** Find the cheapest carrier */
    public ShippingQuote findCheapest(Package pkg) {
        return getAllQuotes(pkg).stream()
                .findFirst()
                .orElseThrow(() -> new RuntimeException("No carriers available"));
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class ShippingDemo {
    public static void main(String[] args) {
        ShippingCalculator calculator = new ShippingCalculator();

        Package pkg = new Package(10.0, 12, 8, 6, "10001", "90210");

        // Single carrier quote
        calculator.setStrategy(new FedExStrategy());
        System.out.println(calculator.calculate(pkg));

        // Compare all carriers
        System.out.println("\n--- All Quotes (sorted by price) ---");
        calculator.getAllQuotes(pkg).forEach(System.out::println);

        // Auto-select cheapest
        System.out.println("\nCheapest: " + calculator.findCheapest(pkg));
    }
}
```

### Interview Talking Points

- **Why not a single method with carrier enum?** Each carrier has fundamentally different pricing logic (flat rate vs weight-based vs dimensional). A switch statement with 50+ lines per case is unmaintainable.
- **Real-world:** E-commerce platforms (Shopify, WooCommerce) use this pattern to integrate multiple shipping APIs behind a common interface.
- **Extension:** Add `ShippingStrategy.getTrackingUrl(trackingNumber)` to make the strategy responsible for carrier-specific tracking as well.
