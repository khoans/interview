**13.** Payment Gateway Integration — Adapter pattern. Integrate Stripe, PayPal, Square with unified interface. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## Payment Gateway Integration — Adapter Pattern

### Pattern: Adapter (Object Adapter)

**Why Adapter?** Each payment gateway (Stripe, PayPal, Square) has a completely different API — different method names, parameter types, response formats. Adapter wraps each vendor SDK behind a unified `PaymentGateway` interface so our code never depends on vendor specifics.

### Text UML

```
┌──────────────────────────┐
│    <<interface>>         │
│    PaymentGateway        │
├──────────────────────────┤
│ + charge(req): Result    │
│ + refund(txnId, amt): R  │
│ + getTransaction(id): T  │
└──────────┬───────────────┘
           │
    ┌──────┼──────────┬──────────────┐
    │      │          │              │
StripeAdapter PayPalAdapter SquareAdapter
    │              │              │
    ▼              ▼              ▼
StripeSDK    PayPalSDK      SquareSDK
(3rd party)  (3rd party)    (3rd party)
```

### Key Design Decisions

1. **Object adapter (composition)** — Wrap vendor SDK instances, don't extend them. Vendor classes aren't designed for inheritance
2. **Unified DTOs** — `ChargeRequest`, `PaymentResult`, `TransactionInfo` are our domain objects. Adapters translate to/from vendor formats
3. **Exception normalization** — Each vendor throws different exceptions; adapters catch and wrap in `PaymentException`
4. **Gateway factory** — Select adapter at runtime based on config or merchant preference

### SOLID Principles Applied

- **SRP**: Each adapter handles one vendor's translation logic
- **OCP**: Add new gateway (Braintree) by writing a new adapter — zero changes to existing code
- **DIP**: Service layer depends on `PaymentGateway` interface, never on Stripe/PayPal directly
- **LSP**: All adapters are interchangeable — swap gateways without changing business logic

### Java Code

