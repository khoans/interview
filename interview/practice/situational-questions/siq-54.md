**54.** How did you prevent SQL injection in your application?

========== THIS SECTION IS THE ANSWER ==========

Parameterized queries everywhere, no exceptions. We never built SQL strings by concatenating user input.

On the automotive marketplace, our PostgreSQL interactions went through Spring Data JPA with Hibernate, which uses PreparedStatement under the hood — all parameters are bound, not concatenated. For custom queries, we used @Query with named parameters (e.g., @Query("SELECT v FROM Vehicle v WHERE v.make = :make")) rather than string interpolation. For the Solr search layer, we applied the same principle — user search terms were escaped using Solr's ClientUtils.escapeQueryChars() before being passed to the query builder. We also had input validation at the controller level using @Valid and custom validators to reject obviously malicious input (like strings containing SQL keywords in unexpected fields) before it reached the service layer.

The trade-off: parameterized queries handle 99% of SQL injection prevention, but you also need to watch for second-order injection — where previously stored data gets used unsafely in a dynamic query. We caught one such case in a reporting service where dealer names were used in a native query. The lesson: even with an ORM, audit every native query and every place raw SQL appears.
