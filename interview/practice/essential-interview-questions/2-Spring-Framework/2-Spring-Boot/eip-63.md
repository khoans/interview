## 63. What is Spring Boot? How is it different from Spring?

**Spring Boot is Spring with conventions over configuration — it auto-configures based on your dependencies.**

Spring is the framework. Spring Boot is the opinionated wrapper that eliminates boilerplate setup.

**Key differences:**
- Spring: You configure beans, XML, component scanning, view resolvers manually
- Spring Boot: `@SpringBootApplication` + starter dependencies = working app in 5 minutes
- Spring Boot provides embedded servers (Tomcat, Jetty), actuator endpoints, externalized config out of the box

**Trade-off:**
- Spring Boot speeds up development but hides magic — debugging auto-configuration failures can be frustrating
- Less control over fine-grained configuration; you fight the framework if you need non-standard setups

**Real-world example:**
Our team spent 3 days setting up a Spring MVC project — XML config, dependency management, server deployment. Switched to Spring Boot: `spring-boot-starter-web`, one `application.properties`, `mvn spring-boot:run` — working REST API in 20 minutes. But when we needed custom security chains, we spent 2 days deciphering auto-config vs. manual beans. Now we use Spring Boot defaults for 80% of services, pure Spring for complex security/legacy integrations.

**Bottom line:** Spring Boot is Spring with batteries included. Start with Boot; drop to Spring when you need control.
