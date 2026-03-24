**30.** Design a Text Formatting System using the Strategy pattern. Support Plain Text, HTML, Markdown, and JSON formatting.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Text Formatting System — Strategy Pattern

**Pattern:** Strategy (Behavioral)
**Why Strategy?** Each output format has entirely different rendering rules. Strategy lets the same content render as HTML, Markdown, JSON, or plain text without conditionals.

**SOLID Principles Applied:**
- **SRP:** Each formatter handles one output format
- **OCP:** Add XML, YAML formatters without changing existing code
- **DIP:** TextDocument depends on FormatStrategy interface

### Text UML Diagram

```
┌───────────────┐      ┌─────────────────────┐
│ TextDocument   │─────→│  <<interface>>       │
│───────────────│      │  FormatStrategy      │
│ -title        │      │─────────────────────│
│ -sections[]   │      │ +formatDocument()    │
│ -formatter    │      │ +formatHeading()     │
│ +render()     │      │ +formatParagraph()   │
│ +setFormatter()│     │ +formatList()        │
└───────────────┘      │ +formatBold()        │
                       └────────┬────────────┘
                                │ implements
                  ┌─────┬───────┼───────┬──────┐
               Plain   HTML  Markdown  JSON  (new)
```

### Key Design Decisions

1. **Document model is format-agnostic** — Content stored as structured data, not raw text
2. **Granular formatting methods** — Each element (heading, list, bold) has its own method for composability
3. **Complete document rendering** — `formatDocument()` delegates to element-level methods
4. **Immutable content model** — Sections/elements are immutable; only output format changes

### Java Implementation