```java
import java.math.BigDecimal;
import java.time.Instant;
import java.util.*;

// ===================== Our Domain Objects =====================

public enum PaymentStatus { SUCCESS, FAILED, PENDING, REFUNDED }

public class ChargeRequest {
    private final BigDecimal amount;
    private final String currency;
    private final String paymentMethodToken;
    private final String description;
    private final Map<String, String> metadata;

    public ChargeRequest(BigDecimal amount, String currency, String token, String description) {
        this.amount = amount;
        this.currency = currency;
        this.paymentMethodToken = token;
        this.description = description;
        this.metadata = new HashMap<>();
    }

    public BigDecimal getAmount() { return amount; }
    public String getCurrency() { return currency; }
    public String getPaymentMethodToken() { return paymentMethodToken; }
    public String getDescription() { return description; }
    public Map<String, String> getMetadata() { return metadata; }
}

public class PaymentResult {
    private final String transactionId;
    private final PaymentStatus status;
    private final BigDecimal amount;
    private final String gatewayName;
    private final Instant processedAt;

    public PaymentResult(String transactionId, PaymentStatus status,
                         BigDecimal amount, String gatewayName) {
        this.transactionId = transactionId;
        this.status = status;
        this.amount = amount;
        this.gatewayName = gatewayName;
        this.processedAt = Instant.now();
    }

    public String getTransactionId() { return transactionId; }
    public PaymentStatus getStatus() { return status; }
    public BigDecimal getAmount() { return amount; }
    public String getGatewayName() { return gatewayName; }
}

public class PaymentException extends RuntimeException {
    private final String gatewayName;
    private final String errorCode;

    public PaymentException(String gatewayName, String errorCode, String message, Throwable cause) {
        super(message, cause);
        this.gatewayName = gatewayName;
        this.errorCode = errorCode;
    }

    public String getGatewayName() { return gatewayName; }
    public String getErrorCode() { return errorCode; }
}

// ===================== Unified Interface =====================

public interface PaymentGateway {
    PaymentResult charge(ChargeRequest request);
    PaymentResult refund(String transactionId, BigDecimal amount);
    String getGatewayName();
}

// ===================== Third-Party SDKs (Simulated) =====================

// Stripe SDK — uses cents, returns Charge object
class StripeCharge {
    String id;
    long amountInCents;
    String status; // "succeeded", "failed"
}

class StripeSDK {
    public StripeCharge createCharge(long amountCents, String currency,
                                     String source, String desc) {
        StripeCharge charge = new StripeCharge();
        charge.id = "ch_" + UUID.randomUUID().toString().substring(0, 8);
        charge.amountInCents = amountCents;
        charge.status = "succeeded";
        return charge;
    }

    public StripeCharge refundCharge(String chargeId, long amountCents) {
        StripeCharge refund = new StripeCharge();
        refund.id = "re_" + UUID.randomUUID().toString().substring(0, 8);
        refund.amountInCents = amountCents;
        refund.status = "succeeded";
        return refund;
    }
}

// PayPal SDK — uses dollars (double), returns PayPalPayment
class PayPalPayment {
    String paymentId;
    double total;
    String state; // "approved", "failed"
}

class PayPalSDK {
    public PayPalPayment executePayment(double amount, String currencyCode,
                                         String token, String intent) {
        PayPalPayment payment = new PayPalPayment();
        payment.paymentId = "PAY-" + UUID.randomUUID().toString().substring(0, 8);
        payment.total = amount;
        payment.state = "approved";
        return payment;
    }

    public PayPalPayment refundSale(String saleId, double amount) {
        PayPalPayment refund = new PayPalPayment();
        refund.paymentId = "REF-" + UUID.randomUUID().toString().substring(0, 8);
        refund.total = amount;
        refund.state = "approved";
        return refund;
    }
}

// Square SDK — uses Money object, returns SquarePayment
class SquareMoney {
    long amount; // in smallest currency unit
    String currency;
}

class SquarePaymentResult {
    String id;
    SquareMoney amountMoney;
    String status; // "COMPLETED", "FAILED"
}

class SquareSDK {
    public SquarePaymentResult createPayment(SquareMoney money, String sourceId, String note) {
        SquarePaymentResult result = new SquarePaymentResult();
        result.id = "sq_" + UUID.randomUUID().toString().substring(0, 8);
        result.amountMoney = money;
        result.status = "COMPLETED";
        return result;
    }

    public SquarePaymentResult refundPayment(String paymentId, SquareMoney money) {
        SquarePaymentResult result = new SquarePaymentResult();
        result.id = "sqr_" + UUID.randomUUID().toString().substring(0, 8);
        result.amountMoney = money;
        result.status = "COMPLETED";
        return result;
    }
}

// ===================== Adapters =====================

public class StripeAdapter implements PaymentGateway {
    private final StripeSDK stripe;

    public StripeAdapter(StripeSDK stripe) {
        this.stripe = stripe;
    }

    @Override
    public PaymentResult charge(ChargeRequest request) {
        try {
            // Convert dollars to cents for Stripe
            long cents = request.getAmount().multiply(BigDecimal.valueOf(100)).longValue();
            StripeCharge charge = stripe.createCharge(
                cents, request.getCurrency(),
                request.getPaymentMethodToken(), request.getDescription());

            PaymentStatus status = "succeeded".equals(charge.status)
                ? PaymentStatus.SUCCESS : PaymentStatus.FAILED;
            return new PaymentResult(charge.id, status, request.getAmount(), getGatewayName());
        } catch (Exception e) {
            throw new PaymentException("Stripe", "STRIPE_ERROR", e.getMessage(), e);
        }
    }

    @Override
    public PaymentResult refund(String transactionId, BigDecimal amount) {
        long cents = amount.multiply(BigDecimal.valueOf(100)).longValue();
        StripeCharge refund = stripe.refundCharge(transactionId, cents);
        return new PaymentResult(refund.id, PaymentStatus.REFUNDED, amount, getGatewayName());
    }

    @Override
    public String getGatewayName() { return "Stripe"; }
}

public class PayPalAdapter implements PaymentGateway {
    private final PayPalSDK paypal;

    public PayPalAdapter(PayPalSDK paypal) {
        this.paypal = paypal;
    }

    @Override
    public PaymentResult charge(ChargeRequest request) {
        try {
            PayPalPayment payment = paypal.executePayment(
                request.getAmount().doubleValue(), request.getCurrency(),
                request.getPaymentMethodToken(), "sale");

            PaymentStatus status = "approved".equals(payment.state)
                ? PaymentStatus.SUCCESS : PaymentStatus.FAILED;
            return new PaymentResult(payment.paymentId, status,
                request.getAmount(), getGatewayName());
        } catch (Exception e) {
            throw new PaymentException("PayPal", "PAYPAL_ERROR", e.getMessage(), e);
        }
    }

    @Override
    public PaymentResult refund(String transactionId, BigDecimal amount) {
        PayPalPayment refund = paypal.refundSale(transactionId, amount.doubleValue());
        return new PaymentResult(refund.paymentId, PaymentStatus.REFUNDED,
            amount, getGatewayName());
    }

    @Override
    public String getGatewayName() { return "PayPal"; }
}

public class SquareAdapter implements PaymentGateway {
    private final SquareSDK square;

    public SquareAdapter(SquareSDK square) {
        this.square = square;
    }

    @Override
    public PaymentResult charge(ChargeRequest request) {
        try {
            SquareMoney money = new SquareMoney();
            money.amount = request.getAmount().multiply(BigDecimal.valueOf(100)).longValue();
            money.currency = request.getCurrency();

            SquarePaymentResult result = square.createPayment(
                money, request.getPaymentMethodToken(), request.getDescription());

            PaymentStatus status = "COMPLETED".equals(result.status)
                ? PaymentStatus.SUCCESS : PaymentStatus.FAILED;
            return new PaymentResult(result.id, status, request.getAmount(), getGatewayName());
        } catch (Exception e) {
            throw new PaymentException("Square", "SQUARE_ERROR", e.getMessage(), e);
        }
    }

    @Override
    public PaymentResult refund(String transactionId, BigDecimal amount) {
        SquareMoney money = new SquareMoney();
        money.amount = amount.multiply(BigDecimal.valueOf(100)).longValue();
        money.currency = "USD";
        SquarePaymentResult result = square.refundPayment(transactionId, money);
        return new PaymentResult(result.id, PaymentStatus.REFUNDED, amount, getGatewayName());
    }

    @Override
    public String getGatewayName() { return "Square"; }
}
```

### Usage

```java
// Configure gateways
Map<String, PaymentGateway> gateways = Map.of(
    "stripe", new StripeAdapter(new StripeSDK()),
    "paypal", new PayPalAdapter(new PayPalSDK()),
    "square", new SquareAdapter(new SquareSDK())
);

// Merchant selects gateway at runtime
String merchantGateway = "stripe";
PaymentGateway gateway = gateways.get(merchantGateway);

ChargeRequest request = new ChargeRequest(
    BigDecimal.valueOf(99.99), "USD", "tok_visa_123", "Order #1001");

PaymentResult result = gateway.charge(request);
// Works identically regardless of which gateway is selected
```

### Interview Talking Points

- **Real-world**: On our marketplace, we integrated Stripe for US payments and PayPal for international. Adapter pattern let us add new gateways without touching checkout logic
- **Key translation**: Stripe uses cents (long), PayPal uses dollars (double), Square uses Money object — adapter normalizes to BigDecimal
- **Exception normalization**: Each SDK throws vendor-specific exceptions; adapters wrap them in `PaymentException` with uniform error codes
- **Spring integration**: Each adapter is a `@Component`; inject `Map<String, PaymentGateway>` via Spring and select by merchant config
