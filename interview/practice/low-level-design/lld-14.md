**14.** Legacy System Integration — Adapter pattern. Adapt legacy inventory methods to new interface. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## Legacy System Integration — Adapter Pattern

### Pattern: Adapter (Object Adapter)

**Why Adapter?** The legacy inventory system uses old method names, XML formats, and procedural APIs. The new system expects a clean REST-style interface with JSON DTOs. Adapter bridges the gap without rewriting the legacy system.

### Text UML

```
┌──────────────────────────┐
│    <<interface>>         │
│    InventoryService      │
├──────────────────────────┤
│ + getProduct(id): Product│
│ + updateStock(id, qty)   │
│ + search(criteria): List │
│ + reserveStock(id, qty)  │
│ + releaseReservation(id) │
└──────────┬───────────────┘
           │
┌──────────┴───────────────┐
│  LegacyInventoryAdapter  │
│  (adapts legacy to new)  │
├──────────────────────────┤
│ - legacy: LegacySystem   │
├──────────────────────────┤
│ + getProduct(id)          │ ──> legacy.fetchItemRecord(int)
│ + updateStock(id, qty)    │ ──> legacy.modifyStockLevel(int, int)
│ + search(criteria)        │ ──> legacy.xmlQueryItems(String)
│ + reserveStock(id, qty)   │ ──> legacy.holdInventory(int, int)
│ + releaseReservation(id)  │ ──> legacy.cancelHold(int)
└──────────────────────────┘

┌──────────────────────────┐
│    LegacyInventorySystem │
│    (cannot modify)       │
├──────────────────────────┤
│ + fetchItemRecord(int): HashMap │
│ + modifyStockLevel(int, int): int │
│ + xmlQueryItems(String): String   │
│ + holdInventory(int, int): int    │
│ + cancelHold(int): boolean        │
└──────────────────────────┘
```

### Key Design Decisions

1. **Don't modify legacy** — The legacy system works in production; changing it risks breaking existing consumers
2. **Data format translation** — Legacy returns `HashMap` and XML; adapter converts to typed DTOs
3. **ID type conversion** — Legacy uses `int` IDs; new system uses `String` UUIDs. Adapter maps between them
4. **Error translation** — Legacy uses return codes (-1 for error); adapter throws typed exceptions

### SOLID Principles Applied

- **SRP**: Adapter only translates between interfaces; no business logic
- **OCP**: New system evolves independently; adapter absorbs changes
- **DIP**: New code depends on `InventoryService` interface, never touches legacy directly
- **ISP**: New interface exposes only what new system needs, hiding legacy complexity

### Java Code

