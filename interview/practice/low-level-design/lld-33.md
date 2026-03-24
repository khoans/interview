**33.** Design an Event Management System using the Observer pattern. Implement an event bus with publish/subscribe.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Event Management System — Observer Pattern (Event Bus)

**Pattern:** Observer + Mediator (Behavioral)
**Why Event Bus?** Full decoupling — publishers don't know subscribers exist. Central bus routes events by type. Standard in enterprise systems.

**SOLID Principles Applied:**
- **SRP:** Each subscriber handles one concern; bus handles routing
- **OCP:** New event types and subscribers added without modifying bus
- **DIP:** Publishers and subscribers depend on Event abstraction, not each other

### Text UML Diagram

```
Publisher A ──publish──→ ┌──────────────┐ ──notify──→ Subscriber 1
Publisher B ──publish──→ │   EventBus   │ ──notify──→ Subscriber 2
Publisher C ──publish──→ │──────────────│ ──notify──→ Subscriber 3
                         │ -subscribers │
                         │   Map<Type,  │
                         │    List<Fn>> │
                         │ +subscribe() │
                         │ +publish()   │
                         │ +unsubscribe()│
                         └──────────────┘

Events flow: Publisher → EventBus → matching Subscribers (by event type)
```

### Key Design Decisions

1. **Type-safe subscriptions** — Subscribe to `OrderCreatedEvent.class`, not string topics
2. **Generic event handler** — `EventHandler<T>` ensures type safety at subscribe time
3. **Singleton bus (optional)** — Single bus for app-wide events; multiple buses for isolation
4. **Async support** — Option to dispatch events asynchronously via executor
5. **Priority ordering** — Handlers can define execution priority

### Java Implementation

