**17.** Data Stream Processing — Decorator pattern. Optional compression, encryption, buffering, logging. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## Data Stream Processing — Decorator Pattern

### Pattern: Decorator

**Why Decorator?** Stream processing needs are combinatorial: encrypt+compress, compress+log, buffer+encrypt+log, etc. Each processing step is independent and stackable. Decorator avoids the 2^4 = 16 subclass explosion.

### Text UML

```
┌───────────────────────────┐
│    <<interface>>          │
│    DataStream             │
├───────────────────────────┤
│ + write(data): byte[]     │
│ + read(): byte[]          │
│ + getDescription(): String│
└──────────┬────────────────┘
           │
    ┌──────┴──────────────────────────┐
    │                                  │
┌───┴──────────────┐     ┌────────────┴──────────┐
│ BaseDataStream   │     │ <<abstract>>          │
│ (raw byte stream)│     │ StreamDecorator       │
└──────────────────┘     ├───────────────────────┤
                         │ - wrapped: DataStream │
                         └──────────┬────────────┘
                                    │
                     ┌──────┬───────┼──────┐
                     │      │       │      │
               Compression Encryption Buffering Logging
               Decorator   Decorator  Decorator Decorator
```

### Key Design Decisions

1. **byte[] in/out** — Each decorator transforms bytes and passes to next. Clean pipeline
2. **Bidirectional** — `write()` applies transformations forward; `read()` applies them in reverse
3. **Order matters** — Compress then encrypt (smaller ciphertext) vs encrypt then compress (no compression benefit)
4. **Transparent stacking** — Each decorator is unaware of others in the chain

### SOLID Principles Applied

- **SRP**: Each decorator handles exactly one transformation
- **OCP**: Add new transformations (checksum, rate-limiting) without modifying existing ones
- **LSP**: Decorated stream IS-A DataStream — any consumer works with any combination
- **DIP**: Processing pipeline depends on DataStream interface

### Java Code

