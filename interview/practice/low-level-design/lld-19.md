**19.** File System Explorer — Composite pattern. Files and folders with uniform operations (getSize, delete). Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## File System Explorer — Composite Pattern

### Pattern: Composite

**Why Composite?** Files and folders must be treated uniformly — `getSize()` on a file returns its size; `getSize()` on a folder returns the sum of all children recursively. Client code shouldn't care whether it's dealing with a leaf or a branch.

### Text UML

```
┌──────────────────────────────┐
│      <<interface>>           │
│      FileSystemItem          │
├──────────────────────────────┤
│ + getName(): String          │
│ + getSize(): long            │
│ + delete(): void             │
│ + getPath(): String          │
│ + isDirectory(): boolean     │
│ + search(name): List<Item>   │
└──────────┬───────────────────┘
           │
    ┌──────┴──────────────┐
    │                      │
┌───┴──────┐    ┌─────────┴────────┐
│  File    │    │  Directory       │
│  (Leaf)  │    │  (Composite)     │
├──────────┤    ├──────────────────┤
│ - name   │    │ - name           │
│ - size   │    │ - children: List │
│ - ext    │    ├──────────────────┤
└──────────┘    │ + add(item)      │
                │ + remove(item)   │
                │ + getChildren()  │
                └──────────────────┘
```

### Key Design Decisions

1. **Uniform interface** — Both File and Directory implement `FileSystemItem`; clients call `getSize()` without knowing the type
2. **Recursive getSize()** — Directory sums children's sizes; children may be subdirectories (recursive by nature)
3. **search() on both** — File checks its own name; Directory searches recursively through all descendants
4. **Path tracking** — Each item knows its parent path for full path reconstruction

### SOLID Principles Applied

- **SRP**: File handles leaf operations; Directory manages children + recursive aggregation
- **OCP**: Add new item types (SymLink, MountPoint) by implementing FileSystemItem
- **LSP**: File and Directory are interchangeable where FileSystemItem is expected
- **DIP**: All operations depend on the FileSystemItem abstraction

### Java Code

```java
import java.util.*;
import java.util.stream.Collectors;

// --- File System Item Interface ---
public interface FileSystemItem {
    String getName();
    long getSize();
    void delete();
    String getPath();
    boolean isDirectory();
    List<FileSystemItem> search(String name);
}

// --- File (Leaf) ---
public class File implements FileSystemItem {
    private final String name;
    private final long size;
    private final String extension;
    private String parentPath;
    private boolean deleted = false;

    public File(String name, long size) {
        this.name = name;
        this.size = size;
        int dotIndex = name.lastIndexOf('.');
        this.extension = dotIndex > 0 ? name.substring(dotIndex + 1) : "";
        this.parentPath = "";
    }

    @Override
    public String getName() { return name; }

    @Override
    public long getSize() { return deleted ? 0 : size; }

    @Override
    public void delete() {
        this.deleted = true;
        System.out.println("Deleted file: " + getPath());
    }

    @Override
    public String getPath() {
        return parentPath.isEmpty() ? name : parentPath + "/" + name;
    }

    @Override
    public boolean isDirectory() { return false; }

    @Override
    public List<FileSystemItem> search(String searchName) {
        List<FileSystemItem> results = new ArrayList<>();
        if (!deleted && name.toLowerCase().contains(searchName.toLowerCase())) {
            results.add(this);
        }
        return results;
    }

    public String getExtension() { return extension; }
    void setParentPath(String path) { this.parentPath = path; }

    @Override
    public String toString() {
        return String.format("%s (%s)", name, formatSize(size));
    }

    private String formatSize(long bytes) {
        if (bytes < 1024) return bytes + "B";
        if (bytes < 1024 * 1024) return (bytes / 1024) + "KB";
        return (bytes / (1024 * 1024)) + "MB";
    }
}

// --- Directory (Composite) ---
public class Directory implements FileSystemItem {
    private final String name;
    private final List<FileSystemItem> children;
    private String parentPath;

    public Directory(String name) {
        this.name = name;
        this.children = new ArrayList<>();
        this.parentPath = "";
    }

    public void add(FileSystemItem item) {
        children.add(item);
        // Set parent path for path tracking
        if (item instanceof File) {
            ((File) item).setParentPath(getPath());
        } else if (item instanceof Directory) {
            ((Directory) item).setParentPath(getPath());
        }
    }

    public void remove(FileSystemItem item) {
        children.remove(item);
    }

    public List<FileSystemItem> getChildren() {
        return Collections.unmodifiableList(children);
    }

    @Override
    public String getName() { return name; }

    @Override
    public long getSize() {
        // Recursive: sum of all children's sizes
        return children.stream().mapToLong(FileSystemItem::getSize).sum();
    }

    @Override
    public void delete() {
        // Recursive: delete all children first
        List<FileSystemItem> toDelete = new ArrayList<>(children);
        for (FileSystemItem child : toDelete) {
            child.delete();
        }
        children.clear();
        System.out.println("Deleted directory: " + getPath());
    }

    @Override
    public String getPath() {
        return parentPath.isEmpty() ? name : parentPath + "/" + name;
    }

    @Override
    public boolean isDirectory() { return true; }

    @Override
    public List<FileSystemItem> search(String searchName) {
        List<FileSystemItem> results = new ArrayList<>();
        if (name.toLowerCase().contains(searchName.toLowerCase())) {
            results.add(this);
        }
        for (FileSystemItem child : children) {
            results.addAll(child.search(searchName));
        }
        return results;
    }

    void setParentPath(String path) { this.parentPath = path; }

    // Print tree structure
    public String printTree(String indent) {
        StringBuilder sb = new StringBuilder();
        sb.append(indent).append("[DIR] ").append(name).append("/\n");
        for (FileSystemItem child : children) {
            if (child instanceof Directory) {
                sb.append(((Directory) child).printTree(indent + "  "));
            } else {
                sb.append(indent).append("  ").append(child).append("\n");
            }
        }
        return sb.toString();
    }
}
```

### Usage

```java
// Build file system tree
Directory root = new Directory("project");

Directory src = new Directory("src");
src.add(new File("Main.java", 2048));
src.add(new File("Service.java", 4096));
src.add(new File("Repository.java", 3072));

Directory resources = new Directory("resources");
resources.add(new File("application.yml", 512));
resources.add(new File("schema.sql", 1024));

Directory test = new Directory("test");
test.add(new File("MainTest.java", 1536));

root.add(src);
root.add(resources);
root.add(test);
root.add(new File("pom.xml", 768));
root.add(new File("README.md", 256));

// Uniform operations — works on files AND directories
System.out.println("Total project size: " + root.getSize() + " bytes");
// 13312 bytes

System.out.println("Src folder size: " + src.getSize() + " bytes");
// 9216 bytes

// Search recursively
List<FileSystemItem> results = root.search(".java");
results.forEach(r -> System.out.println("Found: " + r.getPath()));
// Found: project/src/Main.java
// Found: project/src/Service.java
// Found: project/src/Repository.java
// Found: project/test/MainTest.java

// Print tree
System.out.println(root.printTree(""));

// Delete a directory — recursively deletes all children
test.delete();
```

### Interview Talking Points

- **Recursive by nature**: `getSize()` on Directory recursively sums children — this is THE point of Composite
- **Real Java API**: `java.io.File` is a Composite — `listFiles()` returns children, `length()` works on files, `delete()` works on both
- **Uniform treatment**: Client code calls `getSize()` on any `FileSystemItem` — no `instanceof` checks needed
- **Tree traversal**: `search()` is depth-first; for large file systems, consider breadth-first or parallel streams
