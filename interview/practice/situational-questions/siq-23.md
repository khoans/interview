**23.** Explain the N+1 problem. Have you encountered it? How did you solve it?

========== THIS SECTION IS THE ANSWER ==========

The N+1 problem is when your code executes 1 query to fetch a list of N parent entities, then N additional queries to fetch each parent's related data. So for 100 dealers, you get 1 + 100 = 101 database queries instead of 2.

I encountered this in our dealer service. We had a JPA entity Dealer with a @OneToMany relationship to VehicleListing using lazy loading. The API endpoint fetched a list of dealers and then serialized their active listings. Hibernate executed one query to load the dealers, then fired a separate SELECT for each dealer's listings during JSON serialization. With 50 dealers on a page, that was 51 queries — I spotted it in Kibana logs where a single API call generated dozens of SQL statements.

I solved it with two approaches depending on the use case:

1. **JOIN FETCH in JPQL:** For the API that needed both dealers and listings, I wrote `SELECT d FROM Dealer d JOIN FETCH d.listings WHERE d.region = :region`. This loaded everything in a single query with a JOIN.

2. **@BatchSize(size = 50):** For endpoints where I couldn't easily change the query (shared repository methods), I added `@BatchSize(size = 50)` on the listings collection. Hibernate then fetched listings for 50 dealers in a single IN query instead of 50 individual queries.

**Trade-off:** JOIN FETCH can return large result sets with duplicated parent data (Cartesian product issue with multiple collections). For cases with multiple @OneToMany relationships, I used @BatchSize or EntityGraph instead to avoid the Cartesian explosion.
