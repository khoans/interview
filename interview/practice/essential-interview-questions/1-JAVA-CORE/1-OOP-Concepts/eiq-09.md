# 9. What is covariant return type?

**Answer:** Covariant return type lets you override a method and return a more specific type than the parent method. Introduced in Java 5. Solves unnecessary casting problem.

---

## What It Is

**Before Java 5:** Override must match exact signature, including return type.

**After Java 5:** Override can return a subtype of the parent's return type.

**Trade-off:** More type safety, cleaner code. But can confuse readers if overused.

---

## Example Scenario

**Without covariant return type:**

Parent class: `Document clone()` returns `Document`

Child class: Must return `Document` too, even if it's `PDFDocument`. Caller needs to cast: `(PDFDocument) doc.clone()`

**With covariant return type:**

Parent class: `Document clone()` returns `Document`

Child class: `PDFDocument clone()` returns `PDFDocument`. No cast needed. Type-safe.

---

## Real-World Use Case

**Builder Pattern:**

`ParentBuilder` has method `build()` returning `ParentObject`

`ChildBuilder` extends it, overrides `build()` returning `ChildObject` (which extends `ParentObject`)

**Benefit:** Fluent API stays type-safe. No casting when chaining:

`ChildBuilder builder = new ChildBuilder(); ChildObject obj = builder.build();`

**Without covariant:** Would return `ParentObject`, need cast back to `ChildObject`.

---

## Where It Applies

**Works with:**
- Method overriding (subclass returns subtype)
- Factory methods (return specific product type)
- Builder pattern (return specific builder type)

**Doesn't apply to:**
- Static methods (not overridden, hidden)
- Private methods (not inherited)
- Final methods (can't override)

---

## Why It Matters

**Type safety:** Compiler catches mismatches, not runtime.

**Readability:** No scattered casts throughout code.

**Maintainability:** Change return type in one place, not every call site.

**From experience:** We reduced 200+ casts in our codebase after refactoring builders to use covariant returns.

---

## Interview Tip

Keep it practical, not academic:

> "Covariant return type lets overrides return more specific types. Before Java 5, overriding `clone()` meant returning `Object` — callers always cast. Now, `PDFDocument.clone()` returns `PDFDocument`. I use it in builder patterns — child builders return their specific type, so fluent APIs stay type-safe without casts."
