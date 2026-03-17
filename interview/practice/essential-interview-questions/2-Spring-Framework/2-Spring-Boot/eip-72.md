## 72. What are Spring Boot starters?

**Spring Boot starters are curated dependency descriptors — one dependency pulls in everything you need for a specific feature.**

Instead of hunting for compatible library versions, you add one starter and Spring Boot handles the rest.

**How they work:**
- `spring-boot-starter-web` → Spring MVC, Tomcat, Jackson, validation
- `spring-boot-starter-data-jpa` → Hibernate, Spring Data JPA, connection pooling
- `spring-boot-starter-test` → JUnit, Mockito, Hamcrest, Spring Test
- Over 50 starters available, plus you can create custom ones

**Why they matter:**
- Eliminates dependency hell — versions are tested together
- Reduces pom.xml from 20+ dependencies to 3-4 starters
- Auto-configuration kicks in based on what's on the classpath

**Trade-off:**
- Convenience vs. bloat — starters pull in transitive dependencies you may not need (larger JARs)
- Less control over individual library versions — you must override explicitly
- Can hide what's actually in your application — security audits become harder

**Real-world example:**
Our team spent 2 days resolving version conflicts between Hibernate, Jackson, and Spring Data — incompatible versions caused runtime failures. Switched to starters: `spring-boot-starter-data-jpa`, `spring-boot-starter-web`. All version conflicts disappeared — Spring Boot's BOM manages compatibility. Build time dropped 30%, deployment failures dropped to zero. But our JAR grew from 25MB to 40MB due to transitive dependencies. We now use `mvn dependency:tree` to audit and exclude unused transitive deps.

**Rule of thumb:** Start with starters. Optimize with exclusions only after measuring actual impact.
