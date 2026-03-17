## 40. What are the new Date/Time API classes in Java 8?

**Java 8 introduced immutable, thread-safe date/time classes: LocalDate, LocalTime, LocalDateTime, ZonedDateTime, and Instant.**

Replaced the legacy Date and Calendar classes which were mutable and notoriously buggy.

**Key classes:**
- `LocalDate`, `LocalTime`, `LocalDateTime` — date/time without timezone
- `ZonedDateTime` — date/time with timezone
- `Instant` — Unix timestamp, machine time
- `DateTimeFormatter` — immutable formatter (replaced SimpleDateFormat)

**Why they matter:**
- All classes are immutable — thread-safe without synchronization
- Clear separation between machine time (Instant) and human time (LocalDate)
- Fluent API — no more Calendar's confusing zero-based months

**Trade-off:**
- New API is cleaner but requires Java 8+ — legacy systems stuck on Java 7 need Joda-Time or custom wrappers
- Migration cost: every date comparison, formatter, and database mapping needs review

**Real-world example:**
Our scheduling system used `SimpleDateFormat` — not thread-safe. Under high load (500 req/sec), dates corrupted randomly. Fixed with `ThreadLocal` — worked but added 3ms per request. Migrated to Java 8's `DateTimeFormatter` — immutable, thread-safe, zero synchronization. Date bugs dropped from 12/month to zero. Migration took 3 days; should've done it sooner.

**Rule of thumb:** Never use Date/Calendar in new code. LocalDate/LocalDateTime for business dates, Instant for timestamps.
