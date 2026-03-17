## 90. What is the N+1 problem? How do you solve it?

**The N+1 problem is when you execute 1 query to fetch parent records, then N additional queries to fetch related children — one query per parent.**

It happens when lazy loading is triggered in a loop. Performance goes from O(1) to O(N) database round-trips.

**Why it happens:**
- You fetch a list of entities (1 query)
- You iterate and access a lazy-loaded association (N queries)
- Each access triggers a separate SELECT

**How to solve it:**
- `JOIN FETCH` in JPQL — fetch parent and children in one query
- `@EntityGraph` — declarative fetch plan
- Batch fetching (`@BatchSize`) — fetch children in batches instead of one-by-one

**Trade-off:**
- JOIN FETCH loads everything upfront — more memory, potential cartesian product explosion
- Batch fetching reduces queries but still loads more data than needed
- Eager fetching seems easy but couples you to the fetch strategy

**Real-world example:**
Our order history page loaded 50 orders, then iterated to display order items. Page took 6 seconds — 1 query for orders + 50 queries for items. Fixed with `JOIN FETCH o.items` — dropped to 120ms (83% reduction). But one report joined 4 associations — result set exploded to 50K rows (cartesian product). Switched to `@BatchSize(size=10)` — 6 queries total, 400ms. Different queries need different solutions.

**Rule of thumb:** Profile first with SQL logging. Use JOIN FETCH for single associations. Use `@BatchSize` for multiple associations or large collections.
