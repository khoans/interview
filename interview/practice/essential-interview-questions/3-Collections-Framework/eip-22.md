## 22. What is the difference between ArrayList and LinkedList?

ArrayList is a dynamic array — contiguous memory, fast random access. LinkedList is a doubly-linked list — nodes scattered in memory, fast insertion/removal at known positions. In practice, use ArrayList almost always.

**The textbook answer versus reality:**

Textbooks say LinkedList wins for insertions and deletions. That's technically true — O(1) at a known node versus O(n) for ArrayList shifting elements. But in real-world performance, ArrayList almost always wins, even for insertions. The reason is CPU cache locality. ArrayList's contiguous memory means sequential access loads entire cache lines. LinkedList's scattered nodes cause cache misses on every traversal, and each node carries 24+ bytes of overhead for the prev/next pointers.

**When ArrayList wins:**
- Random access by index — O(1) versus O(n) for LinkedList
- Iteration — cache-friendly sequential memory access
- Memory efficiency — no per-element pointer overhead
- Appending to the end — amortized O(1), same as LinkedList

**When LinkedList might win:**
- Frequent insertion/removal at the head — like implementing a queue or deque. But even then, ArrayDeque outperforms LinkedList for queue operations
- When you already have a reference to a node and need O(1) removal — rare in Java since ListIterator is the only way to do this

**Real-world example:** We had a message processing pipeline using LinkedList as a buffer, adding and removing from both ends. Profiling showed 15% of CPU time on cache misses during iteration. Switching to ArrayDeque — same interface, contiguous memory — dropped processing latency by 20% with zero code logic changes.

**Bottom line:** Default to ArrayList. The only time I'd consider LinkedList is if someone showed me profiler data proving it's needed — and I've never seen that happen in production.
