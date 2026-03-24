**23.** E-Commerce Order Processing — Facade pattern. Coordinate Inventory, Payment, Shipping, Notification, Loyalty, Analytics. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## E-Commerce Order Processing — Facade Pattern

### Pattern: Facade

**Why Facade?** Placing an order touches 6 subsystems in a specific sequence: validate inventory, process payment, reserve stock, create shipment, send notification, award loyalty points, track analytics. Without a facade, the controller must orchestrate all 6 — violating SRP and duplicating logic across endpoints.

### Text UML

```
┌──────────────────────────────────────┐
│        OrderFacade                   │
├──────────────────────────────────────┤
│ - inventoryService: InventoryService │
│ - paymentService: PaymentService     │
│ - shippingService: ShippingService   │
│ - notificationService: NotifService  │
│ - loyaltyService: LoyaltyService     │
│ - analyticsService: AnalyticsService │
├──────────────────────────────────────┤
│ + placeOrder(request): OrderResult   │
│ + cancelOrder(orderId): CancelResult │
│ + returnOrder(orderId): ReturnResult │
└──────────────────────────────────────┘
        │
        │ coordinates (in sequence)
        ▼
  ┌───────────┐  ┌──────────┐  ┌──────────┐
  │ Inventory │  │ Payment  │  │ Shipping │
  │ Service   │  │ Service  │  │ Service  │
  └───────────┘  └──────────┘  └──────────┘
  ┌───────────┐  ┌──────────┐  ┌──────────┐
  │Notification│  │ Loyalty  │  │Analytics │
  │ Service   │  │ Service  │  │ Service  │
  └───────────┘  └──────────┘  └──────────┘
```

### Key Design Decisions

1. **Transaction-like orchestration** — If payment fails after inventory reservation, facade compensates (releases reservation)
2. **Non-critical failures are tolerated** — Notification/Analytics failures don't roll back the order
3. **OrderResult encapsulates outcome** — Single return object with order ID, status, tracking, and any warnings
4. **Cancel/Return also coordinated** — Reverse operations need the same multi-service orchestration

### SOLID Principles Applied

- **SRP**: Facade orchestrates; each service handles its domain
- **OCP**: Add new steps (fraud check, tax calculation) by injecting new services
- **DIP**: Facade depends on service interfaces, not implementations
- **Law of Demeter**: Controller talks to OrderFacade only

### Java Code

