**49.** Design an Airline Booking System using mixed patterns. Strategy (pricing) + State (booking states) + Factory (payment methods) + Builder (passenger info).

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Airline Booking System — Mixed Patterns

**Patterns:** Strategy + State + Factory + Builder
**Why mixed?** Pricing varies by class/season (Strategy), bookings have lifecycle (State), payment creation varies (Factory), complex passenger records need structured construction (Builder).

### Text UML Diagram

```
Builder (Passenger)          Factory (Payment)
┌──────────────────┐        ┌──────────────────┐
│ PassengerBuilder │        │ PaymentFactory   │
│──────────────────│        │──────────────────│
│ +name()          │        │ +create(type)    │
│ +passport()      │        │  -> CreditCard   │
│ +meal()          │        │  -> PayPal       │
│ +luggage()       │        │  -> BankTransfer │
│ +seatPref()      │        └──────────────────┘
│ +build()         │
└──────────────────┘        Strategy (Pricing)
                            ┌──────────────────┐
State (Booking)             │ <<interface>>     │
PENDING ──pay──→ CONFIRMED  │ PricingStrategy   │
CONFIRMED ──checkin──→      │──────────────────│
  CHECKED_IN                │ +calculate()     │
PENDING ──timeout──→        │  Economy, Biz,   │
  CANCELLED                 │  First, Promo    │
CONFIRMED ──cancel──→       └──────────────────┘
  CANCELLED (refund)
```

### Key Design Decisions

