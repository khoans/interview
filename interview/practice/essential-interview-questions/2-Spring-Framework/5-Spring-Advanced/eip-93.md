## 93. What is @Transactional annotation?

**@Transactional declares that a method or class should run within a database transaction — all-or-nothing execution with automatic rollback on failure.**

It's declarative transaction management — no manual `connection.commit()` or `connection.rollback()`.

**How it works:**
- Spring creates a proxy around the bean
- Before method: opens a transaction (or joins existing one)
- After method: commits on success, rolls back on RuntimeException
- Propagation controls nesting: REQUIRED (join), REQUIRES_NEW (always new), NESTED (savepoint)

**Why it matters:**
- Data integrity — partial updates don't corrupt the database
- Boilerplate elimination — no try-catch-rollback in every service method
- Composability — multiple DAO calls in one method become atomic

**Trade-off:**
- Hidden cost: @Transactional adds 5-10ms overhead per call (proxy + transaction management)
- Self-invocation trap: calling a @Transactional method from within the same class bypasses the proxy — no transaction
- Overuse: annotating entire classes starts transactions for read operations unnecessarily

**Real-world example:**
We had a fund transfer service — debit account A, credit account B. Originally two separate DAO calls. Network blip between calls: account A debited, account B not credited. $50K in limbo, 3 days to reconcile. Added @Transactional to the transfer method — both operations atomic. Zero data corruption incidents in 18 months. But we had a performance issue: a read-only report method was @Transactional (default READ_WRITE). Database held read locks unnecessarily, causing deadlocks under load. Changed to `@Transactional(readOnly=true)` — deadlocks dropped 90%. Now we audit @Transactional usage quarterly.

**Rule of thumb:** @Transactional on service methods (not DAOs, not controllers). Use `readOnly=true` for queries. Never call @Transactional methods from within the same class.
