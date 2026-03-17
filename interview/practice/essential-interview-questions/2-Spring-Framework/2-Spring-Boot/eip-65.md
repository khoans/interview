## 65. What is @SpringBootApplication annotation?

**@SpringBootApplication is a convenience annotation that combines @Configuration, @EnableAutoConfiguration, and @ComponentScan.**

It's the entry point for Spring Boot applications — one annotation instead of three.

**What it does:**
- `@Configuration`: Marks the class as a source of bean definitions
- `@EnableAutoConfiguration`: Tells Spring Boot to auto-configure based on classpath dependencies
- `@ComponentScan`: Scans the package and sub-packages for @Component, @Service, @Repository, @Controller

**Why it matters:**
- Reduces boilerplate — single annotation vs. three separate ones
- Convention over configuration: place your main class in the root package, everything gets scanned automatically
- Auto-configuration is the magic — DataSource, JPA, MVC all configured without XML

**Trade-off:**
- Simplicity but less control — if you need custom component scanning or want to disable auto-configuration, you must break it into the three separate annotations
- Default scan is package-based — moving your main class can accidentally exclude beans

**Real-world example:**
We had a monolith with 400+ beans. @SpringBootApplication scanned everything — startup took 90 seconds. Split into three annotations: @ComponentScan on specific packages, selective @EnableAutoConfiguration excludes (disabled JPA auto-config for one module). Startup dropped to 25 seconds. The combined annotation is great for microservices; large apps benefit from explicit configuration.

**Rule of thumb:** Use @SpringBootApplication for new services. Split it when you need fine-grained control or faster startup.