1. **Builder** for passengers — Many optional fields (meal, seat pref, frequent flyer, luggage). Builder prevents telescoping constructors.
2. **Strategy** for pricing — Economy, Business, First Class each have different base rate, tax, and fee structures.
3. **State** for booking — Enforces valid transitions (can't check in before paying, refund rules depend on state).
4. **Factory** for payments — Each payment method has different validation and processing.

### Java Implementation

```java
import java.util.*;

// ============================================================
// BUILDER — Passenger information
// ============================================================

class Passenger {
    private final String firstName;
    private final String lastName;
    private final String passportNumber;
    private final String mealPreference;   // optional
    private final String seatPreference;   // optional
    private final int luggageCount;        // optional
    private final String frequentFlyerId;  // optional

    private Passenger(Builder builder) {
        this.firstName = builder.firstName;
        this.lastName = builder.lastName;
        this.passportNumber = builder.passportNumber;
        this.mealPreference = builder.mealPreference;
        this.seatPreference = builder.seatPreference;
        this.luggageCount = builder.luggageCount;
        this.frequentFlyerId = builder.frequentFlyerId;
    }

    public String getFullName() { return firstName + " " + lastName; }
    public String getPassportNumber() { return passportNumber; }
    public String getMealPreference() { return mealPreference; }
    public String getSeatPreference() { return seatPreference; }
    public int getLuggageCount() { return luggageCount; }
    public String getFrequentFlyerId() { return frequentFlyerId; }

    @Override
    public String toString() {
        return String.format("%s (Passport: %s, Meal: %s, Seat: %s, Bags: %d)",
                getFullName(), passportNumber, mealPreference, seatPreference, luggageCount);
    }

    public static class Builder {
        private final String firstName;
        private final String lastName;
        private String passportNumber;
        private String mealPreference = "Standard";
        private String seatPreference = "No preference";
        private int luggageCount = 1;
        private String frequentFlyerId;

        public Builder(String firstName, String lastName) {
            this.firstName = firstName;
            this.lastName = lastName;
        }

        public Builder passport(String passport) { this.passportNumber = passport; return this; }
        public Builder meal(String meal) { this.mealPreference = meal; return this; }
        public Builder seat(String pref) { this.seatPreference = pref; return this; }
        public Builder luggage(int count) { this.luggageCount = count; return this; }
        public Builder frequentFlyer(String id) { this.frequentFlyerId = id; return this; }

        public Passenger build() {
            if (passportNumber == null || passportNumber.isEmpty()) {
                throw new IllegalStateException("Passport number is required");
            }
            return new Passenger(this);
        }
    }
}

// ============================================================
// STRATEGY — Pricing by class
// ============================================================

class FlightInfo {
    private final String flightNumber;
    private final double basePrice;
    private final double distanceMiles;

    public FlightInfo(String flightNumber, double basePrice, double distanceMiles) {
        this.flightNumber = flightNumber;
        this.basePrice = basePrice;
        this.distanceMiles = distanceMiles;
    }

    public String getFlightNumber() { return flightNumber; }
    public double getBasePrice() { return basePrice; }
    public double getDistanceMiles() { return distanceMiles; }
}

interface PricingStrategy {
    double calculate(FlightInfo flight, Passenger passenger);
    String getClassName();
}

class EconomyPricing implements PricingStrategy {
    @Override
    public double calculate(FlightInfo flight, Passenger passenger) {
        double price = flight.getBasePrice();
        // Extra luggage fee
        if (passenger.getLuggageCount() > 1) {
            price += (passenger.getLuggageCount() - 1) * 35.0;
        }
        // Tax
        price *= 1.12;
        return price;
    }
    @Override public String getClassName() { return "Economy"; }
}

class BusinessPricing implements PricingStrategy {
    @Override
    public double calculate(FlightInfo flight, Passenger passenger) {
        double price = flight.getBasePrice() * 2.5; // 2.5x economy
        // 2 bags included, $50 each additional
        if (passenger.getLuggageCount() > 2) {
            price += (passenger.getLuggageCount() - 2) * 50.0;
        }
        // Frequent flyer discount
        if (passenger.getFrequentFlyerId() != null) {
            price *= 0.90; // 10% discount
        }
        price *= 1.12;
        return price;
    }
    @Override public String getClassName() { return "Business"; }
}

class FirstClassPricing implements PricingStrategy {
    @Override
    public double calculate(FlightInfo flight, Passenger passenger) {
        double price = flight.getBasePrice() * 5.0; // 5x economy
        // All luggage included
        if (passenger.getFrequentFlyerId() != null) {
            price *= 0.85; // 15% discount
        }
        price *= 1.12;
        return price;
    }
    @Override public String getClassName() { return "First Class"; }
}

// ============================================================
// FACTORY — Payment methods
// ============================================================

interface PaymentMethod {
    boolean process(double amount);
    String getType();
}

class CreditCardPayment implements PaymentMethod {
    private final String cardNumber;
    private final String expiry;

    public CreditCardPayment(String cardNumber, String expiry) {
        this.cardNumber = cardNumber;
        this.expiry = expiry;
    }

    @Override
    public boolean process(double amount) {
        System.out.printf("Processing $%.2f via Credit Card ending in %s%n",
                amount, cardNumber.substring(cardNumber.length() - 4));
        return true;
    }
    @Override public String getType() { return "Credit Card"; }
}

class PayPalPayment implements PaymentMethod {
    private final String email;
    public PayPalPayment(String email) { this.email = email; }

    @Override
    public boolean process(double amount) {
        System.out.printf("Processing $%.2f via PayPal (%s)%n", amount, email);
        return true;
    }
    @Override public String getType() { return "PayPal"; }
}

class PaymentFactory {
    public PaymentMethod create(String type, Map<String, String> details) {
        return switch (type.toLowerCase()) {
            case "credit_card" -> new CreditCardPayment(
                    details.get("cardNumber"), details.get("expiry"));
            case "paypal" -> new PayPalPayment(details.get("email"));
            default -> throw new IllegalArgumentException("Unknown payment type: " + type);
        };
    }
}

// ============================================================
// STATE — Booking lifecycle
// ============================================================

interface BookingState {
    void pay(Booking booking, PaymentMethod payment);
    void cancel(Booking booking);
    void checkIn(Booking booking);
    String getStateName();
}

class PendingState implements BookingState {
    @Override
    public void pay(Booking booking, PaymentMethod payment) {
        double amount = booking.getTotalPrice();
        if (payment.process(amount)) {
            booking.setPaymentMethod(payment);
            System.out.printf("Booking %s paid: $%.2f via %s%n",
                    booking.getId(), amount, payment.getType());
            booking.setState(new ConfirmedState());
        } else {
            System.out.println("Payment failed!");
        }
    }

    @Override
    public void cancel(Booking booking) {
        System.out.println("Booking " + booking.getId() + " cancelled (no charge)");
        booking.setState(new CancelledState());
    }

    @Override public void checkIn(Booking b) { System.out.println("Pay first to check in"); }
    @Override public String getStateName() { return "PENDING"; }
}

class ConfirmedState implements BookingState {
    @Override public void pay(Booking b, PaymentMethod p) { System.out.println("Already paid"); }

    @Override
    public void cancel(Booking booking) {
        double refund = booking.getTotalPrice() * 0.80; // 20% cancellation fee
        System.out.printf("Booking %s cancelled. Refund: $%.2f (80%%)%n", booking.getId(), refund);
        booking.setState(new CancelledState());
    }

    @Override
    public void checkIn(Booking booking) {
        System.out.println("Checked in: " + booking.getPassenger().getFullName()
                + " on flight " + booking.getFlight().getFlightNumber());
        booking.setState(new CheckedInState());
    }
    @Override public String getStateName() { return "CONFIRMED"; }
}

class CheckedInState implements BookingState {
    @Override public void pay(Booking b, PaymentMethod p) { System.out.println("Already paid"); }
    @Override
    public void cancel(Booking booking) {
        double refund = booking.getTotalPrice() * 0.50; // 50% late cancel fee
        System.out.printf("Late cancellation. Refund: $%.2f (50%%)%n", refund);
        booking.setState(new CancelledState());
    }
    @Override public void checkIn(Booking b) { System.out.println("Already checked in"); }
    @Override public String getStateName() { return "CHECKED_IN"; }
}

class CancelledState implements BookingState {
    @Override public void pay(Booking b, PaymentMethod p) { done(); }
    @Override public void cancel(Booking b) { done(); }
    @Override public void checkIn(Booking b) { done(); }
    @Override public String getStateName() { return "CANCELLED"; }
    private void done() { System.out.println("Booking is cancelled"); }
}

// ============================================================
// BOOKING — aggregates all patterns
// ============================================================

class Booking {
    private final String id;
    private final Passenger passenger;
    private final FlightInfo flight;
    private final PricingStrategy pricing;
    private BookingState state;
    private PaymentMethod paymentMethod;
    private final double totalPrice;

    public Booking(String id, Passenger passenger, FlightInfo flight, PricingStrategy pricing) {
        this.id = id;
        this.passenger = passenger;
        this.flight = flight;
        this.pricing = pricing;
        this.totalPrice = pricing.calculate(flight, passenger);
        this.state = new PendingState();
    }

    public void pay(PaymentMethod payment) { state.pay(this, payment); }
    public void cancel() { state.cancel(this); }
    public void checkIn() { state.checkIn(this); }

    public String getId() { return id; }
    public Passenger getPassenger() { return passenger; }
    public FlightInfo getFlight() { return flight; }
    public double getTotalPrice() { return totalPrice; }
    public void setState(BookingState state) { this.state = state; }
    public void setPaymentMethod(PaymentMethod pm) { this.paymentMethod = pm; }

    public void printSummary() {
        System.out.printf("%nBooking %s [%s]%n  Passenger: %s%n  Flight: %s (%s)%n  Price: $%.2f%n",
                id, state.getStateName(), passenger, flight.getFlightNumber(),
                pricing.getClassName(), totalPrice);
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class AirlineBookingDemo {
    public static void main(String[] args) {
        // Build passenger using Builder
        Passenger passenger = new Passenger.Builder("John", "Doe")
                .passport("US123456789")
                .meal("Vegetarian")
                .seat("Window")
                .luggage(2)
                .frequentFlyer("FF-98765")
                .build();

        FlightInfo flight = new FlightInfo("UA-456", 350.00, 2500);

        // Create booking with Business pricing strategy
        Booking booking = new Booking("BK-001", passenger, flight, new BusinessPricing());
        booking.printSummary();

        // Pay using factory-created payment
        PaymentFactory paymentFactory = new PaymentFactory();
        PaymentMethod payment = paymentFactory.create("credit_card",
                Map.of("cardNumber", "4111111111111111", "expiry", "12/27"));

        booking.pay(payment);
        booking.printSummary();

        // Check in
        booking.checkIn();

        // Economy booking — different pricing
        System.out.println("\n=== Economy Booking ===");
        Passenger economy = new Passenger.Builder("Jane", "Smith")
                .passport("UK987654321")
                .luggage(3)
                .build();

        Booking econBooking = new Booking("BK-002", economy, flight, new EconomyPricing());
        econBooking.printSummary();

        // Cancel before paying (no charge)
        econBooking.cancel();
    }
}
```

### Interview Talking Points

- **Builder is essential here:** Passenger has 7+ fields, 4 optional. Without Builder, you'd have a constructor with 7 params or setters on a mutable object. Builder gives clear, readable construction with validation.
- **State-dependent refund:** Cancellation refund percentage changes by state (100% pending, 80% confirmed, 50% checked-in). This is exactly how airlines work — and it's cleanly modeled with State pattern.
- **Real-world:** Amadeus and Sabre (airline booking systems) use state machines for PNR (Passenger Name Record) lifecycle. Pricing uses strategy-like rule engines. Payment gateways use factory pattern.
