**26.** Design a Database Connection Protection system using the Proxy pattern. Implement lazy connection, query caching, and audit logging.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Database Connection Protection — Proxy Pattern

**Pattern:** Proxy (Structural)
**Why Proxy?** Controls access to expensive database connection — lazy init, caching, and audit without changing client code.

**SOLID Principles Applied:**
- **SRP:** Proxy handles cross-cutting concerns (cache, audit), real connection handles queries
- **OCP:** Add new proxies (rate limiting, retry) without modifying DatabaseConnection
- **DIP:** Client depends on DatabaseConnection interface, not concrete class

### Text UML Diagram

```
┌─────────────────────┐
│    <<interface>>     │
│  DatabaseConnection  │
│─────────────────────│
│ +connect()           │
│ +disconnect()        │
│ +executeQuery(sql)   │
│ +isConnected()       │
└─────────┬───────────┘
          │ implements
    ┌─────┴──────┐
    │            │
┌───▼────┐  ┌───▼──────────────┐
│  Real  │  │ DatabaseProxy    │
│Database│  │──────────────────│
│Connect │  │ -realConnection  │
│  ion   │  │ -queryCache      │
│        │  │ -auditLog        │
└────────┘  │ -connected       │
            └──────────────────┘

Client ──→ DatabaseProxy ──→ RealDatabaseConnection
              │
              ├── lazy connect (on first query)
              ├── cache queries (Map<sql, result>)
              └── audit log (timestamp + sql)
```

### Key Design Decisions

1. **Lazy connection** — Don't connect until first query; saves resources for connections that may never be used
2. **Query cache with TTL** — Cache SELECT results to reduce DB load; invalidate on write operations
3. **Audit logging** — Log every query with timestamp for compliance; proxy is transparent to client
4. **Thread safety** — synchronized connect to prevent multiple connections in concurrent access

### Java Implementation

