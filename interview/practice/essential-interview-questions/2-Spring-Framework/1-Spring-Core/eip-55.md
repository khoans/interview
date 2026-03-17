55. What are the different bean scopes in Spring?

**Spring supports six bean scopes: singleton, prototype, request, session, application, and websocket. The first two work in any context; the last four are web-aware.**

Singleton creates one instance per Spring container — the default. Prototype creates a new instance every time you request the bean. Request, session, and application tie the bean lifecycle to HTTP request, user session, and ServletContext respectively.

**Trade-offs:**
- Singleton: Memory efficient, but must be thread-safe — no mutable instance state
- Prototype: Stateful, but Spring doesn't manage full lifecycle — no destruction callbacks
- Request/Session: Convenient for user state, but couples you to web context — can't use in batch jobs
- Application: Global across all requests, but rare — most "global" state should be singleton or external cache

**Real-world example:**
We built an e-commerce cart service. Originally singleton — one cart for all users. Disaster. Switched to session scope — one cart per user session. Worked, but sessions expired, carts lost. Final solution: prototype-scoped cart bean, but we persisted to Redis. Spring created new instances, Redis held the state. For audit logging, used singleton — stateless, thread-safe, logged everything asynchronously.

**Key insight:** 90% of beans should be singleton — stateless services, repositories, utilities. Use prototype when you need stateful beans. Request/session scopes are legacy thinking — in microservices, session state should be in Redis or JWT, not in Spring beans. Don't overuse scopes — if you need something beyond singleton, question your design first.
