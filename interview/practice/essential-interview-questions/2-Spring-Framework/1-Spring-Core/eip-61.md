61. What is @Primary annotation used for?

**@Primary marks a bean as the default candidate when multiple beans of the same type exist — it's the fallback when no @Qualifier is specified.**

When Spring sees multiple beans of the same type, it doesn't know which to inject. @Primary tells Spring: "This is the default one." You can still use @Qualifier to inject the non-primary bean when needed.

**Trade-offs:**
- Default behavior: One primary bean, others are explicit — clean for 80/20 use cases
- Flexibility: Can override with @Qualifier when you need the non-default
- Single primary: Only one @Primary per type — multiple primaries cause ambiguity again
- Alternative: @Qualifier everywhere is explicit but verbose

**Real-world example:**
We had MySQL for production, H2 for testing — both implement DataSource. Didn't want @Qualifier on every repository. Marked MySQL DataSource as @Primary in production config, H2 as @Primary in test config. Most injections got the right one automatically. One report needed both — used @Qualifier("mysqlDataSource") there.

**Key insight:** @Primary is about setting sensible defaults. Use it when one implementation is the common case — production database, main payment provider, default email sender. It reduces @Qualifier noise. But don't overuse — if you have 5 implementations with no clear default, @Primary hides the complexity. Make it explicit with @Qualifier instead.
