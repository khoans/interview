32. What is a functional interface? Give examples.

**A functional interface has exactly one abstract method — it's the target type for lambda expressions and method references.**

Annotated with `@FunctionalInterface` (optional but recommended). The compiler enforces the "one abstract method" rule — if you add a second, it won't compile.

**Key examples:**
- `Predicate<T>`: Takes one argument, returns boolean — used for filtering
  - Example: `Predicate<Order> isValid = o -> o.getAmount() > 0`
  - Usage: `orders.stream().filter(isValid).collect(...)`
- `Function<T,R>`: Transforms T to R — used for mapping
  - Example: `Function<String, Long> parse = s -> Long.valueOf(s)`
  - Usage: `ids.stream().map(parse).collect(...)`
- `Consumer<T>`: Takes one argument, returns nothing — used for side effects
  - Example: `Consumer<Order> log = o -> logger.info("Processing: " + o.getId())`
  - Usage: `orders.forEach(log)`
- `Supplier<T>`: Takes nothing, returns T — used for lazy generation
  - Example: `Supplier<LocalDate> today = () -> LocalDate.now()`
  - Usage: `LocalDate date = today.get()` (called on-demand)
- `Comparator<T>`: Compares two objects, returns int — used for sorting
  - Example: `Comparator<Order> byDate = Comparator.comparing(Order::getCreatedAt)`
  - Usage: `orders.stream().sorted(byDate).collect(...)`

**Trade-offs:**
- Lambdas: Concise but less explicit — harder to read if the interface isn't clear
- Method references: Cleanest when the method already exists (`String::valueOf`)
- Custom functional interfaces: More descriptive names, but adds classes

**Real-world example:**
We built a validation pipeline using `Predicate<Order>` — each validator was a lambda checking one rule (`o -> o.getAmount() > 0`, `o -> o.getCustomerId() != null`). Chained them with `and()`/`or()`. Made validation rules composable and testable. Could swap validators without touching the pipeline logic.

**Key insight:** Functional interfaces turn behavior into data — you can pass logic as parameters. But don't overuse them. If a lambda is more than one line, extract it to a named method.

**Custom functional interface example:**

```java
@FunctionalInterface
public interface OrderValidator {
    boolean validate(Order order);
    
    // Default method to combine validators
    default OrderValidator and(OrderValidator other) {
        return o -> this.validate(o) && other.validate(o);
    }
}

// Usage:
OrderValidator amountCheck = o -> o.getAmount() > 0;
OrderValidator customerCheck = o -> o.getCustomerId() != null;
OrderValidator combined = amountCheck.and(customerCheck);

boolean isValid = combined.validate(order);
```

**Why custom?** Built-in interfaces don't always express intent. `OrderValidator` is clearer than `Predicate<Order>` — the name documents the purpose. We used this pattern for validation rules, pricing strategies, and notification filters.

**Built-in functional interface method names:**

Each functional interface exposes one abstract method — that's what the lambda implements:

```java
// Predicate<T> — method: boolean test(T t)
Predicate<Order> isValid = o -> o.getAmount() > 0;
isValid.test(order);  // returns true/false

// Function<T,R> — method: R apply(T t)
Function<String, Long> parse = s -> Long.valueOf(s);
parse.apply("123");  // returns Long 123

// Consumer<T> — method: void accept(T t)
Consumer<Order> log = o -> System.out.println("Order: " + o.getId());
log.accept(order);  // prints "Order: 12345"

// Supplier<T> — method: T get()
Supplier<LocalDate> today = () -> LocalDate.now();
today.get();  // returns current date (e.g., 2026-03-16)

// Comparator<T> — method: int compare(T o1, T o2)
Comparator<Order> byDate = Comparator.comparing(Order::getCreatedAt);
byDate.compare(order1, order2);  // returns -ve/0/+ve based on date order
```

**Key insight:** The method name matters for readability. `predicate.test(order)` is vague. `validator.validate(order)` is explicit. That's why custom interfaces help — you name the method for your domain.
