**98.** How did you ensure backward compatibility when changing APIs?

========== THIS SECTION IS THE ANSWER ==========

We used API versioning, additive-only changes, and a deprecation process to ensure backward compatibility across our microservices.

Our APIs were versioned via URL path (/v1/vehicles/search, /v2/vehicles/search). Within a version, we only made additive changes — new fields in responses, new optional query parameters — never removed or renamed existing fields. When we added the natural language search feature with AWS Bedrock, we created /v2/vehicles/search that accepted a naturalQuery parameter alongside the structured filters, while /v1 continued to work exactly as before. For inter-service communication, we used Spring Cloud Contract to define provider-consumer contracts — the vehicle search service published a contract, and downstream consumers (e.g., the dealer dashboard BFF) verified against it in their CI pipeline. If a change broke a contract, the provider's build failed before merging. We maintained deprecated API versions for at least 2 sprints (4 weeks), with a Deprecated header and sunset date in the response.

Lesson: backward compatibility is harder than it sounds. We once added a required field to a request DTO without realizing an internal service was still sending the old format. Spring Cloud Contract would have caught it, but that service hadn't adopted contract testing yet. After that, we mandated contract tests for all service-to-service APIs.
