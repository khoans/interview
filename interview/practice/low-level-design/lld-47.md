**47.** Design a Ride-Sharing App using mixed patterns. Factory (vehicle types) + Strategy (pricing) + Observer (location updates) + State (ride states).

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Ride-Sharing App — Mixed Patterns

**Patterns:** Factory + Strategy + Observer + State
**Why mixed?** Ride-sharing has distinct concerns: vehicle creation (Factory), dynamic pricing (Strategy), real-time tracking (Observer), and ride lifecycle (State).

### Text UML Diagram

```
Factory                 Strategy                Observer
┌────────────┐    ┌──────────────┐    ┌──────────────────┐
│VehicleFactory│   │<<interface>> │    │  LocationTracker  │
│────────────│    │PricingStrategy│   │  (Subject)        │
│+create()   │    │──────────────│    │──────────────────│
└────────────┘    │+calculate()  │    │ +addObserver()    │
  │               └──────┬──────┘    │ +updateLocation() │
  ├── Economy              │          └──────────────────┘
  ├── Premium        Standard│             │ notifies
  └── SUV            Surge   │       ┌─────┴──────┐
                     Pool    │     Rider    Driver
                                  Tracker  Dashboard

State (Ride Lifecycle)
  REQUESTED ──match──→ MATCHED ──pickup──→ IN_PROGRESS ──arrive──→ COMPLETED
                           │                    │
                       timeout──→ CANCELLED  cancel──→ CANCELLED
```

### Key Design Decisions

1. **Factory** creates vehicles with different capacities and features
2. **Strategy** for pricing — standard, surge, pool each have unique calculation
3. **Observer** for live location — rider and driver apps get GPS updates
4. **State** manages ride lifecycle — enforces valid transitions

### Java Implementation

