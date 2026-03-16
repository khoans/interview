# 1. What are the 4 principles of OOP? Explain each with examples.

**Answer:** Encapsulation, Abstraction, Inheritance, and Polymorphism. They solve coupling and complexity problems.

---

## Encapsulation
Bundle data with methods, hide internal state. You control what's exposed and validate changes.

**Trade-off:** More boilerplate, but prevents invalid states.

**Example:** BankAccount — balance is private, you call `withdraw()` which validates before changing. Can't accidentally set negative balance.

**Why it matters:** Catches bugs at compile time instead of production.

---

## Abstraction
Hide complexity, expose only essentials. You work with *what* something does, not *how*.

**Trade-off:** Adds indirection layers, but enables swapping implementations.

**Example:** Repository interface — business logic calls `save()`, doesn't know if it's MySQL, PostgreSQL, or MongoDB. We switched databases without touching business logic.

**Why it matters:** Lets you change implementation without rewriting callers.

---

## Inheritance
Child class gets parent's behavior, can override or extend. Models "is-a" relationships.

**Trade-off:** Tight coupling — change parent, break children. Fragile base class problem.

**Example:** Vehicle → Car, Truck, Motorcycle. All share `start()`, `stop()`, `fuel()`. But we learned: prefer composition. Use inheritance only for true "is-a" with shared behavior, not just code reuse.

**Why it matters:** Reduces duplication, but use sparingly.

---

## Polymorphism
One interface, multiple implementations. Same call behaves differently based on object type.

**Trade-off:** Flexibility vs. traceability — harder to know which implementation runs.

**Example:** Payment interface with CreditCard, PayPal, Bitcoin. Checkout calls `payment.process()` — doesn't know which type. Adding new payment method means new class, zero changes to checkout code.

**Why it matters:** Open/Closed Principle — open for extension, closed for modification.

---

## Key Insight

These aren't academic concepts — they solve real problems:

| Principle | Problem Solved | Real Impact |
|-----------|----------------|-------------|
| Encapsulation | Invalid state bugs | Catches errors early |
| Abstraction | Implementation lock-in | Swap without rewriting |
| Inheritance | Code duplication | Reuse shared behavior |
| Polymorphism | Rigid code | Add features without modification |

**Interview tip:** When asked this, don't just define — explain *why* each exists and *when* you've used it. That separates seniors from juniors.