```java
import java.util.*;
import java.util.concurrent.*;
import java.util.function.Consumer;

// ============================================================
// BASE EVENT
// ============================================================

abstract class Event {
    private final long timestamp;
    private final String source;

    protected Event(String source) {
        this.timestamp = System.currentTimeMillis();
        this.source = source;
    }

    public long getTimestamp() { return timestamp; }
    public String getSource() { return source; }
}

// ============================================================
// CONCRETE EVENTS
// ============================================================

class OrderCreatedEvent extends Event {
    private final String orderId;
    private final double amount;

    public OrderCreatedEvent(String source, String orderId, double amount) {
        super(source);
        this.orderId = orderId;
        this.amount = amount;
    }

    public String getOrderId() { return orderId; }
    public double getAmount() { return amount; }
}

class PaymentProcessedEvent extends Event {
    private final String orderId;
    private final boolean success;

    public PaymentProcessedEvent(String source, String orderId, boolean success) {
        super(source);
        this.orderId = orderId;
        this.success = success;
    }

    public String getOrderId() { return orderId; }
    public boolean isSuccess() { return success; }
}

class UserRegisteredEvent extends Event {
    private final String userId;
    private final String email;

    public UserRegisteredEvent(String source, String userId, String email) {
        super(source);
        this.userId = userId;
        this.email = email;
    }

    public String getUserId() { return userId; }
    public String getEmail() { return email; }
}

// ============================================================
// EVENT HANDLER — type-safe functional interface
// ============================================================

@FunctionalInterface
interface EventHandler<T extends Event> {
    void handle(T event);
}

// ============================================================
// SUBSCRIPTION — wraps handler with metadata
// ============================================================

class Subscription<T extends Event> {
    private final String id;
    private final EventHandler<T> handler;
    private final int priority; // lower = higher priority

    public Subscription(String id, EventHandler<T> handler, int priority) {
        this.id = id;
        this.handler = handler;
        this.priority = priority;
    }

    public String getId() { return id; }
    public EventHandler<T> getHandler() { return handler; }
    public int getPriority() { return priority; }
}

// ============================================================
// EVENT BUS — central pub/sub dispatcher
// ============================================================

class EventBus {
    private final Map<Class<? extends Event>, List<Subscription<?>>> subscribers =
            new ConcurrentHashMap<>();
    private final ExecutorService asyncExecutor;
    private final boolean asyncMode;

    public EventBus() {
        this(false);
    }

    public EventBus(boolean asyncMode) {
        this.asyncMode = asyncMode;
        this.asyncExecutor = asyncMode
                ? Executors.newFixedThreadPool(4)
                : null;
    }

    /**
     * Subscribe to an event type with a handler.
     * Returns subscription ID for unsubscribing.
     */
    public <T extends Event> String subscribe(Class<T> eventType,
                                               EventHandler<T> handler) {
        return subscribe(eventType, handler, 100); // default priority
    }

    public <T extends Event> String subscribe(Class<T> eventType,
                                               EventHandler<T> handler,
                                               int priority) {
        String subscriptionId = UUID.randomUUID().toString().substring(0, 8);
        Subscription<T> subscription = new Subscription<>(subscriptionId, handler, priority);

        subscribers.computeIfAbsent(eventType, k -> new CopyOnWriteArrayList<>())
                .add(subscription);

        // Sort by priority after adding
        subscribers.get(eventType).sort(
                Comparator.comparingInt(s -> ((Subscription<?>) s).getPriority()));

        return subscriptionId;
    }

    /**
     * Unsubscribe by subscription ID.
     */
    public void unsubscribe(String subscriptionId) {
        for (List<Subscription<?>> subs : subscribers.values()) {
            subs.removeIf(s -> s.getId().equals(subscriptionId));
        }
    }

    /**
     * Publish an event to all matching subscribers.
     */
    @SuppressWarnings("unchecked")
    public <T extends Event> void publish(T event) {
        List<Subscription<?>> subs = subscribers.get(event.getClass());
        if (subs == null || subs.isEmpty()) return;

        for (Subscription<?> sub : subs) {
            Subscription<T> typedSub = (Subscription<T>) sub;
            if (asyncMode && asyncExecutor != null) {
                asyncExecutor.submit(() -> safeHandle(typedSub, event));
            } else {
                safeHandle(typedSub, event);
            }
        }
    }

    private <T extends Event> void safeHandle(Subscription<T> sub, T event) {
        try {
            sub.getHandler().handle(event);
        } catch (Exception e) {
            System.err.println("Error in handler " + sub.getId() + ": " + e.getMessage());
        }
    }

    public void shutdown() {
        if (asyncExecutor != null) {
            asyncExecutor.shutdown();
        }
    }
}

// ============================================================
// SUBSCRIBER SERVICES — business logic reacting to events
// ============================================================

class InventoryService {
    public void registerHandlers(EventBus bus) {
        bus.subscribe(OrderCreatedEvent.class, event -> {
            System.out.printf("[Inventory] Reserving stock for order %s%n", event.getOrderId());
        }, 10); // high priority — reserve stock first
    }
}

class NotificationService {
    public void registerHandlers(EventBus bus) {
        bus.subscribe(OrderCreatedEvent.class, event -> {
            System.out.printf("[Notification] Order confirmation email for order %s ($%.2f)%n",
                    event.getOrderId(), event.getAmount());
        }, 50); // lower priority — notify after stock reserved

        bus.subscribe(PaymentProcessedEvent.class, event -> {
            String status = event.isSuccess() ? "successful" : "failed";
            System.out.printf("[Notification] Payment %s for order %s%n",
                    status, event.getOrderId());
        });

        bus.subscribe(UserRegisteredEvent.class, event -> {
            System.out.printf("[Notification] Welcome email to %s%n", event.getEmail());
        });
    }
}

class AnalyticsService {
    public void registerHandlers(EventBus bus) {
        bus.subscribe(OrderCreatedEvent.class, event -> {
            System.out.printf("[Analytics] Tracking order %s, amount=$%.2f%n",
                    event.getOrderId(), event.getAmount());
        });

        bus.subscribe(UserRegisteredEvent.class, event -> {
            System.out.printf("[Analytics] New user registered: %s%n", event.getUserId());
        });
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class EventBusDemo {
    public static void main(String[] args) {
        EventBus bus = new EventBus(); // sync mode

        // Register service handlers
        new InventoryService().registerHandlers(bus);
        new NotificationService().registerHandlers(bus);
        new AnalyticsService().registerHandlers(bus);

        // Publish events — services react independently
        System.out.println("=== Order Created ===");
        bus.publish(new OrderCreatedEvent("OrderController", "ORD-001", 150.00));

        System.out.println("\n=== Payment Processed ===");
        bus.publish(new PaymentProcessedEvent("PaymentGateway", "ORD-001", true));

        System.out.println("\n=== User Registered ===");
        bus.publish(new UserRegisteredEvent("AuthService", "USR-042", "alice@example.com"));

        bus.shutdown();
    }
}
```

### Interview Talking Points

- **Event Bus vs Direct Observer:** Observer requires subject to know observer interface. Event Bus fully decouples — publishers don't import subscriber classes at all.
- **Real-world:** Spring's `ApplicationEventPublisher`, Google Guava EventBus, Vert.x EventBus all follow this pattern. Kafka takes it further with persistent, distributed event streaming.
- **Trade-off:** Full decoupling makes debugging harder — hard to trace "who handles this event?" Use logging and event tracing (correlation IDs) in production.
- **Async consideration:** Sync bus is simpler to debug; async bus prevents slow handlers from blocking publishers. Choose based on latency requirements.
