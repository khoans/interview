**25.** Image Loading System — Proxy pattern. Lazy loading + caching for high-res images. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## Image Loading System — Proxy Pattern

### Pattern: Proxy (Virtual Proxy + Caching Proxy)

**Why Proxy?** High-resolution images are expensive to load (network I/O, memory). Virtual proxy defers loading until `display()` is actually called. Caching proxy avoids reloading the same image. The client code is unaware — it talks to the same `Image` interface.

### Text UML

```
┌───────────────────────────┐
│    <<interface>>          │
│    Image                  │
├───────────────────────────┤
│ + display(): void         │
│ + getWidth(): int         │
│ + getHeight(): int        │
│ + getFilename(): String   │
│ + getSizeBytes(): long    │
└──────────┬────────────────┘
           │
    ┌──────┼──────────────┐
    │      │              │
┌───┴──────────┐  ┌───────┴──────────────┐
│ RealImage    │  │ ImageProxy            │
│ (expensive)  │  │ (lazy load + cache)   │
├──────────────┤  ├──────────────────────┤
│ - pixels     │  │ - realImage: Image   │
│ - loaded     │  │ - filename: String   │
└──────────────┘  │ - loaded: boolean    │
                  │ - cache: ImageCache  │
                  └──────────────────────┘

┌───────────────────────────────┐
│   ImageCache (Singleton)      │
├───────────────────────────────┤
│ - cache: Map<String, Image>   │
│ - maxSize: int                │
├───────────────────────────────┤
│ + get(key): Image             │
│ + put(key, image): void       │
│ + evict(key): void            │
└───────────────────────────────┘
```

### Key Design Decisions

1. **Virtual Proxy** — RealImage not created until `display()` is called. Placeholder shown until then
2. **Caching Proxy** — Once loaded, image is cached. Subsequent requests serve from cache
3. **Same interface** — Client code doesn't know if it's talking to proxy or real image
4. **Loading indicator** — Proxy shows "Loading..." placeholder while real image loads in background

### SOLID Principles Applied

- **SRP**: RealImage handles pixel data; Proxy handles lazy loading + caching
- **OCP**: Add new proxy types (protection proxy, logging proxy) without changing RealImage
- **LSP**: Proxy IS-A Image — fully substitutable
- **DIP**: Client depends on Image interface, never on RealImage directly

### Java Code

