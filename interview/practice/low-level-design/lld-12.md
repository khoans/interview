**12.** Database Connection Pool — Prototype pattern. Clone pre-created connections, reset state. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## Database Connection Pool — Prototype Pattern

### Pattern: Prototype + Object Pool

**Why Prototype?** Creating DB connections from scratch is expensive (~50-200ms for TCP handshake, auth, SSL). Pre-create a template connection, clone it to populate the pool, reset state on return.

### Text UML

```
┌──────────────────────────────┐
│     DatabaseConnection       │
├──────────────────────────────┤
│ - connectionId: String       │
│ - url: String                │
│ - username: String           │
│ - autoCommit: boolean        │
│ - transactionIsolation: int  │
│ - inUse: boolean             │
│ - createdAt: Instant         │
├──────────────────────────────┤
│ + deepClone(): Connection    │
│ + reset(): void              │
│ + isValid(): boolean         │
│ + close(): void              │
└──────────────────────────────┘

┌──────────────────────────────────┐
│       ConnectionPool             │
├──────────────────────────────────┤
│ - pool: BlockingQueue<Connection>│
│ - template: DatabaseConnection   │
│ - maxSize: int                   │
│ - activeCount: AtomicInteger     │
├──────────────────────────────────┤
│ + getConnection(): Connection    │
│ + releaseConnection(conn): void  │
│ + getPoolStats(): PoolStats      │
│ + shutdown(): void               │
└──────────────────────────────────┘

┌────────────────────────┐
│    PoolConfig          │
├────────────────────────┤
│ - url: String          │
│ - username: String     │
│ - password: String     │
│ - minSize: int         │
│ - maxSize: int         │
│ - maxWait: Duration    │
│ - validationQuery: Str │
└────────────────────────┘
```

### Key Design Decisions

1. **Template connection** — One fully configured connection serves as prototype for cloning
2. **BlockingQueue** — Thread-safe pool with built-in wait/timeout for connection acquisition
3. **reset() on return** — Clear transaction state, auto-commit, temp tables before reuse
4. **Validation before use** — `isValid()` check ensures connection isn't stale/broken
5. **Pool stats** — Monitor active/idle/total for operational visibility

### SOLID Principles Applied

- **SRP**: `DatabaseConnection` manages connection state; `ConnectionPool` manages lifecycle
- **OCP**: Add connection types (read replica, write master) by cloning different templates
- **DIP**: Pool works with `DatabaseConnection` abstraction

### Java Code

