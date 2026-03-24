**2.** Design a database for a library system. A book can have multiple authors. An author can write multiple books. A member can borrow multiple books. Describe the tables and relationships.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### book
| Column | Type | Constraints |
|--------|------|-------------|
| book_id | BIGINT | PK, AUTO_INCREMENT |
| isbn | VARCHAR(13) | UNIQUE, NOT NULL |
| title | VARCHAR(300) | NOT NULL |
| published_year | SMALLINT | NULL |
| copies_available | INT | NOT NULL, DEFAULT 1 |

### author
| Column | Type | Constraints |
|--------|------|-------------|
| author_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| bio | TEXT | NULL |

### book_author (junction)
| Column | Type | Constraints |
|--------|------|-------------|
| book_id | BIGINT | PK, FK → book(book_id) |
| author_id | BIGINT | PK, FK → author(author_id) |

### member
| Column | Type | Constraints |
|--------|------|-------------|
| member_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| membership_date | DATE | NOT NULL |

### borrowing
| Column | Type | Constraints |
|--------|------|-------------|
| borrowing_id | BIGINT | PK, AUTO_INCREMENT |
| member_id | BIGINT | FK → member(member_id), NOT NULL |
| book_id | BIGINT | FK → book(book_id), NOT NULL |
| borrow_date | DATE | NOT NULL |
| due_date | DATE | NOT NULL |
| return_date | DATE | NULL |

## Relationships
- **Book ↔ Author**: M:N via `book_author`
- **Member → Borrowing**: 1:N (a member can borrow many times)
- **Book → Borrowing**: 1:N (same book can be borrowed multiple times)

## ER Diagram
```
+--------+       +-------------+       +--------+
| author |       | book_author |       |  book  |
+--------+       +-------------+       +--------+
|author_id|─1:N─| author_id(PK)|─N:1─|book_id  |
| name   |       | book_id (PK)|       | isbn    |
| bio    |       +-------------+       | title   |
+--------+                             | pub_year|
                                       | copies  |
              +-----------+            +----+----+
              | borrowing |                 |
              +-----------+                 |
              |borrowing_id|                |
              | member_id──|──N:1──member   |
              | book_id────|──N:1──────────-+
              | borrow_date|
              | due_date   |
              | return_date|
              +-----------+
```

## Key Design Insights
- **`borrowing` is NOT a junction table** — it's a transactional record. Unlike `book_author`, a member can borrow the same book multiple times, so we use a surrogate `borrowing_id` PK instead of a composite key.
- **`return_date` being NULL** means the book is still checked out. This is a common pattern: `WHERE return_date IS NULL` gives you all currently borrowed books. Index on `(book_id, return_date)` speeds up availability checks.
- **Trade-off**: `copies_available` is denormalized — it could be computed from borrowing records, but maintaining a counter avoids an expensive COUNT query every time someone checks availability. You accept the risk of inconsistency for read performance.
