**40.** Design a Data Import System using the Template Method pattern. Steps: Open, Read Header, Validate, Parse, Transform, Save, Close. Varies by CSV/Excel/XML.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Data Import System — Template Method Pattern

**Pattern:** Template Method (Behavioral)
**Why Template Method?** Import workflow is identical (open, validate, parse, save, close) but each file format has different parsing logic. Template defines the skeleton; subclasses fill in format-specific steps.

**SOLID Principles Applied:**
- **SRP:** Base class owns the workflow; subclasses own format-specific parsing
- **OCP:** Add JSON, Parquet importers without touching the base workflow
- **DIP:** Client depends on abstract DataImporter, not specific format classes

### Text UML Diagram

```
┌──────────────────────────────┐
│   <<abstract>>               │
│   DataImporter               │
│──────────────────────────────│
│ # importData() ← TEMPLATE   │  ← final method: defines the algorithm
│──────────────────────────────│
│ # openFile()       abstract  │  ← subclass implements
│ # readHeader()     abstract  │
│ # validateRow()    abstract  │
│ # parseRow()       abstract  │
│ # transformRow()   hook      │  ← optional override
│ # saveData()       concrete  │  ← shared implementation
│ # closeFile()      abstract  │
│ # onError()        hook      │  ← optional override
└──────────┬───────────────────┘
           │ extends
    ┌──────┼──────┐
    │      │      │
  CSV   Excel   XML
Importer Importer Importer
```

### Key Design Decisions

1. **Template method is `final`** — Prevents subclasses from changing the workflow order
2. **Hook methods** — `transformRow()` and `onError()` have default implementations but can be overridden
3. **Validation per row** — Skip invalid rows rather than failing entire import
4. **ImportResult** — Returns stats (imported, skipped, errors) for caller to inspect

### Java Implementation

