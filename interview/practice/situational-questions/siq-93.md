**93.** How did you handle test data? Real data, fixtures, factories?

========== THIS SECTION IS THE ANSWER ==========

We used a combination of factories and fixtures, never real production data in tests.

For unit tests, we used builder patterns and factory methods — a VehicleTestFactory that created Vehicle objects with sensible defaults, and you could override specific fields for your test case. For integration tests, we had SQL fixtures (INSERT scripts) loaded via @Sql annotation that set up a known baseline in the Testcontainers PostgreSQL — typically 50-100 representative records per table. For Solr tests, we had a JSON fixture file with 1,000 vehicle documents covering edge cases: vehicles with no price, dealers with special characters in names, very old model years. We used @Transactional on integration tests so each test ran in a transaction that rolled back automatically, keeping tests isolated. For DynamoDB test data, we had a TestDataInitializer that inserted items before each test class and cleaned up after. We never used production data — apart from privacy concerns, production data is too unpredictable for repeatable tests.

Lesson: invest in good test factories early. We initially copy-pasted test data across tests, and when the Vehicle entity added a required field, we had to update 200+ test files. After switching to centralized factories, schema changes required updating only one place.
