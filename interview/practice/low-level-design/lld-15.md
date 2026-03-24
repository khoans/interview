**15.** Multi-Cloud Storage — Adapter pattern. Unified interface for AWS S3, GCS, Azure Blob. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## Multi-Cloud Storage — Adapter Pattern

### Pattern: Adapter (Object Adapter)

**Why Adapter?** Each cloud provider has a different SDK with different method names, authentication, and response types. Adapter provides one `CloudStorage` interface so application code is cloud-agnostic.

### Text UML

```
┌───────────────────────────┐
│    <<interface>>          │
│    CloudStorage           │
├───────────────────────────┤
│ + upload(key, data): URL  │
│ + download(key): byte[]   │
│ + delete(key): void       │
│ + exists(key): boolean    │
│ + listFiles(prefix): List │
│ + getSignedUrl(key, exp)  │
└──────────┬────────────────┘
           │
    ┌──────┼──────────┬──────────────┐
    │      │          │              │
S3Adapter  GCSAdapter AzureAdapter
    │         │           │
    ▼         ▼           ▼
AmazonS3   Storage    BlobServiceClient
(AWS SDK)  (GCP SDK)  (Azure SDK)
```

### Key Design Decisions

1. **Unified key format** — All operations use `bucket/path/file.ext` convention; adapters translate to provider-specific format
2. **Signed URL support** — Pre-signed URLs for temporary access without exposing credentials
3. **Stream-based upload** — Accept `InputStream` + size for large files, avoid loading entire file into memory
4. **Provider-agnostic exceptions** — Wrap SDK exceptions in `StorageException`

### SOLID Principles Applied

- **SRP**: Each adapter handles one provider's SDK translation
- **OCP**: Add MinIO or DigitalOcean Spaces by writing new adapter
- **DIP**: Application depends on `CloudStorage`, never imports AWS/GCP/Azure SDK directly
- **LSP**: All adapters are substitutable — switch providers via config

### Java Code

