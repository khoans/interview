53. What is Inversion of Control (IoC)?

**Inversion of Control is a design principle where control flow is inverted — instead of your code calling frameworks, the framework calls your code. Dependency injection is one implementation of IoC.**

Traditional code: you create objects, manage lifecycle, wire dependencies. IoC: a container (Spring) creates objects, manages lifecycle, injects dependencies. You write business logic; Spring handles infrastructure.

**Trade-offs:**
- Control: You give up object creation control, gain centralized configuration and lifecycle management
- Flexibility: Swap implementations without code changes, but adds indirection — harder to trace execution
- Testing: Easy to inject mocks, but tests require Spring context or manual wiring

**Real-world example:**
We built a notification system originally with direct instantiation — `new EmailSender()`, `new SmsSender()`. Adding push notifications meant modifying 15 classes. Refactored to Spring IoC — declared beans for each sender, injected `NotificationSender` interface. Added push provider in one file. The inversion: Spring decides which implementation, when to create it, how to configure it. Our code just uses it.

**Key insight:** IoC isn't just about dependency injection — it's about who controls the application flow. In Spring, you write POJOs, declare what you need, and Spring assembles everything. The trade-off: debugging is harder because wiring happens at runtime. But the benefit — loose coupling and testability — is worth it for any non-trivial application.
