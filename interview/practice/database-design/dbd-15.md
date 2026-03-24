**15.** Design a database for a bank. A customer can have multiple accounts. An account can have multiple transactions. A branch can have multiple employees. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### branch
| Column | Type | Constraints |
|--------|------|-------------|
| branch_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| address | TEXT | NOT NULL |
| phone | VARCHAR(20) | NULL |

### employee
| Column | Type | Constraints |
|--------|------|-------------|
| employee_id | BIGINT | PK, AUTO_INCREMENT |
| branch_id | BIGINT | FK → branch(branch_id), NOT NULL |
| name | VARCHAR(200) | NOT NULL |
| position | VARCHAR(100) | NOT NULL |
| hire_date | DATE | NOT NULL |

### customer
| Column | Type | Constraints |
|--------|------|-------------|
| customer_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| phone | VARCHAR(20) | NULL |
| address | TEXT | NULL |
| id_document | VARCHAR(50) | UNIQUE, NOT NULL |

### account
| Column | Type | Constraints |
|--------|------|-------------|
| account_id | BIGINT | PK, AUTO_INCREMENT |
| customer_id | BIGINT | FK → customer(customer_id), NOT NULL |
| branch_id | BIGINT | FK → branch(branch_id), NOT NULL |
| account_number | VARCHAR(20) | UNIQUE, NOT NULL |
| account_type | VARCHAR(20) | NOT NULL |
| balance | DECIMAL(15,2) | NOT NULL, DEFAULT 0.00 |
| opened_at | DATE | NOT NULL |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'ACTIVE' |

### transaction
| Column | Type | Constraints |
|--------|------|-------------|
| transaction_id | BIGINT | PK, AUTO_INCREMENT |
| account_id | BIGINT | FK → account(account_id), NOT NULL |
| transaction_type | VARCHAR(20) | NOT NULL |
| amount | DECIMAL(15,2) | NOT NULL, CHECK (amount > 0) |
| balance_after | DECIMAL(15,2) | NOT NULL |
| description | VARCHAR(500) | NULL |
| created_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP |

## Relationships
- **Branch → Employee**: 1:N
- **Branch → Account**: 1:N (account opened at a branch)
- **Customer → Account**: 1:N
- **Account → Transaction**: 1:N

## ER Diagram
```
+--------+       +----------+       +---------+       +-------------+
| branch |       | account  |       | customer|       |             |
+--------+       +----------+       +---------+       |             |
|branch_id|─1:N──|account_id|──N:1──|cust_id  |       |             |
| name   |       |customer_id|      | name    |       |             |
| address|       |branch_id |       | email   |       |             |
| phone  |       |acct_number|      | phone   |       |             |
+---+----+       |acct_type |       | address |       |             |
    |            | balance  |       |id_doc   |       |             |
    |1:N         | opened_at|       +---------+       |             |
    |            | status   |                         |             |
+---+------+     +----+-----+                         |             |
| employee |          |1:N                             |             |
+----------+     +----+--------+                       |             |
|employee_id|    | transaction |                       |             |
| branch_id|     +-------------+                       |             |
| name    |      |transaction_id|                      |             |
| position|      | account_id  |                       |             |
| hire_date|     | txn_type    |                       |             |
+----------+     | amount      |                       |             |
                 |balance_after|                        |             |
                 | description |                       |             |
                 | created_at  |                        |             |
                 +-------------+                       |             |
```

## Key Design Insights
- **`balance_after` in transaction** creates an audit trail — you can reconstruct the account balance at any point in time without scanning all transactions. This is essential for banking reconciliation. The current `account.balance` should always equal the `balance_after` of the latest transaction.
- **`balance` on account is denormalized** but absolutely necessary in banking. You can't afford `SUM(amount)` over millions of transactions for every balance check. The trade-off is maintaining consistency — use database transactions: `BEGIN → INSERT transaction → UPDATE account.balance → COMMIT`.
- **Trade-off**: `transaction` table will be the largest table by far (append-only, millions of rows). Partition by `created_at` (monthly/yearly) for query performance. Index on `(account_id, created_at DESC)` for statement generation. In production, banks often archive transactions older than 7 years to a separate cold storage.
