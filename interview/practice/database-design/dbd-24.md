**24.** Design a database for a supply chain system. A manufacturer can produce multiple products. A retailer can sell products from multiple manufacturers. A product can be shipped to multiple locations. Describe the tables.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### manufacturer
| Column | Type | Constraints |
|--------|------|-------------|
| manufacturer_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| country | VARCHAR(100) | NOT NULL |
| contact_email | VARCHAR(255) | NULL |

### product
| Column | Type | Constraints |
|--------|------|-------------|
| product_id | BIGINT | PK, AUTO_INCREMENT |
| manufacturer_id | BIGINT | FK → manufacturer(manufacturer_id), NOT NULL |
| sku | VARCHAR(50) | UNIQUE, NOT NULL |
| name | VARCHAR(300) | NOT NULL |
| unit_cost | DECIMAL(10,2) | NOT NULL |
| weight_kg | DECIMAL(8,2) | NULL |

### retailer
| Column | Type | Constraints |
|--------|------|-------------|
| retailer_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| address | TEXT | NOT NULL |
| contact_email | VARCHAR(255) | NULL |

### location
| Column | Type | Constraints |
|--------|------|-------------|
| location_id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| address | TEXT | NOT NULL |
| location_type | VARCHAR(50) | NOT NULL |

### retailer_product (junction — Retailer ↔ Product)
| Column | Type | Constraints |
|--------|------|-------------|
| retailer_id | BIGINT | PK, FK → retailer(retailer_id) |
| product_id | BIGINT | PK, FK → product(product_id) |
| retail_price | DECIMAL(10,2) | NOT NULL |
| quantity_in_stock | INT | NOT NULL, DEFAULT 0 |

### shipment
| Column | Type | Constraints |
|--------|------|-------------|
| shipment_id | BIGINT | PK, AUTO_INCREMENT |
| product_id | BIGINT | FK → product(product_id), NOT NULL |
| from_location_id | BIGINT | FK → location(location_id), NULL |
| to_location_id | BIGINT | FK → location(location_id), NOT NULL |
| quantity | INT | NOT NULL, CHECK (quantity > 0) |
| shipped_date | DATE | NOT NULL |
| delivered_date | DATE | NULL |
| status | VARCHAR(20) | NOT NULL, DEFAULT 'IN_TRANSIT' |

## Relationships
- **Manufacturer → Product**: 1:N
- **Retailer ↔ Product**: M:N via `retailer_product`
- **Product → Shipment**: 1:N
- **Location → Shipment**: 1:N (twice — from and to)

## ER Diagram
```
+--------------+       +---------+       +------------------+       +----------+
| manufacturer |       | product |       | retailer_product |       | retailer |
+--------------+       +---------+       +------------------+       +----------+
|manufacturer_id|─1:N──|product_id|─1:N──|product_id   (PK) |──N:1──|retailer_id|
| name         |       |manuf_id |       |retailer_id  (PK) |       | name     |
| country      |       | sku     |       | retail_price     |       | address  |
|contact_email |       | name    |       |qty_in_stock      |       |email     |
+--------------+       |unit_cost|       +------------------+       +----------+
                       | weight  |
                       +----+----+
                            |1:N
                       +----+------+
                       |  shipment |
                       +-----------+
                       |shipment_id|
                       | product_id|
                       |from_loc_id|──N:1──location
                       | to_loc_id |──N:1──location
                       | quantity  |
                       |shipped_dt |
                       |delivered_dt|
                       | status    |
                       +-----------+
```

## Key Design Insights
- **`shipment` has two FKs to `location`** (from and to) — same pattern as the flight/airport design. This models the product's journey through the supply chain: manufacturer warehouse → distribution center → retailer store. `from_location_id` is nullable for initial production (product originates from manufacturer).
- **`retailer_product` captures the retail markup** — `unit_cost` on product vs `retail_price` on the junction. This separation lets you analyze margins: `retail_price - unit_cost` per product per retailer.
- **Trade-off**: `quantity_in_stock` on `retailer_product` is denormalized — it should be derivable from shipments received minus sales. In production, you'd maintain this counter via triggers or application logic and add a `stock_movement` audit table. For the interview, the denormalized counter is acceptable — just mention the reconciliation concern.
