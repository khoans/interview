**38.** Have you ever had to rollback a deployment? What was the process?

========== THIS SECTION IS THE ANSWER ==========

Yes, I mentioned the incident where a DTO change caused 15% of search requests to fail. Here's the exact rollback process we followed:

**Step 1: Detect.** New Relic alert fired — error rate exceeded 5% threshold within 2 minutes of deployment. I confirmed in Kibana logs: UnrecognizedPropertyException in the aggregation service.

**Step 2: Decision.** We had a rule: if the issue affects more than 5% of users and the fix isn't immediately obvious, rollback first, debug later. The on-call engineer (me that week) made the call within 3 minutes.

**Step 3: Rollback execution.** Since our ECS task definitions used Docker images tagged with git commit hashes, I ran: update the ECS service to use the previous commit's image tag. ECS performed a rolling deployment back to the old version. Alternatively, in Elastic Beanstalk for some legacy services, we used the "Deploy previous version" button which redeployed the last known good application version.

**Step 4: Verify.** Watched New Relic for error rate to drop back to baseline. Confirmed in Kibana that the exception stopped appearing. Total recovery time: about 8 minutes from detection to full rollback.

**Step 5: Post-mortem.** We wrote a brief incident report: what happened, timeline, root cause (missing contract test between services), and action items (add @JsonIgnoreProperties, implement contract testing, deploy producer before consumer).

**Lesson:** The ability to rollback quickly is more important than preventing every bad deployment. Keep your rollback process to under 5 minutes. Tag every deployment artifact with its commit hash so you can always go back to exactly what was running before.
