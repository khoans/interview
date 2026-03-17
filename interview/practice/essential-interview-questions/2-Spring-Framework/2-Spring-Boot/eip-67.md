## 67. How does @EnableAutoConfiguration work?

**@EnableAutoConfiguration imports AutoConfigurationImportSelector, which reads META-INF/spring.factories and conditionally loads configuration classes based on your classpath.**

It's the engine behind Spring Boot's magic — scanning for libraries you have and auto-configuring beans accordingly.

**How it works:**
- `@EnableAutoConfiguration` uses `@Import(AutoConfigurationImportSelector.class)`
- Spring Boot scans all `META-INF/spring.factories` files on the classpath
- Each auto-configuration class has conditions (`@ConditionalOnClass`, `@ConditionalOnMissingBean`, `@ConditionalOnProperty`)
- Only configurations whose conditions match get loaded

**Why this design:**
- Decoupling: Auto-configurations are optional — they load only if their target classes exist
- Override-friendly: `@ConditionalOnMissingBean` means your custom bean wins
- Centralized: All auto-configurations listed in one file, easy to debug

**Trade-off:**
- Startup cost: Spring Boot evaluates 200+ conditions on every launch — adds 2-5 seconds
- Debugging complexity: When auto-config fails, you need `--debug` to see the condition evaluation report
- Classpath bloat: Accidentally including a library can trigger unwanted auto-configuration

**Real-world example:**
We added `spring-boot-starter-data-jpa` for one service — accidentally pulled JPA into a service that used JDBC templates. Auto-config created an EntityManager, tried to connect to a database that didn't exist. Service failed at startup. Took 4 hours to trace because the stack trace pointed to JDBC, not JPA. Now we use `@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)` for non-JPA services and audit dependencies with `mvn dependency:tree`.

**Rule of thumb:** Use `--debug` flag to see auto-config reports. Exclude what you don't need explicitly.
