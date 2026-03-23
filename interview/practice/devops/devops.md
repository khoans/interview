# 50 DevOps Interview Questions for Software Engineers

## Questions 1-10: CI/CD Fundamentals

**1. What is CI/CD?**
Explain the concepts of Continuous Integration, Continuous Delivery, and Continuous Deployment. What are the differences between them?

**2. CI/CD Pipeline Stages**
Describe the typical stages in a CI/CD pipeline. What happens in each stage and why is the order important?

**3. Build Artifacts**
What is a build artifact? How should artifacts be managed and versioned in a CI/CD pipeline?

**4. Branching Strategy for CI/CD**
How does your branching strategy (GitFlow, trunk-based, GitHub Flow) affect your CI/CD pipeline design?

**5. Pipeline as Code**
What does "pipeline as code" mean? What are the advantages over GUI-configured pipelines?

**6. Blue-Green Deployment**
Explain the blue-green deployment strategy. What are its advantages and when would you choose it over other strategies?

**7. Canary Deployment**
What is a canary deployment? How does it differ from blue-green? What metrics do you monitor during a canary release?

**8. Rolling Deployment**
Explain rolling deployments. What are the trade-offs compared to blue-green and canary strategies?

**9. Rollback Strategy**
How do you design a rollback strategy for a failed deployment? What are the challenges with database rollbacks?

**10. Feature Flags**
What are feature flags and how do they relate to deployment strategies? How can they decouple deployment from release?

## Questions 11-20: Docker & Containerization

**11. Docker vs Virtual Machines**
What is the fundamental difference between Docker containers and virtual machines? When would you choose one over the other?

**12. Dockerfile Best Practices**
How do you optimize a Dockerfile for production? Explain multi-stage builds, layer caching, and image size reduction.

**13. Docker Networking**
Explain Docker networking modes (bridge, host, overlay). How do containers communicate with each other?

**14. Docker Volumes**
What are Docker volumes? What is the difference between volumes, bind mounts, and tmpfs mounts?

**15. Docker Compose**
When would you use Docker Compose? How does it differ from Kubernetes? What are its limitations in production?

**16. Container Security**
What are the security best practices for running containers in production? (non-root user, image scanning, secrets management)

**17. Container Orchestration**
Why do we need container orchestration? Compare Docker Swarm and Kubernetes at a high level.

**18. Docker Image Registry**
What is a container registry? How do you manage image tagging and versioning? What is the danger of using the `latest` tag?

**19. Health Checks in Docker**
How do you implement health checks in Docker? What is the difference between HEALTHCHECK in Dockerfile and orchestrator-level health checks?

**20. Docker in CI/CD**
How do you integrate Docker into a CI/CD pipeline? Explain the build, test, push, and deploy flow for containerized applications.

## Questions 21-30: Infrastructure & Cloud

**21. Infrastructure as Code (IaC)**
What is Infrastructure as Code? Compare Terraform, CloudFormation, and Pulumi. What are the benefits over manual provisioning?

**22. Immutable Infrastructure**
What is immutable infrastructure? How does it differ from mutable infrastructure? What problems does it solve?

**23. Auto-Scaling**
Explain horizontal vs vertical scaling. How does auto-scaling work in cloud environments? What metrics trigger scaling?

**24. Load Balancing**
Explain the different types of load balancers (L4 vs L7). What algorithms are used for distributing traffic?

**25. DNS and Service Discovery**
How does DNS work in a cloud/microservices environment? What is service discovery and why is it needed?

**26. Cloud Regions and Availability Zones**
What are regions and availability zones? How do you design for high availability across multiple regions?

**27. CDN (Content Delivery Network)**
What is a CDN and how does it work? When should you use a CDN and what types of content benefit most?

**28. Serverless Architecture**
What is serverless computing (e.g., AWS Lambda)? What are the pros and cons compared to traditional server-based deployments?

**29. Environment Parity**
What is dev/staging/production parity? Why is it important and how do you achieve it?

**30. Cost Optimization**
What strategies do you use to optimize cloud infrastructure costs? (right-sizing, reserved instances, spot instances, auto-scaling)

## Questions 31-40: Monitoring, Logging & Reliability

**31. Observability vs Monitoring**
What is the difference between observability and monitoring? What are the three pillars of observability?

**32. Centralized Logging**
Why is centralized logging important? Describe a typical logging stack (e.g., ELK, CloudWatch, Datadog). What should you log and what should you avoid?

**33. Application Metrics**
What metrics should you collect for a web application? Explain the RED method (Rate, Errors, Duration) and USE method (Utilization, Saturation, Errors).

**34. Alerting Strategy**
How do you design an effective alerting strategy? What is alert fatigue and how do you prevent it?

**35. Distributed Tracing**
What is distributed tracing? Why is it important in microservices? How do tools like Jaeger or Zipkin work?

**36. SLA, SLO, and SLI**
Define SLA, SLO, and SLI. Give an example of each for a REST API service.

**37. Incident Management**
Describe your incident management process. What happens when a production alert fires? What is a post-mortem?

**38. Chaos Engineering**
What is chaos engineering? How does it improve system reliability? Give examples of chaos experiments.

**39. Log Levels and Structured Logging**
What are the standard log levels (TRACE, DEBUG, INFO, WARN, ERROR, FATAL)? What is structured logging and why is it preferred?

**40. APM (Application Performance Monitoring)**
What is APM? How do tools like New Relic, Datadog, or Dynatrace help you understand application performance?

## Questions 41-50: Version Control, Security & Culture

**41. Git Branching Strategies**
Compare GitFlow, GitHub Flow, and trunk-based development. Which would you recommend for a team of 10 developers and why?

**42. Git Merge vs Rebase**
What is the difference between merge and rebase? When should you use each? What are the risks of rebasing?

**43. Secrets Management**
How do you manage secrets (API keys, database passwords) in a DevOps pipeline? Compare solutions: environment variables, Vault, AWS Secrets Manager, etc.

**44. SSL/TLS Certificates**
How do SSL/TLS certificates work in a deployment pipeline? How do you automate certificate renewal? What is Let's Encrypt?

**45. Security Scanning in CI/CD**
What types of security scanning can you integrate into a CI/CD pipeline? (SAST, DAST, dependency scanning, container scanning)

**46. Database Migrations in CI/CD**
How do you handle database schema migrations in an automated pipeline? What tools do you use? How do you handle rollbacks?

**47. GitOps**
What is GitOps? How does it differ from traditional CI/CD? What are its advantages for Kubernetes deployments?

**48. DevOps Culture**
What is the DevOps culture? How does it break down silos between development and operations? What is "shift left"?

**49. Backup and Disaster Recovery**
How do you design a backup and disaster recovery strategy? What is RPO (Recovery Point Objective) and RTO (Recovery Time Objective)?

**50. On-Call and Runbooks**
What makes a good on-call rotation? What is a runbook and how does it help during incidents? How do you reduce mean time to recovery (MTTR)?
