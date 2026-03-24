**1.** Configuration Manager — Singleton pattern. Global config settings loaded once, accessed everywhere. Design the classes.

```Java
public class SystemConfig {
    private static volatile SystemConfig instance;
    private SystemConfig() {
        loadConfigs();
    }

    private void loadConfigs() {
        // logic
    }

    public static SystemConfig getInstance() {
        if (instance == null) {
            synchronized (SystemConfig.class) {
                if (instance == null) {
                    instance = new SystemConfig();
                    return instance;
                }
            }
        }
        return instance;
    }
}
```


Alternative

```Java
public class SystemConfig {
    private SystemConfig() {
        loadConfigs();
    }

    private void loadConfigs() {
        // Logic
    }

    public static SystemConfig getInstance() {
        return Holder.INSTANCE;
    }

    private static class Holder {
        private static final SystemConfig INSTANCE = new SystemConfig();
    }
}
```

========== THIS SECTION IS THE ANSWER ==========

## Configuration Manager — Singleton Pattern

### Pattern: Singleton (Double-Checked Locking + Holder Idiom)

**Why Singleton?** Config must be loaded once and shared globally. Multiple instances = inconsistent settings, wasted memory, potential race conditions on startup.

### Text UML

```
┌─────────────────────────────────────┐
│        ConfigurationManager         │
├─────────────────────────────────────┤
│ - instance: ConfigurationManager    │
│ - properties: Map<String, String>   │
│ - loadedAt: Instant                 │
├─────────────────────────────────────┤
│ - ConfigurationManager()            │
│ + getInstance(): ConfigurationManager│
│ + get(key): String                  │
│ + getInt(key): int                  │
│ + getBoolean(key): boolean          │
│ + reload(): void                    │
└─────────────────────────────────────┘
```

### Key Design Decisions

1. **Double-checked locking** — `volatile` + `synchronized` prevents partial construction visibility across threads
2. **Holder idiom alternative** — Leverages JVM class loading guarantee (lazy + thread-safe without `synchronized`)
3. **Immutable config map** — Use `Collections.unmodifiableMap` to prevent accidental mutation
4. **Reload with lock** — Allow hot-reload but protect with write lock

### SOLID Principles Applied

- **SRP**: Only manages configuration state
- **OCP**: Add new config keys without modifying the class
- **DIP**: Consumers depend on `ConfigurationManager` interface, not file I/O details

### Java Code

```java
import java.io.InputStream;
import java.time.Instant;
import java.util.*;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class ConfigurationManager {

    private static volatile ConfigurationManager instance;

    private Map<String, String> properties;
    private Instant loadedAt;
    private final ReadWriteLock lock = new ReentrantReadWriteLock();

    // Private constructor — loads config from classpath
    private ConfigurationManager() {
        this.properties = loadFromFile("application.properties");
        this.loadedAt = Instant.now();
    }

    // Double-checked locking — thread-safe, lazy init
    public static ConfigurationManager getInstance() {
        if (instance == null) {
            synchronized (ConfigurationManager.class) {
                if (instance == null) {
                    instance = new ConfigurationManager();
                }
            }
        }
        return instance;
    }

    public String get(String key) {
        lock.readLock().lock();
        try {
            return properties.get(key);
        } finally {
            lock.readLock().unlock();
        }
    }

    public String get(String key, String defaultValue) {
        String val = get(key);
        return val != null ? val : defaultValue;
    }

    public int getInt(String key, int defaultValue) {
        String val = get(key);
        return val != null ? Integer.parseInt(val) : defaultValue;
    }

    public boolean getBoolean(String key, boolean defaultValue) {
        String val = get(key);
        return val != null ? Boolean.parseBoolean(val) : defaultValue;
    }

    // Hot-reload: re-read config file, swap map atomically
    public void reload() {
        lock.writeLock().lock();
        try {
            this.properties = loadFromFile("application.properties");
            this.loadedAt = Instant.now();
        } finally {
            lock.writeLock().unlock();
        }
    }

    public Instant getLoadedAt() {
        return loadedAt;
    }

    private Map<String, String> loadFromFile(String filename) {
        Map<String, String> map = new HashMap<>();
        try (InputStream is = getClass().getClassLoader().getResourceAsStream(filename)) {
            Properties props = new Properties();
            if (is != null) {
                props.load(is);
            }
            for (String name : props.stringPropertyNames()) {
                map.put(name, props.getProperty(name));
            }
        } catch (Exception e) {
            throw new RuntimeException("Failed to load config: " + filename, e);
        }
        return Collections.unmodifiableMap(map);
    }
}
```

### Alternative: Holder Idiom (Preferred in Modern Java)

```java
public class ConfigurationManager {

    private ConfigurationManager() {
        // load config
    }

    public static ConfigurationManager getInstance() {
        return Holder.INSTANCE;
    }

    // JVM guarantees thread-safe, lazy class loading
    private static class Holder {
        private static final ConfigurationManager INSTANCE = new ConfigurationManager();
    }
}
```

### Interview Talking Points

- **Why not enum singleton?** Enum works but lacks flexibility for reload and testing (hard to mock)
- **Spring equivalent**: `@Configuration` + `@Value` — Spring manages singleton scope via IoC container
- **Production reality**: In Spring Boot projects, I use `@ConfigurationProperties` with refresh scope. Standalone Singleton is useful for libraries or non-Spring modules
- **ReadWriteLock**: Allows concurrent reads, exclusive writes — essential for hot-reload scenarios
