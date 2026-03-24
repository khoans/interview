**95.** How did you handle flaky tests? Tests that sometimes pass and sometimes fail?

========== THIS SECTION IS THE ANSWER ==========

Flaky tests were treated as high-priority bugs — they eroded team confidence in the CI pipeline. We had a zero-tolerance policy: flaky tests were either fixed immediately or quarantined.

The most common causes in our project: (1) Time-dependent tests — tests that used LocalDateTime.now() instead of a fixed clock, so they failed around midnight or daylight saving transitions. Fix: inject a Clock and use Clock.fixed() in tests. (2) Ordering dependencies — tests that relied on data from a previous test. Fix: ensure each test set up and tore down its own data using @Transactional rollback or @BeforeEach cleanup. (3) Port conflicts — integration tests with embedded servers grabbing the same port. Fix: use random ports (0) and inject the actual port. (4) Async timing issues — tests asserting immediately after an async SQS message. Fix: use Awaitility library with sensible timeouts (await().atMost(5, SECONDS).until(...)). We tracked flaky test occurrences in GitHub Actions — any test that failed then passed on retry was flagged for investigation.

Lesson: the root cause of flaky tests is almost always shared mutable state or non-deterministic timing. Making tests truly independent and using explicit wait mechanisms eliminates 95% of flakiness.
