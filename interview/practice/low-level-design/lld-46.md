**46.** Design a Coffee Machine System using mixed patterns. Factory (coffee types) + Decorator (add-ons) + State (machine states) + Observer (inventory alerts).

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Coffee Machine System — Mixed Patterns

**Patterns:** Factory + Decorator + State + Observer
**Why mixed?** Real systems are never single-pattern. Factory creates base coffees, Decorator adds customizations, State manages machine lifecycle, Observer monitors inventory.

### Text UML Diagram

```
┌──────────────────┐    ┌─────────────────┐    ┌────────────────┐
│  CoffeeFactory   │───→│ <<interface>>    │←───│  <<abstract>>  │
│  (creates base)  │    │    Coffee        │    │ CoffeeDecorator│
│──────────────────│    │─────────────────│    │ (add-ons)      │
│ +create(type)    │    │ +getDescription()│    └────────────────┘
└──────────────────┘    │ +getCost()       │         │
                        └─────────────────┘    Milk, Whip, Syrup

┌──────────────────┐    ┌──────────────────┐
│  CoffeeMachine   │───→│ <<interface>>     │
│  (context)       │    │  MachineState    │
│──────────────────│    │──────────────────│
│ -state           │    │ +selectCoffee()  │
│ -inventory       │    │ +brew()          │
│ +selectCoffee()  │    │ +dispense()      │
│ +brew()          │    └────────┬─────────┘
│ +dispense()      │        Idle│Ready│Brewing│OutOfStock
└──────────────────┘

┌──────────────────┐    ┌──────────────────┐
│   Inventory      │───→│ <<interface>>     │
│  (subject)       │    │ InventoryObserver│
│──────────────────│    │──────────────────│
│ -ingredients     │    │ +onLowStock()    │
│ +use()           │    │ +onOutOfStock()  │
│ +addObserver()   │    └──────────────────┘
└──────────────────┘
```

### Key Design Decisions

1. **Factory** creates base coffee (Espresso, Latte, Americano) — separates creation from use
2. **Decorator** adds milk, whip, syrup — stackable add-ons without class explosion
3. **State** manages machine lifecycle — prevents brewing when out of stock
4. **Observer** alerts staff when ingredients run low — proactive restocking

### Java Implementation

