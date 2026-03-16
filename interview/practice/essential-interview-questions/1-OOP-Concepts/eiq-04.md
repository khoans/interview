# 4. When would you use an abstract class vs an interface?

**Answer:** It depends on the relationship. Interface for capabilities across unrelated classes. Abstract class for shared identity and behavior among related classes.

---

## Use Interface When

**Unrelated classes need common capability:**

`Flyable` interface for `Bird`, `Airplane`, `Drone`. They share no common ancestry, but all can fly. Trade-off: flexible, but each class implements logic from scratch.

**You need multiple inheritance of type:**

A class can implement `Comparable`, `Serializable`, `Cloneable` simultaneously. Java doesn't allow extending multiple classes, but multiple interfaces work fine.

**API design — hiding implementation:**

`List`, `Set`, `Map` interfaces. Caller doesn't know if it's ArrayList, LinkedList, HashMap. Lets you swap implementations without breaking code.

**Rule of thumb:** If a class can "be" multiple things (a `Dog` is an `Animal`, `Comparable`, `Serializable`), use interfaces for the capabilities.

---

## Use Abstract Class When

**Related classes with shared behavior:**

`Vehicle` abstract class with `start()`, `stop()`, `fuelLevel` field. `Car`, `Truck`, `Motorcycle` extend it. All vehicles share core behavior and state.

**You have 3+ classes with 40%+ common code:**

We had `EmailNotification`, `SMSNotification`, `PushNotification` — all needed `recipient`, `timestamp`, `send()`, `retry()`. Extracted to `BaseNotification` abstract class. Reduced code by 60%.

**Template method pattern:**

Fixed algorithm with variations. `DataProcessor` abstract class with `load() → process() → save()` flow. Subclasses override only `process()`. Ensures consistent workflow.

**You need to store state:**

Abstract class can have fields (`id`, `createdAt`, `status`). Interface can't. If common state exists, abstract class is the choice.

---

## Decision Framework

```
Are the classes related by "is-a"?
├─ Yes → Do they share 40%+ code or state?
│  ├─ Yes → Abstract Class
│  └─ No → Interface
└─ No → Interface (unrelated classes)

Do you need multiple inheritance of type?
└─ Yes → Interface (must use)

Do you need to store common state?
└─ Yes → Abstract Class (only option)
```

---

## Real-World Scenario

**E-commerce Payment System:**

**Interface:** `PaymentMethod` — `CreditCard`, `PayPal`, `Bitcoin` implement it. Unrelated systems, same contract. Checkout processes any `PaymentMethod`.

**Abstract Class:** `BasePayment` with `amount`, `transactionId`, `timestamp`, `getReceipt()`, `refund()`. All payment types extend it to avoid duplicating these.

**Together:** Interface for polymorphism, abstract class for code reuse. Not competing choices — they solve different problems.

---

## Interview Tip

Show decision-making, not memorization:

> "I use interface when unrelated classes need a common capability — like `Comparable` for sorting. Abstract class when related classes share 40%+ code or state — like `Vehicle` for cars and trucks. If I need both, I use interface for the contract and abstract class for shared implementation. They're complementary, not alternatives."