```java
import java.util.*;
import java.util.concurrent.*;

// --- Image Interface ---
public interface Image {
    void display();
    int getWidth();
    int getHeight();
    String getFilename();
    long getSizeBytes();
}

// --- Real Image (expensive to create) ---
public class RealImage implements Image {
    private final String filename;
    private byte[] pixelData;
    private int width;
    private int height;

    public RealImage(String filename) {
        this.filename = filename;
        loadFromDisk(); // Expensive operation in constructor
    }

    private void loadFromDisk() {
        System.out.println("  [RealImage] Loading " + filename + " from disk...");
        // Simulate slow I/O
        try { Thread.sleep(100); } catch (InterruptedException e) { Thread.currentThread().interrupt(); }
        // Simulate loaded image data
        this.width = 1920;
        this.height = 1080;
        this.pixelData = new byte[width * height * 4]; // RGBA
        System.out.printf("  [RealImage] Loaded %s (%dx%d, %d bytes)%n",
            filename, width, height, pixelData.length);
    }

    @Override
    public void display() {
        System.out.printf("  [RealImage] Displaying %s (%dx%d)%n", filename, width, height);
    }

    @Override
    public int getWidth() { return width; }
    @Override
    public int getHeight() { return height; }
    @Override
    public String getFilename() { return filename; }
    @Override
    public long getSizeBytes() { return pixelData != null ? pixelData.length : 0; }
}

// --- Image Cache ---
public class ImageCache {
    private static final ImageCache INSTANCE = new ImageCache(50);

    private final Map<String, Image> cache;
    private final int maxSize;

    private ImageCache(int maxSize) {
        this.maxSize = maxSize;
        // LinkedHashMap with access-order for LRU
        this.cache = Collections.synchronizedMap(
            new LinkedHashMap<String, Image>(maxSize, 0.75f, true) {
                @Override
                protected boolean removeEldestEntry(Map.Entry<String, Image> eldest) {
                    return size() > maxSize;
                }
            }
        );
    }

    public static ImageCache getInstance() { return INSTANCE; }

    public Image get(String key) { return cache.get(key); }

    public void put(String key, Image image) { cache.put(key, image); }

    public void evict(String key) { cache.remove(key); }

    public int size() { return cache.size(); }

    public void clear() { cache.clear(); }
}

// --- Image Proxy (Virtual + Caching) ---
public class ImageProxy implements Image {
    private final String filename;
    private RealImage realImage;
    private boolean loaded = false;
    private final ImageCache cache;

    // Placeholder dimensions (before loading)
    private static final int PLACEHOLDER_WIDTH = 200;
    private static final int PLACEHOLDER_HEIGHT = 150;

    public ImageProxy(String filename) {
        this.filename = filename;
        this.cache = ImageCache.getInstance();
    }

    @Override
    public void display() {
        if (!loaded) {
            loadImage();
        }
        if (realImage != null) {
            realImage.display();
        }
    }

    @Override
    public int getWidth() {
        return loaded && realImage != null ? realImage.getWidth() : PLACEHOLDER_WIDTH;
    }

    @Override
    public int getHeight() {
        return loaded && realImage != null ? realImage.getHeight() : PLACEHOLDER_HEIGHT;
    }

    @Override
    public String getFilename() { return filename; }

    @Override
    public long getSizeBytes() {
        return loaded && realImage != null ? realImage.getSizeBytes() : 0;
    }

    private void loadImage() {
        // Check cache first
        Image cached = cache.get(filename);
        if (cached instanceof RealImage) {
            System.out.println("  [Proxy] Cache HIT for " + filename);
            this.realImage = (RealImage) cached;
            this.loaded = true;
            return;
        }

        // Cache miss — load and cache
        System.out.println("  [Proxy] Cache MISS for " + filename + " — loading...");
        this.realImage = new RealImage(filename);
        cache.put(filename, realImage);
        this.loaded = true;
    }
}

// --- Image Gallery (Client) ---
public class ImageGallery {
    private final List<Image> images = new ArrayList<>();

    public void addImage(String filename) {
        // Creates proxy only — no disk I/O yet
        images.add(new ImageProxy(filename));
    }

    // Only loads images that are actually displayed
    public void displayPage(int page, int pageSize) {
        int start = page * pageSize;
        int end = Math.min(start + pageSize, images.size());

        System.out.println("\n=== Page " + (page + 1) + " ===");
        for (int i = start; i < end; i++) {
            images.get(i).display();
        }
    }

    public int getTotalImages() { return images.size(); }
}
```

### Usage

```java
ImageGallery gallery = new ImageGallery();

// Add 100 images — only proxies created, no disk I/O
for (int i = 1; i <= 100; i++) {
    gallery.addImage("photo_" + i + ".jpg");
}
System.out.println("Gallery loaded: " + gallery.getTotalImages() + " images (proxies only)");
// No images loaded from disk yet!

// Display page 1 — only these 5 images load from disk
gallery.displayPage(0, 5);
// [Proxy] Cache MISS for photo_1.jpg — loading...
// [RealImage] Loading photo_1.jpg from disk...
// [RealImage] Displaying photo_1.jpg (1920x1080)
// ... (5 images loaded)

// Display page 1 again — all from cache
gallery.displayPage(0, 5);
// [Proxy] Cache HIT for photo_1.jpg
// [RealImage] Displaying photo_1.jpg (1920x1080)
// ... (5 images served from cache)

// Display page 2 — new images load, page 1 stays cached
gallery.displayPage(1, 5);
// [Proxy] Cache MISS for photo_6.jpg — loading...

System.out.println("Cache size: " + ImageCache.getInstance().size());
```

### Interview Talking Points

- **Virtual Proxy vs Caching Proxy**: This combines both — lazy loading (don't load until needed) AND caching (don't reload what's already loaded)
- **Real Java examples**: `java.lang.reflect.Proxy` for dynamic proxies; Hibernate's lazy-loaded entity proxies work exactly this way — entity isn't fetched from DB until a getter is called
- **Spring AOP**: `@Transactional`, `@Cacheable`, `@Async` — all implemented via proxies (CGLIB or JDK dynamic proxy) that wrap the real bean
- **Memory management**: LRU cache with max size prevents OOM when browsing thousands of high-res images. Evicted images will be reloaded via proxy on next access
- **Protection Proxy variation**: Add access control — check user permissions before loading sensitive images
