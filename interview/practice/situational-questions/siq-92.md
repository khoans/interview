**92.** How did you handle integration testing with external services? Did you mock them?

========== THIS SECTION IS THE ANSWER ==========

We used a mix of real test instances, embedded servers, and WireMock depending on the service.

For AWS services (SQS, SNS, DynamoDB, S3), we used LocalStack in Testcontainers — it emulated the full AWS API locally, so our integration tests used the real AWS SDK against a local endpoint. For PostgreSQL, we used Testcontainers with the same PostgreSQL version as production (14.x) and ran Flyway migrations on startup so the schema was always in sync. For Solr, we spun up an embedded Solr instance with a test schema and indexed representative data (~1,000 documents). For third-party REST APIs (pricing service, dealer service, vehicle history), we used WireMock to stub responses — we recorded real responses from staging and replayed them. WireMock also let us test error scenarios: 500 responses, timeouts, malformed JSON. We had a contract testing approach for internal microservices using Spring Cloud Contract — the provider published stubs that consumers verified against.

Lesson: avoid mocking everything. Early on, we mocked the database layer in service tests, which gave us false confidence — tests passed but real queries failed due to SQL syntax issues. Switching to Testcontainers caught those bugs at test time instead of staging.
