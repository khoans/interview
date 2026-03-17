## 41. What is the difference between checked and unchecked exceptions?

**Checked exceptions must be declared or caught. Unchecked exceptions (RuntimeException) don't require handling.**

Checked exceptions extend `Exception`. Unchecked extend `RuntimeException`.

**Why both exist:**
- Checked: recoverable conditions the caller should handle — file not found, network timeout, database constraint
- Unchecked: programming errors — null pointer, array index out of bounds, illegal argument

**Trade-off:**
- Checked exceptions force error handling but encourage catch-and-ignore anti-pattern
- Unchecked exceptions are cleaner but callers can forget to handle recoverable cases
- Overusing checked exceptions creates API friction — every caller must propagate or catch

**Real-world example:**
Our payment service originally threw `IOException` (checked) for network failures. Every layer propagated it — 40% of methods had `throws IOException` even though they couldn't recover. Refactored to `PaymentException` (unchecked) with clear retry semantics. Callers that could retry did; others let it bubble. Reduced boilerplate by 600 lines, made error handling explicit. We kept checked exceptions only for truly recoverable cases — file uploads with fallback storage.

**Rule of thumb:** Use unchecked for business logic, checked only when the caller can genuinely recover.
