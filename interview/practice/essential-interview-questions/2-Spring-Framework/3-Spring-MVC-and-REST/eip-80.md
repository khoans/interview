## 80. What HTTP status codes do you commonly use?

**I use a focused set of status codes that map to real business scenarios — not every code in the spec.**

200 OK for successful reads and writes. 201 Created for resource creation (with Location header). 204 No Content for successful deletes. 400 Bad Request for validation errors. 401 Unauthorized for missing auth. 403 Forbidden for insufficient permissions. 404 Not Found for missing resources. 409 Conflict for constraint violations. 500 Internal Server Error for unexpected failures.

**Why this subset:**
- Covers 95% of real-world scenarios
- Clear semantics for API consumers
- Easy to document and test

**Trade-off:**
- More specific codes (422 Unprocessable Entity, 423 Locked) are more precise but add complexity
- Some frameworks default to 500 for everything — you must explicitly throw the right exception
- Overusing 400 vs. 422 confuses clients — is it malformed JSON (400) or business validation (422)?

**Real-world example:**
Our e-commerce API originally returned 200 for everything — success or failure. Frontend had to parse error messages to detect failures. 30% of support tickets were "why did my order fail?" Switched to proper status codes: 201 for orders, 409 for out-of-stock, 400 for invalid addresses. Support tickets dropped 60%. But we had a debate: duplicate email on registration — 400 or 409? Chose 409 (Conflict) — resource already exists. Consistency matters more than perfection.

**Rule of thumb:** Start with the core 9 codes. Add specialized ones (422, 429, 503) only when your API genuinely needs them.
