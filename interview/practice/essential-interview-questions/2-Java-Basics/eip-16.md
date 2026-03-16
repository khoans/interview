16. What are wrapper classes? Why are they needed?

Wrapper classes are object representations of Java's eight primitive types — Integer for int, Double for double, Boolean for boolean, and so on. They exist because Java's Collections framework and generics only work with objects, not primitives.

**Why they're needed:**

You can't put an int into a HashMap or an ArrayList. You need Integer. That's the primary reason — bridging the gap between primitives and the object-oriented world. They also provide utility methods like Integer.parseInt or Double.isNaN, and they allow null values, which primitives can't represent.

**Trade-off:** Wrappers carry overhead — each Integer object is roughly 16 bytes on the heap versus 4 bytes for a primitive int. In a tight loop processing millions of records, using Integer instead of int can double your memory footprint and kill cache performance. We hit this on a pricing engine that was boxing and unboxing millions of doubles per second — switching to primitive arrays cut GC pause time by 60%.

**The autoboxing trap:** Java auto-converts between primitives and wrappers, which hides the cost. The dangerous case is comparing two Integers with == instead of equals — it works for values -128 to 127 because of the Integer cache, then silently breaks for larger values. That's a classic production bug.

**Bottom line:** Use primitives by default for performance. Use wrappers when you need nullability, collections, or generics. Be aware of autoboxing costs in hot paths.
