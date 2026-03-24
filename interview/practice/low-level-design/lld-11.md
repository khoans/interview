**11.** Document Template System — Prototype pattern. Deep copy templates with nested sections. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## Document Template System — Prototype Pattern

### Pattern: Prototype (Deep Clone with Composite)

**Why Prototype?** Document templates have deeply nested sections (headers, paragraphs, tables, images). Creating from scratch each time is slow and error-prone. Clone a template and customize specific sections.

### Text UML

```
┌──────────────────────────┐
│   <<interface>>          │
│   DocumentElement        │
├──────────────────────────┤
│ + deepClone(): Element   │
│ + render(): String       │
│ + getType(): String      │
└──────────┬───────────────┘
           │
    ┌──────┼──────────┬──────────────┐
    │      │          │              │
TextBlock  Table    ImageBlock   Section
                                (contains List<DocumentElement>)

┌──────────────────────────────┐
│   DocumentTemplate           │
├──────────────────────────────┤
│ - name: String               │
│ - sections: List<Section>    │
│ - metadata: Map              │
├──────────────────────────────┤
│ + deepClone(): DocumentTemplate │
│ + addSection(section): void  │
│ + findSection(name): Section │
└──────────────────────────────┘

┌──────────────────────────────┐
│   TemplateRegistry           │
├──────────────────────────────┤
│ - templates: Map<String, T>  │
├──────────────────────────────┤
│ + register(name, template)   │
│ + createFrom(name): Template │
└──────────────────────────────┘
```

### Key Design Decisions

1. **Composite + Prototype** — Sections contain nested elements; each element knows how to clone itself
2. **Recursive deep clone** — `Section.deepClone()` recursively clones all children
3. **Template registry** — Named templates stored centrally; clone by name
4. **Metadata clone** — Template-level metadata (author, version, styles) are also deep-copied

### SOLID Principles Applied

- **SRP**: Each element type handles its own cloning and rendering
- **OCP**: Add new element types (CodeBlock, Chart) by implementing `DocumentElement`
- **LSP**: All elements are substitutable — Section treats them uniformly

### Java Code

