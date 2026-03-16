# Low-Level Design (LLD) - Answer Checker & Solution Provider

**Usage:** `/check-lld <file-path>`

**Example:** `/check-lld sicpa/low-level-design/1.md`

---

## What This Command Does

When you provide a file path containing a Low-Level Design practice question:

### If You Submitted Code/Design:
1. ✅ **Read the question** and your design/implementation
2. ✅ **Analyze your design** for correctness
3. ✅ **Check for common issues**:
   - Design pattern appropriateness
   - SOLID principles adherence
   - Scalability and extensibility
   - Edge case handling
   - Code quality and readability
4. ✅ **Provide feedback** with:
   - Clear verdict (Correct / Has Issues / Wrong)
   - Table of checks (design, patterns, SOLID, extensibility)
   - UML diagram review
   - Corrected design (if needed)
   - Alternative approaches
   - Interview tips

### If NO Answer Submitted (Empty/Placeholder):
1. ✅ **Read the question** and requirements
2. ✅ **Provide complete solution** with:
   - Full class design with code
   - Detailed analysis of design decisions
   - Why each pattern was chosen
   - UML diagram (text representation)
   - Time/Space complexity
   - Alternative approaches
   - Interview tips
3. ✅ **Write the solution back to the same file** so you can study it later

---

## Supported Question Files

Any file in `sicpa/low-level-design/` or `sicpa/practice/lld/` format:

```
sicpa/low-level-design/1.md
sicpa/low-level-design/2.md
sicpa/practice/lld/coffee-machine.md
sicpa/practice/lld/parking-lot.md
...
```

---

## Evaluation Criteria

### 1. Design Quality (Most Important)
- [ ] Does the design solve the problem?
- [ ] Are responsibilities well-separated?
- [ ] Is the design extensible?
- [ ] Are there any design flaws?

### 2. Design Patterns
- [ ] Are patterns used appropriately?
- [ ] Is over-engineering avoided?
- [ ] Are patterns clearly documented?

### 3. SOLID Principles
- [ ] **S**ingle Responsibility Principle
- [ ] **O**pen/Closed Principle
- [ ] **L**iskov Substitution Principle
- [ ] **I**nterface Segregation Principle
- [ ] **D**ependency Inversion Principle

### 4. Extensibility
- [ ] Can new features be added easily?
- [ ] Is configuration externalized?
- [ ] Are hard-coded values avoided?

### 5. Edge Cases
- [ ] Null/empty input handling
- [ ] Invalid state handling
- [ ] Concurrent access (if applicable)
- [ ] Resource cleanup (if applicable)

### 6. Code Quality
- [ ] Meaningful class/method names
- [ ] Clean, readable code
- [ ] Proper comments/documentation
- [ ] Consistent formatting

---

## Example Usage

### With Submitted Answer:
```
/check-lld sicpa/low-level-design/1.md
```

**Expected Output:**
- Verdict on design correctness
- Table of checks (design, patterns, SOLID, extensibility)
- UML diagram review
- Issues found with explanations
- Corrected design (if needed)
- Alternative approaches
- Interview tips

### Without Submitted Answer (Empty File):
```
/check-lld sicpa/low-level-design/5.md
```

**Expected Output:**
- Complete solution with full code
- Detailed analysis of WHY each design decision was made
- Pattern justification
- UML diagram
- Complexity analysis
- Alternative approaches
- Interview tips
- **Solution is written back to the same file!**

---

## Language Support

| Language | Support Level |
|----------|---------------|
| Java | ✅ Full support |
| Python | ✅ Full support |
| JavaScript/TypeScript | ✅ Full support |
| C++ | ✅ Full support |
| C# | ✅ Full support |
| Other | ✅ Basic support |

---

## Common LLD Patterns Reference

| Pattern | When to Use | Example Questions |
|---------|-------------|-------------------|
| **Singleton** | Single instance needed | Logger, Config, Cache |
| **Factory** | Object creation logic varies | Payment, Notification |
| **Builder** | Complex object construction | Query Builder, Report |
| **Strategy** | Algorithm varies independently | Sorting, Pricing |
| **Observer** | One-to-many dependencies | Stock alerts, Events |
| **Command** | Encapsulate requests | Undo/Redo, Queue |
| **State** | State-dependent behavior | Vending machine, Order |
| **Decorator** | Add responsibilities dynamically | Coffee add-ons, Streams |
| **Composite** | Tree structures | File system, UI |
| **Facade** | Simplify complex subsystem | Home theater, Order |
| **Adapter** | Incompatible interfaces | Payment gateway, Legacy |
| **Proxy** | Control access | Image loading, Access control |
| **Template Method** | Fixed algorithm with variations | Import, Build process |
| **Iterator** | Custom iteration | Tree traversal, Playlist |

---

## SOLID Principles Checklist

