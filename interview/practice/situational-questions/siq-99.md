**99.** How did you handle code quality? Static analysis, linting, code review process?

========== THIS SECTION IS THE ANSWER ==========

We used SonarCloud, code reviews, and automated quality gates in GitHub Actions to maintain code quality across the team.

SonarCloud ran on every PR and checked: test coverage (80% minimum on new code), code smells, bugs, vulnerabilities, and duplicated code (< 3% duplication). The PR couldn't merge if the quality gate failed. For Java, we used Checkstyle and SpotBugs as Gradle plugins — they ran locally and in CI. For the Next.js frontend, ESLint and Prettier enforced consistent style. Code reviews required at least one approval from a senior developer. We had a review checklist: correctness, error handling, security (no SQL injection, proper input validation), test quality, and performance implications. We rotated reviewers weekly to spread knowledge across the team. For architectural decisions (new services, schema changes), we required two reviewers. We also did quarterly tech debt sprints where the team addressed top SonarCloud code smells and refactored areas with low maintainability ratings.

Lesson: automated tools catch syntax and pattern issues, but code reviews catch design problems. SonarCloud won't tell you that a feature is in the wrong service or that a data model will cause scaling issues. Human review is irreplaceable for architectural quality.
