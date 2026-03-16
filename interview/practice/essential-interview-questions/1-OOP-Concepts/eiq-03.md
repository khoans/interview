# 3. What is the difference between interface and abstract class?

**Answer:** Interface defines *what* you can do — a contract. Abstract class defines *what it is* — shared identity and behavior. Different purposes, not interchangeable.

---

## Interface
**Pure contract — no state, no implementation (mostly).**

Multiple classes can implement the same interface. Trade-off: maximum flexibility, but can't share code or state between implementers.

**Example:** `Comparable` interface — any class can implement it (`Person`, `Product`, `Date`). Doesn't matter what the class is, only that it can be compared. We used this for sorting — `Collections.sort()` works on any `Comparable`, doesn't need to know the type.

**When to use:**
- Unrelated classes need common capability (`Flyable` for `Bird`, `Airplane`, `Drone`)
- API design — hide implementation details
- Multiple inheritance of type needed

---

## Abstract Class
**Partial implementation — can have state, shared code, and abstract methods.**

Single inheritance only. Trade-off: can share code and reduce duplication, but locks you into one inheritance hierarchy.

**Example:** `Animal` abstract class with `name`, `age` fields and `eat()`, `sleep()` implementations. `Dog`, `Cat`, `Bird` extend it. All animals share basic behavior, but each has unique `makeSound()` implementation.

**When to use:**
- Related classes with shared behavior (`Vehicle` → `Car`, `Truck`, `Motorcycle`)
- Need to store common state
- Template method pattern — fixed algorithm with variations

---

## Key Differences

| Aspect | Interface | Abstract Class |
|--------|-----------|----------------|
| **Purpose** | Contract (can do) | Identity (is a) |
| **State** | None (only constants) | Can have fields |
| **Methods** | Abstract (mostly) | Both abstract and concrete |
| **Inheritance** | Multiple | Single |
| **Constructor** | No | Yes |
| **Access modifiers** | Public only | Any (private, protected, public) |
| **Performance** | Slightly slower (lookup) | Faster (direct call) |

---

## Real-World Scenario

**Payment System:**

**Interface:** `PaymentMethod` — `CreditCard`, `PayPal`, `Bitcoin` all implement it. Unrelated systems, same contract. `processPayment()` works on any `PaymentMethod`.

**Abstract Class:** `BasePayment` with common fields (`amount`, `timestamp`, `transactionId`) and methods (`getFee()`, `getReceipt()`). Concrete payments extend it to avoid duplicating these fields.

**Together:** Interface for polymorphism, abstract class for code reuse.

---

## Java 8+ Change

Default methods in interfaces blur the line, but principle remains:

- Interface: capability (what you can do)
- Abstract class: identity (what you are)

---

## Interview Tip

Show you understand *why* both exist:

> "Interface is for polymorphism — unrelated classes can be treated uniformly. Abstract class is for code reuse — related classes share common behavior. I use interface for API boundaries, abstract class when I have 3+ classes with 40%+ common code."
