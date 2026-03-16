# 2. What is the difference between abstraction and encapsulation?

**Answer:** Abstraction hides complexity — *what* it does. Encapsulation hides data — *how* it stores state. Different problems, different techniques.

---

## Abstraction
**Hides implementation complexity, exposes essential interface.**

You interact with *what* something does, not *how*. Trade-off: adds indirection layers, but enables swapping implementations without breaking callers.

**Example:** Car steering wheel — you turn it left/right, don't need to know about rack-and-pinion, power steering pump, or hydraulic systems. In code: `List` interface — you call `add()`, `get()`, don't know if it's ArrayList (array) or LinkedList (nodes).

**Why it matters:** Reduces cognitive load. You can work on business logic without understanding database internals.

---

## Encapsulation
**Bundles data with methods, restricts direct access.**

Data is private, accessed only through controlled methods. Trade-off: more boilerplate (getters/setters), but prevents invalid state and catches bugs early.

**Example:** Capsule — medicine inside, shell protects it. In code: `Person` class with private `age` field, public `setAge()` that validates age > 0. Can't accidentally set negative age.

**Why it matters:** Protects invariants. Validation happens in one place, not scattered across codebase.

---

## Key Differences

| Aspect | Abstraction | Encapsulation |
|--------|-------------|---------------|
| **Hides** | Complexity | Data/State |
| **Focus** | Interface (what) | Implementation (how) |
| **Achieved via** | Interfaces, abstract classes | Access modifiers (private, protected) |
| **Solves** | "Too much to know" | "Too much access" |
| **Example** | `List` interface | Private fields with getters/setters |

---

## Real-World Scenario

**ATM Machine:**

**Abstraction:** You see buttons for "Withdraw", "Check Balance", "Transfer". Don't know about backend systems, database queries, or network protocols.

**Encapsulation:** Your account balance is hidden. Can't directly modify it. Must go through `withdraw()` or `deposit()` which validate and log transactions.

**Together:** Abstraction lets you use ATM without training. Encapsulation prevents fraud and errors.

---

## Interview Tip

Don't just define — explain the *problem each solves*:

> "Abstraction manages complexity — I can use `Collections.sort()` without knowing quicksort internals. Encapsulation protects state — my `BankAccount` can't go negative because `withdraw()` validates first. One is about interface, the other about data protection."
