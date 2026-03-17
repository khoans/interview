## 34. What is the difference between intermediate and terminal operations in Streams?

**Intermediate operations return a Stream and are lazy. Terminal operations return a result and trigger execution.**

Intermediate operations (filter, map, sorted) build a pipeline. Nothing happens until you call a terminal operation (collect, forEach, reduce).

**Why this design:**
- Laziness enables optimization — the stream processes elements one-at-a-time, not all filters then all maps
- Short-circuiting: findFirst() stops after finding one match, doesn't process the entire stream

**Trade-off:**
- Lazy evaluation saves memory and CPU for large datasets
- But debugging is harder — you can't inspect the pipeline mid-stream without a terminal operation

**Real-world example:**
We processed 10 million transaction records. Initial code called `.collect(toList())` after each filter — three passes, 4GB heap, frequent GC pauses. Refactored to chain intermediates (`.filter(...).map(...).filter(...)`) then one terminal `.collect()` — single pass, 400MB heap, 70% faster. The key was understanding intermediates are free; terminals are expensive.

**Rule of thumb:** Chain all intermediates, one terminal at the end.