```java
import java.io.*;
import java.net.URL;
import java.time.Duration;
import java.util.*;

// ===================== Domain Objects =====================

public class StorageObject {
    private final String key;
    private final long size;
    private final String contentType;
    private final Date lastModified;

    public StorageObject(String key, long size, String contentType, Date lastModified) {
        this.key = key;
        this.size = size;
        this.contentType = contentType;
        this.lastModified = lastModified;
    }

    public String getKey() { return key; }
    public long getSize() { return size; }
    public String getContentType() { return contentType; }
    public Date getLastModified() { return lastModified; }
}

public class StorageException extends RuntimeException {
    private final String provider;
    public StorageException(String provider, String message, Throwable cause) {
        super("[" + provider + "] " + message, cause);
        this.provider = provider;
    }
    public String getProvider() { return provider; }
}

// ===================== Unified Interface =====================

public interface CloudStorage {
    String upload(String bucket, String key, InputStream data, long size, String contentType);
    byte[] download(String bucket, String key);
    void delete(String bucket, String key);
    boolean exists(String bucket, String key);
    List<StorageObject> listFiles(String bucket, String prefix);
    String getSignedUrl(String bucket, String key, Duration expiry);
    String getProviderName();
}

// ===================== Simulated SDKs =====================

// AWS S3 SDK (simplified)
class AmazonS3Client {
    public void putObject(String bucket, String key, InputStream input, Map<String, String> metadata) {
        // AWS SDK call
    }
    public byte[] getObjectBytes(String bucket, String key) {
        return ("content of " + key).getBytes();
    }
    public void deleteObject(String bucket, String key) {}
    public boolean doesObjectExist(String bucket, String key) { return true; }
    public List<Map<String, Object>> listObjectsV2(String bucket, String prefix) {
        return List.of(Map.of("key", prefix + "file.txt", "size", 1024L,
            "contentType", "text/plain", "lastModified", new Date()));
    }
    public URL generatePresignedUrl(String bucket, String key, Date expiration) {
        try { return new URL("https://" + bucket + ".s3.amazonaws.com/" + key + "?signed=true"); }
        catch (Exception e) { throw new RuntimeException(e); }
    }
}

// GCP Storage SDK (simplified)
class GoogleStorageClient {
    public void create(String bucketName, String blobName, byte[] content, String contentType) {}
    public byte[] readAllBytes(String bucketName, String blobName) {
        return ("content of " + blobName).getBytes();
    }
    public boolean delete(String bucketName, String blobName) { return true; }
    public Map<String, Object> getBlob(String bucketName, String blobName) {
        return Map.of("exists", true);
    }
    public List<Map<String, Object>> listBlobs(String bucketName, String prefix) {
        return List.of(Map.of("name", prefix + "file.txt", "size", 1024L,
            "contentType", "text/plain", "updateTime", new Date()));
    }
    public String signUrl(String bucketName, String blobName, long durationSeconds) {
        return "https://storage.googleapis.com/" + bucketName + "/" + blobName + "?signed=true";
    }
}

// Azure Blob SDK (simplified)
class AzureBlobClient {
    public void uploadBlob(String container, String blobName, InputStream data, long length) {}
    public byte[] downloadBlob(String container, String blobName) {
        return ("content of " + blobName).getBytes();
    }
    public void deleteBlob(String container, String blobName) {}
    public boolean blobExists(String container, String blobName) { return true; }
    public List<Map<String, Object>> listBlobs(String container, String prefix) {
        return List.of(Map.of("name", prefix + "file.txt", "size", 1024L,
            "contentType", "text/plain", "lastModified", new Date()));
    }
    public String generateSasUrl(String container, String blobName, long expiryMinutes) {
        return "https://account.blob.core.windows.net/" + container + "/" + blobName + "?sas=true";
    }
}

// ===================== Adapters =====================

public class S3Adapter implements CloudStorage {
    private final AmazonS3Client s3;

    public S3Adapter(AmazonS3Client s3) {
        this.s3 = s3;
    }

    @Override
    public String upload(String bucket, String key, InputStream data, long size, String contentType) {
        try {
            Map<String, String> metadata = Map.of("Content-Type", contentType);
            s3.putObject(bucket, key, data, metadata);
            return "s3://" + bucket + "/" + key;
        } catch (Exception e) {
            throw new StorageException("S3", "Upload failed: " + key, e);
        }
    }

    @Override
    public byte[] download(String bucket, String key) {
        try {
            return s3.getObjectBytes(bucket, key);
        } catch (Exception e) {
            throw new StorageException("S3", "Download failed: " + key, e);
        }
    }

    @Override
    public void delete(String bucket, String key) {
        s3.deleteObject(bucket, key);
    }

    @Override
    public boolean exists(String bucket, String key) {
        return s3.doesObjectExist(bucket, key);
    }

    @Override
    public List<StorageObject> listFiles(String bucket, String prefix) {
        return s3.listObjectsV2(bucket, prefix).stream()
            .map(m -> new StorageObject(
                (String) m.get("key"), (long) m.get("size"),
                (String) m.get("contentType"), (Date) m.get("lastModified")))
            .toList();
    }

    @Override
    public String getSignedUrl(String bucket, String key, Duration expiry) {
        Date expiration = new Date(System.currentTimeMillis() + expiry.toMillis());
        return s3.generatePresignedUrl(bucket, key, expiration).toString();
    }

    @Override
    public String getProviderName() { return "AWS S3"; }
}

public class GCSAdapter implements CloudStorage {
    private final GoogleStorageClient gcs;

    public GCSAdapter(GoogleStorageClient gcs) {
        this.gcs = gcs;
    }

    @Override
    public String upload(String bucket, String key, InputStream data, long size, String contentType) {
        try {
            byte[] bytes = data.readAllBytes();
            gcs.create(bucket, key, bytes, contentType);
            return "gs://" + bucket + "/" + key;
        } catch (Exception e) {
            throw new StorageException("GCS", "Upload failed: " + key, e);
        }
    }

    @Override
    public byte[] download(String bucket, String key) {
        return gcs.readAllBytes(bucket, key);
    }

    @Override
    public void delete(String bucket, String key) {
        gcs.delete(bucket, key);
    }

    @Override
    public boolean exists(String bucket, String key) {
        Map<String, Object> blob = gcs.getBlob(bucket, key);
        return blob != null && (boolean) blob.get("exists");
    }

    @Override
    public List<StorageObject> listFiles(String bucket, String prefix) {
        return gcs.listBlobs(bucket, prefix).stream()
            .map(m -> new StorageObject(
                (String) m.get("name"), (long) m.get("size"),
                (String) m.get("contentType"), (Date) m.get("updateTime")))
            .toList();
    }

    @Override
    public String getSignedUrl(String bucket, String key, Duration expiry) {
        return gcs.signUrl(bucket, key, expiry.getSeconds());
    }

    @Override
    public String getProviderName() { return "Google Cloud Storage"; }
}

public class AzureBlobAdapter implements CloudStorage {
    private final AzureBlobClient azure;

    public AzureBlobAdapter(AzureBlobClient azure) {
        this.azure = azure;
    }

    @Override
    public String upload(String bucket, String key, InputStream data, long size, String contentType) {
        try {
            azure.uploadBlob(bucket, key, data, size);
            return "https://account.blob.core.windows.net/" + bucket + "/" + key;
        } catch (Exception e) {
            throw new StorageException("Azure", "Upload failed: " + key, e);
        }
    }

    @Override
    public byte[] download(String bucket, String key) {
        return azure.downloadBlob(bucket, key);
    }

    @Override
    public void delete(String bucket, String key) {
        azure.deleteBlob(bucket, key);
    }

    @Override
    public boolean exists(String bucket, String key) {
        return azure.blobExists(bucket, key);
    }

    @Override
    public List<StorageObject> listFiles(String bucket, String prefix) {
        return azure.listBlobs(bucket, prefix).stream()
            .map(m -> new StorageObject(
                (String) m.get("name"), (long) m.get("size"),
                (String) m.get("contentType"), (Date) m.get("lastModified")))
            .toList();
    }

    @Override
    public String getSignedUrl(String bucket, String key, Duration expiry) {
        return azure.generateSasUrl(bucket, key, expiry.toMinutes());
    }

    @Override
    public String getProviderName() { return "Azure Blob Storage"; }
}
```

### Usage

```java
// Select provider based on config
CloudStorage storage = new S3Adapter(new AmazonS3Client());
// Or: new GCSAdapter(new GoogleStorageClient());
// Or: new AzureBlobAdapter(new AzureBlobClient());

// Application code is cloud-agnostic
byte[] fileData = "Hello World".getBytes();
String url = storage.upload("my-bucket", "docs/hello.txt",
    new ByteArrayInputStream(fileData), fileData.length, "text/plain");

byte[] downloaded = storage.download("my-bucket", "docs/hello.txt");
String signedUrl = storage.getSignedUrl("my-bucket", "docs/hello.txt", Duration.ofHours(1));
```

### Interview Talking Points

- **Vendor lock-in prevention**: Adapter pattern is the standard approach for multi-cloud. Change provider by swapping one bean in Spring config
- **Terminology mapping**: AWS="bucket", GCP="bucket", Azure="container" — adapter normalizes naming
- **Signed URLs**: Critical for secure file sharing without exposing storage credentials
- **Spring integration**: `@ConditionalOnProperty("storage.provider")` selects the right adapter bean at startup
