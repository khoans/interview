**94.** How did you achieve good test coverage? What was your coverage target?

========== THIS SECTION IS THE ANSWER ==========

We enforced 80% line coverage on new code through SonarCloud quality gates integrated with GitHub Actions. Overall project coverage was around 75%.

SonarCloud ran on every pull request and blocked merging if coverage on new/changed lines dropped below 80%. We focused coverage on business-critical paths: the search query builder (95% coverage), lead submission flow (90%), and pricing calculation logic (92%). We were pragmatic — we didn't chase 100% on configuration classes, DTOs, or trivial getters/setters. JaCoCo generated the coverage reports, and SonarCloud consumed them. We also tracked coverage trends over time in the SonarCloud dashboard to make sure it didn't drift downward. Code review culture helped too — reviewers would comment on untested business logic in PRs. We had a "test with the PR" rule: any new feature or bug fix required accompanying tests, and the reviewer checked the test quality, not just existence.

Trade-off: coverage targets can lead to gaming — writing trivial tests just to hit the number. We mitigated this through code reviews focused on test quality and by complementing coverage metrics with mutation testing (PIT) on critical modules, which caught tests that executed code but didn't assert meaningful behavior.
