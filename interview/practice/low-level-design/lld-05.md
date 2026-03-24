**5.** Document Export System — Factory pattern. Export to PDF, Excel, CSV, JSON with different creation logic. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## Document Export System — Factory Pattern

### Pattern: Factory Method

**Why Factory?** Each export format has radically different creation logic (PDF needs layout engine, Excel needs cell formatting, CSV is plain text). Factory encapsulates this complexity and lets client code work with a uniform `DocumentExporter` interface.

### Text UML

```
┌────────────────────────┐       ┌─────────────────────────┐
│  <<interface>>         │       │   ExporterFactory       │
│  DocumentExporter      │       ├─────────────────────────┤
├────────────────────────┤       │ + create(type, config): │
│ + export(data): byte[] │       │     DocumentExporter    │
│ + getContentType(): String     └─────────────────────────┘
│ + getFileExtension(): String│
└──────────┬─────────────┘
           │
    ┌──────┼──────────┬──────────────┐
    │      │          │              │
PdfExporter ExcelExporter CsvExporter JsonExporter

┌────────────────────┐
│  ExportConfig      │
├────────────────────┤
│ - title: String    │
│ - author: String   │
│ - columns: List    │
│ - dateFormat: String│
└────────────────────┘
```

### Key Design Decisions

1. **Interface-based** — All exporters share the same contract; callers never cast
2. **ExportConfig** — Encapsulates options (title, columns, formatting) passed to each exporter
3. **byte[] return** — Exporters produce raw bytes; caller decides to write to file, HTTP response, or S3
4. **Enum for type safety** — `ExportFormat` enum prevents invalid type strings

### SOLID Principles Applied

- **SRP**: Each exporter handles only its format's logic
- **OCP**: Add new format (XML, Parquet) by implementing interface + registering in factory
- **DIP**: Client depends on `DocumentExporter` interface, not concrete classes
- **LSP**: All exporters are substitutable — same input, different output format

### Java Code

