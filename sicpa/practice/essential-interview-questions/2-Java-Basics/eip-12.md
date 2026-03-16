12. What is the difference between == and equals()?

**Answer:**

`==` compares references (memory addresses); `equals()` compares logical content.

Use `==` for primitives and reference identity. Use `equals()` for value equality on objects.

Trade-off: `==` is faster (direct comparison) but rarely what you want for objects. `equals()` can be overridden, so behavior depends on implementation.

Critical pitfall: String comparison. `new String("hello") == new String("hello")` returns false (different objects), but `.equals()` returns true (same content).

Real-world issue: We had a production bug where a developer used `==` to compare user IDs (Long objects). Worked in dev (small numbers cached by JVM), failed in production (larger IDs not cached). Always use `.equals()` for object comparison.

Key rule: For primitives, use `==`. For objects, use `.equals()` unless you specifically need identity comparison.
