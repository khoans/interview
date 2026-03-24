**8.** Report Generation System — Builder pattern. Reports with various configs: date range, filters, columns, sorting, formatting. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## Report Generation System — Builder Pattern

### Pattern: Builder (Step Builder variant)

**Why Builder?** Report configuration has 8+ optional parameters. Telescoping constructors would be unreadable. Builder lets callers set only what they need with a fluent, self-documenting API.

### Text UML

```
┌───────────────────────────────┐     ┌──────────────────────┐
│       ReportConfig            │     │  ReportGenerator     │
├───────────────────────────────┤     ├──────────────────────┤
│ - title: String               │     │ + generate(config):  │
│ - dateRange: DateRange        │     │     ReportOutput     │
│ - filters: Map<String,Object> │     └──────────────────────┘
│ - columns: List<ColumnDef>    │
│ - sortBy: List<SortSpec>      │     ┌──────────────────────┐
│ - format: ReportFormat        │     │  ReportOutput        │
│ - pageSize: int               │     ├──────────────────────┤
│ - includeChart: boolean       │     │ - data: byte[]       │
│ - includeSummary: boolean     │     │ - format: ReportFormat│
├───────────────────────────────┤     │ - generatedAt: Instant│
│ + builder(): Builder          │     └──────────────────────┘
└───────────────────────────────┘

┌────────────────────────────────┐
│     ReportConfig.Builder       │
├────────────────────────────────┤
│ + title(t): Builder            │
│ + dateRange(from, to): Builder │
│ + addFilter(k, v): Builder     │
│ + addColumn(col): Builder      │
│ + sortBy(col, dir): Builder    │
│ + format(f): Builder           │
│ + pageSize(n): Builder         │
│ + includeChart(): Builder      │
│ + includeSummary(): Builder    │
│ + build(): ReportConfig        │
└────────────────────────────────┘
```

### Key Design Decisions

1. **Static inner Builder** — Standard Java pattern (see Effective Java Item 2); keeps builder close to its product
2. **Immutable ReportConfig** — Once built, config cannot change. Safe for caching, threading
3. **ColumnDef with formatting** — Each column knows its data type, display name, width, and format pattern
4. **Sensible defaults** — Format=PDF, pageSize=50, includeSummary=true — minimal config still produces a useful report

### SOLID Principles Applied

- **SRP**: Builder constructs config; ReportGenerator produces output; ColumnDef defines columns
- **OCP**: Add new config options (watermark, footer) without breaking existing builder usage
- **DIP**: ReportGenerator depends on ReportConfig abstraction, not concrete formatting details

### Java Code

