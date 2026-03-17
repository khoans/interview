# 8. What is the difference between composition and inheritance?

**Answer:** Inheritance is "is-a" — tight coupling, compile-time. Composition is "has-a" — loose coupling, runtime flexibility. Favor composition, but inheritance has its place.

---

## Inheritance

**"Is-a" relationship — child extends parent.**

Child class gets parent's behavior automatically. Trade-off: simple to use, but tight coupling. Change parent, risk breaking all children. Fragile base class problem.

**Example:** `Dog` extends `Animal`. Dog is an Animal — natural relationship. We used this for UI components — `Button`, `TextBox`, `Dropdown` all extend `Component`. Shared `render()`, `click()`, `disable()`.

**When it works:**
- True "is-a" relationship
- Parent controls what children can override
- Few levels of hierarchy (max 2-3)

**When it breaks:**
- Deep hierarchies (4+ levels) — hard to trace behavior
- Parent changes break unrelated children
- Can't change behavior at runtime

---

## Composition

**"Has-a" relationship — delegate behavior to contained objects.**

Object holds references to other objects, delegates work. Trade-off: more boilerplate (forwarding methods), but loose coupling and runtime flexibility.

**Example:** `Car` has `Engine`, `Transmission`, `Brakes`. Car isn't an Engine — it has one. We delegate `start()` to `Engine.start()`, `shift()` to `Transmission.shift()`. Can swap engine at runtime (electric vs gas).

**When it works:**
- Behavior varies independently
- Need runtime flexibility
- Multiple behaviors needed (can't inherit from multiple classes)

**When it's overkill:**
- Simple, stable hierarchies
- No behavior variation needed

---

## Key Differences

| Aspect | Inheritance | Composition |
|--------|-------------|-------------|
| **Relationship** | Is-a | Has-a |
| **Coupling** | Tight | Loose |
| **Flexibility** | Compile-time | Runtime |
| **Code Reuse** | Automatic (inherits) | Manual (delegate) |
| **Multiple behaviors** | No (single inheritance) | Yes (multiple fields) |
| **Testing** | Hard (parent dependencies) | Easy (mock components) |
| **Refactoring** | Risky (breaks children) | Safe (encapsulated) |

---

## Real-World Scenario

**Employee Management System:**

**Inheritance (problematic):**
`Employee` → `Manager`, `Engineer`, `Salesperson`. Then we need `PartTimeEmployee`, `Contractor`, `RemoteEmployee`. Explosion: `PartTimeRemoteEngineer`? Can't handle all combinations.

**Composition (better):**
`Employee` has `Role` (Engineer, Manager), `EmploymentType` (FullTime, PartTime, Contract), `WorkLocation` (Office, Remote). Mix and match at runtime. Add new role without touching existing code.

**Result:** 10 classes with inheritance vs 4 classes with composition.

---

## Why "Favor Composition Over Inheritance"?

**From experience:**

1. **Easier to change** — Swap components without breaking callers
2. **Better testing** — Mock components independently
3. **No fragile base class** — Changes stay encapsulated
4. **Runtime flexibility** — Change behavior without new classes

**But inheritance isn't evil:**

- Framework code (Spring, Hibernate) uses inheritance heavily
- Template method pattern needs it
- True "is-a" with stable hierarchy is fine

---

## Interview Tip

Show you understand trade-offs, not dogma:

> "I default to composition — it's more flexible and testable. But I've used inheritance successfully for UI components and domain entities with clear 'is-a' relationships. The key is: inheritance for stable hierarchies, composition when behavior varies. I've seen both overused — deep inheritance trees and composition everywhere adding unnecessary complexity."
