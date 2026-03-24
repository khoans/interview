**91.** How did you test your application? What types of tests did you write?

========== THIS SECTION IS THE ANSWER ==========

We had a testing pyramid: unit tests at the base, integration tests in the middle, and a small number of end-to-end tests at the top.

Unit tests covered business logic — service classes, validators, data transformers — using JUnit 5 and Mockito. For example, the vehicle search query builder had extensive unit tests for different filter combinations (make, model, price range, mileage, zip code radius). Integration tests used @SpringBootTest with Testcontainers for PostgreSQL and LocalStack for SQS/SNS/DynamoDB. These tested the full request flow from controller through to the database. For Solr, we had an embedded Solr instance in tests with a representative data set. On the frontend (Next.js), we used Jest for component tests and React Testing Library for user interaction tests. E2E tests ran with Cypress against a staging environment for critical user flows — search, filter, lead submission. SonarCloud enforced minimum 80% line coverage on new code in PRs via GitHub Actions. We ran unit and integration tests on every PR; E2E tests ran nightly.

Trade-off: Testcontainers made integration tests realistic but slow (~3 minutes for the full suite). We parallelized test execution in GitHub Actions using matrix strategy to keep CI under 8 minutes total.
