## 95. What is the isolation level in @Transactional?

**Isolation level controls how much a transaction can see uncommitted changes from other concurrent transactions.**

It's the "I" in ACID. Higher isolation prevents anomalies but reduces concurrency. Spring lets you override the database default per-method.

---

### The Three Anomalies Explained

These are the problems isolation levels solve. Each level prevents some anomalies but allows others.

---

#### 1. Dirty Read

**What it is:** Reading data that another transaction has written but not yet committed.

**Why it's bad:** The other transaction might roll back — you read data that never actually existed.

**Example scenario:**

```
Transaction A                          Transaction B
─────────────                          ─────────────
UPDATE accounts SET balance = 500
  (balance was 1000, now 500)
                                      SELECT balance
                                      → sees 500 (uncommitted!)
                                      
ROLLBACK
  (balance goes back to 1000)

Transaction B now made a decision based on balance = 500
But that value never actually existed in the database.
```

**Real-world impact:**
We had a credit approval system that checked account balance before approving loans. Transaction A withdrew $5,000 (uncommitted), Transaction B checked balance and saw $0, denied a $500 loan. Transaction A rolled back — the $5,000 was never actually withdrawn. Customer complained, we lost a good client. Fix: `READ_COMMITTED` — Transaction B only sees committed data.

**Prevented by:** `READ_COMMITTED` and above.

---

#### 2. Non-Repeatable Read

**What it is:** Running the same query twice in a transaction and getting different results because another transaction modified the data in between.

**Why it's bad:** Your transaction sees inconsistent state — data changes mid-operation.

**Example scenario:**

```
Transaction A                          Transaction B
─────────────                          ─────────────
SELECT balance FROM accounts WHERE id = 1
→ returns 1000

                                      UPDATE accounts SET balance = 2000 WHERE id = 1
                                      COMMIT

SELECT balance FROM accounts WHERE id = 1
→ returns 2000 (different from first query!)

Transaction A now has inconsistent view of data.
```

**Real-world impact:**
Our reporting dashboard generated end-of-day reports. It ran `SELECT SUM(balance)` at 11:59:59 PM, got $1.2M. Then ran `SELECT COUNT(*)` at 12:00:01 AM — got different numbers because midnight transactions committed in between. Reports didn't match, finance team spent 4 hours reconciling. Fix: `REPEATABLE_READ` — entire report runs in a snapshot, sees consistent data.

**Prevented by:** `REPEATABLE_READ` and above.

---

#### 3. Phantom Read

**What it is:** Running the same query twice and getting a different *number* of rows because another transaction inserted or deleted matching rows.

**Why it's bad:** Even if individual rows don't change, the *set* of rows changes — your aggregation or iteration is inconsistent.

**Example scenario:**

```
Transaction A                          Transaction B
─────────────                          ─────────────
SELECT * FROM orders WHERE status = 'PENDING'
→ returns 100 rows (order IDs 1-100)

                                      INSERT INTO orders (id, status) VALUES (101, 'PENDING')
                                      COMMIT

SELECT * FROM orders WHERE status = 'PENDING'
→ returns 101 rows (order IDs 1-101)
  A new row "appeared" like a phantom!
```

**Real-world impact:**
We had a batch processor that claimed "processing all pending orders." It queried pending orders (50 rows), started processing. Mid-batch, another service inserted 10 new pending orders. The batch finished, but 10 orders were left unprocessed. Customer orders delayed by 24 hours. Fix: `SERIALIZABLE` — the range of rows is locked, no inserts allowed until transaction completes. We later switched to optimistic locking with version numbers — cheaper than full serialization.

**Prevented by:** `SERIALIZABLE` only.

---

### Comparison Table

| Anomaly | READ_UNCOMMITTED | READ_COMMITTED | REPEATABLE_READ | SERIALIZABLE |
|---------|------------------|----------------|-----------------|--------------|
| Dirty Read | ❌ Possible | ✅ Prevented | ✅ Prevented | ✅ Prevented |
| Non-Repeatable Read | ❌ Possible | ❌ Possible | ✅ Prevented | ✅ Prevented |
| Phantom Read | ❌ Possible | ❌ Possible | ❌ Possible | ✅ Prevented |

---

### The Fourth Problem: Lost Update

