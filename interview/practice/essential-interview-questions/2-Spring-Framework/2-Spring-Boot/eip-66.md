## 66. What is auto-configuration in Spring Boot?

**Auto-configuration is Spring Boot's mechanism to automatically configure beans based on your classpath dependencies.**

If you have `spring-boot-starter-data-jpa` and a database driver, Spring Boot auto-configures a DataSource, EntityManager, and JPA repositories — zero XML.

**How it works:**
- `@EnableAutoConfiguration` triggers classpath scanning
- Spring Boot checks what's available (H2? MySQL? Thymeleaf?) and configures appropriate beans
- Conditional annotations (`@ConditionalOnClass`, `@ConditionalOnMissingBean`) decide what to configure

**Why it matters:**
- Eliminates 80% of boilerplate configuration
- Sensible defaults: H2 for dev, connection pooling with HikariCP, Jackson for JSON
- You override only what you need — custom DataSource bean replaces the auto-configured one

**Trade-off:**
- Magic vs. control — when auto-configuration breaks, debugging requires understanding conditional logic
- Startup overhead: Spring Boot evaluates 200+ conditions on every launch
- Can configure beans you don't need — larger memory footprint

**Real-world example:**
We had 15 microservices, each with identical DataSource config — 300 lines of duplicated XML. Auto-configuration reduced it to `spring.datasource.url` in properties. But one service needed custom connection pooling — auto-config fought us for 2 days until we used `@ConditionalOnMissingBean` to override properly. Now we document: auto-config for 80%, explicit beans for the 20% that needs tuning.

**Rule of thumb:** Trust auto-configuration until you have a specific requirement it can't meet. Then override explicitly.