```java
import java.time.LocalDate;
import java.time.Instant;
import java.util.*;

// --- Supporting Types ---
public enum ReportFormat { PDF, EXCEL, HTML, CSV }

public enum SortDirection { ASC, DESC }

public class DateRange {
    private final LocalDate from;
    private final LocalDate to;

    public DateRange(LocalDate from, LocalDate to) {
        if (from.isAfter(to)) throw new IllegalArgumentException("from must be before to");
        this.from = from;
        this.to = to;
    }

    public LocalDate getFrom() { return from; }
    public LocalDate getTo() { return to; }
}

public class ColumnDef {
    private final String field;
    private final String displayName;
    private final int width;
    private final String formatPattern; // e.g., "#,##0.00" for currency

    public ColumnDef(String field, String displayName, int width, String formatPattern) {
        this.field = field;
        this.displayName = displayName;
        this.width = width;
        this.formatPattern = formatPattern;
    }

    public String getField() { return field; }
    public String getDisplayName() { return displayName; }
    public int getWidth() { return width; }
    public String getFormatPattern() { return formatPattern; }
}

public class SortSpec {
    private final String column;
    private final SortDirection direction;

    public SortSpec(String column, SortDirection direction) {
        this.column = column;
        this.direction = direction;
    }

    public String getColumn() { return column; }
    public SortDirection getDirection() { return direction; }
}

// --- Immutable Report Config ---
public class ReportConfig {
    private final String title;
    private final DateRange dateRange;
    private final Map<String, Object> filters;
    private final List<ColumnDef> columns;
    private final List<SortSpec> sortBy;
    private final ReportFormat format;
    private final int pageSize;
    private final boolean includeChart;
    private final boolean includeSummary;

    private ReportConfig(Builder builder) {
        this.title = builder.title;
        this.dateRange = builder.dateRange;
        this.filters = Collections.unmodifiableMap(builder.filters);
        this.columns = Collections.unmodifiableList(builder.columns);
        this.sortBy = Collections.unmodifiableList(builder.sortBy);
        this.format = builder.format;
        this.pageSize = builder.pageSize;
        this.includeChart = builder.includeChart;
        this.includeSummary = builder.includeSummary;
    }

    // Getters
    public String getTitle() { return title; }
    public DateRange getDateRange() { return dateRange; }
    public Map<String, Object> getFilters() { return filters; }
    public List<ColumnDef> getColumns() { return columns; }
    public List<SortSpec> getSortBy() { return sortBy; }
    public ReportFormat getFormat() { return format; }
    public int getPageSize() { return pageSize; }
    public boolean isIncludeChart() { return includeChart; }
    public boolean isIncludeSummary() { return includeSummary; }

    public static Builder builder(String title) {
        return new Builder(title);
    }

    // --- Static Inner Builder ---
    public static class Builder {
        // Required
        private final String title;
        // Optional with defaults
        private DateRange dateRange;
        private final Map<String, Object> filters = new LinkedHashMap<>();
        private final List<ColumnDef> columns = new ArrayList<>();
        private final List<SortSpec> sortBy = new ArrayList<>();
        private ReportFormat format = ReportFormat.PDF;
        private int pageSize = 50;
        private boolean includeChart = false;
        private boolean includeSummary = true;

        private Builder(String title) {
            this.title = Objects.requireNonNull(title, "Title is required");
        }

        public Builder dateRange(LocalDate from, LocalDate to) {
            this.dateRange = new DateRange(from, to);
            return this;
        }

        public Builder addFilter(String key, Object value) {
            this.filters.put(key, value);
            return this;
        }

        public Builder addColumn(String field, String displayName, int width) {
            this.columns.add(new ColumnDef(field, displayName, width, null));
            return this;
        }

        public Builder addColumn(String field, String displayName, int width, String pattern) {
            this.columns.add(new ColumnDef(field, displayName, width, pattern));
            return this;
        }

        public Builder sortBy(String column, SortDirection direction) {
            this.sortBy.add(new SortSpec(column, direction));
            return this;
        }

        public Builder format(ReportFormat format) {
            this.format = format;
            return this;
        }

        public Builder pageSize(int pageSize) {
            this.pageSize = pageSize;
            return this;
        }

        public Builder includeChart() {
            this.includeChart = true;
            return this;
        }

        public Builder includeSummary(boolean include) {
            this.includeSummary = include;
            return this;
        }

        public ReportConfig build() {
            if (columns.isEmpty()) {
                throw new IllegalStateException("At least one column is required");
            }
            return new ReportConfig(this);
        }
    }
}

// --- Report Output ---
public class ReportOutput {
    private final byte[] data;
    private final ReportFormat format;
    private final Instant generatedAt;

    public ReportOutput(byte[] data, ReportFormat format) {
        this.data = data;
        this.format = format;
        this.generatedAt = Instant.now();
    }

    public byte[] getData() { return data; }
    public ReportFormat getFormat() { return format; }
    public Instant getGeneratedAt() { return generatedAt; }
}

// --- Report Generator ---
public class ReportGenerator {

    public ReportOutput generate(ReportConfig config) {
        // 1. Fetch data based on filters and date range
        // 2. Apply sorting
        // 3. Format columns with patterns
        // 4. Generate output in requested format
        // Simplified: produce a summary string
        StringBuilder content = new StringBuilder();
        content.append("Report: ").append(config.getTitle()).append("\n");
        if (config.getDateRange() != null) {
            content.append("Period: ").append(config.getDateRange().getFrom())
                   .append(" to ").append(config.getDateRange().getTo()).append("\n");
        }
        content.append("Columns: ");
        for (ColumnDef col : config.getColumns()) {
            content.append(col.getDisplayName()).append(" | ");
        }
        content.append("\nFormat: ").append(config.getFormat());
        content.append("\nPage Size: ").append(config.getPageSize());
        if (config.isIncludeSummary()) content.append("\n[Summary Section]");
        if (config.isIncludeChart()) content.append("\n[Chart Section]");

        return new ReportOutput(content.toString().getBytes(), config.getFormat());
    }
}
```

### Usage

```java
ReportConfig config = ReportConfig.builder("Monthly Sales Report")
    .dateRange(LocalDate.of(2024, 1, 1), LocalDate.of(2024, 1, 31))
    .addFilter("region", "US")
    .addFilter("category", "Electronics")
    .addColumn("date", "Date", 100)
    .addColumn("product", "Product Name", 200)
    .addColumn("revenue", "Revenue ($)", 120, "#,##0.00")
    .addColumn("quantity", "Qty", 80)
    .sortBy("revenue", SortDirection.DESC)
    .format(ReportFormat.EXCEL)
    .pageSize(100)
    .includeChart()
    .build();

ReportOutput output = new ReportGenerator().generate(config);
```

### Interview Talking Points

- **Effective Java Item 2**: This is the canonical Builder pattern from Joshua Bloch — static inner class, private constructor, required params in builder constructor
- **Immutability**: `Collections.unmodifiableList/Map` ensures config cannot be modified after build — thread-safe by design
- **Validation in build()**: Fail fast with clear error messages vs producing invalid reports
- **Real-world**: In our marketplace, dealer performance reports used a similar builder. Marketing could configure dashboards without touching code
