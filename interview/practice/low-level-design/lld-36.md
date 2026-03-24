**36.** Design an Order Processing Queue using the Command pattern. Queue, execute, cancel, and log restaurant orders.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Order Processing Queue — Command Pattern

**Pattern:** Command (Behavioral)
**Why Command?** Restaurant orders are queued, executed in order, can be cancelled before preparation, and must be logged. Command objects naturally support all of these.

**SOLID Principles Applied:**
- **SRP:** Commands encapsulate orders; Queue manages execution order; Kitchen handles preparation
- **OCP:** New order types (delivery, catering) without modifying queue
- **DIP:** Queue processes Command interface, not specific order types

### Text UML Diagram

```
┌───────────────────┐     ┌──────────────────┐     ┌────────────┐
│   OrderQueue      │────→│  <<interface>>    │────→│  Kitchen   │
│───────────────────│     │   OrderCommand    │     │ (Receiver) │
│ -pendingQueue     │     │──────────────────│     └────────────┘
│ -executedLog      │     │ +execute()        │
│ -cancelledLog     │     │ +cancel()         │
│ +submit()         │     │ +getOrderId()     │
│ +processNext()    │     │ +getStatus()      │
│ +cancelOrder()    │     └────────┬─────────┘
│ +processAll()     │              │ implements
└───────────────────┘     ┌────────┼─────────┐
                       DineIn   Takeout   Delivery
                       Order    Order     Order
```

### Key Design Decisions

1. **Order states** — QUEUED -> PREPARING -> COMPLETED or CANCELLED. Only QUEUED orders can be cancelled.
2. **FIFO queue** — Orders processed first-come-first-served with priority override for VIP
3. **Command logging** — Every state transition logged with timestamp for audit
4. **Cancel with reason** — Cancelled orders track who cancelled and why

### Java Implementation

