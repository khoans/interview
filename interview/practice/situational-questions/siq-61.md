**61.** How did you handle sensitive user data (PII)? Encryption at rest? In transit?

========== THIS SECTION IS THE ANSWER ==========

Encryption at rest and in transit as baseline, plus data minimization — don't store what you don't need.

On the automotive marketplace, all PII (user emails, phone numbers, dealer contact info, saved searches) was encrypted at rest using AWS KMS with AES-256 in both PostgreSQL (RDS encryption) and DynamoDB (server-side encryption). All data in transit was TLS 1.2+ — enforced at the load balancer level, and we disabled older TLS versions. Within our application, we applied data minimization: we only collected PII necessary for the feature (e.g., email for account creation, phone number only if the user opted into dealer contact). Internally, we masked PII in logs — our logging framework had a custom filter that replaced email addresses and phone numbers with masked versions (e.g., k***@email.com) in Kibana. Access to production databases containing PII required VPN plus IAM role approval — not every developer had direct DB access.

The trade-off: encryption adds key management overhead, and masking logs makes debugging harder. We accepted both because a PII breach for 30M users would be catastrophic. The lesson: treat PII protection as a first-class architectural concern from day one, not a bolt-on after launch.
