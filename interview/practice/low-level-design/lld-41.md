**41.** Design a Report Generation Framework using the Template Method pattern. Steps: Fetch, Sort, Format, Header/Footer, Export. Varies by report type.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Report Generation Framework — Template Method Pattern

**Pattern:** Template Method (Behavioral)
**Why Template Method?** Report generation follows a fixed pipeline (fetch, sort, format, export) but each report type has different data sources, formatting rules, and output formats.

**SOLID Principles Applied:**
- **SRP:** Base defines workflow; each report type handles its specific formatting
- **OCP:** Add new report types (Inventory, Audit) without modifying base
- **DIP:** Client works with abstract ReportGenerator

### Text UML Diagram

```
┌──────────────────────────────────┐
│   <<abstract>>                   │
│   ReportGenerator                │
│──────────────────────────────────│
│ # generateReport() ← TEMPLATE   │  final
│──────────────────────────────────│
│ # fetchData()          abstract  │
│ # sortData()           hook      │
│ # formatHeader()       abstract  │
│ # formatRow()          abstract  │
│ # formatFooter()       abstract  │
│ # export()             abstract  │
│ # addSummary()         hook      │  optional
└──────────┬───────────────────────┘
           │ extends
    ┌──────┼──────┬──────────┐
  Sales   Employee  Financial
  Report  Report    Report
  (PDF)   (CSV)     (HTML)
```

### Key Design Decisions

1. **ReportData abstraction** — Report data stored as `List<Map<String, Object>>` for flexibility
2. **Sort is a hook** — Default sorts by first column; subclass can override
3. **Export format varies** — Sales=PDF, Employee=CSV, Financial=HTML
4. **Summary section** — Optional hook for totals/aggregations

### Java Implementation

