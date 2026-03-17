## 92. What are cross-cutting concerns?

**Cross-cutting concerns are functionality that affects multiple parts of an application but doesn't belong to any single business domain.**

They "cut across" your class hierarchy — logging, security, transactions, caching, error handling. Every service needs them, but they're not the service's responsibility.

**Common examples:**
- **Logging** — every method needs request/response logging
- **Security** — authentication and authorization checks
- **Transactions** — database commit/rollback logic
- **Caching** — cache-aside patterns for expensive operations
- **Metrics** — timing, counters, health checks

**Why they matter:**
- Without separation, cross-cutting concerns become scattered — same logging code in 50 classes
- Violates Single Responsibility Principle — a `UserService` shouldn't care about SQL transactions
- Hard to change — modifying logging format requires touching 100 files

**Trade-off:**
- Centralizing cross-cutting concerns adds indirection — harder to trace what code actually runs
- Over-abstraction: wrapping everything in aspects makes the system opaque
- Testing complexity — aspects may not apply in unit tests without proper configuration

**Real-world example:**
Our e-commerce platform had security checks in every controller — `if (!user.isAdmin()) return 403`. Duplicated 80 times. When we added a new role (warehouse manager), we spent 3 days finding and updating every check. Refactored to a security aspect with `@PreAuthorize("hasRole('ADMIN')")`. New role took 10 minutes — just update the annotation config. But QA missed a test case: the aspect didn't apply to private methods. A security bypass allowed warehouse managers to access admin endpoints. Fixed by documenting: aspects apply to public methods only, move sensitive logic to separate beans.

**Rule of thumb:** Identify cross-cutting concerns early. Use AOP or Spring's built-in support (@Transactional, @PreAuthorize). Test aspects explicitly — don't assume they work.
