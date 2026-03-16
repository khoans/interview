## 21. What is the difference between List, Set, and Map?

They model three different data relationships. List is an ordered sequence that allows duplicates. Set is an unordered collection of unique elements. Map is a key-value lookup where keys are unique.

**When to use each:**

- **List** — when order matters or duplicates are valid. Order line items in a shopping cart, audit log entries, API response results. ArrayList is the default choice; LinkedList only if you're doing heavy insertion/removal in the middle, which is rare.

- **Set** — when uniqueness is the constraint. Tracking which user IDs have already been processed in a batch job, de-duplicating event streams, permission sets. HashSet for O(1) lookups, TreeSet when you need sorted order — like maintaining a leaderboard of top scores.

- **Map** — when you need key-based access. Configuration properties, caching query results, counting occurrences. HashMap is the workhorse; LinkedHashMap when insertion order matters — like building a response where field order is contractual; TreeMap when you need sorted keys — like time-series data bucketed by timestamp.

**The relationship people miss:** Set is actually backed by Map internally. A HashSet is literally a HashMap where the values are a dummy object. So choosing between Set and Map is really about whether you need to associate a value with each key.

**Real-world trade-off:** On an order processing service, we initially used a List to track processed order IDs and checked contains() before processing. With 100K orders per batch, that O(n) contains check made each batch take 45 minutes. Switching to a HashSet brought it down to 3 minutes — same logic, right data structure.

**Bottom line:** The choice is driven by your access pattern — ordered access (List), uniqueness checks (Set), or key-based lookup (Map). Pick the right one and your code is simpler and faster.
