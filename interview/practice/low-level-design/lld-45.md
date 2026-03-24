**45.** Design a Database Result Set using the Iterator pattern. Handle large result sets with forward/backward/random access without loading all data.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Database Result Set — Iterator Pattern

**Pattern:** Iterator (Behavioral)
**Why Iterator?** Database queries can return millions of rows. Iterator enables cursor-based pagination without loading everything into memory.

**SOLID Principles Applied:**
- **SRP:** Cursor handles navigation; DataSource handles query execution
- **OCP:** Add new cursor types (scrollable, keyset pagination) without changing client
- **DIP:** Client depends on ResultSetIterator interface, not database driver

### Text UML Diagram

```
┌─────────────────┐    ┌────────────────────────┐
│     Client      │───→│  <<interface>>          │
│                 │    │  ResultSetIterator<T>   │
│                 │    │────────────────────────│
│                 │    │ +hasNext()              │
│                 │    │ +next()                 │
│                 │    │ +hasPrevious()          │
│                 │    │ +previous()             │
│                 │    │ +goTo(index)            │
│                 │    │ +getPage(page,size)     │
│                 │    │ +getTotalCount()        │
│                 │    │ +close()                │
│                 │    └─────────┬──────────────┘
│                 │              │ implements
│                 │    ┌─────────┼──────────┐
│                 │  Forward   Scrollable  Keyset
│                 │  Cursor    Cursor      Cursor
└─────────────────┘  (offset)  (LIMIT+    (WHERE id >
                     only)     OFFSET)     last_id)
```

### Key Design Decisions

1. **Cursor-based pagination** — Fetches one page at a time from DB
2. **Forward-only for streaming** — Minimal memory for large exports
3. **Scrollable for UI pagination** — Supports go-to-page, backward navigation
4. **Keyset pagination** — Best performance for large tables (no OFFSET scan)
5. **Auto-closeable** — Implements AutoCloseable to release DB resources

### Java Implementation