```
┌─────────────────────────────────────────────────────────┐
│  SOLID Principles:                                      │
│                                                         │
│  S - Single Responsibility Principle                    │
│  ─────────────────────────────────────                  │
│  ✅ Each class has one reason to change                 │
│  ✅ Each class has one job                              │
│                                                         │
│  O - Open/Closed Principle                              │
│  ─────────────────────────────                          │
│  ✅ Open for extension                                │
│  ✅ Closed for modification                             │
│                                                         │
│  L - Liskov Substitution Principle                      │
│  ─────────────────────────────────────                  │
│  ✅ Subtypes can replace base types                     │
│  ✅ No unexpected behavior in subclasses                │
│                                                         │
│  I - Interface Segregation Principle                    │
│  ─────────────────────────────────────                  │
│  ✅ Many specific interfaces > one general interface    │
│  ✅ No unused methods in interfaces                     │
│                                                         │
│  D - Dependency Inversion Principle                     │
│  ─────────────────────────────────────                  │
│  ✅ Depend on abstractions, not concretions             │
│  ✅ High-level modules don't depend on low-level        │
└─────────────────────────────────────────────────────────┘
```

---

## Complexity Cheat Sheet

| Operation | Time Complexity | Space Complexity |
|-----------|-----------------|------------------|
| Object creation | O(1) | O(1) |
| Map lookup (HashMap) | O(1) average | O(n) |
| Map lookup (TreeMap) | O(log n) | O(n) |
| List iteration | O(n) | O(1) |
| Set lookup (HashSet) | O(1) average | O(n) |
| Sorting | O(n log n) | O(log n) or O(n) |
| Recursion (depth n) | O(n) or O(2ⁿ) | O(n) stack space |
| Nested loops | O(n²) | O(1) |

---

## Quick Reference

| Command | Description |
|---------|-------------|
| `/check-lld <file>` | Check LLD design answer |
| `/check-lld 1.md` | Shorthand (if in same directory) |
| `/check-lld` | List all available questions |

---

## Sample Output Format (With Submitted Answer)

```markdown
# ✅/❌ [Verdict: Correct / Has Issues / Wrong]

```java
// Your code here
```

| Check | Status |
|-------|--------|
| Design Quality | ✅/❌ |
| Design Patterns | ✅/❌ |
| SOLID Principles | ✅/❌ |
| Extensibility | ✅/❌ |
| Edge Cases | ✅/❌ |
| Code Quality | ✅/❌ |

---

## 📊 UML Diagram Review

[Review of class relationships]

---

## ⚠️ Issues Found (if any)

[List any design issues with explanations]

---

## ✅ Corrected Design (if needed)

```java
// Corrected code
```

---

## 🎯 Alternative Approaches

[Show other valid design approaches]

---

## 💡 Interview Tips

[Relevant tips for this type of problem]

---

## 📝 Key Takeaway

[Summary of the main learning point]
```

---

## Sample Output Format (Without Submitted Answer)

```markdown
# ✅ Complete Solution Provided

## 📋 Problem Analysis

[Break down the requirements]

---

## 🏗️ Design Approach

### Why This Design?
[Explain why this approach was chosen]

### Design Patterns Used
| Pattern | Why | Where |
|---------|-----|-------|
| Pattern1 | Reason | Location |

---

## 📊 Class Design

```java
// Complete implementation
```

---

## 🔍 Detailed Analysis

### Why Class A Exists?
[Explain responsibility]

### Why Pattern X Was Chosen?
[Explain pattern justification]

### Why This Over Alternative Y?
[Explain trade-off analysis]

---

## 📈 UML Diagram

```
[ClassA] --(extends)--> [ClassB]
[ClassC] --(implements)--> [InterfaceD]
```

---

## ⏱️ Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Operation1 | O(?) | O(?) |

---

## 🎯 Alternative Approaches

| Approach | Pros | Cons |
|----------|------|------|
| Approach1 | ... | ... |

---

## 💡 Interview Tips

[Relevant tips for this type of problem]

---

## 📝 Key Takeaway

[Summary of the main learning point]

---

✅ **Solution has been written to the file!**
```

---

## Tips for LLD Questions

### 1. Clarify Requirements First
- Ask about scale (how many users?)
- Ask about concurrency requirements
- Ask about persistence needs
- Ask about extensibility requirements

### 2. Identify Core Entities
- What are the main objects?
- What are their responsibilities?
- How do they relate to each other?

### 3. Choose Patterns Wisely
- Don't force patterns
- Use patterns where they naturally fit
- Explain WHY you chose each pattern

### 4. Think About Extensibility
- What might change in the future?
- How easy is it to add new features?
- Is configuration externalized?

### 5. Handle Edge Cases
- Null/empty inputs
- Invalid states
- Concurrent access
- Resource cleanup

### 6. Write Clean Code
- Meaningful names
- Single responsibility
- Proper abstraction
- Comments for complex logic

---

## File Writing Behavior

### When Answer is Empty/Missing:
```
1. Read the question
2. Generate complete solution
3. Write solution to the SAME file
4. Notify user that solution was saved
```

### When Answer is Submitted:
```
1. Read the question and answer
2. Analyze the design
3. Provide feedback (NOT writing to file)
4. Let user make their own corrections
```

---

**Ready to check your LLD designs or provide complete solutions! Just provide the file path.** 🎯
