**3.** Cache Manager — Singleton pattern. In-memory cache, single instance for consistency. Design the classes.

```Java
class UserSession {
    private String sessionId;
    // constructor, getters, etc
}

public class CacheManager {
    private static volatile CacheManager instance;
    private UserSession session;

    private CacheManager() {
        loadConfigs();
    }

    private void loadConfigs() {

    }

    public static CacheManager getInstance() {
        if (instance == null) {
            synchronized (CacheManager.class) {
                if (instance == null) {
                    instance = new CacheManager();
                    return instance;
                }
            }
        }
        return instance;
    }
}
```

========== THIS SECTION IS THE ANSWER ==========

## Cache Manager — Singleton Pattern

### Pattern: Singleton + Generic Cache with TTL

**Why Singleton?** Multiple cache instances = duplicated data, inconsistent reads, wasted memory. One instance ensures all components share the same cached state.

### Text UML

```
┌───────────────────────────────────────┐
│        CacheManager<K, V>             │
│        (Singleton)                    │
├───────────────────────────────────────┤
│ - instance: CacheManager              │
│ - cache: ConcurrentHashMap<K, Entry>  │
│ - maxSize: int                        │
│ - defaultTtl: Duration                │
├───────────────────────────────────────┤
│ + getInstance(): CacheManager         │
│ + put(key, value): void               │
│ + put(key, value, ttl): void          │
│ + get(key): Optional<V>              │
│ + evict(key): void                    │
│ + clear(): void                       │
│ + size(): int                         │
└───────────────────────────────────────┘

┌──────────────────────────┐
│   CacheEntry<V>          │
├──────────────────────────┤
│ - value: V               │
│ - createdAt: Instant     │
│ - ttl: Duration          │
├──────────────────────────┤
│ + isExpired(): boolean   │
└──────────────────────────┘
```

### Key Design Decisions

1. **ConcurrentHashMap** — Lock-free reads, segmented writes; no need for external synchronization
2. **TTL per entry** — Each cached item can have its own expiration (sessions=30min, config=1hr)
3. **Max size with LRU eviction** — Prevents OOM; evict least-recently-used when full
4. **Scheduled cleanup** — Background thread purges expired entries periodically

### SOLID Principles Applied

- **SRP**: CacheManager only manages cache lifecycle; eviction policy is separate concern
- **OCP**: Can add new eviction strategies (LFU, FIFO) without modifying core cache
- **ISP**: Simple get/put interface; advanced features (stats, listeners) optional

### Java Code

```java
import java.time.Duration;
import java.time.Instant;
import java.util.*;
import java.util.concurrent.*;

// --- Cache Entry with TTL ---
public class CacheEntry<V> {
    private final V value;
    private final Instant createdAt;
    private final Duration ttl;
    private volatile Instant lastAccessed;

    public CacheEntry(V value, Duration ttl) {
        this.value = value;
        this.createdAt = Instant.now();
        this.lastAccessed = this.createdAt;
        this.ttl = ttl;
    }

    public V getValue() {
        this.lastAccessed = Instant.now();
        return value;
    }

    public boolean isExpired() {
        return Instant.now().isAfter(createdAt.plus(ttl));
    }

    public Instant getLastAccessed() {
        return lastAccessed;
    }
}

// --- Cache Manager (Singleton) ---
public class CacheManager {

    private static volatile CacheManager instance;

    private final ConcurrentHashMap<String, CacheEntry<Object>> cache;
    private final int maxSize;
    private final Duration defaultTtl;
    private final ScheduledExecutorService cleaner;

    private CacheManager(int maxSize, Duration defaultTtl) {
        this.cache = new ConcurrentHashMap<>();
        this.maxSize = maxSize;
        this.defaultTtl = defaultTtl;

        // Background eviction of expired entries every 60s
        this.cleaner = Executors.newSingleThreadScheduledExecutor(r -> {
            Thread t = new Thread(r, "cache-cleaner");
            t.setDaemon(true);
            return t;
        });
        this.cleaner.scheduleAtFixedRate(this::evictExpired, 60, 60, TimeUnit.SECONDS);
    }

    public static CacheManager getInstance() {
        if (instance == null) {
            synchronized (CacheManager.class) {
                if (instance == null) {
                    instance = new CacheManager(10_000, Duration.ofMinutes(30));
                }
            }
        }
        return instance;
    }

    public void put(String key, Object value) {
        put(key, value, defaultTtl);
    }

    public void put(String key, Object value, Duration ttl) {
        if (cache.size() >= maxSize) {
            evictLeastRecentlyUsed();
        }
        cache.put(key, new CacheEntry<>(value, ttl));
    }

    @SuppressWarnings("unchecked")
    public <V> Optional<V> get(String key) {
        CacheEntry<Object> entry = cache.get(key);
        if (entry == null) return Optional.empty();
        if (entry.isExpired()) {
            cache.remove(key);
            return Optional.empty();
        }
        return Optional.of((V) entry.getValue());
    }

    public void evict(String key) {
        cache.remove(key);
    }

    public void clear() {
        cache.clear();
    }

    public int size() {
        return cache.size();
    }

    private void evictExpired() {
        cache.entrySet().removeIf(e -> e.getValue().isExpired());
    }

    private void evictLeastRecentlyUsed() {
        cache.entrySet().stream()
            .min(Comparator.comparing(e -> e.getValue().getLastAccessed()))
            .ifPresent(e -> cache.remove(e.getKey()));
    }

    public void shutdown() {
        cleaner.shutdown();
    }
}
```

### Usage Example

```java
CacheManager cache = CacheManager.getInstance();

// Store with default TTL (30 min)
cache.put("user:123", userSession);

// Store with custom TTL
cache.put("otp:abc", otpCode, Duration.ofMinutes(5));

// Retrieve
Optional<UserSession> session = cache.get("user:123");
session.ifPresent(s -> System.out.println("Session: " + s.getSessionId()));
```

### Interview Talking Points

- **Why not Guava/Caffeine?** In interviews I show the mechanics. In production I use Caffeine (high-performance, near-optimal eviction) or Spring `@Cacheable` with Redis
- **ConcurrentHashMap vs synchronized Map**: CHM allows concurrent reads without locking — critical for cache hit ratios under high concurrency
- **LRU eviction**: The `stream().min()` approach is O(n). Production LRU uses a linked hash map or Caffeine's Window-TinyLFU
- **Spring integration**: `@Cacheable("users")` with `CacheManager` bean — the framework manages singleton lifecycle via IoC