```java
import java.io.*;
import java.nio.charset.StandardCharsets;
import java.util.Base64;
import java.util.zip.*;

// --- DataStream Interface ---
public interface DataStream {
    byte[] write(byte[] data);
    byte[] read(byte[] data);
    String getDescription();
}

// --- Base Stream (no transformation) ---
public class BaseDataStream implements DataStream {
    @Override
    public byte[] write(byte[] data) {
        return data; // passthrough
    }

    @Override
    public byte[] read(byte[] data) {
        return data; // passthrough
    }

    @Override
    public String getDescription() {
        return "Raw Data";
    }
}

// --- Abstract Decorator ---
public abstract class StreamDecorator implements DataStream {
    protected final DataStream wrapped;

    public StreamDecorator(DataStream wrapped) {
        this.wrapped = wrapped;
    }
}

// --- Compression Decorator ---
public class CompressionDecorator extends StreamDecorator {

    public CompressionDecorator(DataStream wrapped) {
        super(wrapped);
    }

    @Override
    public byte[] write(byte[] data) {
        byte[] compressed = compress(data);
        return wrapped.write(compressed);
    }

    @Override
    public byte[] read(byte[] data) {
        byte[] inner = wrapped.read(data);
        return decompress(inner);
    }

    @Override
    public String getDescription() {
        return wrapped.getDescription() + " + Compression(GZIP)";
    }

    private byte[] compress(byte[] data) {
        try (ByteArrayOutputStream baos = new ByteArrayOutputStream();
             GZIPOutputStream gzip = new GZIPOutputStream(baos)) {
            gzip.write(data);
            gzip.finish();
            return baos.toByteArray();
        } catch (IOException e) {
            throw new RuntimeException("Compression failed", e);
        }
    }

    private byte[] decompress(byte[] data) {
        try (GZIPInputStream gzip = new GZIPInputStream(new ByteArrayInputStream(data));
             ByteArrayOutputStream baos = new ByteArrayOutputStream()) {
            byte[] buffer = new byte[1024];
            int len;
            while ((len = gzip.read(buffer)) != -1) {
                baos.write(buffer, 0, len);
            }
            return baos.toByteArray();
        } catch (IOException e) {
            throw new RuntimeException("Decompression failed", e);
        }
    }
}

// --- Encryption Decorator (XOR for demo; use AES in production) ---
public class EncryptionDecorator extends StreamDecorator {
    private final byte[] key;

    public EncryptionDecorator(DataStream wrapped, String secretKey) {
        super(wrapped);
        this.key = secretKey.getBytes(StandardCharsets.UTF_8);
    }

    @Override
    public byte[] write(byte[] data) {
        byte[] encrypted = xorTransform(data);
        return wrapped.write(encrypted);
    }

    @Override
    public byte[] read(byte[] data) {
        byte[] inner = wrapped.read(data);
        return xorTransform(inner); // XOR is symmetric
    }

    @Override
    public String getDescription() {
        return wrapped.getDescription() + " + Encryption(XOR)";
    }

    private byte[] xorTransform(byte[] data) {
        byte[] result = new byte[data.length];
        for (int i = 0; i < data.length; i++) {
            result[i] = (byte) (data[i] ^ key[i % key.length]);
        }
        return result;
    }
}

// --- Buffering Decorator ---
public class BufferingDecorator extends StreamDecorator {
    private final int bufferSize;
    private ByteArrayOutputStream buffer;

    public BufferingDecorator(DataStream wrapped, int bufferSize) {
        super(wrapped);
        this.bufferSize = bufferSize;
        this.buffer = new ByteArrayOutputStream();
    }

    @Override
    public byte[] write(byte[] data) {
        buffer.write(data, 0, data.length);
        if (buffer.size() >= bufferSize) {
            return flush();
        }
        return new byte[0]; // buffered, not yet flushed
    }

    public byte[] flush() {
        byte[] buffered = buffer.toByteArray();
        buffer.reset();
        return wrapped.write(buffered);
    }

    @Override
    public byte[] read(byte[] data) {
        return wrapped.read(data);
    }

    @Override
    public String getDescription() {
        return wrapped.getDescription() + " + Buffering(" + bufferSize + "B)";
    }
}

// --- Logging Decorator ---
public class LoggingDecorator extends StreamDecorator {
    private final String logPrefix;
    private long totalBytesWritten = 0;
    private long totalBytesRead = 0;

    public LoggingDecorator(DataStream wrapped, String logPrefix) {
        super(wrapped);
        this.logPrefix = logPrefix;
    }

    @Override
    public byte[] write(byte[] data) {
        totalBytesWritten += data.length;
        System.out.printf("[%s] WRITE: %d bytes (total: %d)%n",
            logPrefix, data.length, totalBytesWritten);
        return wrapped.write(data);
    }

    @Override
    public byte[] read(byte[] data) {
        byte[] result = wrapped.read(data);
        totalBytesRead += result.length;
        System.out.printf("[%s] READ: %d bytes (total: %d)%n",
            logPrefix, result.length, totalBytesRead);
        return result;
    }

    @Override
    public String getDescription() {
        return wrapped.getDescription() + " + Logging";
    }
}
```

### Usage

```java
// Build pipeline: Log → Compress → Encrypt → Base stream
DataStream stream = new LoggingDecorator(
    new CompressionDecorator(
        new EncryptionDecorator(
            new BaseDataStream(),
            "my-secret-key"
        )
    ),
    "FileWriter"
);

System.out.println("Pipeline: " + stream.getDescription());
// Pipeline: Raw Data + Encryption(XOR) + Compression(GZIP) + Logging

// Write data through pipeline
byte[] original = "Hello World! This is a test of the data stream processing pipeline."
    .getBytes(StandardCharsets.UTF_8);
byte[] processed = stream.write(original);

// Read data back (reverse pipeline)
byte[] restored = stream.read(processed);
System.out.println(new String(restored, StandardCharsets.UTF_8));
// Hello World! This is a test of the data stream processing pipeline.
```

### Interview Talking Points

- **Java I/O is this exact pattern**: `BufferedOutputStream(GZIPOutputStream(CipherOutputStream(FileOutputStream)))` — the JDK's stream processing uses decorators
- **Order matters**: Compress-then-encrypt is correct (smaller ciphertext). Encrypt-then-compress won't compress well because ciphertext has high entropy
- **Production**: Spring's `ClientHttpRequestInterceptor` chain for RestTemplate, and Servlet `Filter` chain are decorator patterns
- **vs Strategy**: Strategy swaps the entire algorithm. Decorator stacks multiple behaviors on top of each other