```java
import java.util.*;
import java.util.concurrent.CopyOnWriteArrayList;

// ============================================================
// VEHICLE — Factory products
// ============================================================

abstract class Vehicle {
    private final String id;
    private final String model;
    private final int capacity;
    private final double baseRatePerMile;

    protected Vehicle(String id, String model, int capacity, double baseRate) {
        this.id = id;
        this.model = model;
        this.capacity = capacity;
        this.baseRatePerMile = baseRate;
    }

    public String getId() { return id; }
    public String getModel() { return model; }
    public int getCapacity() { return capacity; }
    public double getBaseRatePerMile() { return baseRatePerMile; }
    public abstract String getType();
}

class EconomyVehicle extends Vehicle {
    public EconomyVehicle(String id, String model) { super(id, model, 4, 1.00); }
    @Override public String getType() { return "Economy"; }
}

class PremiumVehicle extends Vehicle {
    public PremiumVehicle(String id, String model) { super(id, model, 4, 2.50); }
    @Override public String getType() { return "Premium"; }
}

class SUVVehicle extends Vehicle {
    public SUVVehicle(String id, String model) { super(id, model, 6, 3.00); }
    @Override public String getType() { return "SUV"; }
}

class VehicleFactory {
    public Vehicle create(String type, String id, String model) {
        return switch (type.toLowerCase()) {
            case "economy" -> new EconomyVehicle(id, model);
            case "premium" -> new PremiumVehicle(id, model);
            case "suv" -> new SUVVehicle(id, model);
            default -> throw new IllegalArgumentException("Unknown vehicle type: " + type);
        };
    }
}

// ============================================================
// PRICING — Strategy
// ============================================================

class RideDetails {
    private final double distanceMiles;
    private final int durationMinutes;
    private final double demandMultiplier; // 1.0 = normal, >1 = high demand
    private final int passengerCount;

    public RideDetails(double distance, int duration, double demand, int passengers) {
        this.distanceMiles = distance;
        this.durationMinutes = duration;
        this.demandMultiplier = demand;
        this.passengerCount = passengers;
    }

    public double getDistanceMiles() { return distanceMiles; }
    public int getDurationMinutes() { return durationMinutes; }
    public double getDemandMultiplier() { return demandMultiplier; }
    public int getPassengerCount() { return passengerCount; }
}

interface PricingStrategy {
    double calculate(Vehicle vehicle, RideDetails details);
    String getName();
}

class StandardPricing implements PricingStrategy {
    private static final double BASE_FEE = 2.50;
    private static final double PER_MINUTE = 0.20;

    @Override
    public double calculate(Vehicle vehicle, RideDetails details) {
        return BASE_FEE
                + (vehicle.getBaseRatePerMile() * details.getDistanceMiles())
                + (PER_MINUTE * details.getDurationMinutes());
    }

    @Override public String getName() { return "Standard"; }
}

class SurgePricing implements PricingStrategy {
    @Override
    public double calculate(Vehicle vehicle, RideDetails details) {
        double standard = new StandardPricing().calculate(vehicle, details);
        return standard * details.getDemandMultiplier();
    }

    @Override public String getName() { return "Surge"; }
}

class PoolPricing implements PricingStrategy {
    @Override
    public double calculate(Vehicle vehicle, RideDetails details) {
        double standard = new StandardPricing().calculate(vehicle, details);
        // Split cost among passengers, with 20% discount
        return (standard * 0.80) / details.getPassengerCount();
    }

    @Override public String getName() { return "Pool"; }
}

// ============================================================
// LOCATION TRACKING — Observer
// ============================================================

class Location {
    private final double lat;
    private final double lng;
    public Location(double lat, double lng) { this.lat = lat; this.lng = lng; }
    @Override public String toString() { return String.format("(%.4f, %.4f)", lat, lng); }
}

interface LocationObserver {
    void onLocationUpdate(String rideId, Location location, String source);
}

class RiderAppObserver implements LocationObserver {
    private final String riderId;
    public RiderAppObserver(String riderId) { this.riderId = riderId; }

    @Override
    public void onLocationUpdate(String rideId, Location location, String source) {
        System.out.printf("  [RiderApp %s] Driver at %s%n", riderId, location);
    }
}

class DriverDashboardObserver implements LocationObserver {
    @Override
    public void onLocationUpdate(String rideId, Location location, String source) {
        System.out.printf("  [DriverDash] Ride %s: %s at %s%n", rideId, source, location);
    }
}

class LocationTracker {
    private final Map<String, List<LocationObserver>> observers = new HashMap<>();

    public void subscribe(String rideId, LocationObserver observer) {
        observers.computeIfAbsent(rideId, k -> new CopyOnWriteArrayList<>()).add(observer);
    }

    public void updateLocation(String rideId, Location location, String source) {
        List<LocationObserver> rideObservers = observers.get(rideId);
        if (rideObservers != null) {
            rideObservers.forEach(o -> o.onLocationUpdate(rideId, location, source));
        }
    }

    public void unsubscribeAll(String rideId) {
        observers.remove(rideId);
    }
}

// ============================================================
// RIDE STATE — State pattern
// ============================================================

enum RideStatus { REQUESTED, MATCHED, IN_PROGRESS, COMPLETED, CANCELLED }

interface RideState {
    void matchDriver(Ride ride, String driverId);
    void startRide(Ride ride);
    void completeRide(Ride ride);
    void cancelRide(Ride ride, String reason);
    RideStatus getStatus();
}

class RequestedState implements RideState {
    @Override
    public void matchDriver(Ride ride, String driverId) {
        ride.setDriverId(driverId);
        System.out.println("Driver " + driverId + " matched to ride " + ride.getId());
        ride.setState(new MatchedState());
    }
    @Override public void startRide(Ride r) { System.out.println("Waiting for driver match"); }
    @Override public void completeRide(Ride r) { System.out.println("Ride hasn't started"); }
    @Override
    public void cancelRide(Ride ride, String reason) {
        System.out.println("Ride " + ride.getId() + " cancelled: " + reason);
        ride.setState(new CancelledState());
    }
    @Override public RideStatus getStatus() { return RideStatus.REQUESTED; }
}

class MatchedState implements RideState {
    @Override public void matchDriver(Ride r, String d) { System.out.println("Already matched"); }
    @Override
    public void startRide(Ride ride) {
        System.out.println("Ride " + ride.getId() + " started!");
        ride.setState(new InProgressState());
    }
    @Override public void completeRide(Ride r) { System.out.println("Ride hasn't started"); }
    @Override
    public void cancelRide(Ride ride, String reason) {
        System.out.println("Ride " + ride.getId() + " cancelled: " + reason);
        ride.setState(new CancelledState());
    }
    @Override public RideStatus getStatus() { return RideStatus.MATCHED; }
}

class InProgressState implements RideState {
    @Override public void matchDriver(Ride r, String d) { System.out.println("Ride in progress"); }
    @Override public void startRide(Ride r) { System.out.println("Already started"); }
    @Override
    public void completeRide(Ride ride) {
        double fare = ride.calculateFare();
        System.out.printf("Ride %s completed! Fare: $%.2f (%s pricing)%n",
                ride.getId(), fare, ride.getPricingStrategy().getName());
        ride.setState(new CompletedState());
    }
    @Override
    public void cancelRide(Ride ride, String reason) {
        System.out.println("Ride " + ride.getId() + " cancelled mid-trip: " + reason);
        ride.setState(new CancelledState());
    }
    @Override public RideStatus getStatus() { return RideStatus.IN_PROGRESS; }
}

class CompletedState implements RideState {
    @Override public void matchDriver(Ride r, String d) { done(); }
    @Override public void startRide(Ride r) { done(); }
    @Override public void completeRide(Ride r) { done(); }
    @Override public void cancelRide(Ride r, String reason) { done(); }
    @Override public RideStatus getStatus() { return RideStatus.COMPLETED; }
    private void done() { System.out.println("Ride is already completed"); }
}

class CancelledState implements RideState {
    @Override public void matchDriver(Ride r, String d) { done(); }
    @Override public void startRide(Ride r) { done(); }
    @Override public void completeRide(Ride r) { done(); }
    @Override public void cancelRide(Ride r, String reason) { done(); }
    @Override public RideStatus getStatus() { return RideStatus.CANCELLED; }
    private void done() { System.out.println("Ride is cancelled"); }
}

// ============================================================
// RIDE — aggregates all patterns
// ============================================================

class Ride {
    private final String id;
    private final String riderId;
    private String driverId;
    private final Vehicle vehicle;
    private final RideDetails details;
    private final PricingStrategy pricingStrategy;
    private RideState state;

    public Ride(String id, String riderId, Vehicle vehicle,
                RideDetails details, PricingStrategy pricing) {
        this.id = id;
        this.riderId = riderId;
        this.vehicle = vehicle;
        this.details = details;
        this.pricingStrategy = pricing;
        this.state = new RequestedState();
    }

    public void matchDriver(String driverId) { state.matchDriver(this, driverId); }
    public void startRide() { state.startRide(this); }
    public void completeRide() { state.completeRide(this); }
    public void cancelRide(String reason) { state.cancelRide(this, reason); }

    public double calculateFare() { return pricingStrategy.calculate(vehicle, details); }

    public String getId() { return id; }
    public void setDriverId(String driverId) { this.driverId = driverId; }
    public void setState(RideState state) { this.state = state; }
    public PricingStrategy getPricingStrategy() { return pricingStrategy; }
    public RideStatus getStatus() { return state.getStatus(); }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class RideSharingDemo {
    public static void main(String[] args) {
        VehicleFactory factory = new VehicleFactory();
        LocationTracker tracker = new LocationTracker();

        // Create a ride request
        Vehicle car = factory.create("premium", "V-001", "Tesla Model 3");
        RideDetails details = new RideDetails(8.5, 22, 1.5, 1); // 8.5 miles, 22 min, surge 1.5x

        Ride ride = new Ride("R-100", "rider-01", car, details, new SurgePricing());

        // Setup location tracking
        tracker.subscribe("R-100", new RiderAppObserver("rider-01"));
        tracker.subscribe("R-100", new DriverDashboardObserver());

        // Ride lifecycle
        System.out.println("=== Ride Lifecycle ===");
        ride.matchDriver("driver-42");
        tracker.updateLocation("R-100", new Location(37.7749, -122.4194), "driver");

        ride.startRide();
        tracker.updateLocation("R-100", new Location(37.7849, -122.4094), "driver");

        ride.completeRide();
        tracker.unsubscribeAll("R-100");

        // Pool ride example
        System.out.println("\n=== Pool Ride ===");
        Vehicle economy = factory.create("economy", "V-002", "Honda Civic");
        RideDetails poolDetails = new RideDetails(5.0, 15, 1.0, 3);
        Ride poolRide = new Ride("R-101", "rider-02", economy, poolDetails, new PoolPricing());
        poolRide.matchDriver("driver-55");
        poolRide.startRide();
        poolRide.completeRide();
    }
}
```

### Interview Talking Points

- **Pattern per concern:** Factory=what vehicle, Strategy=how much to charge, State=what can happen now, Observer=who needs real-time updates. Clean separation.
- **Real Uber/Lyft architecture:** Pricing uses dynamic strategy (ML-based surge). Ride states map to their state machines. Location uses pub/sub (Kafka in production).
- **Scalability:** Observer works in-process here. At Uber scale, location updates go through Kafka topics, and consumers (rider app, driver dashboard, ETA service) subscribe independently.
