**37.** Design a Vending Machine using the State pattern. States: Idle, HasMoney, Dispensing, OutOfStock with transitions.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Vending Machine — State Pattern

**Pattern:** State (Behavioral)
**Why State?** Vending machine behavior changes completely based on current state. State pattern eliminates complex if-else chains and makes transitions explicit.

**SOLID Principles Applied:**
- **SRP:** Each state handles its own logic; machine manages inventory and balance
- **OCP:** Add new states (Maintenance, RefundInProgress) as new classes
- **LSP:** All states implement the same interface — machine treats them uniformly

### Text UML Diagram

```
┌─────────────────┐      ┌─────────────────────┐
│ VendingMachine   │─────→│   <<interface>>      │
│─────────────────│      │  VendingMachineState │
│ -currentState   │      │─────────────────────│
│ -balance        │      │ +insertMoney()       │
│ -inventory      │      │ +selectProduct()     │
│ +setState()     │      │ +dispense()          │
│ +insertMoney()  │      │ +refund()            │
│ +selectProduct()│      └────────┬────────────┘
│ +dispense()     │               │ implements
│ +refund()       │      ┌────────┼────────┬──────────┐
└─────────────────┘      │        │        │          │
                       Idle    HasMoney Dispensing OutOfStock

State Transitions:
  Idle ──insertMoney──→ HasMoney
  HasMoney ──selectProduct──→ Dispensing (if enough money + in stock)
  HasMoney ──refund──→ Idle
  Dispensing ──dispense──→ Idle (if stock remains) or OutOfStock
  OutOfStock ──refund──→ Idle (return money)
  OutOfStock ──restock──→ Idle
```

### Key Design Decisions

1. **States hold reference to machine** — States call `machine.setState()` to transition
2. **Invalid actions print warning** — Don't throw exceptions for user errors (inserting money when dispensing)
3. **Balance tracked on machine** — Shared across states, not duplicated
4. **Product inventory on machine** — States query machine for stock, don't manage it

### Java Implementation

