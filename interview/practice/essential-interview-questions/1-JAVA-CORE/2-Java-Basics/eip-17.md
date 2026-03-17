## 17. What is autoboxing and unboxing?

Autoboxing is Java automatically converting a primitive to its wrapper object, and unboxing is the reverse. The compiler inserts these conversions so you can use primitives and wrappers interchangeably — like adding an int directly to a List of Integer.

**Why it matters in practice:**

Convenience comes with hidden cost. Every autobox creates an object on the heap. In a tight loop summing a million values, if you accidentally declare the accumulator as Long instead of long, you're creating a million throwaway Long objects. We caught exactly this in a metrics aggregation service — a single variable type change from Long to long cut GC pressure by 35%.

**The dangerous pitfall — null unboxing:** If you unbox a null wrapper, you get a NullPointerException, not zero. This is the number one autoboxing bug in production. A method returns Integer from a database lookup, the value is null, you assign it to an int — instant NPE. No compiler warning, no obvious sign in the code.

**The == trap:** Autoboxing interacts badly with equality checks. Java caches Integer values from -128 to 127, so == works in that range. Outside that range, two Integers with the same value are different objects, and == returns false. Always use equals for wrapper comparison.

**Bottom line:** Autoboxing is syntactic sugar that hides real costs. Be deliberate — use primitives in performance-critical paths, and always null-check before unboxing values that come from external sources like databases or APIs.
