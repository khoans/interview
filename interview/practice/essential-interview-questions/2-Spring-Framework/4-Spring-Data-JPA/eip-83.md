## 83. What is Spring Data JPA?

**Spring Data JPA is a repository abstraction layer on top of JPA — it eliminates boilerplate DAO code.**

Instead of writing EntityManager calls, you define interfaces and Spring Data generates implementations automatically.

**How it works:**
- Define `interface UserRepository extends JpaRepository<User, Long>`
- Get CRUD operations for free (save, findById, delete, findAll)
- Declare query methods: `findByEmail(String email)` — no implementation needed
- Custom queries with `@Query` annotation

**Why it matters:**
- 80% less boilerplate — no more repetitive DAO classes
- Method naming convention — query logic is self-documenting
- Pagination and sorting built-in — `Page<User> findAll(Pageable pageable)`

**Trade-off:**
- Convenience vs. control — complex queries require `@Query` or native SQL anyway
- Performance opacity — generated queries may fetch more than needed (N+1 problem)
- Learning curve — method naming rules (`findBy...`, `readBy...`, `getBy...` are all valid but inconsistent)

**Real-world example:**
We had a monolith with 50 DAO classes — each with 30 lines of identical EntityManager code. Migrated to Spring Data JPA: 50 interfaces, 5 lines each. Removed 1,200 lines of boilerplate. But our order search endpoint went from 80ms to 400ms — generated query did eager fetch on 5 associations. Fixed with `@EntityGraph` for selective eager loading. Now we use Spring Data for 90% of queries, hand-tuned JPQL for performance-critical paths.

**Rule of thumb:** Spring Data for CRUD and simple queries. `@Query` with fetch hints for complex reporting. Profile before committing.
