**18.** Design a database for a warehouse inventory system. A warehouse can store multiple products. A product can be in multiple warehouses. A supplier can supply multiple products. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### warehouse
| Column | Type | Constraints |
|--------|------|-------------|
| warehouse_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| address | TEXT | NOT NULL |
| capacity | INT | NULL |

### product
| Column | Type | Constraints |
|--------|------|-------------|
| product_id | BIGINT | PK, AUTO_INCREMENT |
| sku | VARCHAR(50) | UNIQUE, NOT NULL |
| name | VARCHAR(300) | NOT NULL |
| description | TEXT | NULL |
| unit_price | DECIMAL(10,2) | NOT NULL |
| weight_kg | DECIMAL(8,2) | NULL |

### supplier
| Column | Type | Constraints |
|--------|------|-------------|
| supplier_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| contact_email | VARCHAR(255) | NOT NULL |
| phone | VARCHAR(20) | NULL |
| address | TEXT | NULL |

### warehouse_product (junction — Warehouse ↔ Product)
| Column | Type | Constraints |
|--------|------|-------------|
| warehouse_id | BIGINT | PK, FK → warehouse(warehouse_id) |
| product_id | BIGINT | PK, FK → product(product_id) |
| quantity | INT | NOT NULL, DEFAULT 0, CHECK (quantity >= 0) |
| min_stock_level | INT | NOT NULL, DEFAULT 10 |
| last_restocked | DATE | NULL |

### supplier_product (junction — Supplier ↔ Product)
| Column | Type | Constraints |
|--------|------|-------------|
| supplier_id | BIGINT | PK, FK → supplier(supplier_id) |
| product_id | BIGINT | PK, FK → product(product_id) |
| supply_price | DECIMAL(10,2) | NOT NULL |
| lead_time_days | INT | NULL |

## Relationships
- **Warehouse ↔ Product**: M:N via `warehouse_product`
- **Supplier ↔ Product**: M:N via `supplier_product`

## ER Diagram
```
+-----------+    +-------------------+    +---------+    +------------------+    +----------+
| warehouse |    | warehouse_product |    | product |    | supplier_product |    | supplier |
+-----------+    +-------------------+    +---------+    +------------------+    +----------+
|warehouse_id|─1:N|warehouse_id (PK)|N:1─|product_id|─1:N|product_id  (PK)|N:1─|supplier_id|
| name      |    | product_id  (PK)|     | sku      |    |supplier_id (PK)|    | name      |
| address   |    | quantity        |     | name     |    | supply_price   |    |contact_email|
| capacity  |    | min_stock_level |     |unit_price|    | lead_time_days |    | phone     |
+-----------+    | last_restocked  |     | weight   |    +------------------+   | address   |
                 +-------------------+   +---------+                            +----------+
```

## Key Design Insights
- **Two parallel M:N relationships** with `product` at the center — this is a hub-and-spoke pattern. The `warehouse_product` junction stores *inventory state* (how many units are where), while `supplier_product` stores *procurement metadata* (who supplies it and at what cost).
- **`min_stock_level`** enables automated reorder alerts: `WHERE quantity < min_stock_level`. Combined with `supplier_product.lead_time_days`, you can build a reorder system that factors in delivery time. This shows real-world inventory management thinking.
- **Trade-off**: `quantity` in `warehouse_product` is a running total — any stock movement (receiving, shipping, adjusting) should go through a `stock_movement` audit table, then update the quantity. Without the audit trail, you can't debug inventory discrepancies. For the interview, mention this as the production evolution: "I'd add a stock_movement table for traceability, but the core schema starts with the running total."