```java
import java.util.*;

// ============================================================
// PRODUCT
// ============================================================

class Product {
    private final String name;
    private final double price;
    private int quantity;

    public Product(String name, double price, int quantity) {
        this.name = name;
        this.price = price;
        this.quantity = quantity;
    }

    public String getName() { return name; }
    public double getPrice() { return price; }
    public int getQuantity() { return quantity; }
    public void decrementQuantity() { quantity--; }
    public void addQuantity(int amount) { quantity += amount; }
    public boolean isAvailable() { return quantity > 0; }
}

// ============================================================
// STATE INTERFACE
// ============================================================

interface VendingMachineState {
    void insertMoney(double amount);
    void selectProduct(String productCode);
    void dispense();
    void refund();
    String getStateName();
}

// ============================================================
// VENDING MACHINE (CONTEXT)
// ============================================================

class VendingMachine {
    private VendingMachineState currentState;
    private double balance;
    private String selectedProduct;
    private final Map<String, Product> inventory = new LinkedHashMap<>();

    // States (shared instances to avoid creating new objects on each transition)
    final VendingMachineState idleState;
    final VendingMachineState hasMoneyState;
    final VendingMachineState dispensingState;
    final VendingMachineState outOfStockState;

    public VendingMachine() {
        this.idleState = new IdleState(this);
        this.hasMoneyState = new HasMoneyState(this);
        this.dispensingState = new DispensingState(this);
        this.outOfStockState = new OutOfStockState(this);
        this.currentState = idleState;
        this.balance = 0;
    }

    // Delegate to current state
    public void insertMoney(double amount) { currentState.insertMoney(amount); }
    public void selectProduct(String code) { currentState.selectProduct(code); }
    public void dispense() { currentState.dispense(); }
    public void refund() { currentState.refund(); }

    // State management
    public void setState(VendingMachineState state) {
        System.out.println("  State: " + currentState.getStateName() + " -> " + state.getStateName());
        this.currentState = state;
    }

    // Balance management
    public double getBalance() { return balance; }
    public void addBalance(double amount) { balance += amount; }
    public void resetBalance() { balance = 0; }
    public void deductBalance(double amount) { balance -= amount; }

    // Product management
    public void addProduct(String code, Product product) { inventory.put(code, product); }
    public Product getProduct(String code) { return inventory.get(code); }
    public void setSelectedProduct(String code) { selectedProduct = code; }
    public String getSelectedProduct() { return selectedProduct; }

    public boolean hasAnyStock() {
        return inventory.values().stream().anyMatch(Product::isAvailable);
    }

    public void displayProducts() {
        System.out.println("\n--- Products ---");
        inventory.forEach((code, p) ->
                System.out.printf("  %s: %s - $%.2f (%d left)%n",
                        code, p.getName(), p.getPrice(), p.getQuantity()));
        System.out.printf("  Balance: $%.2f | State: %s%n", balance, currentState.getStateName());
    }
}

// ============================================================
// CONCRETE STATES
// ============================================================

class IdleState implements VendingMachineState {
    private final VendingMachine machine;

    public IdleState(VendingMachine machine) { this.machine = machine; }

    @Override
    public void insertMoney(double amount) {
        if (amount <= 0) {
            System.out.println("Invalid amount");
            return;
        }
        machine.addBalance(amount);
        System.out.printf("Inserted $%.2f. Balance: $%.2f%n", amount, machine.getBalance());
        machine.setState(machine.hasMoneyState);
    }

    @Override
    public void selectProduct(String productCode) {
        System.out.println("Please insert money first");
    }

    @Override
    public void dispense() {
        System.out.println("Please insert money and select a product");
    }

    @Override
    public void refund() {
        System.out.println("No money to refund");
    }

    @Override
    public String getStateName() { return "IDLE"; }
}

class HasMoneyState implements VendingMachineState {
    private final VendingMachine machine;

    public HasMoneyState(VendingMachine machine) { this.machine = machine; }

    @Override
    public void insertMoney(double amount) {
        machine.addBalance(amount);
        System.out.printf("Added $%.2f. Balance: $%.2f%n", amount, machine.getBalance());
    }

    @Override
    public void selectProduct(String productCode) {
        Product product = machine.getProduct(productCode);
        if (product == null) {
            System.out.println("Invalid product code: " + productCode);
            return;
        }
        if (!product.isAvailable()) {
            System.out.println(product.getName() + " is out of stock");
            if (!machine.hasAnyStock()) {
                machine.setState(machine.outOfStockState);
            }
            return;
        }
        if (machine.getBalance() < product.getPrice()) {
            System.out.printf("Insufficient funds. Need $%.2f more%n",
                    product.getPrice() - machine.getBalance());
            return;
        }
        machine.setSelectedProduct(productCode);
        machine.setState(machine.dispensingState);
        machine.dispense(); // auto-dispense after selection
    }

    @Override
    public void dispense() {
        System.out.println("Please select a product first");
    }

    @Override
    public void refund() {
        System.out.printf("Refunding $%.2f%n", machine.getBalance());
        machine.resetBalance();
        machine.setState(machine.idleState);
    }

    @Override
    public String getStateName() { return "HAS_MONEY"; }
}

class DispensingState implements VendingMachineState {
    private final VendingMachine machine;

    public DispensingState(VendingMachine machine) { this.machine = machine; }

    @Override
    public void insertMoney(double amount) {
        System.out.println("Please wait, dispensing in progress");
    }

    @Override
    public void selectProduct(String productCode) {
        System.out.println("Please wait, dispensing in progress");
    }

    @Override
    public void dispense() {
        String code = machine.getSelectedProduct();
        Product product = machine.getProduct(code);
        product.decrementQuantity();
        machine.deductBalance(product.getPrice());
        System.out.printf("Dispensing %s! Remaining balance: $%.2f%n",
                product.getName(), machine.getBalance());

        // Return change if any
        if (machine.getBalance() > 0) {
            System.out.printf("Returning change: $%.2f%n", machine.getBalance());
            machine.resetBalance();
        }

        // Transition to appropriate state
        if (!machine.hasAnyStock()) {
            machine.setState(machine.outOfStockState);
        } else {
            machine.setState(machine.idleState);
        }
    }

    @Override
    public void refund() {
        System.out.println("Cannot refund during dispensing");
    }

    @Override
    public String getStateName() { return "DISPENSING"; }
}

class OutOfStockState implements VendingMachineState {
    private final VendingMachine machine;

    public OutOfStockState(VendingMachine machine) { this.machine = machine; }

    @Override
    public void insertMoney(double amount) {
        System.out.println("Machine is out of stock. Cannot accept money.");
    }

    @Override
    public void selectProduct(String productCode) {
        System.out.println("Machine is out of stock.");
    }

    @Override
    public void dispense() {
        System.out.println("Machine is out of stock.");
    }

    @Override
    public void refund() {
        if (machine.getBalance() > 0) {
            System.out.printf("Refunding $%.2f%n", machine.getBalance());
            machine.resetBalance();
        }
        machine.setState(machine.idleState);
    }

    @Override
    public String getStateName() { return "OUT_OF_STOCK"; }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class VendingMachineDemo {
    public static void main(String[] args) {
        VendingMachine machine = new VendingMachine();
        machine.addProduct("A1", new Product("Cola", 1.50, 2));
        machine.addProduct("A2", new Product("Chips", 2.00, 1));
        machine.addProduct("B1", new Product("Water", 1.00, 1));

        machine.displayProducts();

        // Normal purchase
        machine.insertMoney(2.00);
        machine.selectProduct("A1"); // Cola $1.50, get $0.50 change

        // Insufficient funds
        machine.insertMoney(1.00);
        machine.selectProduct("A2"); // Chips $2.00, need $1.00 more
        machine.insertMoney(1.00);
        machine.selectProduct("A2"); // Now enough

        // Buy until out of stock
        machine.insertMoney(1.00);
        machine.selectProduct("B1"); // Last water

        machine.displayProducts();

        // Buy last cola
        machine.insertMoney(1.50);
        machine.selectProduct("A1"); // last item -> OUT_OF_STOCK

        // Try when out of stock
        machine.insertMoney(1.00); // rejected
    }
}
```

### Interview Talking Points

- **Why State over switch/enum?** With 4 states x 4 actions = 16 combinations. Switch statement becomes a 100+ line mess. State pattern gives each state its own class with clear, testable behavior.
- **Shared state instances:** States are created once and reused (Flyweight-like). No garbage collection pressure from creating new state objects on each transition.
- **Real-world:** Java's `Thread` states (NEW, RUNNABLE, BLOCKED, etc.) and Spring StateMachine library follow this exact pattern. Also common in game development and workflow engines.
