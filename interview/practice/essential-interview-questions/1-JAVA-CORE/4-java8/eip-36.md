## 36. What is Optional? Why was it introduced?

**Optional is a container for nullable values — it forces you to handle absence explicitly.**

Introduced in Java 8 to reduce NullPointerExceptions and make null handling part of the API contract.

**Why it matters:**
- Return type signals "may be absent" — caller can't ignore it
- Provides functional methods (map, flatMap, orElse) instead of if-null checks
- Works seamlessly with Streams

**Trade-off:**
- Optional adds clarity but isn't free — object allocation, slight performance cost
- Not serializable — can't use in DTOs, entities, or collections without wrappers

**Real-world example:**
Our `UserRepository.findById()` returned `null` for missing users. Callers forgot null checks — 3 NPEs per week in production, each requiring hotfixes. Switched to `Optional<User>` — now callers must call `orElseThrow()` or `ifPresent()`. NPEs dropped to zero in 2 months. We avoided Optional in entity fields (JPA doesn't support it) and high-frequency methods (added 15ms latency under load).

**Rule of thumb:** Use Optional for return types, not fields or method parameters.