```java
import java.nio.charset.StandardCharsets;
import java.util.*;

// --- Export Format Enum ---
public enum ExportFormat {
    PDF, EXCEL, CSV, JSON
}

// --- Export Configuration ---
public class ExportConfig {
    private final String title;
    private final List<String> columns;
    private final String dateFormat;

    public ExportConfig(String title, List<String> columns, String dateFormat) {
        this.title = title;
        this.columns = columns;
        this.dateFormat = dateFormat;
    }

    public String getTitle() { return title; }
    public List<String> getColumns() { return columns; }
    public String getDateFormat() { return dateFormat; }
}

// --- Exporter Interface ---
public interface DocumentExporter {
    byte[] export(List<Map<String, Object>> data);
    String getContentType();
    String getFileExtension();
}

// --- PDF Exporter ---
public class PdfExporter implements DocumentExporter {
    private final ExportConfig config;

    public PdfExporter(ExportConfig config) {
        this.config = config;
    }

    @Override
    public byte[] export(List<Map<String, Object>> data) {
        // In production: use iText or Apache PDFBox
        StringBuilder pdf = new StringBuilder();
        pdf.append("%PDF-1.4 ").append(config.getTitle()).append("\n");
        // Build table layout with columns
        for (String col : config.getColumns()) {
            pdf.append(col).append("\t");
        }
        pdf.append("\n");
        for (Map<String, Object> row : data) {
            for (String col : config.getColumns()) {
                pdf.append(row.getOrDefault(col, "")).append("\t");
            }
            pdf.append("\n");
        }
        return pdf.toString().getBytes(StandardCharsets.UTF_8);
    }

    @Override
    public String getContentType() { return "application/pdf"; }

    @Override
    public String getFileExtension() { return ".pdf"; }
}

// --- CSV Exporter ---
public class CsvExporter implements DocumentExporter {
    private final ExportConfig config;

    public CsvExporter(ExportConfig config) {
        this.config = config;
    }

    @Override
    public byte[] export(List<Map<String, Object>> data) {
        StringBuilder csv = new StringBuilder();
        // Header row
        csv.append(String.join(",", config.getColumns())).append("\n");
        // Data rows
        for (Map<String, Object> row : data) {
            StringJoiner joiner = new StringJoiner(",");
            for (String col : config.getColumns()) {
                String val = String.valueOf(row.getOrDefault(col, ""));
                // Escape commas in values
                joiner.add(val.contains(",") ? "\"" + val + "\"" : val);
            }
            csv.append(joiner).append("\n");
        }
        return csv.toString().getBytes(StandardCharsets.UTF_8);
    }

    @Override
    public String getContentType() { return "text/csv"; }

    @Override
    public String getFileExtension() { return ".csv"; }
}

// --- JSON Exporter ---
public class JsonExporter implements DocumentExporter {
    private final ExportConfig config;

    public JsonExporter(ExportConfig config) {
        this.config = config;
    }

    @Override
    public byte[] export(List<Map<String, Object>> data) {
        // In production: use Jackson ObjectMapper
        StringBuilder json = new StringBuilder();
        json.append("[\n");
        for (int i = 0; i < data.size(); i++) {
            json.append("  {");
            Map<String, Object> row = data.get(i);
            StringJoiner fields = new StringJoiner(", ");
            for (String col : config.getColumns()) {
                Object val = row.getOrDefault(col, null);
                fields.add("\"" + col + "\": \"" + val + "\"");
            }
            json.append(fields);
            json.append(i < data.size() - 1 ? "},\n" : "}\n");
        }
        json.append("]");
        return json.toString().getBytes(StandardCharsets.UTF_8);
    }

    @Override
    public String getContentType() { return "application/json"; }

    @Override
    public String getFileExtension() { return ".json"; }
}

// --- Excel Exporter ---
public class ExcelExporter implements DocumentExporter {
    private final ExportConfig config;

    public ExcelExporter(ExportConfig config) {
        this.config = config;
    }

    @Override
    public byte[] export(List<Map<String, Object>> data) {
        // In production: use Apache POI
        // Simplified: produce tab-separated content
        StringBuilder excel = new StringBuilder();
        excel.append(String.join("\t", config.getColumns())).append("\n");
        for (Map<String, Object> row : data) {
            StringJoiner joiner = new StringJoiner("\t");
            for (String col : config.getColumns()) {
                joiner.add(String.valueOf(row.getOrDefault(col, "")));
            }
            excel.append(joiner).append("\n");
        }
        return excel.toString().getBytes(StandardCharsets.UTF_8);
    }

    @Override
    public String getContentType() {
        return "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet";
    }

    @Override
    public String getFileExtension() { return ".xlsx"; }
}

// --- Factory ---
public class ExporterFactory {

    public static DocumentExporter create(ExportFormat format, ExportConfig config) {
        switch (format) {
            case PDF:   return new PdfExporter(config);
            case EXCEL: return new ExcelExporter(config);
            case CSV:   return new CsvExporter(config);
            case JSON:  return new JsonExporter(config);
            default:
                throw new IllegalArgumentException("Unsupported format: " + format);
        }
    }
}
```

### Usage

```java
ExportConfig config = new ExportConfig("Sales Report",
    List.of("date", "product", "amount"), "yyyy-MM-dd");

DocumentExporter exporter = ExporterFactory.create(ExportFormat.CSV, config);
byte[] output = exporter.export(salesData);

// Write to HTTP response
response.setContentType(exporter.getContentType());
response.getOutputStream().write(output);
```

### Interview Talking Points

- **Why Simple Factory over Abstract Factory?** One product family (exporters), simple creation logic. Abstract Factory is overkill here
- **Production**: In Spring, register exporters as beans with `@Qualifier` or use a `Map<ExportFormat, DocumentExporter>` injected by Spring
- **Real experience**: On our marketplace, we exported dealer reports in CSV/Excel. The factory pattern let us add PDF export without touching existing code