```java
import java.util.*;
import java.util.stream.Collectors;

// ===================== New System DTOs =====================

public class Product {
    private final String id;
    private final String name;
    private final String sku;
    private final int stockQuantity;
    private final double price;
    private final boolean available;

    public Product(String id, String name, String sku, int stock, double price) {
        this.id = id;
        this.name = name;
        this.sku = sku;
        this.stockQuantity = stock;
        this.price = price;
        this.available = stock > 0;
    }

    public String getId() { return id; }
    public String getName() { return name; }
    public String getSku() { return sku; }
    public int getStockQuantity() { return stockQuantity; }
    public double getPrice() { return price; }
    public boolean isAvailable() { return available; }
}

public class SearchCriteria {
    private final String keyword;
    private final String category;
    private final Double minPrice;
    private final Double maxPrice;

    public SearchCriteria(String keyword, String category, Double minPrice, Double maxPrice) {
        this.keyword = keyword;
        this.category = category;
        this.minPrice = minPrice;
        this.maxPrice = maxPrice;
    }

    public String getKeyword() { return keyword; }
    public String getCategory() { return category; }
    public Double getMinPrice() { return minPrice; }
    public Double getMaxPrice() { return maxPrice; }
}

public class StockReservation {
    private final String reservationId;
    private final String productId;
    private final int quantity;

    public StockReservation(String reservationId, String productId, int quantity) {
        this.reservationId = reservationId;
        this.productId = productId;
        this.quantity = quantity;
    }

    public String getReservationId() { return reservationId; }
}

// ===================== New Interface =====================

public interface InventoryService {
    Product getProduct(String productId);
    void updateStock(String productId, int quantity);
    List<Product> search(SearchCriteria criteria);
    StockReservation reserveStock(String productId, int quantity);
    void releaseReservation(String reservationId);
}

// ===================== Legacy System (Cannot Modify) =====================

public class LegacyInventorySystem {

    // Returns HashMap with string keys: "item_no", "item_name", "sku_code", "qty_on_hand", "unit_price"
    public HashMap<String, Object> fetchItemRecord(int itemNumber) {
        HashMap<String, Object> record = new HashMap<>();
        record.put("item_no", itemNumber);
        record.put("item_name", "Legacy Item " + itemNumber);
        record.put("sku_code", "SKU-" + itemNumber);
        record.put("qty_on_hand", 50);
        record.put("unit_price", 29.99);
        return record;
    }

    // Returns new stock level, or -1 on error
    public int modifyStockLevel(int itemNumber, int newQuantity) {
        return newQuantity >= 0 ? newQuantity : -1;
    }

    // Takes XML query string, returns XML result
    public String xmlQueryItems(String xmlQuery) {
        return "<items><item><no>1</no><name>Widget</name><price>9.99</price><qty>100</qty></item></items>";
    }

    // Returns hold ID (int), or -1 on failure
    public int holdInventory(int itemNumber, int quantity) {
        return 10001; // reservation ID
    }

    // Returns true if cancelled
    public boolean cancelHold(int holdId) {
        return true;
    }
}

// ===================== Adapter =====================

public class LegacyInventoryAdapter implements InventoryService {

    private final LegacyInventorySystem legacy;

    public LegacyInventoryAdapter(LegacyInventorySystem legacy) {
        this.legacy = legacy;
    }

    @Override
    public Product getProduct(String productId) {
        int itemNumber = convertToLegacyId(productId);
        HashMap<String, Object> record = legacy.fetchItemRecord(itemNumber);

        if (record == null || record.isEmpty()) {
            throw new RuntimeException("Product not found: " + productId);
        }

        return new Product(
            productId,
            (String) record.get("item_name"),
            (String) record.get("sku_code"),
            (int) record.get("qty_on_hand"),
            (double) record.get("unit_price")
        );
    }

    @Override
    public void updateStock(String productId, int quantity) {
        int itemNumber = convertToLegacyId(productId);
        int result = legacy.modifyStockLevel(itemNumber, quantity);
        if (result == -1) {
            throw new RuntimeException("Failed to update stock for: " + productId);
        }
    }

    @Override
    public List<Product> search(SearchCriteria criteria) {
        // Convert SearchCriteria to legacy XML query format
        String xmlQuery = buildXmlQuery(criteria);
        String xmlResult = legacy.xmlQueryItems(xmlQuery);

        // Parse XML result into Product list
        return parseXmlToProducts(xmlResult);
    }

    @Override
    public StockReservation reserveStock(String productId, int quantity) {
        int itemNumber = convertToLegacyId(productId);
        int holdId = legacy.holdInventory(itemNumber, quantity);
        if (holdId == -1) {
            throw new RuntimeException("Failed to reserve stock for: " + productId);
        }
        return new StockReservation(String.valueOf(holdId), productId, quantity);
    }

    @Override
    public void releaseReservation(String reservationId) {
        int holdId = Integer.parseInt(reservationId);
        boolean success = legacy.cancelHold(holdId);
        if (!success) {
            throw new RuntimeException("Failed to release reservation: " + reservationId);
        }
    }

    // --- Translation helpers ---

    private int convertToLegacyId(String productId) {
        // New system uses "PROD-123" format; legacy uses int 123
        try {
            return Integer.parseInt(productId.replace("PROD-", ""));
        } catch (NumberFormatException e) {
            throw new IllegalArgumentException("Invalid product ID format: " + productId);
        }
    }

    private String buildXmlQuery(SearchCriteria criteria) {
        StringBuilder xml = new StringBuilder("<query>");
        if (criteria.getKeyword() != null) {
            xml.append("<keyword>").append(criteria.getKeyword()).append("</keyword>");
        }
        if (criteria.getCategory() != null) {
            xml.append("<category>").append(criteria.getCategory()).append("</category>");
        }
        xml.append("</query>");
        return xml.toString();
    }

    private List<Product> parseXmlToProducts(String xml) {
        // Simplified XML parsing — in production use JAXB or Jackson XML
        List<Product> products = new ArrayList<>();
        // Parse each <item> element into Product
        // Simplified: return one product
        products.add(new Product("PROD-1", "Widget", "SKU-1", 100, 9.99));
        return products;
    }
}
```

### Usage

```java
// Wire up adapter
LegacyInventorySystem legacySystem = new LegacyInventorySystem();
InventoryService inventory = new LegacyInventoryAdapter(legacySystem);

// New code uses clean interface — unaware of legacy
Product product = inventory.getProduct("PROD-123");
System.out.println(product.getName() + " — Stock: " + product.getStockQuantity());

inventory.updateStock("PROD-123", 45);

StockReservation reservation = inventory.reserveStock("PROD-123", 5);
// Later: inventory.releaseReservation(reservation.getReservationId());
```

### Interview Talking Points

- **Strangler Fig pattern**: Adapter is step 1 of migrating away from legacy. New features go through the adapter; eventually replace legacy behind it
- **Data format mismatch**: Legacy HashMap/XML vs typed DTOs — adapter is the translation layer
- **ID mapping**: Legacy uses int, new uses String — maintain a mapping table or convention (PROD-{int})
- **Real experience**: On our marketplace, we wrapped a legacy inventory ERP with adapters so the new microservices could communicate without knowing the ERP's SOAP/XML API
