**34.** Food Delivery: Restaurants, Menu Items, Orders, Drivers. A restaurant can have multiple menu items. A customer can place multiple orders. An order can have items from multiple restaurants. A driver can deliver multiple orders.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### customers
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| phone | VARCHAR(20) | NOT NULL, UNIQUE |
| address | VARCHAR(300) | |

### restaurants
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| address | VARCHAR(300) | NOT NULL |
| cuisine_type | VARCHAR(50) | |
| rating | DECIMAL(2,1) | DEFAULT 0 |
| is_active | BOOLEAN | NOT NULL DEFAULT TRUE |

### menu_items
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| restaurant_id | BIGINT | FK → restaurants(id), NOT NULL |
| name | VARCHAR(200) | NOT NULL |
| price | DECIMAL(10,2) | NOT NULL |
| description | TEXT | |
| is_available | BOOLEAN | NOT NULL DEFAULT TRUE |

### drivers
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(100) | NOT NULL |
| phone | VARCHAR(20) | NOT NULL, UNIQUE |
| status | VARCHAR(20) | NOT NULL DEFAULT 'OFFLINE' |

### orders
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| customer_id | BIGINT | FK → customers(id), NOT NULL |
| driver_id | BIGINT | FK → drivers(id) |
| delivery_address | VARCHAR(300) | NOT NULL |
| total_amount | DECIMAL(10,2) | NOT NULL |
| delivery_fee | DECIMAL(10,2) | NOT NULL DEFAULT 0 |
| status | VARCHAR(20) | NOT NULL DEFAULT 'PLACED' |
| placed_at | TIMESTAMP | NOT NULL DEFAULT NOW() |
| delivered_at | TIMESTAMP | |

### order_items
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| order_id | BIGINT | FK → orders(id), NOT NULL |
| menu_item_id | BIGINT | FK → menu_items(id), NOT NULL |
| quantity | INT | NOT NULL, CHECK (quantity > 0) |
| unit_price | DECIMAL(10,2) | NOT NULL |
| special_instructions | TEXT | |

## Relationships
- restaurants → menu_items: **1:N**
- customers → orders: **1:N**
- drivers → orders: **1:N**
- orders → order_items: **1:N**
- order_items → menu_items: **N:1** (items from different restaurants)

## ER Diagram (Text)
```
[customers] 1───N [orders] N───1 [drivers]
                     │
                    1:N
                     │
               [order_items] N───1 [menu_items] N───1 [restaurants]
```

## Example & Insight
Critical: `unit_price` is stored on `order_items`, NOT referenced from `menu_items`. Menu prices change — but a placed order's price must be immutable. This is the snapshot pattern. DoorDash-style multi-restaurant orders work because `order_items` links to `menu_items` from different restaurants. Index `(driver_id, status)` for dispatch queries.

## Trade-off
Multi-restaurant orders simplify the customer experience but complicate logistics — the driver must visit multiple pickups. Many platforms (GrabFood, GoFood) restrict one restaurant per order to simplify operations, then batch nearby orders for the same driver.
