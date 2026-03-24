**43.** Product Catalog with Variants: Product with multiple variants (size, color). Each variant can have different prices and stock.



========== THIS SECTION IS THE ANSWER ==========

## Table Definitions

### products
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| name | VARCHAR(200) | NOT NULL |
| description | TEXT | |
| brand | VARCHAR(100) | |
| category | VARCHAR(100) | |
| base_price | DECIMAL(10,2) | NOT NULL |
| is_active | BOOLEAN | NOT NULL DEFAULT TRUE |
| created_at | TIMESTAMP | NOT NULL DEFAULT NOW() |

### product_variants
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| product_id | BIGINT | FK → products(id), NOT NULL |
| sku | VARCHAR(50) | NOT NULL, UNIQUE |
| price | DECIMAL(10,2) | NOT NULL |
| stock_quantity | INT | NOT NULL DEFAULT 0 |
| is_active | BOOLEAN | NOT NULL DEFAULT TRUE |

### variant_attributes
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| variant_id | BIGINT | FK → product_variants(id), NOT NULL |
| attribute_name | VARCHAR(50) | NOT NULL (size, color, material) |
| attribute_value | VARCHAR(100) | NOT NULL (XL, Red, Cotton) |
| UNIQUE(variant_id, attribute_name) | | |

## Alternative: Option Types approach (Shopify-style)

### option_types
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| product_id | BIGINT | FK → products(id), NOT NULL |
| name | VARCHAR(50) | NOT NULL (Size, Color) |
| position | INT | NOT NULL |
| UNIQUE(product_id, name) | | |

### option_values
| Column | Type | Constraints |
|--------|------|-------------|
| id | BIGINT | PK, AUTO_INCREMENT |
| option_type_id | BIGINT | FK → option_types(id), NOT NULL |
| value | VARCHAR(100) | NOT NULL (S, M, L, XL) |

## Relationships
- products → product_variants: **1:N**
- product_variants → variant_attributes: **1:N** (EAV-style)

## ER Diagram (Text)
```
[products] 1───N [product_variants] 1───N [variant_attributes]
                        │
                     (has SKU,
                      price,
                      stock)
```

## Example & Insight
A T-shirt with 4 sizes x 3 colors = 12 variants. Each variant has its own SKU, price, and stock. The EAV (`variant_attributes`) approach is flexible — add "material" without schema changes. Shopify uses this pattern. Key: orders reference `variant_id`, NOT `product_id`, because you need to know exactly which SKU was purchased and decrement the right stock.

## Trade-off
EAV (variant_attributes) is flexible but hard to query: "find all Red XL items" requires self-joins or pivot queries. Fixed columns (`size VARCHAR, color VARCHAR` on variant) are faster to query but rigid — adding "material" needs a schema change. For most e-commerce, fixed columns for the top 3-4 attributes + a JSON `extra_attributes` column gives the best of both worlds.
