**4. Payment Processing System**
You're building an e-commerce platform that supports multiple payment methods (Credit Card, PayPal, Bitcoin, Bank Transfer). Each payment method has different validation rules, processing logic, and fee calculations. New payment methods will be added frequently. The client code should not depend on concrete payment classes. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## ✅ Solution 1: Simple Factory Pattern (Recommended)

**Why Simple Factory?**
- Centralizes object creation logic in one place
- Caller doesn't need to know concrete payment classes
- Type checking is contained within the factory
- Clean, easy to understand and maintain

```java
// ============================================================================
// REQUEST CLASSES - Strongly typed request objects for each payment method
// ============================================================================

/**
 * Base class for all payment requests.
 * Contains common fields like amount that all payments need.
 */
class PaymentRequest {
    private final double amount;
    
    public PaymentRequest(double amount) {
        this.amount = amount;
    }
    
    public double getAmount() {
        return amount;
    }
}

/**
 * Credit Card specific request with card details.
 * Extends PaymentRequest to inherit common fields.
 */
class CreditCardRequest extends PaymentRequest {
    private final String cardNumber;
    private final String cvv;
    private final String expiryDate;
    
    public CreditCardRequest(String cardNumber, String cvv, String expiryDate, double amount) {
        super(amount);
        this.cardNumber = cardNumber;
        this.cvv = cvv;
        this.expiryDate = expiryDate;
    }
    
    public String getCardNumber() { return cardNumber; }
    public String getCvv() { return cvv; }
    public String getExpiryDate() { return expiryDate; }
}

/**
 * PayPal specific request with email authentication.
 */
class PayPalRequest extends PaymentRequest {
    private final String email;
    
    public PayPalRequest(String email, double amount) {
        super(amount);
        this.email = email;
    }
    
    public String getEmail() { return email; }
}

/**
 * Bitcoin specific request with wallet address.
 */
class BitcoinRequest extends PaymentRequest {
    private final String walletAddress;
    
    public BitcoinRequest(String walletAddress, double amount) {
        super(amount);
        this.walletAddress = walletAddress;
    }
    
    public String getWalletAddress() { return walletAddress; }
}

// ============================================================================
// PAYMENT INTERFACE - Common contract for all payment executions
// ============================================================================

/**
 * Represents an executable payment.
 * Each concrete payment implements this interface with its own logic.
 */
interface Payment {
    /**
     * Execute the payment.
     * @return true if payment succeeded, false otherwise
     */
    boolean execute();
    
    /**
     * Get the transaction fee for this payment.
     */
    double getFee();
}

// ============================================================================
// CONCRETE PAYMENTS - Each payment method has its own implementation
// ============================================================================

/**
 * Credit Card payment implementation.
 * Has access to CreditCardRequest with full type safety.
 */
class CreditCardPayment implements Payment {
    private final CreditCardRequest request;
    private static final double FEE_PERCENTAGE = 0.029; // 2.9% fee
    
    public CreditCardPayment(CreditCardRequest request) {
        this.request = request;
    }
    
    @Override
    public boolean execute() {
        // Type-safe: can directly access credit card specific fields
        if (!validate()) {
            System.out.println("Credit Card validation failed");
            return false;
        }
        
        System.out.println("Processing Credit Card payment:");
        System.out.println("  Card: ****-****-****-" + request.getCardNumber().substring(12));
        System.out.println("  Amount: $" + request.getAmount());
        System.out.println("  Fee: $" + getFee());
        return true;
    }
    
    @Override
    public double getFee() {
        return request.getAmount() * FEE_PERCENTAGE;
    }
    
    /**
     * Validate credit card specific rules.
     */
    private boolean validate() {
        return request.getCardNumber() != null 
            && request.getCardNumber().length() == 16
            && request.getCvv() != null
            && request.getCvv().length() == 3;
    }
}

/**
 * PayPal payment implementation.
 */
class PayPalPayment implements Payment {
    private final PayPalRequest request;
    private static final double FEE_PERCENTAGE = 0.034; // 3.4% fee
    
    public PayPalPayment(PayPalRequest request) {
        this.request = request;
    }
    
    @Override
    public boolean execute() {
        if (!validate()) {
            System.out.println("PayPal validation failed");
            return false;
        }
        
        System.out.println("Processing PayPal payment:");
        System.out.println("  Email: " + request.getEmail());
        System.out.println("  Amount: $" + request.getAmount());
        System.out.println("  Fee: $" + getFee());
        return true;
    }
    
    @Override
    public double getFee() {
        return request.getAmount() * FEE_PERCENTAGE;
    }
    
    private boolean validate() {
        return request.getEmail() != null && request.getEmail().contains("@");
    }
}

/**
 * Bitcoin payment implementation.
 */
class BitcoinPayment implements Payment {
    private final BitcoinRequest request;
    private static final double FIXED_FEE = 1.0; // $1 fixed fee
    
    public BitcoinPayment(BitcoinRequest request) {
        this.request = request;
    }
    
    @Override
    public boolean execute() {
        if (!validate()) {
            System.out.println("Bitcoin validation failed");
            return false;
        }
        
        System.out.println("Processing Bitcoin payment:");
        System.out.println("  Wallet: " + request.getWalletAddress().substring(0, 8) + "...");
        System.out.println("  Amount: $" + request.getAmount());
        System.out.println("  Fee: $" + getFee());
        return true;
    }
    
    @Override
    public double getFee() {
        return FIXED_FEE;
    }
    
    private boolean validate() {
        return request.getWalletAddress() != null && request.getWalletAddress().length() >= 26;
    }
}

// ============================================================================
// SIMPLE FACTORY - Centralized payment creation with type checking
// ============================================================================

/**
 * Simple Factory for creating Payment objects.
 * 
 * Benefits:
 * - Single responsibility: handles all payment creation
 * - Encapsulates type checking logic
 * - Client code doesn't depend on concrete payment classes
 * - Easy to add new payment methods (modify only this class)
 */
public class PaymentFactory {
    
    /**
     * Creates a Payment object based on the payment type.
     * 
     * @param type The payment type identifier (e.g., "credit", "paypal", "bitcoin")
     * @param request The payment request containing payment details
     * @return A Payment object ready to execute
     * @throws IllegalArgumentException if type is unknown or request type mismatches
     */
    public Payment createPayment(String type, PaymentRequest request) {
        return switch (type) {
            case "credit" -> {
                // Type check: ensure request matches expected type
                if (!(request instanceof CreditCardRequest)) {
                    throw new IllegalArgumentException(
                        "Payment type 'credit' requires CreditCardRequest, got: " + 
                        request.getClass().getSimpleName());
                }
                yield new CreditCardPayment((CreditCardRequest) request);
            }
            
            case "paypal" -> {
                if (!(request instanceof PayPalRequest)) {
                    throw new IllegalArgumentException(
                        "Payment type 'paypal' requires PayPalRequest, got: " + 
                        request.getClass().getSimpleName());
                }
                yield new PayPalPayment((PayPalRequest) request);
            }
            
            case "bitcoin" -> {
                if (!(request instanceof BitcoinRequest)) {
                    throw new IllegalArgumentException(
                        "Payment type 'bitcoin' requires BitcoinRequest, got: " + 
                        request.getClass().getSimpleName());
                }
                yield new BitcoinPayment((BitcoinRequest) request);
            }
            
            default -> throw new IllegalArgumentException("Unknown payment type: " + type);
        };
    }
    
    /**
     * Convenience method for credit card payments.
     */
    public Payment createCreditCardPayment(String cardNumber, String cvv, 
                                           String expiryDate, double amount) {
        return createPayment("credit", new CreditCardRequest(cardNumber, cvv, expiryDate, amount));
    }
    
    /**
     * Convenience method for PayPal payments.
     */
    public Payment createPayPalPayment(String email, double amount) {
        return createPayment("paypal", new PayPalRequest(email, amount));
    }
    
    /**
     * Convenience method for Bitcoin payments.
     */
    public Payment createBitcoinPayment(String walletAddress, double amount) {
        return createPayment("bitcoin", new BitcoinRequest(walletAddress, amount));
    }
}

// ============================================================================
// CLIENT USAGE EXAMPLE
// ============================================================================

/**
 * Example client code using the Simple Factory.
 * 
 * Key benefits for client:
 * - No knowledge of concrete Payment classes needed
 * - Type-safe after factory validation
 * - Clean, readable API
 */
public class PaymentClient {
    private final PaymentFactory factory;
    
    public PaymentClient() {
        this.factory = new PaymentFactory();
    }
    
    public void checkout() {
        // Option 1: Using generic createPayment method
        PaymentRequest creditRequest = new CreditCardRequest("1234567890123456", "123", "12/27", 100.0);
        Payment payment1 = factory.createPayment("credit", creditRequest);
        payment1.execute();
        
        // Option 2: Using convenience methods (cleaner!)
        Payment payment2 = factory.createCreditCardPayment("1234567890123456", "123", "12/27", 50.0);
        payment2.execute();
        
        Payment payment3 = factory.createPayPalPayment("user@email.com", 75.0);
        payment3.execute();
        
        Payment payment4 = factory.createBitcoinPayment("1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa", 200.0);
        payment4.execute();
    }
}
```

