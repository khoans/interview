**2.** Logger Service — Singleton pattern. Thread-safe logging to files. Design the classes.

```Java
public class LoggingService {
    private static volatile LoggingService instance;

    private LoggingService() {
        // Loading dependencies
    }

    public static LoggingService getInstance() {
        if (instance == null) {
            synchronized (LoggingService.class) {
                if (instance == null) {
                    instance = new LoggingService();
                    return instance;
                }
            }
        }
        return instance;
    }
}
```

========== THIS SECTION IS THE ANSWER ==========

## Logger Service — Singleton Pattern

### Pattern: Singleton + Strategy (for log destinations)

**Why Singleton?** Multiple logger instances = file handle conflicts, interleaved writes, lost log ordering. One instance with a thread-safe queue ensures consistent output.

### Text UML

```
┌─────────────────────┐       ┌──────────────────┐
│    LoggerService     │       │  <<interface>>    │
│    (Singleton)       │──────>│   LogWriter       │
├─────────────────────┤       ├──────────────────┤
│ - instance: Logger   │       │ + write(entry)    │
│ - logWriter: LogWriter│      └──────┬───────────┘
│ - level: LogLevel    │             │
├─────────────────────┤    ┌────────┼─────────┐
│ + getInstance()      │    │        │         │
│ + info(msg)          │ FileWriter ConsoleWriter  AsyncWriter
│ + warn(msg)          │
│ + error(msg, ex)     │
│ + setLevel(level)    │
└─────────────────────┘

┌──────────────┐
│  <<enum>>    │
│  LogLevel    │
├──────────────┤
│ DEBUG        │
│ INFO         │
│ WARN         │
│ ERROR        │
└──────────────┘
```

### Key Design Decisions

1. **BlockingQueue** — Decouples log producers from file I/O; background thread drains queue
2. **LogWriter interface** — Strategy pattern allows swapping file/console/async destinations
3. **LogLevel filtering** — Only process messages at or above configured level
4. **Thread name + timestamp** — Essential for debugging concurrent systems

### SOLID Principles Applied

- **SRP**: LoggerService orchestrates; LogWriter handles I/O
- **OCP**: New destinations (Kafka, DB) implement LogWriter without changing LoggerService
- **DIP**: Depends on LogWriter abstraction, not concrete file I/O

### Java Code

```java
import java.io.*;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.concurrent.*;

// --- Log Level Enum ---
public enum LogLevel {
    DEBUG(0), INFO(1), WARN(2), ERROR(3);

    private final int severity;
    LogLevel(int severity) { this.severity = severity; }
    public int getSeverity() { return severity; }
}

// --- Log Entry ---
public class LogEntry {
    private final LogLevel level;
    private final String message;
    private final String threadName;
    private final LocalDateTime timestamp;
    private final Throwable throwable;

    public LogEntry(LogLevel level, String message, Throwable throwable) {
        this.level = level;
        this.message = message;
        this.threadName = Thread.currentThread().getName();
        this.timestamp = LocalDateTime.now();
        this.throwable = throwable;
    }

    public String format() {
        String ts = timestamp.format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss.SSS"));
        String base = String.format("[%s] [%s] [%s] %s", ts, level, threadName, message);
        if (throwable != null) {
            StringWriter sw = new StringWriter();
            throwable.printStackTrace(new PrintWriter(sw));
            return base + "\n" + sw.toString();
        }
        return base;
    }

    public LogLevel getLevel() { return level; }
}

// --- LogWriter Interface (Strategy) ---
public interface LogWriter {
    void write(LogEntry entry);
    void close();
}

// --- File-based LogWriter ---
public class FileLogWriter implements LogWriter {
    private final PrintWriter writer;

    public FileLogWriter(String filePath) {
        try {
            this.writer = new PrintWriter(
                new BufferedWriter(new FileWriter(filePath, true)), true);
        } catch (IOException e) {
            throw new RuntimeException("Cannot open log file: " + filePath, e);
        }
    }

    @Override
    public void write(LogEntry entry) {
        writer.println(entry.format());
    }

    @Override
    public void close() {
        writer.close();
    }
}

// --- Logger Service (Singleton) ---
public class LoggerService {

    private static volatile LoggerService instance;

    private final BlockingQueue<LogEntry> queue = new LinkedBlockingQueue<>(10_000);
    private final LogWriter logWriter;
    private volatile LogLevel minLevel = LogLevel.INFO;
    private final Thread writerThread;
    private volatile boolean running = true;

    private LoggerService(LogWriter logWriter) {
        this.logWriter = logWriter;
        this.writerThread = new Thread(this::processQueue, "log-writer");
        this.writerThread.setDaemon(true);
        this.writerThread.start();
    }

    public static LoggerService getInstance() {
        if (instance == null) {
            synchronized (LoggerService.class) {
                if (instance == null) {
                    instance = new LoggerService(new FileLogWriter("application.log"));
                }
            }
        }
        return instance;
    }

    // Convenience methods
    public void debug(String msg) { log(LogLevel.DEBUG, msg, null); }
    public void info(String msg)  { log(LogLevel.INFO, msg, null); }
    public void warn(String msg)  { log(LogLevel.WARN, msg, null); }
    public void error(String msg, Throwable ex) { log(LogLevel.ERROR, msg, ex); }

    public void setLevel(LogLevel level) {
        this.minLevel = level;
    }

    private void log(LogLevel level, String message, Throwable throwable) {
        if (level.getSeverity() < minLevel.getSeverity()) return;
        LogEntry entry = new LogEntry(level, message, throwable);
        if (!queue.offer(entry)) {
            System.err.println("Log queue full, dropping: " + message);
        }
    }

    private void processQueue() {
        while (running || !queue.isEmpty()) {
            try {
                LogEntry entry = queue.poll(100, TimeUnit.MILLISECONDS);
                if (entry != null) {
                    logWriter.write(entry);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                break;
            }
        }
        logWriter.close();
    }

    public void shutdown() {
        running = false;
        try {
            writerThread.join(5000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

### Interview Talking Points

- **Why async queue?** Direct file writes block the caller thread. In high-throughput systems (e.g., our 30M user/month marketplace), blocking on I/O per log call kills performance
- **Bounded queue (10K)** — Prevents OOM under log storms; `offer()` drops instead of blocking
- **Production reality**: We use SLF4J + Logback in Spring Boot, which does exactly this — async appender + rolling file policy. This design mirrors that architecture
- **Testability**: Inject a mock `LogWriter` to verify log output in unit tests without touching filesystem