```java
import java.util.*;
import java.util.stream.Collectors;

// ============================================================
// CONTENT MODEL — format-agnostic document structure
// ============================================================

enum ElementType { HEADING, PARAGRAPH, LIST, CODE_BLOCK }

class TextElement {
    private final ElementType type;
    private final String content;
    private final int level; // heading level (1-3), or 0 for non-headings
    private final List<String> items; // for LIST type

    private TextElement(ElementType type, String content, int level, List<String> items) {
        this.type = type;
        this.content = content;
        this.level = level;
        this.items = items != null ? List.copyOf(items) : List.of();
    }

    public static TextElement heading(int level, String text) {
        return new TextElement(ElementType.HEADING, text, level, null);
    }

    public static TextElement paragraph(String text) {
        return new TextElement(ElementType.PARAGRAPH, text, 0, null);
    }

    public static TextElement list(List<String> items) {
        return new TextElement(ElementType.LIST, null, 0, items);
    }

    public static TextElement codeBlock(String code) {
        return new TextElement(ElementType.CODE_BLOCK, code, 0, null);
    }

    public ElementType getType() { return type; }
    public String getContent() { return content; }
    public int getLevel() { return level; }
    public List<String> getItems() { return items; }
}

// ============================================================
// STRATEGY INTERFACE
// ============================================================

interface FormatStrategy {
    String formatHeading(String text, int level);
    String formatParagraph(String text);
    String formatList(List<String> items);
    String formatCodeBlock(String code);
    String formatBold(String text);
    String getFormatName();
}

// ============================================================
// CONCRETE STRATEGIES
// ============================================================

class PlainTextStrategy implements FormatStrategy {
    @Override
    public String formatHeading(String text, int level) {
        String underline = level == 1 ? "=" : "-";
        return text.toUpperCase() + "\n" + underline.repeat(text.length());
    }

    @Override
    public String formatParagraph(String text) {
        return text;
    }

    @Override
    public String formatList(List<String> items) {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < items.size(); i++) {
            sb.append("  ").append(i + 1).append(". ").append(items.get(i));
            if (i < items.size() - 1) sb.append("\n");
        }
        return sb.toString();
    }

    @Override
    public String formatCodeBlock(String code) {
        return "    " + code.replace("\n", "\n    ");
    }

    @Override
    public String formatBold(String text) {
        return "*" + text + "*";
    }

    @Override
    public String getFormatName() { return "Plain Text"; }
}

class HtmlStrategy implements FormatStrategy {
    @Override
    public String formatHeading(String text, int level) {
        return "<h" + level + ">" + escapeHtml(text) + "</h" + level + ">";
    }

    @Override
    public String formatParagraph(String text) {
        return "<p>" + escapeHtml(text) + "</p>";
    }

    @Override
    public String formatList(List<String> items) {
        StringBuilder sb = new StringBuilder("<ul>\n");
        for (String item : items) {
            sb.append("  <li>").append(escapeHtml(item)).append("</li>\n");
        }
        sb.append("</ul>");
        return sb.toString();
    }

    @Override
    public String formatCodeBlock(String code) {
        return "<pre><code>" + escapeHtml(code) + "</code></pre>";
    }

    @Override
    public String formatBold(String text) {
        return "<strong>" + escapeHtml(text) + "</strong>";
    }

    @Override
    public String getFormatName() { return "HTML"; }

    private String escapeHtml(String text) {
        return text.replace("&", "&amp;").replace("<", "&lt;").replace(">", "&gt;");
    }
}

class MarkdownStrategy implements FormatStrategy {
    @Override
    public String formatHeading(String text, int level) {
        return "#".repeat(level) + " " + text;
    }

    @Override
    public String formatParagraph(String text) {
        return text;
    }

    @Override
    public String formatList(List<String> items) {
        return items.stream()
                .map(item -> "- " + item)
                .collect(Collectors.joining("\n"));
    }

    @Override
    public String formatCodeBlock(String code) {
        return "```\n" + code + "\n```";
    }

    @Override
    public String formatBold(String text) {
        return "**" + text + "**";
    }

    @Override
    public String getFormatName() { return "Markdown"; }
}

class JsonStrategy implements FormatStrategy {
    @Override
    public String formatHeading(String text, int level) {
        return String.format("{\"type\":\"heading\",\"level\":%d,\"text\":\"%s\"}", level, escape(text));
    }

    @Override
    public String formatParagraph(String text) {
        return String.format("{\"type\":\"paragraph\",\"text\":\"%s\"}", escape(text));
    }

    @Override
    public String formatList(List<String> items) {
        String itemsJson = items.stream()
                .map(i -> "\"" + escape(i) + "\"")
                .collect(Collectors.joining(","));
        return String.format("{\"type\":\"list\",\"items\":[%s]}", itemsJson);
    }

    @Override
    public String formatCodeBlock(String code) {
        return String.format("{\"type\":\"code\",\"content\":\"%s\"}", escape(code));
    }

    @Override
    public String formatBold(String text) {
        return String.format("{\"type\":\"bold\",\"text\":\"%s\"}", escape(text));
    }

    @Override
    public String getFormatName() { return "JSON"; }

    private String escape(String text) {
        return text.replace("\\", "\\\\").replace("\"", "\\\"").replace("\n", "\\n");
    }
}

// ============================================================
// CONTEXT — document that renders using a strategy
// ============================================================

class TextDocument {
    private final String title;
    private final List<TextElement> elements = new ArrayList<>();
    private FormatStrategy formatter;

    public TextDocument(String title, FormatStrategy formatter) {
        this.title = title;
        this.formatter = formatter;
    }

    public void setFormatter(FormatStrategy formatter) {
        this.formatter = formatter;
    }

    public void addElement(TextElement element) {
        elements.add(element);
    }

    public String render() {
        StringBuilder output = new StringBuilder();
        output.append(formatter.formatHeading(title, 1)).append("\n\n");

        for (TextElement element : elements) {
            switch (element.getType()) {
                case HEADING -> output.append(formatter.formatHeading(element.getContent(), element.getLevel()));
                case PARAGRAPH -> output.append(formatter.formatParagraph(element.getContent()));
                case LIST -> output.append(formatter.formatList(element.getItems()));
                case CODE_BLOCK -> output.append(formatter.formatCodeBlock(element.getContent()));
            }
            output.append("\n\n");
        }
        return output.toString().trim();
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class TextFormattingDemo {
    public static void main(String[] args) {
        TextDocument doc = new TextDocument("Design Patterns Guide", new MarkdownStrategy());
        doc.addElement(TextElement.heading(2, "Introduction"));
        doc.addElement(TextElement.paragraph("Design patterns are reusable solutions to common problems."));
        doc.addElement(TextElement.list(List.of("Creational", "Structural", "Behavioral")));
        doc.addElement(TextElement.codeBlock("Strategy strategy = new ConcreteStrategy();"));

        // Render as Markdown
        System.out.println("=== MARKDOWN ===");
        System.out.println(doc.render());

        // Switch to HTML — same content, different format
        doc.setFormatter(new HtmlStrategy());
        System.out.println("\n=== HTML ===");
        System.out.println(doc.render());

        // Switch to JSON
        doc.setFormatter(new JsonStrategy());
        System.out.println("\n=== JSON ===");
        System.out.println(doc.render());
    }
}
```

### Interview Talking Points

- **Content vs Presentation separation** — The document model is format-agnostic. This is the same principle behind MVC and template engines.
- **Real-world:** Spring's `HttpMessageConverter` uses strategy-like pattern — same response object rendered as JSON (Jackson), XML (JAXB), or Protobuf based on Accept header.
- **Alternative:** Could combine with Visitor pattern if element types vary independently from format types (double dispatch).
