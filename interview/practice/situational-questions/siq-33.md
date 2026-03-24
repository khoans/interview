**33.** Have you ever had a failed deployment in production? What happened and how did you recover?

========== THIS SECTION IS THE ANSWER ==========

Yes. We had a deployment where a new version of the aggregation service started returning 500 errors for about 15% of search requests immediately after rolling out to production.

**What happened:** A code change modified the response DTO for a downstream pricing service call. The developer updated the aggregation service's DTO but the pricing service hadn't deployed its corresponding change yet. The Jackson deserializer in the aggregation service threw UnrecognizedPropertyException for a new field in the response that it wasn't expecting, but only for certain vehicle types where the pricing service returned that field.

**Detection:** New Relic alerted us within 2 minutes — error rate jumped from 0.1% to 15%. I checked Kibana logs and found the stack trace immediately.

**Recovery:** We rolled back by redeploying the previous Docker image. Since our ECS task definitions referenced images by git commit hash, we updated the task definition to the previous commit's image tag and triggered a new deployment. Total downtime for affected requests was about 8 minutes — 2 minutes to detect, 3 minutes to decide on rollback, 3 minutes for ECS rolling deployment to replace instances.

**Root cause fix:** We introduced contract testing between services. Before deploying any service that changed an API response format, we ran contract tests to verify all consumers could still deserialize the response. We also added `@JsonIgnoreProperties(ignoreUnknown = true)` as a default configuration for all REST clients to make them resilient to additive changes.

**Lesson:** Deployment order matters in microservices. Always make API changes backward-compatible and deploy the producer before the consumer when adding fields, or the consumer before the producer when removing fields.