```java
import java.util.*;

// ============================================================
// DOMAIN MODELS
// ============================================================

class Row {
    private final Map<String, Object> columns;

    public Row(Map<String, Object> columns) {
        this.columns = new LinkedHashMap<>(columns);
    }

    @SuppressWarnings("unchecked")
    public <T> T get(String column) { return (T) columns.get(column); }
    public Map<String, Object> getColumns() { return Collections.unmodifiableMap(columns); }

    @Override
    public String toString() { return columns.toString(); }
}

class Page<T> {
    private final List<T> items;
    private final int pageNumber;
    private final int pageSize;
    private final long totalElements;

    public Page(List<T> items, int pageNumber, int pageSize, long totalElements) {
        this.items = items;
        this.pageNumber = pageNumber;
        this.pageSize = pageSize;
        this.totalElements = totalElements;
    }

    public List<T> getItems() { return items; }
    public int getPageNumber() { return pageNumber; }
    public int getTotalPages() { return (int) Math.ceil((double) totalElements / pageSize); }
    public long getTotalElements() { return totalElements; }
    public boolean hasNext() { return pageNumber < getTotalPages() - 1; }
    public boolean hasPrevious() { return pageNumber > 0; }
}

// ============================================================
// SIMULATED DATA SOURCE — stands in for real JDBC/JPA
// ============================================================

class DataSource {
    private final List<Row> allData;

    public DataSource(List<Row> data) {
        this.allData = data;
    }

    /** Simulate SELECT ... LIMIT pageSize OFFSET offset */
    public List<Row> fetchPage(int offset, int limit) {
        int start = Math.min(offset, allData.size());
        int end = Math.min(offset + limit, allData.size());
        System.out.printf("  [DB] SELECT ... LIMIT %d OFFSET %d (returned %d rows)%n",
                limit, offset, end - start);
        return new ArrayList<>(allData.subList(start, end));
    }

    /** Simulate SELECT ... WHERE id > lastId LIMIT pageSize */
    public List<Row> fetchAfter(Object lastId, int limit) {
        int startIdx = 0;
        if (lastId != null) {
            for (int i = 0; i < allData.size(); i++) {
                if (allData.get(i).get("id").equals(lastId)) {
                    startIdx = i + 1;
                    break;
                }
            }
        }
        int end = Math.min(startIdx + limit, allData.size());
        System.out.printf("  [DB] SELECT ... WHERE id > %s LIMIT %d (returned %d rows)%n",
                lastId, limit, end - startIdx);
        return new ArrayList<>(allData.subList(startIdx, end));
    }

    public long count() { return allData.size(); }
}

// ============================================================
// ITERATOR INTERFACE
// ============================================================

interface ResultSetIterator<T> extends AutoCloseable {
    boolean hasNext();
    T next();
    long getTotalCount();
    void close();
}

// ============================================================
// FORWARD-ONLY CURSOR — streaming, minimal memory
// ============================================================

class ForwardCursor implements ResultSetIterator<Row> {
    private final DataSource dataSource;
    private final int fetchSize;
    private List<Row> buffer;
    private int bufferIndex;
    private int globalOffset;
    private final long totalCount;
    private boolean closed;

    public ForwardCursor(DataSource dataSource, int fetchSize) {
        this.dataSource = dataSource;
        this.fetchSize = fetchSize;
        this.totalCount = dataSource.count();
        this.globalOffset = 0;
        this.bufferIndex = 0;
        this.buffer = Collections.emptyList();
        this.closed = false;
    }

    @Override
    public boolean hasNext() {
        ensureOpen();
        if (bufferIndex < buffer.size()) return true;
        if (globalOffset >= totalCount) return false;
        // Fetch next page
        buffer = dataSource.fetchPage(globalOffset, fetchSize);
        bufferIndex = 0;
        globalOffset += buffer.size();
        return !buffer.isEmpty();
    }

    @Override
    public Row next() {
        if (!hasNext()) throw new NoSuchElementException();
        return buffer.get(bufferIndex++);
    }

    @Override
    public long getTotalCount() { return totalCount; }

    @Override
    public void close() {
        closed = true;
        buffer = null;
    }

    private void ensureOpen() {
        if (closed) throw new IllegalStateException("Cursor is closed");
    }
}

// ============================================================
// SCROLLABLE CURSOR — supports pagination and random access
// ============================================================

class ScrollableCursor implements ResultSetIterator<Row> {
    private final DataSource dataSource;
    private final int pageSize;
    private int currentPage;
    private List<Row> currentPageData;
    private int indexInPage;
    private final long totalCount;
    private boolean closed;

    public ScrollableCursor(DataSource dataSource, int pageSize) {
        this.dataSource = dataSource;
        this.pageSize = pageSize;
        this.totalCount = dataSource.count();
        this.currentPage = -1;
        this.indexInPage = 0;
        this.closed = false;
    }

    @Override
    public boolean hasNext() {
        ensureOpen();
        if (currentPageData != null && indexInPage < currentPageData.size()) return true;
        return (currentPage + 1) * pageSize < totalCount;
    }

    @Override
    public Row next() {
        if (currentPageData == null || indexInPage >= currentPageData.size()) {
            loadPage(currentPage + 1);
        }
        if (indexInPage >= currentPageData.size()) throw new NoSuchElementException();
        return currentPageData.get(indexInPage++);
    }

    public Page<Row> getPage(int pageNumber) {
        loadPage(pageNumber);
        return new Page<>(currentPageData, pageNumber, pageSize, totalCount);
    }

    public void goTo(int globalIndex) {
        int targetPage = globalIndex / pageSize;
        loadPage(targetPage);
        indexInPage = globalIndex % pageSize;
    }

    @Override
    public long getTotalCount() { return totalCount; }

    @Override
    public void close() {
        closed = true;
        currentPageData = null;
    }

    private void loadPage(int page) {
        if (page != currentPage) {
            currentPage = page;
            int offset = page * pageSize;
            currentPageData = dataSource.fetchPage(offset, pageSize);
            indexInPage = 0;
        }
    }

    private void ensureOpen() {
        if (closed) throw new IllegalStateException("Cursor is closed");
    }
}

// ============================================================
// KEYSET CURSOR — best for large datasets (no OFFSET scan)
// ============================================================

class KeysetCursor implements ResultSetIterator<Row> {
    private final DataSource dataSource;
    private final int fetchSize;
    private List<Row> buffer;
    private int bufferIndex;
    private Object lastSeenId;
    private boolean exhausted;
    private final long totalCount;

    public KeysetCursor(DataSource dataSource, int fetchSize) {
        this.dataSource = dataSource;
        this.fetchSize = fetchSize;
        this.totalCount = dataSource.count();
        this.lastSeenId = null;
        this.exhausted = false;
        this.buffer = Collections.emptyList();
        this.bufferIndex = 0;
    }

    @Override
    public boolean hasNext() {
        if (bufferIndex < buffer.size()) return true;
        if (exhausted) return false;
        buffer = dataSource.fetchAfter(lastSeenId, fetchSize);
        bufferIndex = 0;
        if (buffer.isEmpty()) {
            exhausted = true;
            return false;
        }
        lastSeenId = buffer.get(buffer.size() - 1).get("id");
        return true;
    }

    @Override
    public Row next() {
        if (!hasNext()) throw new NoSuchElementException();
        return buffer.get(bufferIndex++);
    }

    @Override
    public long getTotalCount() { return totalCount; }

    @Override
    public void close() {
        buffer = null;
        exhausted = true;
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class ResultSetDemo {
    public static void main(String[] args) {
        // Create sample data
        List<Row> data = new ArrayList<>();
        for (int i = 1; i <= 20; i++) {
            data.add(new Row(Map.of("id", i, "name", "User" + i, "email", "user" + i + "@test.com")));
        }
        DataSource ds = new DataSource(data);

        // Forward-only cursor — streaming 5 rows at a time
        System.out.println("=== Forward Cursor (fetch=5) ===");
        try (ForwardCursor cursor = new ForwardCursor(ds, 5)) {
            int count = 0;
            while (cursor.hasNext() && count < 8) {
                System.out.println("  " + cursor.next());
                count++;
            }
        }

        // Scrollable cursor — random page access
        System.out.println("\n=== Scrollable Cursor (pageSize=5) ===");
        ScrollableCursor scrollable = new ScrollableCursor(ds, 5);
        Page<Row> page2 = scrollable.getPage(2); // jump to page 2
        System.out.println("Page " + page2.getPageNumber() + " of " + page2.getTotalPages());
        page2.getItems().forEach(r -> System.out.println("  " + r));
        scrollable.close();

        // Keyset cursor — efficient for large tables
        System.out.println("\n=== Keyset Cursor (fetch=5) ===");
        try (KeysetCursor keyset = new KeysetCursor(ds, 5)) {
            int count = 0;
            while (keyset.hasNext() && count < 7) {
                System.out.println("  " + keyset.next());
                count++;
            }
            System.out.println("Total: " + keyset.getTotalCount());
        }
    }
}
```

### Interview Talking Points

- **OFFSET vs Keyset pagination:** OFFSET scans and discards rows (O(offset)); keyset uses WHERE clause on indexed column (O(1) seek). For page 10000 with size 20, OFFSET scans 200,000 rows; keyset scans 20.
- **Spring Data parallel:** `Pageable` + `Page<T>` is Spring's scrollable cursor. `Slice<T>` is keyset-like (knows if there's a next page without COUNT).
- **JDBC ResultSet:** Java's `java.sql.ResultSet` is literally this pattern — `ResultSet.TYPE_FORWARD_ONLY` vs `TYPE_SCROLL_INSENSITIVE`. Our ForwardCursor mirrors the JDBC forward-only behavior.
- **Memory safety:** Forward cursor holds only one page in memory at a time. Essential for batch exports of millions of rows.