```java
import java.util.*;

// ============================================================
// DATA MODELS
// ============================================================

class ImportResult {
    private int totalRows;
    private int importedRows;
    private int skippedRows;
    private final List<String> errors = new ArrayList<>();

    public void incrementTotal() { totalRows++; }
    public void incrementImported() { importedRows++; }
    public void incrementSkipped() { skippedRows++; }
    public void addError(String error) { errors.add(error); }

    public int getTotalRows() { return totalRows; }
    public int getImportedRows() { return importedRows; }
    public int getSkippedRows() { return skippedRows; }
    public List<String> getErrors() { return Collections.unmodifiableList(errors); }

    @Override
    public String toString() {
        return String.format("Total: %d | Imported: %d | Skipped: %d | Errors: %d",
                totalRows, importedRows, skippedRows, errors.size());
    }
}

class DataRow {
    private final Map<String, String> columns;

    public DataRow(Map<String, String> columns) {
        this.columns = new LinkedHashMap<>(columns);
    }

    public String get(String column) { return columns.get(column); }
    public void set(String column, String value) { columns.put(column, value); }
    public Map<String, String> getColumns() { return Collections.unmodifiableMap(columns); }

    @Override
    public String toString() { return columns.toString(); }
}

// ============================================================
// ABSTRACT TEMPLATE — defines the import algorithm
// ============================================================

abstract class DataImporter {
    protected List<String> headers;
    protected final List<DataRow> importedData = new ArrayList<>();

    /**
     * TEMPLATE METHOD — defines the import workflow.
     * Final to prevent subclasses from changing the order.
     */
    public final ImportResult importData(String source) {
        ImportResult result = new ImportResult();
        try {
            System.out.println("=== Importing from: " + source + " ===");

            // Step 1: Open file/connection
            openFile(source);

            // Step 2: Read header row
            headers = readHeader();
            System.out.println("Headers: " + headers);

            // Step 3-6: Process each row
            List<String[]> rawRows = readAllRows();
            for (String[] rawRow : rawRows) {
                result.incrementTotal();
                try {
                    // Step 3: Validate
                    if (!validateRow(rawRow)) {
                        result.incrementSkipped();
                        continue;
                    }

                    // Step 4: Parse into DataRow
                    DataRow row = parseRow(rawRow);

                    // Step 5: Transform (hook — optional override)
                    row = transformRow(row);

                    // Step 6: Collect for saving
                    importedData.add(row);
                    result.incrementImported();

                } catch (Exception e) {
                    result.addError("Row " + result.getTotalRows() + ": " + e.getMessage());
                    onError(rawRow, e); // hook
                }
            }

            // Step 7: Save all imported data
            saveData(importedData);

        } catch (Exception e) {
            result.addError("Fatal: " + e.getMessage());
        } finally {
            // Step 8: Close
            closeFile();
        }

        System.out.println("Result: " + result);
        return result;
    }

    // ---- Abstract methods (subclass MUST implement) ----
    protected abstract void openFile(String source);
    protected abstract List<String> readHeader();
    protected abstract List<String[]> readAllRows();
    protected abstract boolean validateRow(String[] rawRow);
    protected abstract DataRow parseRow(String[] rawRow);
    protected abstract void closeFile();

    // ---- Hook methods (subclass CAN override) ----
    protected DataRow transformRow(DataRow row) {
        // Default: no transformation
        return row;
    }

    protected void onError(String[] rawRow, Exception e) {
        // Default: log and continue
        System.err.println("Error processing row: " + Arrays.toString(rawRow) + " - " + e.getMessage());
    }

    // ---- Concrete method (shared by all) ----
    protected void saveData(List<DataRow> data) {
        System.out.println("Saving " + data.size() + " rows to database...");
        // In real app: batch insert via JdbcTemplate/JPA
        data.forEach(row -> System.out.println("  Saved: " + row));
    }
}

// ============================================================
// CONCRETE IMPORTERS
// ============================================================

class CsvImporter extends DataImporter {
    private List<String> lines;
    private String delimiter;

    public CsvImporter() {
        this(",");
    }

    public CsvImporter(String delimiter) {
        this.delimiter = delimiter;
    }

    @Override
    protected void openFile(String source) {
        System.out.println("Opening CSV file: " + source);
        // Simulate reading file lines
        lines = List.of(
                "name,email,age",
                "Alice,alice@test.com,30",
                "Bob,,25",              // invalid: empty email
                "Charlie,charlie@test.com,28"
        );
    }

    @Override
    protected List<String> readHeader() {
        return Arrays.asList(lines.get(0).split(delimiter));
    }

    @Override
    protected List<String[]> readAllRows() {
        List<String[]> rows = new ArrayList<>();
        for (int i = 1; i < lines.size(); i++) {
            rows.add(lines.get(i).split(delimiter, -1));
        }
        return rows;
    }

    @Override
    protected boolean validateRow(String[] rawRow) {
        if (rawRow.length != headers.size()) return false;
        // Check no empty required fields
        for (String field : rawRow) {
            if (field == null || field.trim().isEmpty()) return false;
        }
        return true;
    }

    @Override
    protected DataRow parseRow(String[] rawRow) {
        Map<String, String> columns = new LinkedHashMap<>();
        for (int i = 0; i < headers.size(); i++) {
            columns.put(headers.get(i), rawRow[i].trim());
        }
        return new DataRow(columns);
    }

    @Override
    protected void closeFile() {
        lines = null;
        System.out.println("CSV file closed");
    }
}

class XmlImporter extends DataImporter {
    private List<Map<String, String>> elements;

    @Override
    protected void openFile(String source) {
        System.out.println("Parsing XML file: " + source);
        // Simulate XML parsing into element maps
        elements = List.of(
                Map.of("name", "Alice", "email", "alice@test.com", "age", "30"),
                Map.of("name", "David", "email", "david@test.com", "age", "35")
        );
    }

    @Override
    protected List<String> readHeader() {
        if (elements.isEmpty()) return List.of();
        return new ArrayList<>(elements.get(0).keySet());
    }

    @Override
    protected List<String[]> readAllRows() {
        List<String[]> rows = new ArrayList<>();
        for (Map<String, String> element : elements) {
            String[] row = headers.stream()
                    .map(h -> element.getOrDefault(h, ""))
                    .toArray(String[]::new);
            rows.add(row);
        }
        return rows;
    }

    @Override
    protected boolean validateRow(String[] rawRow) {
        return rawRow.length == headers.size();
    }

    @Override
    protected DataRow parseRow(String[] rawRow) {
        Map<String, String> columns = new LinkedHashMap<>();
        for (int i = 0; i < headers.size(); i++) {
            columns.put(headers.get(i), rawRow[i]);
        }
        return new DataRow(columns);
    }

    @Override
    protected DataRow transformRow(DataRow row) {
        // XML-specific: trim whitespace from all values
        Map<String, String> cleaned = new LinkedHashMap<>();
        row.getColumns().forEach((k, v) -> cleaned.put(k, v.strip()));
        return new DataRow(cleaned);
    }

    @Override
    protected void closeFile() {
        elements = null;
        System.out.println("XML parser closed");
    }
}

class ExcelImporter extends DataImporter {
    private List<String[]> sheetData;

    @Override
    protected void openFile(String source) {
        System.out.println("Opening Excel workbook: " + source);
        // Simulate reading Excel sheet (Apache POI in real app)
        sheetData = List.of(
                new String[]{"name", "email", "age"},
                new String[]{"Eve", "eve@test.com", "27"},
                new String[]{"Frank", "frank@test.com", "33"}
        );
    }

    @Override
    protected List<String> readHeader() {
        return Arrays.asList(sheetData.get(0));
    }

    @Override
    protected List<String[]> readAllRows() {
        return sheetData.subList(1, sheetData.size());
    }

    @Override
    protected boolean validateRow(String[] rawRow) {
        return rawRow.length == headers.size();
    }

    @Override
    protected DataRow parseRow(String[] rawRow) {
        Map<String, String> columns = new LinkedHashMap<>();
        for (int i = 0; i < headers.size(); i++) {
            columns.put(headers.get(i), rawRow[i]);
        }
        return new DataRow(columns);
    }

    @Override
    protected void closeFile() {
        sheetData = null;
        System.out.println("Excel workbook closed");
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class DataImportDemo {
    public static void main(String[] args) {
        DataImporter csvImporter = new CsvImporter();
        csvImporter.importData("users.csv");

        System.out.println();

        DataImporter xmlImporter = new XmlImporter();
        xmlImporter.importData("users.xml");

        System.out.println();

        DataImporter excelImporter = new ExcelImporter();
        excelImporter.importData("users.xlsx");
    }
}
```

### Interview Talking Points

- **Template Method vs Strategy:** Template Method uses *inheritance* — subclass overrides steps. Strategy uses *composition* — inject interchangeable algorithms. Template Method is better when the algorithm skeleton is fixed.
- **Hollywood Principle ("Don't call us, we'll call you"):** Base class calls subclass methods, not the other way around. This inverts control flow.
- **Real-world:** Spring's `JdbcTemplate`, `AbstractController`, and `RestTemplate` all use Template Method. The "Template" in the name is a clue.
- **Hook vs Abstract:** Abstract methods are mandatory; hooks are optional with sensible defaults. Use hooks sparingly — too many make the template confusing.