```java
import java.util.*;
import java.util.concurrent.CopyOnWriteArrayList;

// ============================================================
// COFFEE INTERFACE + CONCRETE COFFEES (Factory products)
// ============================================================

interface Coffee {
    String getDescription();
    double getCost();
}

class Espresso implements Coffee {
    @Override public String getDescription() { return "Espresso"; }
    @Override public double getCost() { return 2.50; }
}

class Latte implements Coffee {
    @Override public String getDescription() { return "Latte"; }
    @Override public double getCost() { return 3.50; }
}

class Americano implements Coffee {
    @Override public String getDescription() { return "Americano"; }
    @Override public double getCost() { return 3.00; }
}

// ============================================================
// FACTORY — creates base coffees
// ============================================================

class CoffeeFactory {
    public Coffee create(String type) {
        return switch (type.toLowerCase()) {
            case "espresso" -> new Espresso();
            case "latte" -> new Latte();
            case "americano" -> new Americano();
            default -> throw new IllegalArgumentException("Unknown coffee: " + type);
        };
    }
}

// ============================================================
// DECORATORS — stackable add-ons
// ============================================================

abstract class CoffeeDecorator implements Coffee {
    protected final Coffee wrapped;
    protected CoffeeDecorator(Coffee wrapped) { this.wrapped = wrapped; }
}

class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee coffee) { super(coffee); }
    @Override public String getDescription() { return wrapped.getDescription() + " + Milk"; }
    @Override public double getCost() { return wrapped.getCost() + 0.50; }
}

class WhipCreamDecorator extends CoffeeDecorator {
    public WhipCreamDecorator(Coffee coffee) { super(coffee); }
    @Override public String getDescription() { return wrapped.getDescription() + " + Whip Cream"; }
    @Override public double getCost() { return wrapped.getCost() + 0.75; }
}

class SyrupDecorator extends CoffeeDecorator {
    private final String flavor;
    public SyrupDecorator(Coffee coffee, String flavor) {
        super(coffee);
        this.flavor = flavor;
    }
    @Override public String getDescription() { return wrapped.getDescription() + " + " + flavor + " Syrup"; }
    @Override public double getCost() { return wrapped.getCost() + 0.60; }
}

// ============================================================
// OBSERVER — inventory alerts
// ============================================================

interface InventoryObserver {
    void onLowStock(String ingredient, int remaining);
    void onOutOfStock(String ingredient);
}

class StaffAlertObserver implements InventoryObserver {
    @Override
    public void onLowStock(String ingredient, int remaining) {
        System.out.printf("[ALERT] %s is LOW: %d remaining. Restock soon!%n", ingredient, remaining);
    }
    @Override
    public void onOutOfStock(String ingredient) {
        System.out.printf("[ALERT] %s is OUT OF STOCK! Machine disabled.%n", ingredient);
    }
}

class ManagerDashboardObserver implements InventoryObserver {
    @Override
    public void onLowStock(String ingredient, int remaining) {
        System.out.printf("[DASHBOARD] Low stock alert: %s (%d)%n", ingredient, remaining);
    }
    @Override
    public void onOutOfStock(String ingredient) {
        System.out.printf("[DASHBOARD] OUT OF STOCK: %s%n", ingredient);
    }
}

// ============================================================
// INVENTORY — subject that notifies observers
// ============================================================

class Inventory {
    private final Map<String, Integer> stock = new HashMap<>();
    private final List<InventoryObserver> observers = new CopyOnWriteArrayList<>();
    private static final int LOW_THRESHOLD = 3;

    public void addObserver(InventoryObserver observer) { observers.add(observer); }

    public void restock(String ingredient, int amount) {
        stock.merge(ingredient, amount, Integer::sum);
    }

    public boolean hasIngredient(String ingredient, int amount) {
        return stock.getOrDefault(ingredient, 0) >= amount;
    }

    public boolean use(String ingredient, int amount) {
        int current = stock.getOrDefault(ingredient, 0);
        if (current < amount) {
            observers.forEach(o -> o.onOutOfStock(ingredient));
            return false;
        }
        stock.put(ingredient, current - amount);
        int remaining = stock.get(ingredient);
        if (remaining <= 0) {
            observers.forEach(o -> o.onOutOfStock(ingredient));
        } else if (remaining <= LOW_THRESHOLD) {
            observers.forEach(o -> o.onLowStock(ingredient, remaining));
        }
        return true;
    }

    public boolean canMake(String coffeeType) {
        return switch (coffeeType.toLowerCase()) {
            case "espresso" -> hasIngredient("coffee_beans", 1) && hasIngredient("water", 1);
            case "latte" -> hasIngredient("coffee_beans", 1) && hasIngredient("water", 1) && hasIngredient("milk", 1);
            case "americano" -> hasIngredient("coffee_beans", 1) && hasIngredient("water", 2);
            default -> false;
        };
    }

    public void consumeFor(String coffeeType) {
        switch (coffeeType.toLowerCase()) {
            case "espresso" -> { use("coffee_beans", 1); use("water", 1); }
            case "latte" -> { use("coffee_beans", 1); use("water", 1); use("milk", 1); }
            case "americano" -> { use("coffee_beans", 1); use("water", 2); }
        }
    }
}

// ============================================================
// STATE — machine states
// ============================================================

interface MachineState {
    void selectCoffee(CoffeeMachine machine, String type);
    void brew(CoffeeMachine machine);
    void dispense(CoffeeMachine machine);
    String getStateName();
}

class IdleMachineState implements MachineState {
    @Override
    public void selectCoffee(CoffeeMachine machine, String type) {
        if (!machine.getInventory().canMake(type)) {
            System.out.println("Cannot make " + type + " — out of ingredients");
            machine.setState(new OutOfStockState());
            return;
        }
        machine.setSelectedType(type);
        System.out.println("Selected: " + type);
        machine.setState(new ReadyState());
    }

    @Override public void brew(CoffeeMachine m) { System.out.println("Select a coffee first"); }
    @Override public void dispense(CoffeeMachine m) { System.out.println("Select a coffee first"); }
    @Override public String getStateName() { return "IDLE"; }
}

class ReadyState implements MachineState {
    @Override public void selectCoffee(CoffeeMachine m, String t) { System.out.println("Already selected. Brew first."); }

    @Override
    public void brew(CoffeeMachine machine) {
        System.out.println("Brewing " + machine.getSelectedType() + "...");
        machine.getInventory().consumeFor(machine.getSelectedType());
        machine.setState(new BrewingState());
        // Auto-dispense after brewing
        machine.dispense();
    }

    @Override public void dispense(CoffeeMachine m) { System.out.println("Brew first"); }
    @Override public String getStateName() { return "READY"; }
}

class BrewingState implements MachineState {
    @Override public void selectCoffee(CoffeeMachine m, String t) { System.out.println("Brewing in progress..."); }
    @Override public void brew(CoffeeMachine m) { System.out.println("Already brewing"); }

    @Override
    public void dispense(CoffeeMachine machine) {
        Coffee coffee = machine.getFactory().create(machine.getSelectedType());
        // Apply decorators
        for (String addon : machine.getAddons()) {
            coffee = switch (addon.toLowerCase()) {
                case "milk" -> new MilkDecorator(coffee);
                case "whip" -> new WhipCreamDecorator(coffee);
                default -> new SyrupDecorator(coffee, addon);
            };
        }
        System.out.printf("Dispensing: %s — $%.2f%n", coffee.getDescription(), coffee.getCost());
        machine.clearOrder();
        machine.setState(new IdleMachineState());
    }

    @Override public String getStateName() { return "BREWING"; }
}

class OutOfStockState implements MachineState {
    @Override public void selectCoffee(CoffeeMachine m, String t) { System.out.println("Machine needs restocking"); }
    @Override public void brew(CoffeeMachine m) { System.out.println("Machine needs restocking"); }
    @Override public void dispense(CoffeeMachine m) { System.out.println("Machine needs restocking"); }
    @Override public String getStateName() { return "OUT_OF_STOCK"; }
}

// ============================================================
// COFFEE MACHINE — ties everything together
// ============================================================

class CoffeeMachine {
    private MachineState state;
    private final Inventory inventory;
    private final CoffeeFactory factory;
    private String selectedType;
    private final List<String> addons = new ArrayList<>();

    public CoffeeMachine() {
        this.state = new IdleMachineState();
        this.inventory = new Inventory();
        this.factory = new CoffeeFactory();
    }

    public void selectCoffee(String type) { state.selectCoffee(this, type); }
    public void addAddon(String addon) { addons.add(addon); }
    public void brew() { state.brew(this); }
    public void dispense() { state.dispense(this); }

    public void setState(MachineState state) { this.state = state; }
    public Inventory getInventory() { return inventory; }
    public CoffeeFactory getFactory() { return factory; }
    public String getSelectedType() { return selectedType; }
    public void setSelectedType(String type) { this.selectedType = type; }
    public List<String> getAddons() { return addons; }
    public void clearOrder() { selectedType = null; addons.clear(); }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class CoffeeMachineDemo {
    public static void main(String[] args) {
        CoffeeMachine machine = new CoffeeMachine();

        // Setup inventory observers
        machine.getInventory().addObserver(new StaffAlertObserver());
        machine.getInventory().addObserver(new ManagerDashboardObserver());

        // Stock ingredients
        machine.getInventory().restock("coffee_beans", 5);
        machine.getInventory().restock("water", 10);
        machine.getInventory().restock("milk", 3);

        // Order 1: Latte + Whip
        System.out.println("\n=== Order 1 ===");
        machine.selectCoffee("latte");
        machine.addAddon("milk");
        machine.addAddon("whip");
        machine.brew();

        // Order 2: Espresso with vanilla syrup
        System.out.println("\n=== Order 2 ===");
        machine.selectCoffee("espresso");
        machine.addAddon("Vanilla");
        machine.brew();

        // Order 3: Latte (will trigger low stock)
        System.out.println("\n=== Order 3 ===");
        machine.selectCoffee("latte");
        machine.brew();

        // Order 4: Latte (will fail — out of milk)
        System.out.println("\n=== Order 4 ===");
        machine.selectCoffee("latte");
    }
}
```

### Interview Talking Points

- **Pattern synergy:** Factory creates, Decorator customizes, State controls flow, Observer reacts. Each pattern has a clear, non-overlapping responsibility.
- **Real-world mapping:** Starbucks POS: Factory=menu item creation, Decorator=customizations, State=order lifecycle, Observer=inventory management system.
- **Why not one big class?** A monolithic `CoffeeMachine` with all logic would be 500+ lines. Patterns split it into ~10 focused classes, each under 50 lines.
