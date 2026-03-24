**53.** How did you handle authorization? Role-based, permission-based, or attribute-based?

========== THIS SECTION IS THE ANSWER ==========

We used role-based access control (RBAC) with Spring Security, augmented with resource-level checks where needed.

On the automotive marketplace, we had roles like ANONYMOUS, USER, DEALER, and ADMIN. Spring Security's @PreAuthorize annotations enforced role checks at the controller level — for example, only DEALER role could access inventory management endpoints, and ADMIN could access the internal analytics dashboard. For finer-grained control, we added resource-level checks: a dealer could only modify their own listings, enforced by comparing the JWT's dealer ID against the resource owner in PostgreSQL. This was implemented as a custom Spring Security expression (e.g., @PreAuthorize("@listingAuth.isOwner(#listingId)")). We avoided full attribute-based access control (ABAC) because our permission model was simple enough that RBAC plus ownership checks covered 95% of cases.

The trade-off: RBAC is simple to understand and audit, but it doesn't scale well if you need complex policies like "dealer can edit listings only during business hours in their timezone." For that, you'd need ABAC or a policy engine like OPA. We kept it simple because our authorization rules were straightforward, and simplicity means fewer bugs in security-critical code.
