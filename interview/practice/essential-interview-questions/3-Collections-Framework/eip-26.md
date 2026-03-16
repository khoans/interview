26. What is the difference between HashSet and TreeSet?

**HashSet uses hashing for O(1) operations; TreeSet uses red-black tree for O(log n) operations with sorted order.**

HashSet gives you fast lookups but no ordering guarantee. TreeSet maintains elements in sorted order — natural ordering or custom comparator — but pays a performance cost on every operation.

**Trade-offs:**
- HashSet: O(1) add/remove/contains, unordered, allows one null
- TreeSet: O(log n) operations, always sorted, no nulls (can't compare null)
- Memory: TreeSet uses more — stores tree structure pointers

**When to use:**
- HashSet: 90% of use cases — deduplication, membership checks, fast lookups
- TreeSet: When you need range queries ("give me all items between X and Y") or guaranteed iteration order

**Real-world example:**
We used HashSet for tracking processed transaction IDs — pure membership check, order didn't matter, millions of lookups per day. TreeSet came in handy for a price monitoring feature — needed to efficiently find all products in a price range ($50-$100) and iterate in sorted order.

**Key insight:** Default to HashSet. TreeSet is a specialty tool — reach for it when you specifically need sorted iteration or range queries, not just because "sorted might be nice."
