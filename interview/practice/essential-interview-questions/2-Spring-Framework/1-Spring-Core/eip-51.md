51. What is dependency injection? What are its benefits?

**Dependency injection is a pattern where objects receive their dependencies from external sources rather than creating them internally. It's the foundation of Spring's Inversion of Control.**

Instead of `new Service()` inside your class, Spring injects the service instance — typically via constructor, setter, or field injection. You declare what you need; Spring provides it.

**Benefits:**
- Loose coupling: Classes don't depend on concrete implementations, just interfaces
- Testability: Swap real dependencies with mocks during testing
- Flexibility: Change implementations without modifying the dependent class
- Centralized configuration: All wiring happens in one place (Spring context)

**Trade-offs:**
- Constructor injection: Explicit, immutable dependencies, but verbose with many parameters
- Setter injection: Optional dependencies, but object can be in incomplete state
- Field injection: Concise, but hides dependencies, harder to test without Spring

**Real-world example:**
Our payment service originally instantiated `PaymentGateway` directly — hardcoded to Stripe. When we needed PayPal support, we had to modify 20 classes. Refactored to constructor injection with `PaymentGateway` interface. Stripe and PayPal became separate implementations. Added a factory to select based on user preference. New payment providers became plug-and-play — no existing code changes. Unit tests improved too — injected mock gateway, zero network calls.

**Key insight:** Dependency injection isn't just about Spring magic — it's about control. You control which implementation, which lifecycle, which configuration. Start with constructor injection — it's explicit and testable. Use setter injection only for optional dependencies. Avoid field injection in production code — it's testing-friendly but hides your class's actual requirements.
