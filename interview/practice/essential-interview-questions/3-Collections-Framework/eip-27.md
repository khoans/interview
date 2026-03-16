27. What is the difference between Comparable and Comparator?

**Comparable defines natural ordering (inside the class); Comparator defines custom ordering (external to the class).**

Comparable uses `compareTo()` — the class knows how to compare itself. Comparator uses `compare()` — you provide the comparison logic externally.

**Trade-offs:**
- Comparable: One natural order per class, modifies the class itself
- Comparator: Multiple orderings possible, no class modification needed
- Comparator is more flexible — swap strategies at runtime

**When to use:**
- Comparable: When there's an obvious natural order — numbers, dates, alphabetical
- Comparator: When you need multiple sort strategies, or can't modify the class

**Real-world example:**
Our `Order` class implements Comparable using orderId — that's the natural order. But for UI display, we needed sorting by date, total amount, and customer name. We created separate Comparators for each — `OrderByDateComparator`, `OrderByAmountComparator`. Same collection, different views without touching the Order class.

**Key insight:** Comparable is "this is how I sort." Comparator is "here's how I want you to sort." Use Comparable for the default, Comparator for everything else.
