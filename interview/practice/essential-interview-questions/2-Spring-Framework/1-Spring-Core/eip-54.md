54. What is a Spring Bean?

**A Spring Bean is any object that Spring IoC container instantiates, assembles, and manages. It's the fundamental unit of Spring application context.**

Beans are defined via @Component, @Service, @Repository, @Controller, or @Bean annotations. Spring creates them at startup (or on-demand), injects dependencies, and manages their lifecycle from creation to destruction.

**Trade-offs:**
- Singleton scope: Default, one instance per container — memory efficient but not thread-safe by default
- Prototype scope: New instance per request — stateful but more memory overhead
- Request/Session scope: Web-aware, tied to HTTP lifecycle — useful for user-specific state
- Lifecycle management: Spring calls @PostConstruct, @PreDestroy — convenient but adds Spring coupling

**Real-world example:**
We had a report generator class — stateless, used everywhere. Made it a @Service bean, Spring managed it. Then we added a dashboard with user-specific filters — needed per-user state. Changed to prototype scope for dashboard, kept singleton for reports. One bean definition, two scopes based on use case. The key: we didn't new up anything — Spring controlled lifecycle, we controlled business logic.

**Key insight:** A Spring Bean is just a POJO with Spring managing its lifecycle. The power isn't in the annotation — it's in what Spring does with it: dependency injection, AOP proxies, lifecycle callbacks. But remember: every @Bean adds to startup time and memory. Don't make everything a bean — only what needs Spring's management.
