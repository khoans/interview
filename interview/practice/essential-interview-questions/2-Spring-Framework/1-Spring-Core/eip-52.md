52. What are the types of dependency injection?

**Spring supports three types of dependency injection: constructor injection, setter injection, and field injection.**

Constructor injection passes dependencies via the class constructor. Setter injection uses setter methods. Field injection uses @Autowired directly on fields via reflection.

**Trade-offs:**
- Constructor injection: Dependencies are explicit and immutable, object is always in valid state. Downside: verbose with 5+ parameters
- Setter injection: Optional dependencies, can change at runtime. Downside: object can exist in incomplete state
- Field injection: Minimal boilerplate. Downside: hides dependencies, impossible to instantiate without Spring, harder to test

**Real-world example:**
We started with field injection everywhere — looked clean, 30% less code. Then we had a class with 8 @Autowired fields. No idea what it actually needed. Refactored to constructor injection — split into 3 smaller classes. The ones with 2-3 dependencies stayed as constructor injection. One had an optional cache — used setter injection there. Field injection? Deleted all of it.

**Key insight:** Constructor injection is the default choice — it's explicit, testable, and enforces dependencies. Use setter injection only for truly optional dependencies. Avoid field injection in production code — it couples you to Spring and makes unit tests harder. Spring team recommends constructor injection for mandatory dependencies.
