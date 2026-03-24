**49.** How do you document your code and APIs? Do you write documentation before or after coding?

========== THIS SECTION IS THE ANSWER ==========

I write API contracts before coding and implementation docs after. The API contract is part of the design; internal docs capture what was actually built.

On the automotive marketplace, for every new endpoint I wrote an OpenAPI spec first — request/response schemas, error codes, example payloads. This went into our Swagger docs automatically via springdoc-openapi. Downstream teams (the Next.js frontend, mobile app) could start integration work in parallel. For complex business logic — like how the search aggregation service merges results from Solr, PostgreSQL, and DynamoDB — I wrote concise README sections explaining the data flow and decision logic, not line-by-line comments. Code comments were reserved for non-obvious "why" explanations, like why we cache certain Solr queries in ElastiCache Redis for 5 minutes but not others.

The trade-off: documentation rots fast. I keep it minimal and close to the code (OpenAPI annotations, README in the service repo). Heavy Confluence pages that nobody updates are worse than no documentation because they actively mislead.
