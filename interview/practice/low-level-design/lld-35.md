**35.** Design a Text Editor with Undo/Redo using the Command pattern. Support Insert, Delete, Format, Cut, and Paste — all undoable.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Text Editor Undo/Redo — Command Pattern

**Pattern:** Command (Behavioral)
**Why Command?** Each edit operation becomes an object with execute/undo. Two stacks (undo/redo) enable full history navigation.

**SOLID Principles Applied:**
- **SRP:** Each command handles one operation; Editor manages document state; History manages stacks
- **OCP:** Add new operations (Find/Replace, Indent) as new command classes
- **DIP:** History works with Command interface, not specific operation types

### Text UML Diagram

```
┌──────────────┐     ┌──────────────┐     ┌────────────────┐
│   Editor     │────→│  CommandHist │────→│ <<interface>>   │
│──────────────│     │──────────────│     │    Command      │
│ -document    │     │ -undoStack   │     │────────────────│
│ -clipboard   │     │ -redoStack   │     │ +execute()      │
│ -history     │     │ +push()      │     │ +undo()         │
│ +insert()    │     │ +undo()      │     │ +getName()      │
│ +delete()    │     │ +redo()      │     └────────┬───────┘
│ +cut()       │     └──────────────┘              │
│ +paste()     │                          ┌────────┼────────┐
│ +format()    │                       Insert   Delete   Format
│ +undo()      │                       Cmd      Cmd      Cmd
│ +redo()      │                       CutCmd   PasteCmd
└──────────────┘
```

### Key Design Decisions

1. **StringBuilder as document model** — Mutable, efficient for insert/delete at positions
2. **Redo cleared on new edit** — Standard behavior: new edit after undo discards redo history
3. **Commands store state for undo** — Each command remembers what it changed (deleted text, previous format)
4. **Clipboard separate from commands** — Cut/Copy set clipboard; Paste reads it

### Java Implementation