```java
import java.util.*;
import java.util.stream.Collectors;

// --- Document Element Interface ---
public interface DocumentElement {
    DocumentElement deepClone();
    String render();
    String getType();
}

// --- Text Block ---
public class TextBlock implements DocumentElement {
    private String content;
    private String style; // "heading", "paragraph", "bold"

    public TextBlock(String content, String style) {
        this.content = content;
        this.style = style;
    }

    @Override
    public DocumentElement deepClone() {
        return new TextBlock(content, style);
    }

    @Override
    public String render() {
        return "[" + style.toUpperCase() + "] " + content;
    }

    @Override
    public String getType() { return "text"; }

    public void setContent(String content) { this.content = content; }
    public String getContent() { return content; }
}

// --- Table Element ---
public class TableElement implements DocumentElement {
    private List<String> headers;
    private List<List<String>> rows;

    public TableElement(List<String> headers, List<List<String>> rows) {
        this.headers = new ArrayList<>(headers);
        this.rows = rows.stream()
            .map(ArrayList::new)
            .collect(Collectors.toList());
    }

    @Override
    public DocumentElement deepClone() {
        List<List<String>> clonedRows = rows.stream()
            .map(ArrayList::new)
            .collect(Collectors.toList());
        return new TableElement(new ArrayList<>(headers), clonedRows);
    }

    @Override
    public String render() {
        StringBuilder sb = new StringBuilder();
        sb.append("| ").append(String.join(" | ", headers)).append(" |\n");
        for (List<String> row : rows) {
            sb.append("| ").append(String.join(" | ", row)).append(" |\n");
        }
        return sb.toString();
    }

    @Override
    public String getType() { return "table"; }

    public void addRow(List<String> row) { rows.add(new ArrayList<>(row)); }
}

// --- Image Block ---
public class ImageBlock implements DocumentElement {
    private String url;
    private String altText;
    private int width;
    private int height;

    public ImageBlock(String url, String altText, int width, int height) {
        this.url = url;
        this.altText = altText;
        this.width = width;
        this.height = height;
    }

    @Override
    public DocumentElement deepClone() {
        return new ImageBlock(url, altText, width, height);
    }

    @Override
    public String render() {
        return String.format("[IMG: %s (%dx%d) alt='%s']", url, width, height, altText);
    }

    @Override
    public String getType() { return "image"; }

    public void setUrl(String url) { this.url = url; }
}

// --- Section (Composite — contains nested elements) ---
public class Section implements DocumentElement {
    private String name;
    private List<DocumentElement> elements;

    public Section(String name) {
        this.name = name;
        this.elements = new ArrayList<>();
    }

    public Section(String name, List<DocumentElement> elements) {
        this.name = name;
        this.elements = new ArrayList<>(elements);
    }

    @Override
    public DocumentElement deepClone() {
        List<DocumentElement> cloned = elements.stream()
            .map(DocumentElement::deepClone)
            .collect(Collectors.toList());
        return new Section(name, cloned);
    }

    @Override
    public String render() {
        StringBuilder sb = new StringBuilder();
        sb.append("=== ").append(name).append(" ===\n");
        for (DocumentElement el : elements) {
            sb.append("  ").append(el.render()).append("\n");
        }
        return sb.toString();
    }

    @Override
    public String getType() { return "section"; }

    public void addElement(DocumentElement element) { elements.add(element); }
    public String getName() { return name; }
    public List<DocumentElement> getElements() { return elements; }
}

// --- Document Template ---
public class DocumentTemplate {
    private String name;
    private List<Section> sections;
    private Map<String, String> metadata;

    public DocumentTemplate(String name) {
        this.name = name;
        this.sections = new ArrayList<>();
        this.metadata = new HashMap<>();
    }

    public DocumentTemplate deepClone() {
        DocumentTemplate clone = new DocumentTemplate(name);
        clone.sections = sections.stream()
            .map(s -> (Section) s.deepClone())
            .collect(Collectors.toList());
        clone.metadata = new HashMap<>(metadata);
        return clone;
    }

    public void addSection(Section section) { sections.add(section); }

    public Section findSection(String sectionName) {
        return sections.stream()
            .filter(s -> s.getName().equals(sectionName))
            .findFirst()
            .orElseThrow(() -> new IllegalArgumentException("Section not found: " + sectionName));
    }

    public void setMetadata(String key, String value) { metadata.put(key, value); }

    public String render() {
        StringBuilder sb = new StringBuilder();
        sb.append("Document: ").append(name).append("\n");
        sb.append("Metadata: ").append(metadata).append("\n\n");
        for (Section section : sections) {
            sb.append(section.render()).append("\n");
        }
        return sb.toString();
    }
}

// --- Template Registry ---
public class TemplateRegistry {
    private final Map<String, DocumentTemplate> templates = new HashMap<>();

    public void register(String name, DocumentTemplate template) {
        templates.put(name, template);
    }

    public DocumentTemplate createFrom(String templateName) {
        DocumentTemplate template = templates.get(templateName);
        if (template == null) {
            throw new IllegalArgumentException("Unknown template: " + templateName);
        }
        return template.deepClone();
    }
}
```

### Usage

```java
// Build an invoice template
DocumentTemplate invoiceTemplate = new DocumentTemplate("Invoice Template");
invoiceTemplate.setMetadata("version", "1.0");
invoiceTemplate.setMetadata("author", "System");

Section header = new Section("Header");
header.addElement(new TextBlock("INVOICE", "heading"));
header.addElement(new TextBlock("Company Name: {company}", "paragraph"));
header.addElement(new ImageBlock("/logo.png", "Company Logo", 200, 50));

Section items = new Section("Line Items");
items.addElement(new TableElement(
    List.of("Item", "Qty", "Price"),
    List.of(List.of("{item}", "{qty}", "{price}"))
));

invoiceTemplate.addSection(header);
invoiceTemplate.addSection(items);

// Register and clone
TemplateRegistry registry = new TemplateRegistry();
registry.register("invoice", invoiceTemplate);

DocumentTemplate myInvoice = registry.createFrom("invoice");
// Customize the clone without affecting the original template
Section myHeader = myInvoice.findSection("Header");
((TextBlock) myHeader.getElements().get(1)).setContent("Company Name: Acme Corp");
```

### Interview Talking Points

- **Composite + Prototype**: Two GoF patterns working together — Composite for tree structure, Prototype for cloning the tree
- **Deep clone verification**: After cloning, modifying the clone must NOT affect the original — this is the key invariant to test
- **Production use**: Document generation libraries (Jasper Reports, Apache POI) use template cloning internally
- **Alternative**: Serialization-based clone (serialize to bytes, deserialize) — simple but slower and requires `Serializable`
