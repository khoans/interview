**40.** Donation Management: Donors, Donations, Charities, Programs. A donor can make multiple donations. A charity can receive donations from multiple donors. A donation can be allocated to multiple programs.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### donors
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(255) | NOT NULL, UNIQUE |
| phone | VARCHAR(20) | |
| is_anonymous | BOOLEAN | NOT NULL DEFAULT FALSE |

### charities
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| registration_number | VARCHAR(50) | UNIQUE |
| description | TEXT | |
| website | VARCHAR(500) | |

### programs
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| charity_id | BIGINT | FK → charities(id), NOT NULL |
| name | VARCHAR(200) | NOT NULL |
| description | TEXT | |
| goal_amount | DECIMAL(12,2) | |
| status | VARCHAR(20) | NOT NULL DEFAULT 'ACTIVE' |

### donations
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| donor_id | BIGINT | FK → donors(id), NOT NULL |
| charity_id | BIGINT | FK → charities(id), NOT NULL |
| total_amount | DECIMAL(12,2) | NOT NULL |
| payment_method | VARCHAR(30) | NOT NULL |
| donated_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| receipt_number | VARCHAR(50) | UNIQUE |

### donation_allocations (1:N from donations — split across programs)
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| donation_id | BIGINT | FK → donations(id), NOT NULL |
| program_id | BIGINT | FK → programs(id), NOT NULL |
| amount | DECIMAL(12,2) | NOT NULL |
| UNIQUE(donation_id, program_id) | | |

## Relationships
- donors → donations: **1:N**
- charities → donations: **1:N**
- charities → programs: **1:N**
- donations → donation_allocations: **1:N**
- donation_allocations → programs: **N:1**

## ER Diagram (Text)
```
[donors] 1───N [donations] N───1 [charities] 1───N [programs]
                     │                                   │
                    1:N                                  N:1
                     │                                   │
                [donation_allocations]───────────────────┘
```

## Example & Insight
The `donation_allocations` table enables split donations — "$100 total, $60 to Education, $40 to Healthcare." Critical constraint: `SUM(allocation.amount) = donation.total_amount`. Enforce via application logic or a database trigger. Index `(charity_id, donated_at)` for generating annual donation reports and tax receipts.

## Trade-off
Allowing split allocations adds flexibility but makes reporting harder — "how much did program X receive?" requires aggregating across allocations, not just summing donations. If most donations go to a single program, a simpler design puts `program_id` directly on `donations` and skips the allocation table. Add the split table only when real multi-program donations are needed.
