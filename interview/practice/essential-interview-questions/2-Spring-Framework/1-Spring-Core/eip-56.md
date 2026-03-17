56. What is the default bean scope in Spring?

**The default bean scope in Spring is singleton — one instance per Spring container.**

Every time you request the bean (via @Autowired or getBean()), Spring returns the same shared instance. This applies to all stereotype annotations (@Component, @Service, @Repository, @Controller) and @Bean methods without explicit scope.

**Trade-offs:**
- Memory efficient: One instance regardless of how many times it's injected
- Thread safety: Must be stateless — no mutable instance variables
- Performance: No creation overhead after initial instantiation
- Testing: Same instance across tests unless context is refreshed

**Real-world example:**
We had a UserService with an instance variable `currentUser` — meant to hold the logged-in user. With singleton scope, User A's data leaked to User B. Fixed by removing instance state — passed user context as method parameters. For stateful beans like shopping carts, we explicitly set scope to prototype.

**Key insight:** Singleton is the right default — most Spring beans are stateless services. If you find yourself needing instance variables, you probably need prototype scope or external state storage (session, database, cache). But first, ask: can I make this stateless?
