# 100 Situational Interview Questions for Software Engineers

## Part 1: General Situational Questions (1-50)
*Similar style to real interview questions — practical, follow-up heavy, mix of technical and behavioral*

### About You & Your Work

**1.** Tell me about yourself and walk me through your most recent project.

**2.** What was your role in the team? How many people? How did you divide work?

**3.** Describe a typical day in your current/last project. What tools do you use daily?

**4.** What is the most challenging feature you have built? What made it difficult?

**5.** Tell me about a time you had to learn a new technology quickly for a project. How did you approach it?

**6.** Have you ever disagreed with a technical decision made by your team lead? What did you do?

**7.** How do you handle code reviews? Have you ever rejected a PR? What was the reason?

**8.** Tell me about a bug that took you the longest to find. How did you eventually solve it?

**9.** How do you estimate tasks? Have you ever underestimated a task badly? What happened?

**10.** How do you handle context switching between multiple tasks or projects?

### System Design & Architecture

**11.** Describe the architecture of your current/last project. Draw it if possible.

**12.** Why did your team choose microservices (or monolith)? What were the trade-offs?

**13.** How does your system handle 30 million users per month? What are the bottlenecks?

**14.** If you had to redesign your system from scratch, what would you change? Why?

**15.** How do your services communicate with each other? REST, gRPC, message queue?

**16.** What happens if one of your microservices goes down? How does the system handle it?

**17.** How do you handle data consistency across multiple services?

**18.** Have you ever had to break a monolith into microservices? Walk me through the process.

**19.** How do you handle shared data between services? Do you duplicate data or use a shared database?

**20.** What caching strategy do you use? When did caching cause problems instead of solving them?

### Database & Performance

**21.** Tell me about a slow query you found in production. How did you identify and fix it?

**22.** How do you decide which columns to index? Have you ever created an index that didn't help?

**23.** Explain the N+1 problem. Have you encountered it? How did you solve it?

**24.** How do you handle database migrations in production without downtime?

**25.** Your database is growing too large and queries are getting slow. What steps do you take?

**26.** How do you handle transactions across multiple services?

**27.** Have you ever had a deadlock in your database? How did you detect and resolve it?

**28.** When would you choose NoSQL over SQL? Give a real example from your experience.

**29.** How do you test database changes before deploying to production?

**30.** Your application suddenly starts getting connection pool exhaustion errors. How do you troubleshoot?

### Deployment & DevOps

**31.** Walk me through how you deploy code from your local machine to production.

**32.** How does your CI/CD pipeline work? What stages does it have?

**33.** Have you ever had a failed deployment in production? What happened and how did you recover?

**34.** How do you manage different configurations for dev, staging, and production?

**35.** How do you handle database migration scripts in your deployment pipeline?

**36.** Explain your git branching strategy. How do you handle hotfixes?

**37.** How do you do zero-downtime deployments?

**38.** Have you ever had to rollback a deployment? What was the process?

**39.** How do you monitor your application in production? What tools do you use?

**40.** How do you handle logging across multiple services?

### Team & Process

**41.** How do you handle technical debt? How do you convince management to allocate time for it?

**42.** Tell me about a time you mentored a junior developer. What was your approach?

**43.** How do you handle a situation where requirements change in the middle of a sprint?

**44.** You found a critical bug in production at 11 PM. What do you do?

**45.** How do you handle disagreements about code quality standards in your team?

**46.** Tell me about a time you had to work with a difficult team member. How did you handle it?

**47.** How do you keep up with new technologies? What did you learn recently?

**48.** Your PM asks for a feature that you think is technically a bad idea. What do you do?

**49.** How do you document your code and APIs? Do you write documentation before or after coding?

**50.** Tell me about a project that failed or was cancelled. What did you learn from it?

---

## Part 2: "How Did You Secure/Handle X" Questions (51-100)
*Experience-based questions — interviewers want to hear YOUR real stories and decisions*

### Application Security

**51.** How did you secure your application? Walk me through the security layers.

**52.** How did you implement authentication in your project? JWT, session, OAuth2? Why that choice?

**53.** How did you handle authorization? Role-based, permission-based, or attribute-based?

**54.** How did you prevent SQL injection in your application?

**55.** How did you handle CORS in your project? Did you ever have issues with it?

**56.** How did you store passwords? What hashing algorithm did you use and why?

**57.** How did you manage API keys and secrets in your application? Where do you store them?

**58.** How did you protect your APIs from abuse? Rate limiting, throttling?

**59.** How did you handle XSS (Cross-Site Scripting) vulnerabilities?

**60.** How did you implement CSRF protection? When is it needed and when is it not?

### Data Protection & Privacy

**61.** How did you handle sensitive user data (PII)? Encryption at rest? In transit?

**62.** How did you handle data backup and recovery? What was your RPO and RTO?

**63.** How did you ensure GDPR/data privacy compliance in your application?

**64.** How did you handle audit logging? Who changed what and when?

**65.** How did you handle data validation? Where do you validate — frontend, backend, or both?

**66.** How did you prevent data leaks in API responses? (e.g., exposing internal IDs, user emails)

**67.** How did you handle file uploads securely? What validations did you apply?

**68.** How did you handle session management? Session timeout, concurrent sessions, session hijacking?

**69.** How did you handle token refresh? What happens when a JWT expires during a user action?

**70.** How did you encrypt communication between your microservices?

### Error Handling & Resilience

**71.** How did you handle errors in your application? Do you have a global error handler?

**72.** How did you handle third-party API failures? Retry? Circuit breaker? Fallback?

**73.** How did you handle timeout issues in your application? What timeout values did you set?

**74.** How did you handle memory leaks in your Java application? How did you detect them?

**75.** How did you handle high traffic spikes? Did your application ever go down under load?

**76.** How did you handle race conditions in your application? Give a specific example.

**77.** How did you handle distributed transactions? Saga pattern? Two-phase commit?

**78.** How did you handle idempotency in your APIs? Why is it important?

**79.** How did you handle message queue failures? What if a consumer crashes mid-processing?

**80.** How did you handle graceful shutdown of your services?

### Performance & Optimization

**81.** How did you optimize the performance of your application? Give a specific example with numbers.

**82.** How did you handle caching? What did you cache and what invalidation strategy did you use?

**83.** How did you optimize database queries in your project? EXPLAIN plan?

**84.** How did you reduce API response time? What was the before/after?

**85.** How did you handle large file processing? Streaming, chunking, async?

**86.** How did you handle pagination in your APIs? Offset vs cursor-based?

**87.** How did you handle bulk operations efficiently? (e.g., importing 1 million records)

**88.** How did you optimize your Docker images for faster deployment?

**89.** How did you handle connection pooling? What settings did you tune?

**90.** How did you measure and improve your application's Web Vitals / page load time?

### Testing & Quality

**91.** How did you test your application? What types of tests did you write?

**92.** How did you handle integration testing with external services? Did you mock them?

**93.** How did you handle test data? Real data, fixtures, factories?

**94.** How did you achieve good test coverage? What was your coverage target?

**95.** How did you handle flaky tests? Tests that sometimes pass and sometimes fail?

**96.** How did you do load testing? What tools did you use? What did you discover?

**97.** How did you handle testing database migrations? Did you test rollbacks?

**98.** How did you ensure backward compatibility when changing APIs?

**99.** How did you handle code quality? Static analysis, linting, code review process?

**100.** How did you handle production incidents? Walk me through your incident response process from alert to resolution.
