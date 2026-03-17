62. What is the bean lifecycle in Spring?

**Spring bean lifecycle is: instantiate → populate properties → initialize → use → destroy. Spring provides hooks at each stage via annotations and interfaces.**

Spring creates the bean instance, injects dependencies (@Autowired), calls initialization callbacks (@PostConstruct, InitializingBean), the bean is ready for use, then on shutdown Spring calls destruction callbacks (@PreDestroy, DisposableBean).

**Key callbacks:**
- @PostConstruct: After dependency injection — use for initialization logic
- @PreDestroy: Before bean destruction — use for cleanup (close connections, stop threads)
- BeanPostProcessor: Custom logic before/after initialization — advanced, used by Spring internally
- ApplicationContextAware: Get reference to container — use sparingly, couples to Spring

**Trade-offs:**
- @PostConstruct: Clean, standard (JSR-250), but limited — can't throw checked exceptions
- InitializingBean: More control, but Spring-specific coupling
- Destruction: Only for singleton beans — prototype beans aren't tracked for cleanup

**Real-world example:**
We had a cache manager that loaded data on startup. Used @PostConstruct to warm the cache — ran after dependencies injected. Had a scheduler that needed shutdown — @PreDestroy stopped the executor service gracefully. One bean post-processor validated all @Service beans had transactional annotations — caught bugs at startup, not runtime.

**Key insight:** Most beans don't need lifecycle callbacks — they're stateless, no setup or cleanup. Use @PostConstruct for initialization that depends on injected beans (can't do in constructor). Use @PreDestroy for resource cleanup. But keep it minimal — heavy initialization in @PostConstruct slows startup. Consider lazy initialization or async startup for heavy tasks.
