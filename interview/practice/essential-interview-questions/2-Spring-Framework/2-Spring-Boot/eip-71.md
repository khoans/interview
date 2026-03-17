## 71. What is @ConfigurationProperties?

**@ConfigurationProperties binds external configuration properties to a type-safe Java class — validated at startup.**

It's the structured alternative to @Value for grouped, related settings.

**How it works:**
- Annotate a POJO with `@ConfigurationProperties(prefix="app.datasource")`
- Spring Boot binds matching properties to fields automatically
- Supports nested properties, lists, maps
- Add `@Validated` and JSR-303 annotations for validation

**Why it matters:**
- Type safety — compiler catches typos, IDE provides autocomplete
- Centralized configuration — one class shows all available properties
- Validation at startup — fail fast if required properties are missing
- Easier testing — instantiate the class directly, no Spring context needed

**Trade-off:**
- More boilerplate than @Value — you need a separate class
- Slightly more complex for single-property injection
- Requires Spring Boot — not available in plain Spring

**Real-world example:**
We had a payment service with 15 @Value annotations scattered across 3 classes — timeouts, retry counts, circuit breaker thresholds. A typo in `payment.retry.attempts` vs `payment.retry.attempt` caused a production outage (defaulted to 0 retries). Migrated to @ConfigurationProperties with `@Validated` — one `PaymentProperties` class, validation ensures `retryCount > 0`. Caught 4 configuration errors at startup in the first month. Zero typos since.

**Rule of thumb:** Use @Value for 1-2 simple properties. Use @ConfigurationProperties for anything more complex or when validation matters.
