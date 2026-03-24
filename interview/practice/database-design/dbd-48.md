**48.** Shipping and Tracking: Orders, Shipments, Tracking Events, Items. An order can have multiple shipments. A shipment can have multiple tracking events. A shipment can contain multiple items.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### orders
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| customer_id | BIGINT | NOT NULL |
| order_number | VARCHAR(30) | NOT NULL, UNIQUE |
| shipping_address | TEXT | NOT NULL |
| status | VARCHAR(20) | NOT NULL DEFAULT 'PROCESSING' |
| placed_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### shipments
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| order_id | BIGINT | FK → orders(id), NOT NULL |
| tracking_number | VARCHAR(50) | NOT NULL, UNIQUE |
| carrier | VARCHAR(50) | NOT NULL (FEDEX, UPS, DHL, USPS) |
| status | VARCHAR(20) | NOT NULL DEFAULT 'CREATED' |
| shipped_at | TIMESTAMP | |
| estimated_delivery | DATE | |
| delivered_at | TIMESTAMP | |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### shipment_items
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| shipment_id | BIGINT | FK → shipments(id), NOT NULL |
| order_item_id | BIGINT | NOT NULL |
| quantity | INT | NOT NULL, CHECK (quantity > 0) |
| UNIQUE(shipment_id, order_item_id) | | |

### tracking_events
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| shipment_id | BIGINT | FK → shipments(id), NOT NULL |
| status | VARCHAR(30) | NOT NULL |
| location | VARCHAR(200) | |
| description | TEXT | |
| event_time | TIMESTAMP | NOT NULL |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

## Relationships
- orders → shipments: **1:N** (split shipments)
- shipments → shipment_items: **1:N**
- shipments → tracking_events: **1:N**

## ER Diagram (Text)
```
[orders] 1───N [shipments] 1───N [tracking_events]
                    │
                   1:N
                    │
             [shipment_items]
```

## Example & Insight
Amazon splits orders into multiple shipments when items ship from different warehouses. Status flow for tracking_events: LABEL_CREATED → PICKED_UP → IN_TRANSIT → OUT_FOR_DELIVERY → DELIVERED. The latest tracking event determines `shipment.status` — derive it: `SELECT DISTINCT ON (shipment_id) * FROM tracking_events ORDER BY shipment_id, event_time DESC`. Index `(shipment_id, event_time DESC)` for this query. Index `(tracking_number)` for customer tracking page lookups.

## Trade-off
Storing `status` on both shipment and tracking_events is redundant — shipment status should derive from the latest event. But querying "all in-transit shipments" without denormalization requires a subquery on every tracking_events row. Denormalize `shipment.status` and update it via trigger or application code whenever a new tracking event is inserted. Worth the trade-off for dashboard/reporting queries.
