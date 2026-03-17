## 70. What is @Value annotation?

**@Value injects external configuration properties into Spring beans — from application.properties, environment variables, or system properties.**

It's how you externalize configuration instead of hardcoding values.

**How it works:**
- `@Value("${property.key}")` injects a property value
- Supports SpEL: `@Value("#{systemProperties['user.home']}")`
- Default values: `@Value("${timeout:30}")` — uses 30 if property is missing
- Can inject into fields, constructors, or method parameters

**Why it matters:**
- Externalizes configuration — same code, different environments (dev, staging, prod)
- Enables runtime tuning without recompilation
- Integrates with Spring Cloud Config for centralized configuration management

**Trade-off:**
- Field injection with @Value makes testing harder — you can't instantiate the class without Spring context
- No type safety — typos in property names fail at runtime, not compile time
- Scattered configuration: hard to track which properties are used where

**Real-world example:**
We had 40 microservices, each with hardcoded timeouts and retry counts. Deploying to production required code changes — risky and slow. Migrated to @Value with environment-specific properties files. Deployment time dropped from 2 hours (code change + regression test) to 5 minutes (config change only). But we had 3 production incidents from typos in property names (`timeot` vs `timeout`). Now we use @ConfigurationProperties for complex config — type-safe, validated at startup.

**Rule of thumb:** Use @Value for simple, single properties. Use @ConfigurationProperties for grouped, related settings.
