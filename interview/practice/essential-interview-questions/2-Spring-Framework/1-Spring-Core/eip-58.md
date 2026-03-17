58. What is @Autowired? How does it work?

**@Autowired tells Spring to inject a dependency by type — Spring finds a matching bean in the application context and injects it.**

Spring scans your class for @Autowired fields, constructors, or setters. At runtime, it looks up the required type in the application context and injects the bean. If multiple beans match, it fails unless you use @Qualifier.

**Trade-offs:**
- Constructor @Autowired: Explicit dependencies, immutable, testable — recommended
- Field @Autowired: Concise but hides dependencies, harder to test without Spring
- Setter @Autowired: Optional dependencies, but object can be in incomplete state
- Required by default: Fails if bean not found — use required=false for optional

**Real-world example:**
We had a PaymentService with @Autowired on a field — worked fine until we added PayPal alongside Stripe. Two beans of type PaymentGateway, Spring didn't know which to inject. Application failed at startup. Fixed with constructor injection + @Qualifier("stripePaymentGateway"). Explicit, clear, and testable.

**Key insight:** @Autowired is Spring's magic for wiring beans. The magic is convenient but can obscure what your class actually needs. Use constructor injection — it makes dependencies visible in the API. And remember: @Autowired on constructors is optional in Spring 4.3+ if there's only one constructor.
