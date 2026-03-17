## 64. What are the advantages of Spring Boot?

**Spring Boot eliminates boilerplate configuration and gets you to production faster with sensible defaults.**

Key advantages: auto-configuration, embedded servers, starter dependencies, actuator endpoints, and externalized configuration.

**Why it matters:**
- Auto-configuration: Spring Boot configures beans based on your classpath — no XML, minimal annotations
- Embedded servers: Run as `java -jar`, no WAR deployment, no application server setup
- Starters: One dependency pulls in everything you need (`spring-boot-starter-web`, `spring-boot-starter-data-jpa`)
- Actuator: Health checks, metrics, info endpoints out of the box — critical for production monitoring

**Trade-off:**
- Faster development but larger JARs (embedded Tomcat adds 10-15MB)
- Convention over configuration means fighting the framework for non-standard requirements
- Auto-configuration magic makes debugging harder when things break

**Real-world example:**
We migrated 12 microservices from Spring to Spring Boot. Deployment time dropped from 45 minutes (WAR to Tomcat) to 5 minutes (JAR to Docker). New service setup went from 2 days to 2 hours. But one service needed custom security chains — auto-configuration conflicts cost us 3 days to resolve. Now we use Spring Boot for 90% of services, pure Spring for edge cases with complex legacy requirements.

**Bottom line:** Spring Boot trades flexibility for velocity. For most teams, that's the right trade.
