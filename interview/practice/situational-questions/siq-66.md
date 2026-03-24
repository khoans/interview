**66.** How did you prevent data leaks in API responses? (e.g., exposing internal IDs, user emails)

========== THIS SECTION IS THE ANSWER ==========

Dedicated response DTOs for every endpoint — never expose entity objects directly. Each API returns only the fields the consumer needs.

On the automotive marketplace, we had a strict rule: JPA entities never appeared in controller responses. Every endpoint had a response DTO (e.g., VehicleSearchResponse, DealerPublicProfile) that contained only fields the client needed. For example, the vehicle search API returned make, model, price, and a public listing ID — but never the internal PostgreSQL primary key, the dealer's email, or the internal Solr document ID. We used MapStruct for entity-to-DTO mapping, which also gave us compile-time safety against accidentally exposing new fields when an entity changed. For paginated responses, we also stripped internal metadata like total database row counts that could leak information about data volume to competitors.

The trade-off: maintaining separate DTOs for each endpoint adds boilerplate. But the alternative — using @JsonIgnore on entity fields — is fragile because adding a new field to the entity exposes it by default unless someone remembers to annotate it. The lesson: whitelist what you expose (DTO with explicit fields) rather than blacklist what you hide (@JsonIgnore). Whitelisting fails safe — new fields are hidden by default.
