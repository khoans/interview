**21.** UI Component System — Composite pattern. Simple and container components with render/enable/disable. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## UI Component System — Composite Pattern

### Pattern: Composite

**Why Composite?** UI trees are inherently recursive: a Panel contains Buttons, Labels, and other Panels. `render()`, `enable()`, `disable()` must propagate through the entire tree uniformly. Client code shouldn't distinguish between a single Button and a Panel full of components.

### Text UML

```
┌────────────────────────────────┐
│      <<abstract>>              │
│      UIComponent               │
├────────────────────────────────┤
│ # name: String                 │
│ # enabled: boolean             │
│ # visible: boolean             │
├────────────────────────────────┤
│ + render(): String             │
│ + enable(): void               │
│ + disable(): void              │
│ + show(): void                 │
│ + hide(): void                 │
│ + isEnabled(): boolean         │
└──────────┬─────────────────────┘
           │
    ┌──────┴──────────────┐
    │                      │
┌───┴─────────┐   ┌───────┴───────────┐
│ Leaf comps  │   │ Container         │
│ Button      │   │ (Composite)       │
│ Label       │   ├───────────────────┤
│ TextInput   │   │ - children: List  │
│ CheckBox    │   │ + add(component)  │
│ Image       │   │ + remove(comp)    │
└─────────────┘   │ + getChildren()   │
                  └───────────────────┘
                       │
                  Panel, Form,
                  Toolbar, Dialog
```

### Key Design Decisions

1. **Abstract base class** — Common state (enabled, visible, name) shared by all components
2. **Recursive render()** — Container's render includes all children's rendered output
3. **Propagating enable/disable** — Disabling a Panel disables all children recursively
4. **Visitor-friendly** — Tree structure supports visitor pattern for cross-cutting concerns (validation, serialization)

### SOLID Principles Applied

- **SRP**: Each component handles its own rendering; Container manages children
- **OCP**: Add new component types (Slider, Dropdown) without modifying existing ones
- **LSP**: All components are substitutable — Container treats them uniformly
- **DIP**: Layout engines and event handlers depend on UIComponent abstraction

### Java Code

