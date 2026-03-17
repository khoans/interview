## 98. What is @PreAuthorize and @PostAuthorize?

**@PreAuthorize checks permissions before the method executes. @PostAuthorize checks after execution, with access to the return value.**

They're Spring Security's method-level annotations for fine-grained access control beyond simple roles.

**How they work:**
- `@PreAuthorize("hasRole('ADMIN')")` — block access unless user is admin
- `@PreAuthorize("#userId == authentication.principal.id")` — SpEL, users can only access their own data
- `@PostAuthorize("returnObject.owner == authentication.name")` — check the returned object's owner

**Why they matter:**
- Security at the method level — not just URL patterns
- Business logic in security decisions — "user can only edit their own posts"
- DRY: one annotation vs. if-checks in every method

**Trade-off:**
- SpEL expressions can get complex — hard to read, hard to test
- @PostAuthorize executes the method first — expensive queries run even if access is denied
- Debugging difficulty: security failures throw generic AccessDeniedException, not always clear why

**Real-world example:**
We had a document service with `getDocument(id)` — originally checked ownership inside the method. Duplicated in 12 services. Switched to `@PostAuthorize("returnObject.ownerId == authentication.principal.departmentId")`. Removed 200 lines of duplicate checks. But we had a performance issue: loading a 10MB document, then denying access. Changed to `@PreAuthorize("hasAccessToDocument(#id)")` with a custom security expression that checks ownership first. Response time dropped 80% for denied requests.

**Rule of thumb:** Use @PreAuthorize for cheap checks (roles, IDs). Use @PostAuthorize only when you need the return value. For complex logic, write custom security expressions.
