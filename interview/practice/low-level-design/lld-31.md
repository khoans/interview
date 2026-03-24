**31.** Design a Stock Price Alert System using the Observer pattern. Support subscribe/unsubscribe to stock updates with different notification methods.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Stock Price Alert System — Observer Pattern

**Pattern:** Observer (Behavioral)
**Why Observer?** Multiple investors/systems need to react to stock price changes independently. Decouples the price feed from notification logic.

**SOLID Principles Applied:**
- **SRP:** StockExchange manages prices; each observer handles its own notification channel
- **OCP:** Add new alert types (Slack, webhook) without changing StockExchange
- **ISP:** Observers only implement the notification interface they need

### Text UML Diagram

```
┌──────────────────┐       ┌──────────────────┐
│  StockExchange   │──────→│  <<interface>>    │
│  (Subject)       │ 1..*  │  StockObserver    │
│──────────────────│       │──────────────────│
│ -observers       │       │ +onPriceChange()  │
│ -stockPrices     │       │ +getObserverId()  │
│ +subscribe()     │       └────────┬─────────┘
│ +unsubscribe()   │                │ implements
│ +updatePrice()   │         ┌──────┼──────┐
│ +notifyAll()     │         │      │      │
└──────────────────┘     Email   SMS   Dashboard
                         Alert  Alert   Alert

┌──────────────────┐
│  AlertCondition   │ ← Configurable threshold per observer
│──────────────────│
│ +symbol           │
│ +targetPrice      │
│ +direction (UP/DN)│
└──────────────────┘
```

### Key Design Decisions

1. **Per-symbol subscription** — Observers subscribe to specific stock symbols, not all stocks
2. **Alert conditions** — Observers set thresholds (price above/below target), not just raw updates
3. **Multiple notification channels** — Email, SMS, Dashboard are independent observer implementations
4. **Unsubscribe support** — Clean removal to prevent memory leaks

### Java Implementation

