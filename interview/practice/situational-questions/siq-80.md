**80.** How did you handle graceful shutdown of your services?

========== THIS SECTION IS THE ANSWER ==========

We used Spring Boot's built-in graceful shutdown with a 30-second timeout, combined with ECS container stop signals and load balancer draining.

When ECS sent a SIGTERM to our container, Spring Boot stopped accepting new requests but continued processing in-flight ones for up to 30 seconds (server.shutdown=graceful, spring.lifecycle.timeout-per-shutdown-phase=30s). The ECS task had a stopTimeout of 45 seconds, giving the app time to finish before a SIGKILL. On the load balancer side, we configured target group deregistration delay to 30 seconds, so the ALB stopped routing new requests to the draining instance before Spring even received SIGTERM. For SQS consumers, we registered a shutdown hook that stopped polling for new messages and waited for current messages to finish processing before acknowledging them — this prevented messages from being lost mid-processing.

Lesson: graceful shutdown is a chain — load balancer, container orchestrator, application, and message consumers all need to be coordinated. We had an issue early on where ECS killed the container after 10 seconds (default) while Spring was still processing requests. Aligning the timeouts (ALB drain < app shutdown < ECS stop timeout) fixed it.
