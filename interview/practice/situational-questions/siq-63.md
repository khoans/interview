**63.** How did you ensure GDPR/data privacy compliance in your application?

========== THIS SECTION IS THE ANSWER ==========

Data minimization, explicit consent, right to deletion, and audit trails. Privacy was built into the architecture, not bolted on.

On the automotive marketplace, we implemented several GDPR-aligned practices even though our primary audience was US-based — because best practices are universal and we had some international traffic. First, data minimization: we only collected fields essential for each feature, no "nice to have" PII. Second, consent: user registration included explicit opt-in for marketing emails and dealer contact, stored as separate boolean flags in PostgreSQL — not buried in terms of service. Third, right to deletion: we built a "delete my account" API that triggered a cascade — removing user records from PostgreSQL, DynamoDB saved searches, ElastiCache session data, and queuing an async job via SQS to purge data from Solr indexes and analytics pipelines. Fourth, all PII access was audit-logged (who accessed what, when) and logs were retained for compliance review.

The trade-off: true data deletion across a distributed system is complex — you need to track every service that holds user data. We maintained a data inventory document mapping PII to storage locations. The lesson: build deletion and consent into your data model from the start. Retrofitting a "delete user everywhere" feature into 10+ microservices after launch is 10x harder.
