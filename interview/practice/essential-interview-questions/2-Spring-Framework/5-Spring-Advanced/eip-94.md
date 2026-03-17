## 94. What are the propagation levels in @Transactional?

**Propagation levels control what happens when a @Transactional method calls another @Transactional method.**

Do they share the same transaction? Does the inner method start a new one? Can the inner method commit even if the outer fails?

---

### The Problem Propagation Solves

Imagine this:

```
Method A (transactional)
  └─ calls Method B (transactional)
```

**Question:** If B fails and rolls back, should A also roll back? If B succeeds but A fails later, should B's changes be rolled back too?

Propagation gives you control over these scenarios.

---

### The Three You'll Actually Use

**1. REQUIRED (default — use this 90% of the time)**

> "Join the existing transaction. If none exists, create a new one."

- B runs in the *same* transaction as A
- If B rolls back → A rolls back
- If A rolls back → B rolls back
- They succeed or fail together

**When to use:** Most business operations. You want atomicity — everything commits or nothing does.

---

**2. REQUIRES_NEW**

> "Pause the outer transaction. Start a fresh, independent transaction."

- B gets its *own* transaction, separate from A
- If B rolls back → A can still commit (if it catches the exception)
- If A rolls back → B is NOT affected (already committed)
- They are independent

**When to use:** Audit logging, notifications, or when you need partial commits.

---

**3. NESTED**

> "Create a savepoint inside the existing transaction."

- B runs in A's transaction, but with a savepoint
- If B rolls back → only B's changes undo, A can continue
- If A rolls back → everything rolls back (including B)
- Partial rollback within a larger transaction

**When to use:** When you want to recover from failures mid-transaction without aborting everything.

---

### Real-World Example: Order Processing

**Scenario:** Process an order with 4 steps:

```
processOrder()
  ├─ reserveInventory()
  ├─ chargePayment()
  ├─ logAudit()
  └─ sendConfirmation()
```

**Original code:** All methods used `REQUIRED` (default).

**What happened:**
1. `reserveInventory()` — succeeded, inventory reserved
2. `chargePayment()` — **FAILED** (card declined)
3. Transaction rolled back — inventory reservation undone
4. BUT: inventory was also written to a separate warehouse system (non-transactional)
5. Result: 200 orders/day had "reserved" inventory that didn't exist

**The fix:**

| Method | Propagation | Why |
|--------|-------------|-----|
| `reserveInventory()` | `REQUIRES_NEW` | Commit reservation immediately, then release if payment fails |
| `chargePayment()` | `REQUIRED` | Part of main transaction |
| `logAudit()` | `REQUIRES_NEW` | Always log, even if order fails |
| `sendConfirmation()` | `REQUIRED` | Only send if entire order succeeds |

**Outcome:**
- Inventory reservations dropped to zero (no more orphans)
- Audit logs captured failed orders (helped debugging)
- Confirmation emails only sent for successful orders

---

### The Other Four (Rarely Used)

| Propagation | Behavior | When |
|-------------|----------|------|
| `SUPPORTS` | Join if exists, otherwise no transaction | Read-only methods that work either way |
| `NOT_SUPPORTED` | Suspend any existing transaction | Operations that must NOT run in a transaction (batch jobs) |
| `MANDATORY` | Must join existing, throw exception if none | Internal methods that require a transaction |
| `NEVER` | Must NOT run in a transaction, throw exception if one exists | Legacy code that breaks under transactions |

---

### Common Pitfalls

**1. Self-invocation trap**

```
class OrderService {
    @Transactional
    public void methodA() {
        methodB();  // @Transactional IGNORED — same class!
    }
    
    @Transactional(propagation = REQUIRES_NEW)
    public void methodB() { }
}
```

**Problem:** Spring's proxy can't intercept internal calls. `methodB()` runs with no transaction.

**Fix:** Inject the service into itself or move `methodB()` to a separate class.

---

**2. Swallowing exceptions**

```
@Transactional
public void outer() {
    try {
        inner();  // REQUIRES_NEW, fails
    } catch (Exception e) {
        // swallowed — outer still commits
    }
}
```

**Problem:** You used `REQUIRES_NEW` to isolate, but now failures are silent.

**Fix:** Log the exception or trigger compensation logic.

---

**3. Overusing REQUIRES_NEW**

**Problem:** Every method starts its own transaction → 50 transactions per request → database overload.

**Fix:** Default to `REQUIRED`. Only use `REQUIRES_NEW` when you have a specific reason.

---

### Rule of Thumb

1. **Start with `REQUIRED`** — it's the default for a reason
2. **Use `REQUIRES_NEW`** for audit logs, notifications, or when partial commits are required
3. **Use `NESTED`** if you need mid-transaction recovery (and your database supports savepoints)
4. **Audit quarterly** — search for `@Transactional` and verify propagation is intentional

---

### Quick Decision Tree

```
Does this method need to commit even if the caller fails?
├─ YES → REQUIRES_NEW (e.g., audit log)
└─ NO → Does this method need to rollback independently?
    ├─ YES → NESTED (partial rollback)
    └─ NO → REQUIRED (default, safest)
```