```java
import java.util.*;
import java.util.concurrent.LinkedBlockingQueue;

// ============================================================
// DOMAIN MODELS
// ============================================================

enum OrderStatus { QUEUED, PREPARING, COMPLETED, CANCELLED }

class MenuItem {
    private final String name;
    private final double price;
    private final int prepTimeMinutes;

    public MenuItem(String name, double price, int prepTimeMinutes) {
        this.name = name;
        this.price = price;
        this.prepTimeMinutes = prepTimeMinutes;
    }

    public String getName() { return name; }
    public double getPrice() { return price; }
    public int getPrepTimeMinutes() { return prepTimeMinutes; }
}

class OrderLog {
    private final String orderId;
    private final OrderStatus status;
    private final long timestamp;
    private final String message;

    public OrderLog(String orderId, OrderStatus status, String message) {
        this.orderId = orderId;
        this.status = status;
        this.timestamp = System.currentTimeMillis();
        this.message = message;
    }

    @Override
    public String toString() {
        return String.format("[%d] Order %s: %s - %s", timestamp, orderId, status, message);
    }
}

// ============================================================
// RECEIVER — Kitchen
// ============================================================

class Kitchen {
    public void prepare(String orderId, List<MenuItem> items) {
        System.out.println("Kitchen: Preparing order " + orderId);
        for (MenuItem item : items) {
            System.out.println("  -> " + item.getName() + " (" + item.getPrepTimeMinutes() + " min)");
        }
    }

    public void cancelPreparation(String orderId) {
        System.out.println("Kitchen: Cancelled preparation for " + orderId);
    }
}

// ============================================================
// COMMAND INTERFACE
// ============================================================

interface OrderCommand {
    void execute();
    void cancel(String reason);
    String getOrderId();
    OrderStatus getStatus();
    double getTotal();
}

// ============================================================
// CONCRETE COMMANDS
// ============================================================

class DineInOrderCommand implements OrderCommand {
    private final String orderId;
    private final int tableNumber;
    private final List<MenuItem> items;
    private final Kitchen kitchen;
    private OrderStatus status;

    public DineInOrderCommand(String orderId, int tableNumber,
                              List<MenuItem> items, Kitchen kitchen) {
        this.orderId = orderId;
        this.tableNumber = tableNumber;
        this.items = new ArrayList<>(items);
        this.kitchen = kitchen;
        this.status = OrderStatus.QUEUED;
    }

    @Override
    public void execute() {
        status = OrderStatus.PREPARING;
        System.out.println("Dine-in order " + orderId + " for table " + tableNumber);
        kitchen.prepare(orderId, items);
        status = OrderStatus.COMPLETED;
    }

    @Override
    public void cancel(String reason) {
        if (status == OrderStatus.QUEUED) {
            status = OrderStatus.CANCELLED;
            System.out.println("Dine-in order " + orderId + " cancelled: " + reason);
        } else {
            throw new IllegalStateException("Cannot cancel order in " + status + " status");
        }
    }

    @Override public String getOrderId() { return orderId; }
    @Override public OrderStatus getStatus() { return status; }
    @Override public double getTotal() {
        return items.stream().mapToDouble(MenuItem::getPrice).sum();
    }
}

class TakeoutOrderCommand implements OrderCommand {
    private final String orderId;
    private final String customerName;
    private final List<MenuItem> items;
    private final Kitchen kitchen;
    private OrderStatus status;

    public TakeoutOrderCommand(String orderId, String customerName,
                               List<MenuItem> items, Kitchen kitchen) {
        this.orderId = orderId;
        this.customerName = customerName;
        this.items = new ArrayList<>(items);
        this.kitchen = kitchen;
        this.status = OrderStatus.QUEUED;
    }

    @Override
    public void execute() {
        status = OrderStatus.PREPARING;
        System.out.println("Takeout order " + orderId + " for " + customerName);
        kitchen.prepare(orderId, items);
        // Package for takeout
        System.out.println("Packaging order " + orderId + " for pickup");
        status = OrderStatus.COMPLETED;
    }

    @Override
    public void cancel(String reason) {
        if (status == OrderStatus.QUEUED) {
            status = OrderStatus.CANCELLED;
            System.out.println("Takeout order " + orderId + " cancelled: " + reason);
        } else {
            throw new IllegalStateException("Cannot cancel order in " + status + " status");
        }
    }

    @Override public String getOrderId() { return orderId; }
    @Override public OrderStatus getStatus() { return status; }
    @Override public double getTotal() {
        return items.stream().mapToDouble(MenuItem::getPrice).sum();
    }
}

class DeliveryOrderCommand implements OrderCommand {
    private final String orderId;
    private final String address;
    private final List<MenuItem> items;
    private final Kitchen kitchen;
    private OrderStatus status;
    private static final double DELIVERY_FEE = 5.99;

    public DeliveryOrderCommand(String orderId, String address,
                                List<MenuItem> items, Kitchen kitchen) {
        this.orderId = orderId;
        this.address = address;
        this.items = new ArrayList<>(items);
        this.kitchen = kitchen;
        this.status = OrderStatus.QUEUED;
    }

    @Override
    public void execute() {
        status = OrderStatus.PREPARING;
        System.out.println("Delivery order " + orderId + " to " + address);
        kitchen.prepare(orderId, items);
        System.out.println("Dispatching delivery for " + orderId);
        status = OrderStatus.COMPLETED;
    }

    @Override
    public void cancel(String reason) {
        if (status == OrderStatus.QUEUED) {
            status = OrderStatus.CANCELLED;
            System.out.println("Delivery order " + orderId + " cancelled: " + reason);
        } else {
            throw new IllegalStateException("Cannot cancel order in " + status + " status");
        }
    }

    @Override public String getOrderId() { return orderId; }
    @Override public OrderStatus getStatus() { return status; }
    @Override public double getTotal() {
        return items.stream().mapToDouble(MenuItem::getPrice).sum() + DELIVERY_FEE;
    }
}

// ============================================================
// ORDER QUEUE — invoker with logging
// ============================================================

class OrderQueue {
    private final Queue<OrderCommand> pendingQueue = new LinkedList<>();
    private final List<OrderCommand> completedOrders = new ArrayList<>();
    private final List<OrderCommand> cancelledOrders = new ArrayList<>();
    private final List<OrderLog> auditLog = new ArrayList<>();

    public void submit(OrderCommand order) {
        pendingQueue.add(order);
        log(order.getOrderId(), OrderStatus.QUEUED, "Order submitted, total: $" + order.getTotal());
        System.out.println("Order " + order.getOrderId() + " queued. Queue size: " + pendingQueue.size());
    }

    public void processNext() {
        OrderCommand order = pendingQueue.poll();
        if (order == null) {
            System.out.println("No orders in queue");
            return;
        }
        if (order.getStatus() == OrderStatus.CANCELLED) {
            System.out.println("Skipping cancelled order " + order.getOrderId());
            return;
        }
        log(order.getOrderId(), OrderStatus.PREPARING, "Processing started");
        order.execute();
        completedOrders.add(order);
        log(order.getOrderId(), OrderStatus.COMPLETED, "Order completed");
    }

    public void processAll() {
        while (!pendingQueue.isEmpty()) {
            processNext();
        }
    }

    public boolean cancelOrder(String orderId, String reason) {
        for (OrderCommand order : pendingQueue) {
            if (order.getOrderId().equals(orderId) && order.getStatus() == OrderStatus.QUEUED) {
                order.cancel(reason);
                cancelledOrders.add(order);
                log(orderId, OrderStatus.CANCELLED, reason);
                return true;
            }
        }
        System.out.println("Order " + orderId + " not found or cannot be cancelled");
        return false;
    }

    public void printAuditLog() {
        System.out.println("\n--- Audit Log ---");
        auditLog.forEach(System.out::println);
    }

    public void printSummary() {
        System.out.println("\n--- Order Summary ---");
        System.out.println("Pending: " + pendingQueue.size());
        System.out.println("Completed: " + completedOrders.size());
        System.out.println("Cancelled: " + cancelledOrders.size());
        double totalRevenue = completedOrders.stream().mapToDouble(OrderCommand::getTotal).sum();
        System.out.printf("Total Revenue: $%.2f%n", totalRevenue);
    }

    private void log(String orderId, OrderStatus status, String message) {
        auditLog.add(new OrderLog(orderId, status, message));
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class OrderQueueDemo {
    public static void main(String[] args) {
        Kitchen kitchen = new Kitchen();
        OrderQueue queue = new OrderQueue();

        // Menu items
        MenuItem burger = new MenuItem("Burger", 12.99, 10);
        MenuItem fries = new MenuItem("Fries", 4.99, 5);
        MenuItem pizza = new MenuItem("Pizza", 15.99, 15);
        MenuItem salad = new MenuItem("Salad", 8.99, 3);

        // Submit orders
        queue.submit(new DineInOrderCommand("ORD-001", 5, List.of(burger, fries), kitchen));
        queue.submit(new TakeoutOrderCommand("ORD-002", "Alice", List.of(pizza), kitchen));
        queue.submit(new DeliveryOrderCommand("ORD-003", "123 Main St", List.of(burger, salad), kitchen));
        queue.submit(new DineInOrderCommand("ORD-004", 3, List.of(fries, salad), kitchen));

        // Cancel an order before processing
        queue.cancelOrder("ORD-002", "Customer changed mind");

        // Process all
        System.out.println("\n=== Processing Orders ===");
        queue.processAll();

        queue.printSummary();
        queue.printAuditLog();
    }
}
```

### Interview Talking Points

- **Queue + Command = job processing:** This pattern is the foundation of message queues (RabbitMQ, SQS). Each message is a serialized command, consumers execute them.
- **Idempotency:** In production, commands should be idempotent — re-executing the same order shouldn't double-charge. Add orderId-based dedup.
- **Why cancel is on Command, not Queue?** Command knows its own state and validation rules. Queue just routes the cancel request. Follows Tell Don't Ask.