---

## ✅ Solution 2: Abstract Factory Pattern (For Extensibility)

**Why Abstract Factory?**
- Each payment method has its own factory class
- Open/Closed Principle: add new factories without modifying existing code
- Better for large systems with many payment methods
- Each factory can have its own configuration

```java
// ============================================================================
// ABSTRACT FACTORY INTERFACE - Contract for payment method factories
// ============================================================================

/**
 * Abstract factory interface for creating payments.
 * 
 * Each payment method (Credit Card, PayPal, Bitcoin) implements this interface.
 * This allows the PaymentProcessor to work with any factory without knowing details.
 */
interface PaymentMethodFactory {
    /**
     * Create a payment from a request.
     * @param request The payment request
     * @return A Payment object ready to execute
     * @throws IllegalArgumentException if request type doesn't match
     */
    Payment createPayment(PaymentRequest request);
    
    /**
     * Check if this factory supports the given request type.
     * @param requestType The class type of the request
     * @return true if supported
     */
    boolean supports(Class<? extends PaymentRequest> requestType);
    
    /**
     * Get the payment method identifier.
     */
    String getPaymentType();
}

// ============================================================================
// CONCRETE FACTORIES - One factory per payment method
// ============================================================================

/**
 * Factory for creating Credit Card payments.
 * Responsible only for Credit Card related logic.
 */
class CreditCardPaymentFactory implements PaymentMethodFactory {
    private static final String PAYMENT_TYPE = "credit";
    
    @Override
    public Payment createPayment(PaymentRequest request) {
        // Type-safe: this factory only handles CreditCardRequest
        if (!(request instanceof CreditCardRequest ccRequest)) {
            throw new IllegalArgumentException(
                "CreditCardFactory cannot handle: " + request.getClass().getSimpleName());
        }
        return new CreditCardPayment(ccRequest);
    }
    
    @Override
    public boolean supports(Class<? extends PaymentRequest> requestType) {
        return CreditCardRequest.class.isAssignableFrom(requestType);
    }
    
    @Override
    public String getPaymentType() {
        return PAYMENT_TYPE;
    }
}

/**
 * Factory for creating PayPal payments.
 */
class PayPalPaymentFactory implements PaymentMethodFactory {
    private static final String PAYMENT_TYPE = "paypal";
    
    @Override
    public Payment createPayment(PaymentRequest request) {
        if (!(request instanceof PayPalRequest paypalRequest)) {
            throw new IllegalArgumentException(
                "PayPalFactory cannot handle: " + request.getClass().getSimpleName());
        }
        return new PayPalPayment(paypalRequest);
    }
    
    @Override
    public boolean supports(Class<? extends PaymentRequest> requestType) {
        return PayPalRequest.class.isAssignableFrom(requestType);
    }
    
    @Override
    public String getPaymentType() {
        return PAYMENT_TYPE;
    }
}

/**
 * Factory for creating Bitcoin payments.
 */
class BitcoinPaymentFactory implements PaymentMethodFactory {
    private static final String PAYMENT_TYPE = "bitcoin";
    
    @Override
    public Payment createPayment(PaymentRequest request) {
        if (!(request instanceof BitcoinRequest btcRequest)) {
            throw new IllegalArgumentException(
                "BitcoinFactory cannot handle: " + request.getClass().getSimpleName());
        }
        return new BitcoinPayment(btcRequest);
    }
    
    @Override
    public boolean supports(Class<? extends PaymentRequest> requestType) {
        return BitcoinRequest.class.isAssignableFrom(requestType);
    }
    
    @Override
    public String getPaymentType() {
        return PAYMENT_TYPE;
    }
}

// ============================================================================
// PAYMENT PROCESSOR - Registry that manages all factories
// ============================================================================

/**
 * Central processor that registers and manages payment factories.
 * 
 * Benefits:
 * - Factories can be added/removed at runtime
 * - No switch statement - uses polymorphism
 * - Open for extension, closed for modification
 */
public class PaymentProcessor {
    private final Map<String, PaymentMethodFactory> factories;
    
    public PaymentProcessor() {
        this.factories = new HashMap<>();
        registerDefaults();
    }
    
    /**
     * Register default payment factories.
     * Can be overridden to customize available payment methods.
     */
    protected void registerDefaults() {
        registerFactory(new CreditCardPaymentFactory());
        registerFactory(new PayPalPaymentFactory());
        registerFactory(new BitcoinPaymentFactory());
    }
    
    /**
     * Register a new payment factory.
     * This allows adding new payment methods without modifying existing code.
     * 
     * @param factory The factory to register
     */
    public void registerFactory(PaymentMethodFactory factory) {
        factories.put(factory.getPaymentType(), factory);
        System.out.println("Registered payment method: " + factory.getPaymentType());
    }
    
    /**
     * Process a payment using the appropriate factory.
     * 
     * @param paymentType The type of payment (e.g., "credit", "paypal")
     * @param request The payment request
     * @return The executed Payment object
     * @throws IllegalArgumentException if payment type is unknown
     */
    public Payment processPayment(String paymentType, PaymentRequest request) {
        PaymentMethodFactory factory = factories.get(paymentType);
        
        if (factory == null) {
            throw new IllegalArgumentException("Unknown payment type: " + paymentType);
        }
        
        // Factory handles type validation internally
        Payment payment = factory.createPayment(request);
        
        // Execute the payment
        if (payment.execute()) {
            System.out.println("Payment successful! Fee: $" + payment.getFee());
        } else {
            System.out.println("Payment failed!");
        }
        
        return payment;
    }
    
    /**
     * Get all available payment types.
     */
    public Set<String> getAvailablePaymentTypes() {
        return factories.keySet();
    }
    
    /**
     * Check if a payment type is available.
     */
    public boolean isPaymentTypeAvailable(String paymentType) {
        return factories.containsKey(paymentType);
    }
}

// ============================================================================
// EXTENSIBILITY EXAMPLE - Adding a new payment method
// ============================================================================

/**
 * Example: Adding a new "Bank Transfer" payment method.
 * 
 * With Abstract Factory, we only need to:
 * 1. Create BankTransferRequest class
 * 2. Create BankTransferPayment class
 * 3. Create BankTransferPaymentFactory class
 * 4. Register the factory
 * 
 * NO existing code needs modification! (Open/Closed Principle)
 */

class BankTransferRequest extends PaymentRequest {
    private final String accountNumber;
    private final String routingNumber;
    
    public BankTransferRequest(String accountNumber, String routingNumber, double amount) {
        super(amount);
        this.accountNumber = accountNumber;
        this.routingNumber = routingNumber;
    }
    
    public String getAccountNumber() { return accountNumber; }
    public String getRoutingNumber() { return routingNumber; }
}

class BankTransferPayment implements Payment {
    private final BankTransferRequest request;
    private static final double FIXED_FEE = 0.50;
    
    public BankTransferPayment(BankTransferRequest request) {
        this.request = request;
    }
    
    @Override
    public boolean execute() {
        System.out.println("Bank Transfer: $" + request.getAmount());
        System.out.println("  Account: ****" + request.getAccountNumber().substring(4));
        return true;
    }
    
    @Override
    public double getFee() {
        return FIXED_FEE;
    }
}

class BankTransferFactory implements PaymentMethodFactory {
    @Override
    public Payment createPayment(PaymentRequest request) {
        if (!(request instanceof BankTransferRequest bankRequest)) {
            throw new IllegalArgumentException("Expected BankTransferRequest");
        }
        return new BankTransferPayment(bankRequest);
    }
    
    @Override
    public boolean supports(Class<? extends PaymentRequest> requestType) {
        return BankTransferRequest.class.isAssignableFrom(requestType);
    }
    
    @Override
    public String getPaymentType() {
        return "bank_transfer";
    }
}

// ============================================================================
// CLIENT USAGE EXAMPLE - Abstract Factory
// ============================================================================

/**
 * Example client using Abstract Factory pattern.
 */
public class PaymentProcessorClient {
    private final PaymentProcessor processor;
    
    public PaymentProcessorClient() {
        this.processor = new PaymentProcessor();
    }
    
    public void checkout() {
        // Process payments through the processor
        processor.processPayment("credit", new CreditCardRequest("1234567890123456", "123", "12/27", 100.0));
        processor.processPayment("paypal", new PayPalRequest("user@email.com", 50.0));
        processor.processPayment("bitcoin", new BitcoinRequest("wallet123", 200.0));
        
        // Add a new payment method at runtime!
        processor.registerFactory(new BankTransferFactory());
        
        // Now bank transfer is available
        processor.processPayment("bank_transfer", new BankTransferRequest("12345678", "87654321", 500.0));
    }
}
```