```java
import java.util.*;

// ============================================================
// DOCUMENT MODEL
// ============================================================

class TextDocument {
    private final StringBuilder content;
    private final Map<String, String> formatting; // key = "bold:0-5", value = format info

    public TextDocument() {
        this.content = new StringBuilder();
        this.formatting = new LinkedHashMap<>();
    }

    public void insert(int position, String text) {
        content.insert(position, text);
    }

    public String delete(int position, int length) {
        String deleted = content.substring(position, position + length);
        content.delete(position, position + length);
        return deleted;
    }

    public void applyFormat(int start, int end, String format) {
        formatting.put(format + ":" + start + "-" + end, format);
    }

    public String removeFormat(String key) {
        return formatting.remove(key);
    }

    public String getText() { return content.toString(); }
    public int length() { return content.length(); }

    @Override
    public String toString() {
        return content.toString();
    }
}

// ============================================================
// COMMAND INTERFACE
// ============================================================

interface EditorCommand {
    void execute();
    void undo();
    String getName();
}

// ============================================================
// CONCRETE COMMANDS
// ============================================================

class InsertCommand implements EditorCommand {
    private final TextDocument document;
    private final int position;
    private final String text;

    public InsertCommand(TextDocument document, int position, String text) {
        this.document = document;
        this.position = position;
        this.text = text;
    }

    @Override
    public void execute() {
        document.insert(position, text);
    }

    @Override
    public void undo() {
        document.delete(position, text.length());
    }

    @Override
    public String getName() { return "Insert '" + text + "' at " + position; }
}

class DeleteCommand implements EditorCommand {
    private final TextDocument document;
    private final int position;
    private final int length;
    private String deletedText; // saved for undo

    public DeleteCommand(TextDocument document, int position, int length) {
        this.document = document;
        this.position = position;
        this.length = length;
    }

    @Override
    public void execute() {
        deletedText = document.delete(position, length);
    }

    @Override
    public void undo() {
        document.insert(position, deletedText);
    }

    @Override
    public String getName() { return "Delete " + length + " chars at " + position; }
}

class FormatCommand implements EditorCommand {
    private final TextDocument document;
    private final int start;
    private final int end;
    private final String format; // "bold", "italic", "underline"
    private String formatKey;

    public FormatCommand(TextDocument document, int start, int end, String format) {
        this.document = document;
        this.start = start;
        this.end = end;
        this.format = format;
    }

    @Override
    public void execute() {
        formatKey = format + ":" + start + "-" + end;
        document.applyFormat(start, end, format);
        System.out.println("Applied " + format + " to range [" + start + ", " + end + ")");
    }

    @Override
    public void undo() {
        document.removeFormat(formatKey);
        System.out.println("Removed " + format + " from range [" + start + ", " + end + ")");
    }

    @Override
    public String getName() { return "Format " + format + " [" + start + "-" + end + ")"; }
}

class CutCommand implements EditorCommand {
    private final TextDocument document;
    private final Clipboard clipboard;
    private final int position;
    private final int length;
    private String cutText;
    private String previousClipboard;

    public CutCommand(TextDocument document, Clipboard clipboard, int position, int length) {
        this.document = document;
        this.clipboard = clipboard;
        this.position = position;
        this.length = length;
    }

    @Override
    public void execute() {
        previousClipboard = clipboard.getContent();
        cutText = document.delete(position, length);
        clipboard.setContent(cutText);
    }

    @Override
    public void undo() {
        document.insert(position, cutText);
        clipboard.setContent(previousClipboard);
    }

    @Override
    public String getName() { return "Cut " + length + " chars at " + position; }
}

class PasteCommand implements EditorCommand {
    private final TextDocument document;
    private final Clipboard clipboard;
    private final int position;
    private String pastedText;

    public PasteCommand(TextDocument document, Clipboard clipboard, int position) {
        this.document = document;
        this.clipboard = clipboard;
        this.position = position;
    }

    @Override
    public void execute() {
        pastedText = clipboard.getContent();
        if (pastedText != null && !pastedText.isEmpty()) {
            document.insert(position, pastedText);
        }
    }

    @Override
    public void undo() {
        if (pastedText != null && !pastedText.isEmpty()) {
            document.delete(position, pastedText.length());
        }
    }

    @Override
    public String getName() { return "Paste '" + pastedText + "' at " + position; }
}

// ============================================================
// CLIPBOARD
// ============================================================

class Clipboard {
    private String content = "";
    public String getContent() { return content; }
    public void setContent(String content) { this.content = content; }
}

// ============================================================
// COMMAND HISTORY — undo/redo stacks
// ============================================================

class CommandHistory {
    private final Deque<EditorCommand> undoStack = new ArrayDeque<>();
    private final Deque<EditorCommand> redoStack = new ArrayDeque<>();
    private final int maxHistory;

    public CommandHistory(int maxHistory) {
        this.maxHistory = maxHistory;
    }

    public void executeAndPush(EditorCommand command) {
        command.execute();
        undoStack.push(command);
        redoStack.clear(); // new action invalidates redo
        if (undoStack.size() > maxHistory) {
            // Remove oldest (bottom of stack) — would need Deque as list for this
        }
    }

    public void undo() {
        if (undoStack.isEmpty()) {
            System.out.println("Nothing to undo");
            return;
        }
        EditorCommand command = undoStack.pop();
        command.undo();
        redoStack.push(command);
        System.out.println("Undo: " + command.getName());
    }

    public void redo() {
        if (redoStack.isEmpty()) {
            System.out.println("Nothing to redo");
            return;
        }
        EditorCommand command = redoStack.pop();
        command.execute();
        undoStack.push(command);
        System.out.println("Redo: " + command.getName());
    }

    public boolean canUndo() { return !undoStack.isEmpty(); }
    public boolean canRedo() { return !redoStack.isEmpty(); }
}

// ============================================================
// EDITOR — high-level API using commands internally
// ============================================================

class TextEditor {
    private final TextDocument document;
    private final Clipboard clipboard;
    private final CommandHistory history;

    public TextEditor() {
        this.document = new TextDocument();
        this.clipboard = new Clipboard();
        this.history = new CommandHistory(100);
    }

    public void insert(int position, String text) {
        history.executeAndPush(new InsertCommand(document, position, text));
    }

    public void delete(int position, int length) {
        history.executeAndPush(new DeleteCommand(document, position, length));
    }

    public void format(int start, int end, String format) {
        history.executeAndPush(new FormatCommand(document, start, end, format));
    }

    public void cut(int position, int length) {
        history.executeAndPush(new CutCommand(document, clipboard, position, length));
    }

    public void paste(int position) {
        history.executeAndPush(new PasteCommand(document, clipboard, position));
    }

    public void undo() { history.undo(); }
    public void redo() { history.redo(); }

    public String getText() { return document.getText(); }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class TextEditorDemo {
    public static void main(String[] args) {
        TextEditor editor = new TextEditor();

        editor.insert(0, "Hello World");
        System.out.println("After insert: '" + editor.getText() + "'");

        editor.insert(5, " Beautiful");
        System.out.println("After insert: '" + editor.getText() + "'");
        // "Hello Beautiful World"

        editor.format(6, 15, "bold");

        editor.delete(5, 10);
        System.out.println("After delete: '" + editor.getText() + "'");
        // "Hello World"

        editor.undo(); // undo delete
        System.out.println("After undo:   '" + editor.getText() + "'");
        // "Hello Beautiful World"

        editor.redo(); // redo delete
        System.out.println("After redo:   '" + editor.getText() + "'");
        // "Hello World"

        // Cut and paste
        editor.cut(0, 5);
        System.out.println("After cut:    '" + editor.getText() + "'");
        // " World"

        editor.paste(6);
        System.out.println("After paste:  '" + editor.getText() + "'");
        // " WorldHello"

        // Multiple undos
        editor.undo(); // undo paste
        editor.undo(); // undo cut
        System.out.println("After 2 undos: '" + editor.getText() + "'");
        // "Hello World"
    }
}
```

### Interview Talking Points

- **Why not Memento for undo?** Memento saves entire state snapshots — expensive for large documents. Command-based undo stores only the delta (what changed). Best for text editors.
- **Redo invalidation:** Industry standard — typing after undo clears redo. Git doesn't have "redo" for the same reason (new commit after reset creates a new branch of history).
- **Real-world:** IntelliJ IDEA, VS Code, and all editors use Command pattern for undo. Also used in database transaction logs (WAL) where each operation is a reversible command.