```java
import java.math.BigDecimal;
import java.time.LocalDate;
import java.util.*;

// ===================== DTOs =====================

public class OrderRequest {
    private final String customerId;
    private final List<OrderItem> items;
    private final String paymentMethodToken;
    private final String shippingAddress;

    public OrderRequest(String customerId, List<OrderItem> items,
                        String paymentMethodToken, String shippingAddress) {
        this.customerId = customerId;
        this.items = items;
        this.paymentMethodToken = paymentMethodToken;
        this.shippingAddress = shippingAddress;
    }

    public String getCustomerId() { return customerId; }
    public List<OrderItem> getItems() { return items; }
    public String getPaymentMethodToken() { return paymentMethodToken; }
    public String getShippingAddress() { return shippingAddress; }

    public BigDecimal getTotalAmount() {
        return items.stream()
            .map(i -> i.getPrice().multiply(BigDecimal.valueOf(i.getQuantity())))
            .reduce(BigDecimal.ZERO, BigDecimal::add);
    }
}

public class OrderItem {
    private final String productId;
    private final int quantity;
    private final BigDecimal price;

    public OrderItem(String productId, int quantity, BigDecimal price) {
        this.productId = productId;
        this.quantity = quantity;
        this.price = price;
    }

    public String getProductId() { return productId; }
    public int getQuantity() { return quantity; }
    public BigDecimal getPrice() { return price; }
}

public class OrderResult {
    private final String orderId;
    private final boolean success;
    private final String trackingNumber;
    private final String message;
    private final List<String> warnings;

    public OrderResult(String orderId, boolean success, String trackingNumber,
                       String message, List<String> warnings) {
        this.orderId = orderId;
        this.success = success;
        this.trackingNumber = trackingNumber;
        this.message = message;
        this.warnings = warnings;
    }

    public String getOrderId() { return orderId; }
    public boolean isSuccess() { return success; }
    public String getTrackingNumber() { return trackingNumber; }
    public String getMessage() { return message; }
    public List<String> getWarnings() { return warnings; }
}

// ===================== Subsystem Services =====================

public class InventoryService {
    public boolean checkAvailability(String productId, int quantity) {
        System.out.println("  [Inventory] Checking stock for " + productId + " qty=" + quantity);
        return true;
    }
    public String reserveStock(String productId, int quantity) {
        System.out.println("  [Inventory] Reserved " + quantity + " of " + productId);
        return "RES-" + UUID.randomUUID().toString().substring(0, 6);
    }
    public void releaseReservation(String reservationId) {
        System.out.println("  [Inventory] Released reservation " + reservationId);
    }
    public void confirmReservation(String reservationId) {
        System.out.println("  [Inventory] Confirmed reservation " + reservationId);
    }
}

public class PaymentService {
    public String processPayment(String customerId, BigDecimal amount, String token) {
        System.out.println("  [Payment] Processing $" + amount + " for customer " + customerId);
        return "TXN-" + UUID.randomUUID().toString().substring(0, 6);
    }
    public void refund(String transactionId, BigDecimal amount) {
        System.out.println("  [Payment] Refunding $" + amount + " for txn " + transactionId);
    }
}

public class ShippingService {
    public String createShipment(String orderId, String address, List<OrderItem> items) {
        System.out.println("  [Shipping] Creating shipment for order " + orderId);
        return "TRACK-" + UUID.randomUUID().toString().substring(0, 8);
    }
    public LocalDate getEstimatedDelivery(String trackingNumber) {
        return LocalDate.now().plusDays(5);
    }
}

public class NotificationService {
    public void sendOrderConfirmation(String customerId, String orderId, String trackingNumber) {
        System.out.println("  [Notification] Order confirmation sent to " + customerId);
    }
    public void sendCancellationNotice(String customerId, String orderId) {
        System.out.println("  [Notification] Cancellation notice sent to " + customerId);
    }
}

public class LoyaltyService {
    public int awardPoints(String customerId, BigDecimal amount) {
        int points = amount.intValue(); // 1 point per dollar
        System.out.println("  [Loyalty] Awarded " + points + " points to " + customerId);
        return points;
    }
    public void revokePoints(String customerId, int points) {
        System.out.println("  [Loyalty] Revoked " + points + " points from " + customerId);
    }
}

public class AnalyticsService {
    public void trackOrderPlaced(String orderId, BigDecimal amount, int itemCount) {
        System.out.println("  [Analytics] Tracked order " + orderId + " amount=$" + amount);
    }
    public void trackOrderCancelled(String orderId) {
        System.out.println("  [Analytics] Tracked cancellation of " + orderId);
    }
}

// ===================== Facade =====================

public class OrderFacade {
    private final InventoryService inventoryService;
    private final PaymentService paymentService;
    private final ShippingService shippingService;
    private final NotificationService notificationService;
    private final LoyaltyService loyaltyService;
    private final AnalyticsService analyticsService;

    public OrderFacade(InventoryService inventory, PaymentService payment,
                       ShippingService shipping, NotificationService notification,
                       LoyaltyService loyalty, AnalyticsService analytics) {
        this.inventoryService = inventory;
        this.paymentService = payment;
        this.shippingService = shipping;
        this.notificationService = notification;
        this.loyaltyService = loyalty;
        this.analyticsService = analytics;
    }

    public OrderResult placeOrder(OrderRequest request) {
        String orderId = "ORD-" + UUID.randomUUID().toString().substring(0, 8);
        List<String> warnings = new ArrayList<>();
        List<String> reservations = new ArrayList<>();

        System.out.println("=== Processing Order " + orderId + " ===");

        try {
            // Step 1: Check & Reserve Inventory
            for (OrderItem item : request.getItems()) {
                if (!inventoryService.checkAvailability(item.getProductId(), item.getQuantity())) {
                    return new OrderResult(orderId, false, null,
                        "Product " + item.getProductId() + " out of stock", List.of());
                }
                String resId = inventoryService.reserveStock(item.getProductId(), item.getQuantity());
                reservations.add(resId);
            }

            // Step 2: Process Payment
            String transactionId;
            try {
                transactionId = paymentService.processPayment(
                    request.getCustomerId(), request.getTotalAmount(),
                    request.getPaymentMethodToken());
            } catch (Exception e) {
                // Compensate: release all reservations
                reservations.forEach(inventoryService::releaseReservation);
                return new OrderResult(orderId, false, null,
                    "Payment failed: " + e.getMessage(), List.of());
            }

            // Step 3: Confirm Inventory Reservations
            reservations.forEach(inventoryService::confirmReservation);

            // Step 4: Create Shipment
            String trackingNumber = shippingService.createShipment(
                orderId, request.getShippingAddress(), request.getItems());

            // Step 5: Non-critical operations (don't fail the order)
            try {
                notificationService.sendOrderConfirmation(
                    request.getCustomerId(), orderId, trackingNumber);
            } catch (Exception e) {
                warnings.add("Confirmation email failed — will retry");
            }

            try {
                loyaltyService.awardPoints(request.getCustomerId(), request.getTotalAmount());
            } catch (Exception e) {
                warnings.add("Loyalty points will be awarded later");
            }

            try {
                analyticsService.trackOrderPlaced(
                    orderId, request.getTotalAmount(), request.getItems().size());
            } catch (Exception e) {
                // Analytics failure is silent
            }

            System.out.println("=== Order " + orderId + " completed ===\n");
            return new OrderResult(orderId, true, trackingNumber,
                "Order placed successfully", warnings);

        } catch (Exception e) {
            // Compensate on unexpected failure
            reservations.forEach(inventoryService::releaseReservation);
            return new OrderResult(orderId, false, null,
                "Unexpected error: " + e.getMessage(), List.of());
        }
    }
}
```

### Usage

```java
// Wire up services (in Spring: all @Autowired)
OrderFacade orderFacade = new OrderFacade(
    new InventoryService(), new PaymentService(),
    new ShippingService(), new NotificationService(),
    new LoyaltyService(), new AnalyticsService());

// Place order — one call coordinates 6 services
OrderRequest request = new OrderRequest("CUST-001",
    List.of(
        new OrderItem("PROD-A", 2, BigDecimal.valueOf(29.99)),
        new OrderItem("PROD-B", 1, BigDecimal.valueOf(49.99))
    ),
    "tok_visa_123", "123 Main St, Austin TX");

OrderResult result = orderFacade.placeOrder(request);

if (result.isSuccess()) {
    System.out.println("Order: " + result.getOrderId());
    System.out.println("Tracking: " + result.getTrackingNumber());
}
```

### Interview Talking Points

- **Compensation pattern**: If payment fails after inventory reservation, facade releases reservations — this is the Saga pattern in miniature
- **Critical vs non-critical**: Payment/Inventory failures = order fails. Notification/Analytics failures = order succeeds with warnings
- **Real-world**: On our marketplace, order placement coordinated inventory (dealer stock), payment (Stripe), shipping (FedEx API), and email (SendGrid). Facade kept the controller thin
- **Spring equivalent**: `@Service OrderFacade` with `@Transactional` for critical steps + `@Async` for notification/analytics
