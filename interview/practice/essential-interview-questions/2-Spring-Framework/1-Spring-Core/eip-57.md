57. What is the difference between @Component and @Bean?

**@Component marks a class as a Spring-managed bean — auto-detected via classpath scanning. @Bean is a method annotation that tells Spring the method's return value should be a bean — used in @Configuration classes.**

@Component is for your own classes — Spring finds them and registers them. @Bean is for third-party classes or when you need custom initialization logic — you explicitly create the bean in a configuration method.

**Trade-offs:**
- @Component: Less boilerplate, auto-registered, but limited control over creation
- @Bean: Full control — you can call constructors, set properties, conditionally create, but requires @Configuration class
- Use case: @Component for your services, repositories; @Bean for external libraries, DataSource, custom setup

**Real-world example:**
Our own services — UserService, OrderService — all use @Service (specialized @Component). Clean, auto-detected. But we integrated Stripe SDK — StripeClient isn't annotated, we can't modify it. Used @Bean in Config class: created StripeClient with API keys from properties, set timeouts, added retry logic. One place, full control.

**Key insight:** @Component says "Spring, manage this class." @Bean says "Spring, here's how to create this bean." Start with @Component for your code. Reach for @Bean when you need customization or third-party integration.
