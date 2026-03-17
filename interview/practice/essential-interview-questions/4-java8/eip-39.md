## 39. What is the method reference syntax (::)?

**Method references are shorthand for lambdas that call a single existing method.**

`obj::method` means "call this method on the given input" — cleaner than `(x) -> obj.method(x)`.

**Four forms:**
- Static: `ClassName::staticMethod`
- Instance: `instance::method`
- Arbitrary instance: `ClassName::instanceMethod`
- Constructor: `ClassName::new`

**Why they exist:**
- Less boilerplate when the lambda just forwards arguments
- More readable — intent is clearer without parameter noise

**Trade-off:**
- Cleaner syntax but can obscure which method is actually called
- Overusing method references on complex chains makes debugging harder — stack traces show the reference, not the call site

**Real-world example:**
We had `orders.stream().map(o -> o.getTotal()).collect(...)` — 120 occurrences across the codebase. Switched to `orders.stream().map(Order::getTotal).collect(...)` — cut 400 lines, improved readability. But one team used `obj::getValue` where `getValue` had side effects — took 6 hours to trace because the method reference hid the call. Now we document: method references for pure functions only.

**Rule of thumb:** Use when the method name is self-documenting. Expand to lambda when you need comments.
