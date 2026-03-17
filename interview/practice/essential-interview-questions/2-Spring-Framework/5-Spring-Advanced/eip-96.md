## 96. What happens when a transactional method calls another transactional method?

**It depends on the propagation level — but by default (REQUIRED), they run in the same transaction.**

The outer transaction controls both. If the inner method fails, both roll back. If the outer fails later, the inner rolls back too.

**The proxy trap:** If both methods are in the same class, the inner `@Transactional` is ignored — Spring's proxy can't intercept self-calls.

**Trade-off:**
- Same transaction (REQUIRED): Atomic but coupled — inner failures always rollback outer
- New transaction (REQUIRES_NEW): Isolated but complex — need exception handling to prevent silent failures
- Self-call bypass: Looks like it should work but doesn't — requires refactoring to separate beans

**Real-world example:**
We had `placeOrder()` calling `updateInventory()` — both `@Transactional(REQUIRED)`. Inventory update failed mid-order, order rolled back correctly. But we added `logAudit()` with `@Transactional(REQUIRES_NEW)` — audit committed even when order failed. Problem: audit showed "order placed" for failed orders. Confused support team for weeks. Fix: changed audit to log before transaction starts, or include transaction status in the log.

**Rule of thumb:** Keep transactional boundaries at the service layer. Don't call `@Transactional` methods from within the same class — extract to a separate service if you need different propagation.