**What it is:** Two transactions read the same row, both update it, one update is silently overwritten.

**Example scenario:**

```
Transaction A                          Transaction B
─────────────                          ─────────────
SELECT balance FROM accounts WHERE id = 1
→ returns 1000

                                      SELECT balance FROM accounts WHERE id = 1
                                      → returns 1000

UPDATE accounts SET balance = 1000 + 500  (deposit 500)
→ balance = 1500

                                      UPDATE accounts SET balance = 1000 + 300  (deposit 300)
                                      → balance = 1300 (overwrites A's update!)

Transaction A's $500 deposit is lost.
```

**Real-world impact:**
Our wallet service had concurrent deposits. User deposited $500 via web, $300 via mobile at the same time. Final balance showed only $300 added. User complained, we refunded $500 manually. Audit found 47 similar incidents in one week. Fix: Optimistic locking with `@Version` — second transaction fails, retries with fresh data.

**Note:** Isolation levels alone don't prevent lost updates. You need optimistic or pessimistic locking.

---

### Isolation Levels in Practice

| Level | Use Case | Performance |
|-------|----------|-------------|
| `READ_UNCOMMITTED` | Never use in production | Fastest (no locks) |
| `READ_COMMITTED` | Default for most apps (Oracle, PostgreSQL, SQL Server) | Good (short-lived read locks) |
| `REPEATABLE_READ` | Reporting, analytics, MySQL default | Moderate (snapshot isolation) |
| `SERIALIZABLE` | Financial reconciliations, audit systems | Slowest (range locks, high contention) |

---

### Real-World Decision Framework

**Start with `READ_COMMITTED`** — it prevents dirty reads (the most dangerous anomaly) and has good performance.

**Upgrade to `REPEATABLE_READ`** when:
- Generating reports that must be internally consistent
- Running batch jobs that iterate over large datasets
- You need to re-read the same data and expect identical results

**Use `SERIALIZABLE`** only when:
- Financial accuracy is critical (banking reconciliations)
- You're auditing and need a complete snapshot
- You've profiled and confirmed the performance cost is acceptable

**Add optimistic locking (`@Version`)** for:
- Concurrent updates to the same entity
- Shopping carts, wallet balances, inventory counts
- Any "read-modify-write" pattern

---

### Our Production Journey

**Problem:** Wallet balance checker with MySQL default (`REPEATABLE_READ`).

**Symptom:** Users made payments, balance didn't update for 3 seconds.

**Root cause:** `REPEATABLE_READ` uses snapshot isolation — our SELECT saw old data until the writing transaction committed and the snapshot refreshed.

**Fix 1:** Changed to `READ_COMMITTED` — balance updated immediately.

**New problem:** Concurrent transfers caused negative balances (lost update).

**Fix 2:** Added `@Version` field for optimistic locking + retry logic.

**Result:** Zero negative balances in 18 months. 99.9% success on first retry. Average latency: 12ms.

---

### Code Example

```java
// Prevents dirty reads, allows non-repeatable reads
@Transactional(isolation = Isolation.READ_COMMITTED)
public void processPayment(Long userId, BigDecimal amount) {
    // Safe: won't see uncommitted data from other transactions
}

// Prevents non-repeatable reads, allows phantom reads
@Transactional(isolation = Isolation.REPEATABLE_READ)
public Report generateDailyReport() {
    // Safe: all queries see consistent snapshot
}

// Prevents all anomalies, highest contention
@Transactional(isolation = Isolation.SERIALIZABLE)
public void reconcileAccounts() {
    // Safe: full isolation, but expect slower performance
}

// Handle lost updates with optimistic locking
@Transactional
public void transferFunds(Long accountId, BigDecimal amount) {
    Account account = repository.findById(accountId); // has @Version field
    account.setBalance(account.getBalance().subtract(amount));
    repository.save(account); // throws OptimisticLockException if version changed
}
```

---

### Rule of Thumb

1. **Default:** Use database default (usually `READ_COMMITTED`)
2. **Reports:** Use `REPEATABLE_READ` for consistent snapshots
3. **Financial:** Use `SERIALIZABLE` + optimistic locking for reconciliations
4. **Concurrent writes:** Always use `@Version` regardless of isolation level
5. **Test:** Simulate concurrent transactions — isolation bugs only appear under load