```java
import java.util.*;
import java.util.concurrent.ConcurrentHashMap;

// ============================================================
// INTERFACE — common contract for real and proxy connections
// ============================================================

interface DatabaseConnection {
    void connect();
    void disconnect();
    String executeQuery(String sql);
    boolean isConnected();
}

// ============================================================
// REAL SUBJECT — actual database connection
// ============================================================

class RealDatabaseConnection implements DatabaseConnection {
    private final String url;
    private final String username;
    private boolean connected;

    public RealDatabaseConnection(String url, String username) {
        this.url = url;
        this.username = username;
        this.connected = false;
    }

    @Override
    public void connect() {
        // Simulate expensive connection setup
        System.out.println("Connecting to " + url + " as " + username + "...");
        this.connected = true;
        System.out.println("Connected successfully.");
    }

    @Override
    public void disconnect() {
        System.out.println("Disconnecting from " + url);
        this.connected = false;
    }

    @Override
    public String executeQuery(String sql) {
        if (!connected) {
            throw new IllegalStateException("Not connected to database");
        }
        // Simulate query execution
        System.out.println("Executing: " + sql);
        return "ResultSet[" + sql.hashCode() + "]";
    }

    @Override
    public boolean isConnected() {
        return connected;
    }
}

// ============================================================
// CACHE ENTRY — stores cached query result with expiration
// ============================================================

class CacheEntry {
    private final String result;
    private final long expiresAt;

    public CacheEntry(String result, long ttlMillis) {
        this.result = result;
        this.expiresAt = System.currentTimeMillis() + ttlMillis;
    }

    public boolean isExpired() {
        return System.currentTimeMillis() > expiresAt;
    }

    public String getResult() {
        return result;
    }
}

// ============================================================
// AUDIT ENTRY — immutable log record
// ============================================================

class AuditEntry {
    private final String sql;
    private final long timestamp;
    private final String source;
    private final boolean cached;

    public AuditEntry(String sql, String source, boolean cached) {
        this.sql = sql;
        this.timestamp = System.currentTimeMillis();
        this.source = source;
        this.cached = cached;
    }

    @Override
    public String toString() {
        return String.format("[%d] source=%s cached=%s sql=%s",
                timestamp, source, cached, sql);
    }
}

// ============================================================
// PROXY — lazy connect, caching, audit logging
// ============================================================

class DatabaseConnectionProxy implements DatabaseConnection {
    private RealDatabaseConnection realConnection;
    private final String url;
    private final String username;

    // Query cache: sql -> CacheEntry
    private final Map<String, CacheEntry> queryCache = new ConcurrentHashMap<>();
    private static final long CACHE_TTL_MS = 60_000; // 1 minute

    // Audit log
    private final List<AuditEntry> auditLog = Collections.synchronizedList(new ArrayList<>());

    public DatabaseConnectionProxy(String url, String username) {
        this.url = url;
        this.username = username;
        // Note: realConnection is NOT created here (lazy)
    }

    @Override
    public synchronized void connect() {
        if (realConnection == null || !realConnection.isConnected()) {
            realConnection = new RealDatabaseConnection(url, username);
            realConnection.connect();
        }
    }

    @Override
    public void disconnect() {
        if (realConnection != null && realConnection.isConnected()) {
            realConnection.disconnect();
            queryCache.clear();
        }
    }

    @Override
    public String executeQuery(String sql) {
        // 1. Lazy connect — only connect on first actual query
        if (realConnection == null || !realConnection.isConnected()) {
            connect();
        }

        String normalizedSql = sql.trim().toLowerCase();

        // 2. Cache check — only for SELECT queries
        if (isReadQuery(normalizedSql)) {
            CacheEntry cached = queryCache.get(normalizedSql);
            if (cached != null && !cached.isExpired()) {
                auditLog.add(new AuditEntry(sql, "cache", true));
                System.out.println("Cache HIT for: " + sql);
                return cached.getResult();
            }
        }

        // 3. Execute real query
        String result = realConnection.executeQuery(sql);

        // 4. Cache result if SELECT
        if (isReadQuery(normalizedSql)) {
            queryCache.put(normalizedSql, new CacheEntry(result, CACHE_TTL_MS));
        } else {
            // Write query — invalidate entire cache
            queryCache.clear();
        }

        // 5. Audit log
        auditLog.add(new AuditEntry(sql, "database", false));

        return result;
    }

    @Override
    public boolean isConnected() {
        return realConnection != null && realConnection.isConnected();
    }

    public List<AuditEntry> getAuditLog() {
        return Collections.unmodifiableList(auditLog);
    }

    public void clearCache() {
        queryCache.clear();
    }

    private boolean isReadQuery(String sql) {
        return sql.startsWith("select");
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class DatabaseClient {
    public static void main(String[] args) {
        // Client uses interface — doesn't know it's a proxy
        DatabaseConnection db = new DatabaseConnectionProxy(
                "jdbc:postgresql://localhost:5432/mydb", "admin");

        // No connection yet — lazy init
        System.out.println("Connected? " + db.isConnected()); // false

        // First query triggers connection
        String r1 = db.executeQuery("SELECT * FROM users WHERE id = 1");

        // Second identical query hits cache
        String r2 = db.executeQuery("SELECT * FROM users WHERE id = 1");

        // Write query invalidates cache
        db.executeQuery("UPDATE users SET name = 'John' WHERE id = 1");

        // This SELECT will miss cache (invalidated by UPDATE)
        String r3 = db.executeQuery("SELECT * FROM users WHERE id = 1");

        // Print audit log
        DatabaseConnectionProxy proxy = (DatabaseConnectionProxy) db;
        System.out.println("\n--- Audit Log ---");
        proxy.getAuditLog().forEach(System.out::println);

        db.disconnect();
    }
}
```

### Interview Talking Points

- **Why Proxy over Decorator?** Both wrap objects, but Proxy controls *access* (lifecycle, permissions), Decorator adds *behavior*. Here we control when the connection is created and who can query.
- **Cache invalidation strategy:** Simple approach — clear all cache on any write. In production, use more granular invalidation per table.
- **Real-world usage:** Spring's `LazyConnectionDataSourceProxy` does exactly this — wraps DataSource to defer connection acquisition until first Statement execution.