```java
import java.util.*;
import java.util.stream.Collectors;

// ============================================================
// DATA MODELS
// ============================================================

class ReportConfig {
    private final String title;
    private final String author;
    private final Date generatedAt;

    public ReportConfig(String title, String author) {
        this.title = title;
        this.author = author;
        this.generatedAt = new Date();
    }

    public String getTitle() { return title; }
    public String getAuthor() { return author; }
    public Date getGeneratedAt() { return generatedAt; }
}

// ============================================================
// ABSTRACT TEMPLATE
// ============================================================

abstract class ReportGenerator {
    protected ReportConfig config;
    protected List<String> columns;
    protected List<Map<String, Object>> data;

    /**
     * TEMPLATE METHOD — fixed report generation pipeline.
     */
    public final String generateReport(ReportConfig config) {
        this.config = config;
        StringBuilder output = new StringBuilder();

        System.out.println("Generating report: " + config.getTitle());

        // Step 1: Fetch data from source
        data = fetchData();
        System.out.println("Fetched " + data.size() + " rows");

        // Step 2: Sort data (hook — has default)
        data = sortData(data);

        // Step 3: Format header
        output.append(formatHeader());

        // Step 4: Format each row
        for (Map<String, Object> row : data) {
            output.append(formatRow(row));
        }

        // Step 5: Add summary (hook — optional)
        String summary = addSummary(data);
        if (summary != null) {
            output.append(summary);
        }

        // Step 6: Format footer
        output.append(formatFooter());

        // Step 7: Export
        String result = output.toString();
        export(result);
        return result;
    }

    // ---- Abstract methods ----
    protected abstract List<Map<String, Object>> fetchData();
    protected abstract String formatHeader();
    protected abstract String formatRow(Map<String, Object> row);
    protected abstract String formatFooter();
    protected abstract void export(String content);

    // ---- Hook methods ----
    protected List<Map<String, Object>> sortData(List<Map<String, Object>> data) {
        // Default: no sorting
        return data;
    }

    protected String addSummary(List<Map<String, Object>> data) {
        // Default: no summary
        return null;
    }
}

// ============================================================
// SALES REPORT — formatted as simple text/PDF-like
// ============================================================

class SalesReport extends ReportGenerator {

    @Override
    protected List<Map<String, Object>> fetchData() {
        columns = List.of("date", "product", "quantity", "revenue");
        List<Map<String, Object>> rows = new ArrayList<>();
        rows.add(Map.of("date", "2025-01-15", "product", "Widget A", "quantity", 100, "revenue", 2500.00));
        rows.add(Map.of("date", "2025-01-16", "product", "Widget B", "quantity", 50, "revenue", 1750.00));
        rows.add(Map.of("date", "2025-01-15", "product", "Widget C", "quantity", 75, "revenue", 3000.00));
        return rows;
    }

    @Override
    protected List<Map<String, Object>> sortData(List<Map<String, Object>> data) {
        data.sort(Comparator.comparing(row -> row.get("date").toString()));
        return data;
    }

    @Override
    protected String formatHeader() {
        return String.format("====== %s ======%nAuthor: %s | Date: %s%n%n%-12s %-15s %8s %10s%n%s%n",
                config.getTitle(), config.getAuthor(), config.getGeneratedAt(),
                "Date", "Product", "Qty", "Revenue",
                "-".repeat(50));
    }

    @Override
    protected String formatRow(Map<String, Object> row) {
        return String.format("%-12s %-15s %8s %10s%n",
                row.get("date"), row.get("product"),
                row.get("quantity"), String.format("$%.2f", ((Number) row.get("revenue")).doubleValue()));
    }

    @Override
    protected String addSummary(List<Map<String, Object>> data) {
        double totalRevenue = data.stream()
                .mapToDouble(r -> ((Number) r.get("revenue")).doubleValue())
                .sum();
        int totalQty = data.stream()
                .mapToInt(r -> ((Number) r.get("quantity")).intValue())
                .sum();
        return String.format("%s%n%-12s %-15s %8d %10s%n", "-".repeat(50),
                "", "TOTAL", totalQty, String.format("$%.2f", totalRevenue));
    }

    @Override
    protected String formatFooter() {
        return "\n====== End of Report ======\n";
    }

    @Override
    protected void export(String content) {
        System.out.println("Exporting Sales Report as PDF...");
        // In real app: use iText or Apache PDFBox
    }
}

// ============================================================
// EMPLOYEE REPORT — formatted as CSV
// ============================================================

class EmployeeReport extends ReportGenerator {

    @Override
    protected List<Map<String, Object>> fetchData() {
        columns = List.of("id", "name", "department", "salary");
        List<Map<String, Object>> rows = new ArrayList<>();
        rows.add(Map.of("id", "E001", "name", "Alice", "department", "Engineering", "salary", 95000));
        rows.add(Map.of("id", "E002", "name", "Bob", "department", "Marketing", "salary", 75000));
        rows.add(Map.of("id", "E003", "name", "Charlie", "department", "Engineering", "salary", 105000));
        return rows;
    }

    @Override
    protected List<Map<String, Object>> sortData(List<Map<String, Object>> data) {
        data.sort(Comparator.comparing(row -> row.get("name").toString()));
        return data;
    }

    @Override
    protected String formatHeader() {
        return String.join(",", columns) + "\n";
    }

    @Override
    protected String formatRow(Map<String, Object> row) {
        return columns.stream()
                .map(col -> row.get(col).toString())
                .collect(Collectors.joining(",")) + "\n";
    }

    @Override
    protected String formatFooter() {
        return "# Generated: " + config.getGeneratedAt() + "\n";
    }

    @Override
    protected void export(String content) {
        System.out.println("Exporting Employee Report as CSV...");
        // In real app: write to file with BufferedWriter
    }
}

// ============================================================
// FINANCIAL REPORT — formatted as HTML
// ============================================================

class FinancialReport extends ReportGenerator {

    @Override
    protected List<Map<String, Object>> fetchData() {
        columns = List.of("category", "q1", "q2", "q3", "q4");
        List<Map<String, Object>> rows = new ArrayList<>();
        rows.add(Map.of("category", "Revenue", "q1", 50000, "q2", 62000, "q3", 58000, "q4", 71000));
        rows.add(Map.of("category", "Expenses", "q1", 35000, "q2", 38000, "q3", 36000, "q4", 42000));
        return rows;
    }

    @Override
    protected String formatHeader() {
        StringBuilder sb = new StringBuilder();
        sb.append("<html><head><title>").append(config.getTitle()).append("</title></head>\n");
        sb.append("<body><h1>").append(config.getTitle()).append("</h1>\n");
        sb.append("<table border='1'><tr>");
        for (String col : columns) {
            sb.append("<th>").append(col).append("</th>");
        }
        sb.append("</tr>\n");
        return sb.toString();
    }

    @Override
    protected String formatRow(Map<String, Object> row) {
        StringBuilder sb = new StringBuilder("<tr>");
        for (String col : columns) {
            sb.append("<td>").append(row.get(col)).append("</td>");
        }
        sb.append("</tr>\n");
        return sb.toString();
    }

    @Override
    protected String formatFooter() {
        return "</table>\n<p>Generated by: " + config.getAuthor() + "</p>\n</body></html>\n";
    }

    @Override
    protected void export(String content) {
        System.out.println("Exporting Financial Report as HTML...");
        // In real app: write HTML file or send via email
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class ReportDemo {
    public static void main(String[] args) {
        ReportConfig salesConfig = new ReportConfig("Q1 Sales Report", "Finance Team");
        ReportGenerator salesReport = new SalesReport();
        String salesOutput = salesReport.generateReport(salesConfig);
        System.out.println(salesOutput);

        ReportConfig empConfig = new ReportConfig("Employee Directory", "HR Dept");
        ReportGenerator empReport = new EmployeeReport();
        String empOutput = empReport.generateReport(empConfig);
        System.out.println(empOutput);
    }
}
```

### Interview Talking Points

- **Template Method in Spring:** `JdbcTemplate.execute()` defines the workflow (get connection, execute, handle errors, close). Your callback fills in the query-specific part. Same with `RestTemplate`.
- **Hook vs Abstract:** `sortData()` and `addSummary()` are hooks (optional). `fetchData()` and `formatRow()` are abstract (required). This balance gives flexibility without forcing unnecessary overrides.
- **When to switch to Strategy:** If you need to mix-and-match (e.g., fetch from DB but export as PDF, or fetch from API but export as CSV), the pipeline steps become independent strategies rather than a fixed template.
