## 84. What is the difference between JPA and Hibernate?

**JPA is a specification (interface). Hibernate is an implementation (concrete code).**

JPA defines *what* an ORM should do. Hibernate is *how* it gets done. You can swap Hibernate for EclipseLink or OpenJPA without changing your code — they all implement JPA.

**Why the distinction matters:**
- JPA: Standard API (`EntityManager`, `@Entity`, `@Query`) — portable across implementations
- Hibernate: JPA provider plus extra features — batch fetching, second-level cache, custom types
- You code to JPA, deploy with Hibernate

**Trade-off:**
- JPA gives you portability but limits you to lowest-common-denominator features
- Hibernate gives you advanced features but locks you in — migrating to another provider requires rewriting Hibernate-specific annotations
- Performance tuning often requires Hibernate-specific settings anyway

**Real-world example:**
We started a banking project using pure JPA — regulatory requirement said "vendor-neutral architecture." Six months in, we needed batch fetching for report generation (10K records). JPA didn't support it well. Switched to Hibernate-specific `@BatchSize` — query time dropped from 8 seconds to 400ms. But we violated the portability requirement. Compromise: JPA for core transactions (might change providers), Hibernate features for reporting (performance-critical, unlikely to migrate). Most teams just use Hibernate directly — portability is a nice-to-have, not a real requirement.

**Rule of thumb:** Use JPA annotations for entity mapping. Use Hibernate features when you need them — just document the dependency clearly.
