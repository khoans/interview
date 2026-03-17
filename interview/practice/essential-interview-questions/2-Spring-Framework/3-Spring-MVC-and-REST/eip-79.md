## 79. What is the difference between PUT and PATCH?

**PUT replaces the entire resource. PATCH updates only the fields you send.**

PUT is idempotent — sending the same request twice has the same effect as once. PATCH may not be idempotent depending on implementation.

**When to use each:**
- PUT: Full resource replacement — user profile update where you send all fields
- PATCH: Partial update — changing just the email or phone number without resending everything

**Trade-off:**
- PUT is simpler — server just replaces the record. But clients must send the full object, even for one-field changes
- PATCH is efficient — less data over the wire. But server must handle merge logic, validate partial updates, and deal with concurrent modifications

**Real-world example:**
Our user API originally used PUT for profile updates. Frontend had to fetch the full profile, merge local changes, send everything back. 2KB payloads, 120ms average. Switched to PATCH for partial updates — frontend sends only changed fields. Payloads dropped to 200 bytes, latency dropped to 45ms. But we introduced a bug: two users updating different fields simultaneously — last write wins, data loss. Fixed with optimistic locking (`@Version` field). Now we use PUT for admin bulk updates (replace everything), PATCH for user self-service (partial updates).

**Rule of thumb:** PATCH for user-facing APIs (efficiency matters). PUT for internal/admin APIs (simplicity matters).
