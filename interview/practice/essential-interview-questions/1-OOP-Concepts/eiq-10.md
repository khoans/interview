# 10. What is SOLID principle and give example?

**Answer:** SOLID is five design principles for maintainable, extensible code. Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion. I use them as a checklist when reviewing code.

---

## S — Single Responsibility Principle

**One class, one reason to change.**

A class should have only one job. If it has multiple responsibilities, changes to one affect the other.

**Trade-off:** More classes, but each is simpler and focused.

**Example:** `UserService` handles user logic. `UserRepository` handles database. `UserValidator` handles validation. Before: one `UserManager` class with 500 lines doing everything. After: three 150-line classes. Easier to test, easier to change.

**Smell:** Class has methods for validation, persistence, business logic, and sending emails.

---

## O — Open/Closed Principle

**Open for extension, closed for modification.**

You should add new features by writing new code, not changing existing working code.

**Why:** Every time you modify working code, you risk breaking it. New tests needed. Regression bugs.

**Trade-off:** More abstraction upfront, but safer changes later.

**Concrete Example:**

Before (violates OCP):
```
processPayment(type, amount):
  if type == "credit": processCredit(amount)
  if type == "paypal": processPaypal(amount)
  if type == "bitcoin": processBitcoin(amount)
```
Adding a 4th payment method means modifying this function. Risk: break existing payments.

After (follows OCP):
```
Payment interface with process(amount)
CreditPayment, PaypalPayment, BitcoinPayment implement it
processPayment(payment): payment.process(amount)
```
Adding a 4th payment? New class only. Zero changes to existing code.

**Smell:** Switch statements or if-else chains checking types. Ask: "Do I need to touch working code to add this feature?"

---

## L — Liskov Substitution Principle

**Subtypes must be substitutable for base types without breaking behavior.**

If your code works with a parent class, it should work the same way with any child class.

**Why:** Polymorphism only works if children behave like parents expect.

**Trade-off:** Constrains how you design inheritance. Sometimes composition is better.

**Concrete Example:**

Violation:
```
Rectangle: setWidth(w), setHeight(h), getArea()
Square extends Rectangle: setWidth(w) also sets height to w
```
Code expecting Rectangle: `r.setWidth(5); r.setHeight(10);` expects area = 50.
Gets Square: area = 100. Behavior changed. Violation.

Fix: Square and Rectangle are siblings, both implement Shape. No inheritance between them.

**Real Example We Had:**

`FileStorage` with `save(data)`, `delete(path)`. `CloudStorage` extends it.

Problem: `delete()` in cloud storage required 2FA confirmation. Local storage didn't. Code calling `delete()` assumed instant deletion. Cloud storage broke the contract.

Fix: Both implement `Storage` interface. Code knows which it's using.

**Smell:** 
- `instanceof` checks before calling methods ("If it's X, do this. If Y, do that")
- `UnsupportedOperationException` in overrides ("This method doesn't work for this type")
- Documentation saying "This method has no effect for X subclass"

---

## I — Interface Segregation Principle

**Many specific interfaces beat one fat interface.**

Don't force clients to depend on methods they don't use.

**Trade-off:** More interfaces, but cleaner contracts.

**Example:** `Worker` interface with `work()`, `eat()`, `sleep()`. `Robot` implements `Worker` but can't eat. Split: `Workable`, `Eatable`, `Sleepable`. `Human` implements all three. `Robot` implements only `Workable`.

**Smell:** Interfaces with 10+ methods, or classes with empty method implementations.

---

## D — Dependency Inversion Principle

**Depend on abstractions, not concretions.**

High-level modules shouldn't depend on low-level modules. Both depend on abstractions.

**Trade-off:** More indirection, but swappable implementations.

**Example:** `OrderService` depends on `PaymentGateway` interface, not `StripePaymentGateway` class. Switching to PayPal means new implementation, zero changes to `OrderService`. We switched payment providers in 2 hours because of this.

**Smell:** `new` keyword inside business logic classes, or importing concrete implementations.

---

## SOLID Together — Real Example

**E-commerce Order System:**

**S:** `OrderService` (business logic), `OrderRepository` (persistence), `OrderValidator` (validation), `EmailService` (notifications).

**O:** `PaymentMethod` interface. New payment = new class, no `OrderService` changes.

**L:** All payment implementations honor the `PaymentMethod` contract. No surprises.

**I:** `Payable`, `Refundable`, `Transferable` interfaces. Bitcoin doesn't support refunds — implements only `Payable`.

**D:** `OrderService` depends on `PaymentMethod` interface, injected via constructor.

**Result:** Added 3 payment methods in 6 months. Zero bugs in existing payments.

---

## When NOT to Apply SOLID

**It depends on context:**

- **Small projects:** Full SOLID might be over-engineering
- **Prototypes:** Speed matters more than extensibility
- **Simple CRUD:** Don't abstract everything

**From experience:** I apply SOLID when:
- Requirements will change (they always do)
- Multiple developers on codebase
- System will live more than 6 months

---

## Interview Tip

Show you use SOLID as a tool, not religion:

> "SOLID is my checklist for code reviews. Single Responsibility — one job per class. Open/Closed — add features without modifying working code. Liskov — subtypes don't break expectations. Interface Segregation — no fat interfaces. Dependency Inversion — depend on abstractions. I used SOLID to refactor a 2000-line service into 10 focused classes. Maintenance time dropped 70%. But I don't apply SOLID to prototypes — it's about trade-offs, not dogma."
