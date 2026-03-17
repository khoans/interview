33. What is a lambda expression?

**A lambda is an anonymous function — a block of code you can pass around like data, without writing a full class.**

It's syntactic sugar for implementing a functional interface. Instead of writing `new Comparator<Order>() { public int compare(...) { ... } }`, you write `(o1, o2) -> o1.getDate().compareTo(o2.getDate())`.

**Trade-offs:**
- Conciseness: 10 lines become 1, but can hurt readability if overused
- Type inference: Compiler figures out types, but explicit is sometimes clearer
- Debugging: Stack traces show `lambda$0` instead of method names — harder to trace

**Real-world example:**
We had a sorting feature with five different `Comparator` implementations — each was a separate class, 20-30 lines. Replaced them with lambdas inline at the call site. Reduced 150 lines to 15. But when sorting logic got complex (null handling, multiple fields), we extracted back to named methods — one-liners became hard to read.

**Key insight:** Lambdas shine for simple, single-purpose behavior passed as arguments. If your lambda is more than one line or does anything non-trivial, extract it to a method. Readability beats cleverness.
