## 81. How do you handle exceptions in Spring Boot?

**I use @ControllerAdvice with @ExceptionHandler for centralized exception handling — one place, consistent responses.**

Instead of try-catch in every controller, you define exception-to-response mappings globally.

**How it works:**
- `@ControllerAdvice` — global interceptor for all controllers
- `@ExceptionHandler` — methods that handle specific exception types
- Return ResponseEntity with appropriate status code and error body
- Can handle Spring exceptions (DataIntegrityViolationException) and custom ones

**Why this approach:**
- Separation of concerns — controllers focus on business logic, not error formatting
- Consistency — every error has the same structure (timestamp, message, status)
- Testable — exception handlers are regular beans, easy to unit test

**Trade-off:**
- Centralized handling can become a god class — 50+ exception mappings in one file
- Loses context — handler doesn't know which controller or endpoint threw the exception
- Over-catching: catching Exception swallows everything, including bugs you should see

**Real-world example:**
Our payment API had try-catch blocks in 40 controllers — duplicate logging, inconsistent error responses. Some returned 500, some 400, some 200 with error messages in the body. Refactored to @ControllerAdvice with 8 exception handlers (PaymentFailedException, ValidationException, ResourceNotFoundException, etc.). Error response time dropped from 200ms to 50ms (no duplicate logging). Support tickets dropped 45% because error messages became consistent. But we had one bug: a NullPointerException in a utility class wasn't caught — no specific handler, defaulted to 500. Added a fallback handler for RuntimeException with alerting. Now we catch real exceptions early, log bugs separately.

**Rule of thumb:** Handle business exceptions explicitly (4xx). Let bugs bubble up as 500 with alerting.
