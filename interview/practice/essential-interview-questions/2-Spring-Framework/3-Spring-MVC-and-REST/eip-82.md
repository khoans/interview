## 82. What is @ControllerAdvice?

**@ControllerAdvice is a global exception handler and model initializer for all Spring MVC controllers.**

It lets you define cross-cutting concerns — exception handling, data binding, model attributes — in one place instead of duplicating across controllers.

**What it does:**
- `@ExceptionHandler` — catch exceptions from any controller
- `@InitBinder` — customize data binding (date formats, custom editors)
- `@ModelAttribute` — add common model attributes (user context, navigation data)

**Why it matters:**
- DRY principle — no more try-catch in every controller method
- Consistency — all errors formatted the same way
- Separation of concerns — controllers handle business logic, advice handles errors

**Trade-off:**
- Global scope affects all controllers — hard to have different behavior for different API versions
- Can become a god class — 100+ lines of exception mappings, hard to maintain
- Debugging complexity — exception handling is implicit, stack traces don't show the advice

**Real-world example:**
We had 3 microservices, each with 20+ controllers. Every controller had identical try-catch blocks for validation errors — 15 lines duplicated 60 times. Refactored to @ControllerAdvice with 5 exception handlers. Removed 800 lines of boilerplate across services. But one service needed different error formats for v1 and v2 APIs — global advice forced the same format. We split into `@ControllerAdvice("com.api.v1")` and `@ControllerAdvice("com.api.v2")` with package scanning. Now v1 returns XML errors (legacy clients), v2 returns JSON.

**Rule of thumb:** One @ControllerAdvice per service. Split by package if you need different behavior for different API versions.