```java
import java.time.Duration;
import java.time.Instant;
import java.util.UUID;
import java.util.concurrent.*;
import java.util.concurrent.atomic.AtomicInteger;

// --- Pool Configuration ---
public class PoolConfig {
    private final String url;
    private final String username;
    private final String password;
    private final int minSize;
    private final int maxSize;
    private final Duration maxWaitTime;
    private final String validationQuery;

    public PoolConfig(String url, String username, String password,
                      int minSize, int maxSize, Duration maxWaitTime) {
        this.url = url;
        this.username = username;
        this.password = password;
        this.minSize = minSize;
        this.maxSize = maxSize;
        this.maxWaitTime = maxWaitTime;
        this.validationQuery = "SELECT 1";
    }

    public String getUrl() { return url; }
    public String getUsername() { return username; }
    public String getPassword() { return password; }
    public int getMinSize() { return minSize; }
    public int getMaxSize() { return maxSize; }
    public Duration getMaxWaitTime() { return maxWaitTime; }
    public String getValidationQuery() { return validationQuery; }
}

// --- Database Connection (Prototype) ---
public class DatabaseConnection {
    private final String connectionId;
    private final String url;
    private final String username;
    private boolean autoCommit;
    private int transactionIsolation;
    private volatile boolean inUse;
    private final Instant createdAt;
    private boolean closed;

    public DatabaseConnection(String url, String username) {
        this.connectionId = UUID.randomUUID().toString().substring(0, 8);
        this.url = url;
        this.username = username;
        this.autoCommit = true;
        this.transactionIsolation = 2; // TRANSACTION_READ_COMMITTED
        this.inUse = false;
        this.createdAt = Instant.now();
        this.closed = false;
        // Simulate expensive connection setup
        simulateConnect();
    }

    // Private constructor for cloning
    private DatabaseConnection(String url, String username, boolean autoCommit,
                                int isolation) {
        this.connectionId = UUID.randomUUID().toString().substring(0, 8);
        this.url = url;
        this.username = username;
        this.autoCommit = autoCommit;
        this.transactionIsolation = isolation;
        this.inUse = false;
        this.createdAt = Instant.now();
        this.closed = false;
        simulateConnect();
    }

    // Prototype: clone the connection config, create fresh connection
    public DatabaseConnection deepClone() {
        return new DatabaseConnection(url, username, autoCommit, transactionIsolation);
    }

    // Reset state for reuse (clear transaction, temp tables, session vars)
    public void reset() {
        this.autoCommit = true;
        this.transactionIsolation = 2;
        this.inUse = false;
        // In real impl: ROLLBACK if transaction active, clear temp tables
    }

    public boolean isValid() {
        // In real impl: execute validation query with timeout
        return !closed && createdAt.plusSeconds(3600).isAfter(Instant.now());
    }

    public void close() {
        this.closed = true;
    }

    public void setInUse(boolean inUse) { this.inUse = inUse; }
    public boolean isInUse() { return inUse; }
    public String getConnectionId() { return connectionId; }

    private void simulateConnect() {
        // Real: TCP handshake + auth + SSL negotiation
    }

    @Override
    public String toString() {
        return "Conn[" + connectionId + "] " + url + " inUse=" + inUse;
    }
}

// --- Pool Stats ---
public class PoolStats {
    private final int totalConnections;
    private final int activeConnections;
    private final int idleConnections;

    public PoolStats(int total, int active, int idle) {
        this.totalConnections = total;
        this.activeConnections = active;
        this.idleConnections = idle;
    }

    @Override
    public String toString() {
        return String.format("Pool[total=%d, active=%d, idle=%d]",
            totalConnections, activeConnections, idleConnections);
    }
}

// --- Connection Pool ---
public class ConnectionPool {
    private final BlockingQueue<DatabaseConnection> availableConnections;
    private final DatabaseConnection template;
    private final PoolConfig config;
    private final AtomicInteger totalCreated = new AtomicInteger(0);
    private final AtomicInteger activeCount = new AtomicInteger(0);

    public ConnectionPool(PoolConfig config) {
        this.config = config;
        this.availableConnections = new LinkedBlockingQueue<>(config.getMaxSize());

        // Create template connection
        this.template = new DatabaseConnection(config.getUrl(), config.getUsername());

        // Pre-populate pool with minSize clones
        for (int i = 0; i < config.getMinSize(); i++) {
            DatabaseConnection conn = template.deepClone();
            availableConnections.offer(conn);
            totalCreated.incrementAndGet();
        }
    }

    public DatabaseConnection getConnection() {
        // Try to get from pool
        DatabaseConnection conn = availableConnections.poll();

        if (conn != null) {
            if (conn.isValid()) {
                conn.setInUse(true);
                activeCount.incrementAndGet();
                return conn;
            } else {
                // Stale connection — discard, create new
                conn.close();
            }
        }

        // Pool empty — create new if under max
        if (totalCreated.get() < config.getMaxSize()) {
            conn = template.deepClone();
            totalCreated.incrementAndGet();
            conn.setInUse(true);
            activeCount.incrementAndGet();
            return conn;
        }

        // At max — wait for one to be returned
        try {
            conn = availableConnections.poll(
                config.getMaxWaitTime().toMillis(), TimeUnit.MILLISECONDS);
            if (conn == null) {
                throw new RuntimeException("Connection pool exhausted, timeout after "
                    + config.getMaxWaitTime());
            }
            conn.setInUse(true);
            activeCount.incrementAndGet();
            return conn;
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            throw new RuntimeException("Interrupted while waiting for connection", e);
        }
    }

    public void releaseConnection(DatabaseConnection conn) {
        conn.reset(); // Reset state for reuse
        activeCount.decrementAndGet();
        if (!availableConnections.offer(conn)) {
            // Pool full — discard
            conn.close();
            totalCreated.decrementAndGet();
        }
    }

    public PoolStats getStats() {
        int active = activeCount.get();
        int idle = availableConnections.size();
        return new PoolStats(active + idle, active, idle);
    }

    public void shutdown() {
        DatabaseConnection conn;
        while ((conn = availableConnections.poll()) != null) {
            conn.close();
        }
        template.close();
    }
}
```

### Usage

```java
PoolConfig config = new PoolConfig(
    "jdbc:postgresql://localhost:5432/mydb",
    "admin", "secret",
    5,   // minSize
    20,  // maxSize
    Duration.ofSeconds(5) // maxWait
);

ConnectionPool pool = new ConnectionPool(config);

// Acquire connection
DatabaseConnection conn = pool.getConnection();
try {
    // Use connection for queries
    System.out.println("Using: " + conn);
} finally {
    pool.releaseConnection(conn); // Always release!
}

System.out.println(pool.getStats()); // Pool[total=5, active=0, idle=5]
```

### Interview Talking Points

- **Prototype role**: Template connection is cloned to populate pool — avoids repeating configuration for each new connection
- **Production**: HikariCP (fastest Java pool) uses similar concepts — pre-warm connections, validate on borrow, reset on return
- **reset() is critical**: Without state reset, previous transaction's isolation level or uncommitted changes leak to the next user
- **BlockingQueue**: Natural fit for pool — thread-safe borrow/return with built-in wait semantics
