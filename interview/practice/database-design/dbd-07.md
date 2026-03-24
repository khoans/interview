**7.** Design a database for a restaurant. A menu item can belong to multiple categories. An order can have multiple menu items. A waiter can serve multiple tables. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### category
| Column | Type | Constraints |
|--------|------|-------------|
| category_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL, UNIQUE |
| description | VARCHAR(500) | NULL |

### menu_item
| Column | Type | Constraints |
|--------|------|-------------|
| item_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| price | DECIMAL(8,2) | NOT NULL |
| is_available | BOOLEAN | NOT NULL, DEFAULT TRUE |

### menu_item_category (junction)
| Column | Type | Constraints |
|--------|------|-------------|
| item_id | BIGINT | PK, FK → menu_item(item_id) |
| category_id | BIGINT | PK, FK → category(category_id) |

### waiter
| Column | Type | Constraints |
|--------|------|-------------|
| waiter_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| phone | VARCHAR(20) | NULL |

### restaurant_table
| Column | Type | Constraints |
|--------|------|-------------|
| table_id | BIGINT | PK, AUTO_INCREMENT |
| table_number | INT | NOT NULL, UNIQUE |
| seats | INT | NOT NULL |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'AVAILABLE' |

### orders
| Column | Type | Constraints |
|--------|------|-------------|
| order_id | BIGINT | PK, AUTO_INCREMENT |
| table_id | BIGINT | FK → restaurant_table(table_id), NOT NULL |
| waiter_id | BIGINT | FK → waiter(waiter_id), NOT NULL |
| order_time | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'OPEN' |
| total_amount | DECIMAL(10,2) | NULL |

### order_item (junction — Order ↔ MenuItem)
| Column | Type | Constraints |
|--------|------|-------------|
| order_id | BIGINT | PK, FK → orders(order_id) |
| item_id | BIGINT | PK, FK → menu_item(item_id) |
| quantity | INT | NOT NULL, CHECK (quantity > 0) |
| unit_price | DECIMAL(8,2) | NOT NULL |
| notes | VARCHAR(500) | NULL |

## Relationships
- **Menu Item ↔ Category**: M:N via `menu_item_category`
- **Order ↔ Menu Item**: M:N via `order_item`
- **Waiter → Orders**: 1:N (waiter serves multiple orders)
- **Table → Orders**: 1:N (a table can have multiple orders over time)

## ER Diagram
```
+----------+     +-------------------+     +-----------+
| category |     |menu_item_category |     | menu_item |
+----------+     +-------------------+     +-----------+
|category_id|─1:N| category_id (PK)  |N:1─| item_id   |
| name     |     | item_id     (PK)  |     | name      |
+----------+     +-------------------+     | price     |
                                           |is_available|
+--------+    +--------+    +------------+ +-----+-----+
| waiter |    | orders |    | order_item |       |
+--------+    +--------+    +------------+       |
|waiter_id|─1:N|order_id|─1:N|order_id(PK)|      |
| name   |    |table_id|    | item_id(PK)|──N:1──+
+--------+    |waiter_id|   | quantity   |
              |order_time|  | unit_price |
   table──N:1─|status   |  | notes      |
              +--------+   +------------+
```

## Key Design Insights
- **Two M:N relationships** here: menu_item↔category (structural) and order↔menu_item (transactional). The key difference is `order_item` stores `unit_price` (snapshot) while `menu_item_category` has no extra attributes — pure association.
- **Named `restaurant_table`** not `table` — `TABLE` is a SQL reserved keyword. This bites many developers in production. Same reason `orders` not `order`.
- **Trade-off**: `notes` on `order_item` handles special requests ("no onions", "extra spicy"). In a real POS system, you'd model modifiers as a separate table, but for an interview answer, a notes field shows you understand the need without over-engineering.