---

## 📊 Pattern Comparison

| Aspect | Simple Factory | Abstract Factory |
|--------|----------------|------------------|
| **Classes** | Few (1 factory) | Many (1 interface + N factories) |
| **Type Safety** | Runtime (centralized) | Runtime (per factory) |
| **Extensibility** | Modify factory class | Add new factory class |
| **OCP Compliance** | ❌ Must modify switch | ✅ Add new class |
| **Complexity** | Low | Medium-High |
| **Best For** | Small-Medium systems | Large, frequently changing systems |

---

## 💡 Interview Tips

### Q: "When to use Simple vs Abstract Factory?"
```
Simple Factory:
- Few payment methods (< 5)
- Payment methods don't change often
- Want simpler code

Abstract Factory:
- Many payment methods
- New methods added frequently
- Each method needs complex setup
- Want OCP compliance
```

### Q: "How to add a new payment method?"
```java
// Simple Factory: Add case to switch
case "applepay" -> new ApplePayPayment((ApplePayRequest) request);

// Abstract Factory: Create new factory class (no modification!)
class ApplePayFactory implements PaymentMethodFactory { ... }
processor.registerFactory(new ApplePayFactory());
```

### Q: "Why not use Strategy pattern?"
```
Strategy is for interchangeable algorithms.
Factory is for object creation.

Here we need to CREATE payment objects,
not switch between algorithms at runtime.
```

---

## 📝 Key Takeaway

> **Simple Factory** - Best for most cases:
> - Single class handles all creation
> - Type checking centralized
> - Clean, understandable
>
> **Abstract Factory** - Best for extensibility:
> - Each payment has its own factory
> - OCP: add without modifying
> - Better for large, changing systems