```java
import java.util.*;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.CopyOnWriteArrayList;

// ============================================================
// VALUE OBJECTS
// ============================================================

class StockPrice {
    private final String symbol;
    private final double price;
    private final double previousPrice;
    private final long timestamp;

    public StockPrice(String symbol, double price, double previousPrice) {
        this.symbol = symbol;
        this.price = price;
        this.previousPrice = previousPrice;
        this.timestamp = System.currentTimeMillis();
    }

    public String getSymbol() { return symbol; }
    public double getPrice() { return price; }
    public double getPreviousPrice() { return previousPrice; }
    public long getTimestamp() { return timestamp; }

    public double getChangePercent() {
        if (previousPrice == 0) return 0;
        return ((price - previousPrice) / previousPrice) * 100;
    }
}

enum AlertDirection { UP, DOWN, ANY }

class AlertCondition {
    private final String symbol;
    private final double targetPrice;
    private final AlertDirection direction;

    public AlertCondition(String symbol, double targetPrice, AlertDirection direction) {
        this.symbol = symbol;
        this.targetPrice = targetPrice;
        this.direction = direction;
    }

    public boolean isTriggered(StockPrice update) {
        if (!update.getSymbol().equals(symbol)) return false;
        return switch (direction) {
            case UP -> update.getPrice() >= targetPrice && update.getPreviousPrice() < targetPrice;
            case DOWN -> update.getPrice() <= targetPrice && update.getPreviousPrice() > targetPrice;
            case ANY -> true; // notify on every change
        };
    }

    public String getSymbol() { return symbol; }
    public double getTargetPrice() { return targetPrice; }
    public AlertDirection getDirection() { return direction; }
}

// ============================================================
// OBSERVER INTERFACE
// ============================================================

interface StockObserver {
    void onPriceChange(StockPrice priceUpdate);
    String getObserverId();
}

// ============================================================
// CONCRETE OBSERVERS
// ============================================================

class EmailAlertObserver implements StockObserver {
    private final String email;
    private final List<AlertCondition> conditions = new ArrayList<>();

    public EmailAlertObserver(String email) {
        this.email = email;
    }

    public void addCondition(AlertCondition condition) {
        conditions.add(condition);
    }

    @Override
    public void onPriceChange(StockPrice update) {
        for (AlertCondition condition : conditions) {
            if (condition.isTriggered(update)) {
                System.out.printf("[EMAIL -> %s] ALERT: %s hit $%.2f (target: $%.2f %s) change: %.2f%%%n",
                        email, update.getSymbol(), update.getPrice(),
                        condition.getTargetPrice(), condition.getDirection(),
                        update.getChangePercent());
            }
        }
    }

    @Override
    public String getObserverId() { return "email:" + email; }
}

class SmsAlertObserver implements StockObserver {
    private final String phoneNumber;
    private final List<AlertCondition> conditions = new ArrayList<>();

    public SmsAlertObserver(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }

    public void addCondition(AlertCondition condition) {
        conditions.add(condition);
    }

    @Override
    public void onPriceChange(StockPrice update) {
        for (AlertCondition condition : conditions) {
            if (condition.isTriggered(update)) {
                System.out.printf("[SMS -> %s] %s: $%.2f (%.2f%%)%n",
                        phoneNumber, update.getSymbol(), update.getPrice(),
                        update.getChangePercent());
            }
        }
    }

    @Override
    public String getObserverId() { return "sms:" + phoneNumber; }
}

class DashboardObserver implements StockObserver {
    private final String dashboardId;
    private final Map<String, StockPrice> latestPrices = new HashMap<>();

    public DashboardObserver(String dashboardId) {
        this.dashboardId = dashboardId;
    }

    @Override
    public void onPriceChange(StockPrice update) {
        latestPrices.put(update.getSymbol(), update);
        System.out.printf("[DASHBOARD %s] Updated %s: $%.2f (%.2f%%)%n",
                dashboardId, update.getSymbol(), update.getPrice(),
                update.getChangePercent());
    }

    public Map<String, StockPrice> getLatestPrices() {
        return Collections.unmodifiableMap(latestPrices);
    }

    @Override
    public String getObserverId() { return "dashboard:" + dashboardId; }
}

// ============================================================
// SUBJECT — Stock Exchange with per-symbol subscriptions
// ============================================================

class StockExchange {
    // symbol -> list of observers interested in that symbol
    private final Map<String, List<StockObserver>> observers = new ConcurrentHashMap<>();
    // symbol -> current price
    private final Map<String, Double> stockPrices = new ConcurrentHashMap<>();

    public void subscribe(String symbol, StockObserver observer) {
        observers.computeIfAbsent(symbol, k -> new CopyOnWriteArrayList<>()).add(observer);
        System.out.println(observer.getObserverId() + " subscribed to " + symbol);
    }

    public void unsubscribe(String symbol, StockObserver observer) {
        List<StockObserver> symbolObservers = observers.get(symbol);
        if (symbolObservers != null) {
            symbolObservers.remove(observer);
            System.out.println(observer.getObserverId() + " unsubscribed from " + symbol);
        }
    }

    public void updatePrice(String symbol, double newPrice) {
        double previousPrice = stockPrices.getOrDefault(symbol, 0.0);
        stockPrices.put(symbol, newPrice);

        StockPrice update = new StockPrice(symbol, newPrice, previousPrice);
        notifyObservers(symbol, update);
    }

    private void notifyObservers(String symbol, StockPrice update) {
        List<StockObserver> symbolObservers = observers.get(symbol);
        if (symbolObservers != null) {
            for (StockObserver observer : symbolObservers) {
                try {
                    observer.onPriceChange(update);
                } catch (Exception e) {
                    System.err.println("Error notifying " + observer.getObserverId()
                            + ": " + e.getMessage());
                }
            }
        }
    }

    public double getPrice(String symbol) {
        return stockPrices.getOrDefault(symbol, 0.0);
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class StockAlertDemo {
    public static void main(String[] args) {
        StockExchange exchange = new StockExchange();

        // Create observers with alert conditions
        EmailAlertObserver emailAlert = new EmailAlertObserver("investor@example.com");
        emailAlert.addCondition(new AlertCondition("AAPL", 200.0, AlertDirection.UP));
        emailAlert.addCondition(new AlertCondition("GOOGL", 150.0, AlertDirection.DOWN));

        SmsAlertObserver smsAlert = new SmsAlertObserver("+1-555-0123");
        smsAlert.addCondition(new AlertCondition("AAPL", 200.0, AlertDirection.UP));

        DashboardObserver dashboard = new DashboardObserver("main-board");

        // Subscribe to symbols
        exchange.subscribe("AAPL", emailAlert);
        exchange.subscribe("AAPL", smsAlert);
        exchange.subscribe("AAPL", dashboard);
        exchange.subscribe("GOOGL", emailAlert);
        exchange.subscribe("GOOGL", dashboard);

        // Simulate price updates
        System.out.println("\n--- Price Updates ---");
        exchange.updatePrice("AAPL", 195.00); // below threshold
        exchange.updatePrice("AAPL", 201.50); // triggers UP alert!
        exchange.updatePrice("GOOGL", 160.00); // above threshold
        exchange.updatePrice("GOOGL", 148.00); // triggers DOWN alert!

        // Unsubscribe
        exchange.unsubscribe("AAPL", smsAlert);
        exchange.updatePrice("AAPL", 205.00); // SMS won't fire
    }
}
```

### Interview Talking Points

- **Why CopyOnWriteArrayList?** Prevents `ConcurrentModificationException` when observers modify the list during iteration (e.g., unsubscribe in callback). Trade-off: writes are expensive, reads are cheap — fits this use case.
- **Push vs Pull model:** This uses push (subject sends data). Pull model (observer calls `subject.getState()`) gives observers more control but adds coupling.
- **Real-world:** Spring's `ApplicationEventPublisher`, Kafka consumers, and RxJava all implement Observer/Pub-Sub at different scales.