```java
import java.util.*;

// --- Abstract UI Component ---
public abstract class UIComponent {
    protected String name;
    protected boolean enabled = true;
    protected boolean visible = true;

    public UIComponent(String name) {
        this.name = name;
    }

    public abstract String render(int depth);

    public void enable() { this.enabled = true; }
    public void disable() { this.enabled = false; }
    public void show() { this.visible = true; }
    public void hide() { this.visible = false; }
    public boolean isEnabled() { return enabled; }
    public boolean isVisible() { return visible; }
    public String getName() { return name; }

    protected String indent(int depth) {
        return "  ".repeat(depth);
    }
}

// ===================== Leaf Components =====================

public class Button extends UIComponent {
    private final String text;
    private final String onClick;

    public Button(String name, String text, String onClick) {
        super(name);
        this.text = text;
        this.onClick = onClick;
    }

    @Override
    public String render(int depth) {
        if (!visible) return "";
        String state = enabled ? "" : " disabled";
        return indent(depth) + String.format("<button name=\"%s\"%s onClick=\"%s\">%s</button>",
            name, state, onClick, text);
    }
}

public class Label extends UIComponent {
    private String text;

    public Label(String name, String text) {
        super(name);
        this.text = text;
    }

    @Override
    public String render(int depth) {
        if (!visible) return "";
        return indent(depth) + String.format("<label name=\"%s\">%s</label>", name, text);
    }

    public void setText(String text) { this.text = text; }
}

public class TextInput extends UIComponent {
    private final String placeholder;
    private String value = "";

    public TextInput(String name, String placeholder) {
        super(name);
        this.placeholder = placeholder;
    }

    @Override
    public String render(int depth) {
        if (!visible) return "";
        String state = enabled ? "" : " disabled";
        return indent(depth) + String.format(
            "<input name=\"%s\" placeholder=\"%s\" value=\"%s\"%s/>",
            name, placeholder, value, state);
    }

    public void setValue(String value) { this.value = value; }
    public String getValue() { return value; }
}

public class CheckBox extends UIComponent {
    private boolean checked = false;
    private final String label;

    public CheckBox(String name, String label) {
        super(name);
        this.label = label;
    }

    @Override
    public String render(int depth) {
        if (!visible) return "";
        String checkedAttr = checked ? " checked" : "";
        String state = enabled ? "" : " disabled";
        return indent(depth) + String.format(
            "<checkbox name=\"%s\"%s%s>%s</checkbox>", name, checkedAttr, state, label);
    }

    public void setChecked(boolean checked) { this.checked = checked; }
}

// ===================== Container (Composite) =====================

public class Container extends UIComponent {
    private final List<UIComponent> children = new ArrayList<>();
    private final String tag; // "div", "form", "panel", etc.

    public Container(String name, String tag) {
        super(name);
        this.tag = tag;
    }

    public void add(UIComponent component) {
        children.add(component);
    }

    public void remove(UIComponent component) {
        children.remove(component);
    }

    public List<UIComponent> getChildren() {
        return Collections.unmodifiableList(children);
    }

    // Recursive enable — propagates to all children
    @Override
    public void enable() {
        super.enable();
        children.forEach(UIComponent::enable);
    }

    // Recursive disable — propagates to all children
    @Override
    public void disable() {
        super.disable();
        children.forEach(UIComponent::disable);
    }

    // Recursive hide — propagates to all children
    @Override
    public void hide() {
        super.hide();
        children.forEach(UIComponent::hide);
    }

    @Override
    public void show() {
        super.show();
        children.forEach(UIComponent::show);
    }

    @Override
    public String render(int depth) {
        if (!visible) return "";
        StringBuilder sb = new StringBuilder();
        sb.append(indent(depth)).append(String.format("<%s name=\"%s\">", tag, name)).append("\n");
        for (UIComponent child : children) {
            String rendered = child.render(depth + 1);
            if (!rendered.isEmpty()) {
                sb.append(rendered).append("\n");
            }
        }
        sb.append(indent(depth)).append(String.format("</%s>", tag));
        return sb.toString();
    }

    // Find component by name (recursive)
    public Optional<UIComponent> findByName(String componentName) {
        if (this.name.equals(componentName)) return Optional.of(this);
        for (UIComponent child : children) {
            if (child.getName().equals(componentName)) return Optional.of(child);
            if (child instanceof Container) {
                Optional<UIComponent> found = ((Container) child).findByName(componentName);
                if (found.isPresent()) return found;
            }
        }
        return Optional.empty();
    }
}
```

### Usage

```java
// Build a login form UI
Container loginForm = new Container("loginForm", "form");

loginForm.add(new Label("titleLabel", "Sign In"));
loginForm.add(new TextInput("emailInput", "Enter email"));
loginForm.add(new TextInput("passwordInput", "Enter password"));
loginForm.add(new CheckBox("rememberMe", "Remember me"));

Container buttonBar = new Container("buttonBar", "div");
buttonBar.add(new Button("submitBtn", "Sign In", "handleLogin()"));
buttonBar.add(new Button("cancelBtn", "Cancel", "handleCancel()"));
loginForm.add(buttonBar);

// Render the entire UI tree
System.out.println(loginForm.render(0));
// <form name="loginForm">
//   <label name="titleLabel">Sign In</label>
//   <input name="emailInput" placeholder="Enter email" value=""/>
//   <input name="passwordInput" placeholder="Enter password" value=""/>
//   <checkbox name="rememberMe">Remember me</checkbox>
//   <div name="buttonBar">
//     <button name="submitBtn" onClick="handleLogin()">Sign In</button>
//     <button name="cancelBtn" onClick="handleCancel()">Cancel</button>
//   </div>
// </form>

// Disable entire form (propagates to all children)
loginForm.disable();

// Find and modify a specific component
loginForm.findByName("emailInput").ifPresent(c -> {
    if (c instanceof TextInput) {
        ((TextInput) c).setValue("user@example.com");
    }
});
```

### Interview Talking Points

- **Swing/AWT uses this**: `JPanel.add(JButton)`, `JFrame.add(JPanel)` — Java's entire GUI framework is Composite pattern
- **Recursive propagation**: `disable()` on a form disables every child — single call controls the entire subtree
- **React/Angular analog**: Component tree with `props` propagation is the same concept in a different paradigm
- **findByName()**: Recursive search through the component tree — similar to `document.getElementById()` in DOM
