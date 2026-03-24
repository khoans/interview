**3.** Design a database for an e-commerce store. A customer can place multiple orders. An order can contain multiple products. A product can be in multiple orders. Describe the tables to be created.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### customer
| Column | Type | Constraints |
|--------|------|-------------|
| customer_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| email | VARCHAR(255) | UNIQUE, NOT NULL |
| phone | VARCHAR(20) | NULL |
| address | TEXT | NULL |
| created_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP |

### product
| Column | Type | Constraints |
|--------|------|-------------|
| product_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(300) | NOT NULL |
| description | TEXT | NULL |
| price | DECIMAL(12,2) | NOT NULL |
| stock_quantity | INT | NOT NULL, DEFAULT 0 |
| category | VARCHAR(100) | NULL |

### orders
| Column | Type | Constraints |
|--------|------|-------------|
| order_id | BIGINT | PK, AUTO_INCREMENT |
| customer_id | BIGINT | FK → customer(customer_id), NOT NULL |
| order_date | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'PENDING' |
| total_amount | DECIMAL(12,2) | NOT NULL |
| shipping_address | TEXT | NOT NULL |

### order_item (junction)
| Column | Type | Constraints |
|--------|------|-------------|
| order_id | BIGINT | PK, FK → orders(order_id) |
| product_id | BIGINT | PK, FK → product(product_id) |
| quantity | INT | NOT NULL, CHECK (quantity > 0) |
| unit_price | DECIMAL(12,2) | NOT NULL |

## Relationships
- **Customer → Orders**: 1:N
- **Order ↔ Product**: M:N via `order_item`

## ER Diagram
```
+----------+       +----------+       +------------+       +---------+
| customer |       |  orders  |       | order_item |       | product |
+----------+       +----------+       +------------+       +---------+
|customer_id|─1:N─|order_id   |─1:N─| order_id(PK)|─N:1─|product_id|
| name     |      |customer_id|      | product_id  |      | name    |
| email    |      |order_date |      | quantity    |      | price   |
| phone    |      |status     |      | unit_price  |      | stock   |
| address  |      |total_amt  |      +------------+       |category |
+----------+      |ship_addr  |                            +---------+
                  +----------+
```

## Key Design Insights
- **Snapshot `unit_price` in `order_item`**, not a live reference to `product.price`. Product prices change over time — if you only store the FK, historical orders would show current prices instead of what the customer actually paid. This is the most common mistake in e-commerce DB design.
- **`total_amount` in `orders`** is intentionally denormalized. You could compute it from `SUM(quantity * unit_price)` in `order_item`, but for reporting dashboards and order history, pre-computing saves expensive JOINs on millions of rows.
- **Trade-off**: Table named `orders` (not `order`) because `ORDER` is a SQL reserved keyword. Small detail, but causes painful debugging when you forget to quote it. Always avoid reserved words for table names.
