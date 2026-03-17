## 91. What is AOP (Aspect-Oriented Programming)?

**AOP is a programming paradigm that separates cross-cutting concerns — functionality that spans multiple classes — from business logic.**

Instead of duplicating logging, security, or transaction code everywhere, you define it once in an aspect and apply it declaratively.

**How it works:**
- **Aspect** — the cross-cutting concern (logging, security, transactions)
- **Pointcut** — where to apply the aspect (method execution, annotation presence)
- **Advice** — what code to run (before, after, around the method)
- **Weaving** — applying aspects to target objects (runtime in Spring)

**Why it matters:**
- DRY principle — no more copy-paste logging code across 50 services
- Separation of concerns — business logic stays clean, infrastructure code is centralized
- Maintainability — change logging strategy in one place, not 200 methods

**Trade-off:**
- Magic vs. clarity — reading a method doesn't show what aspects apply to it
- Debugging complexity — stack traces include proxy layers, harder to trace execution flow
- Performance overhead — dynamic proxies add 1-3ms per call (negligible for most apps)

**Real-world example:**
We had a payment service with 30 endpoints. Each method had identical code: start transaction, log request, call service, commit/rollback, log response. 40 lines of boilerplate per method. Refactored to AOP: one `@Aspect` with `@Around` advice for logging and transactions. Removed 1,100 lines of duplicate code. But debugging became harder — a NullPointerException showed 12 proxy frames before the real method. We added custom annotations (`@Logged`, `@Timed`) to make aspects explicit. Now onboarding takes 2 days instead of 2 weeks — new devs learn aspects once, not per-method.

**Rule of thumb:** AOP for infrastructure concerns (logging, security, transactions). Avoid for business logic — it should be visible in the code.
