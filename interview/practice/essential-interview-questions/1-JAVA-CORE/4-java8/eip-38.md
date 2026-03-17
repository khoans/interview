## 38. What are default methods in interfaces?

**Default methods allow interfaces to provide a concrete implementation — classes inherit it automatically.**

Introduced in Java 8 to enable interface evolution without breaking existing implementations.

**Why they exist:**
- Before Java 8, adding a method to an interface broke all implementers
- Default methods let you extend interfaces backward-compatibly
- Critical for adding features to Collections API (stream(), parallelStream()) without breaking every List implementation

**Trade-off:**
- Enables API evolution but introduces multiple inheritance complexity
- If a class implements two interfaces with the same default method, it must override and resolve the conflict

**Real-world example:**
We built a plugin architecture with a `Processor` interface. Version 1 had `process()`. Version 2 needed `validate()` — without default methods, we'd break 40+ existing plugins. Made `validate()` a default method returning `true` — existing plugins continued working, new ones could override. Saved us a major version bump and 3 weeks of coordinating with plugin authors.

**Key insight:** Default methods are for optional behavior or sensible defaults — not for core contract methods.
