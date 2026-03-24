**11.** Describe the architecture of your current/last project. Draw it if possible.

========== THIS SECTION IS THE ANSWER ==========

Our automotive marketplace served 30M users/month using a microservices architecture on AWS, deployed across us-east-1 and us-west-2.

The frontend was a Next.js application served via CloudFront CDN. Behind an Application Load Balancer, we had an aggregation service — the main Spring Boot service that orchestrated calls to downstream services. For search, the aggregation service called Apache Solr clusters. For vehicle details, pricing, and dealer data, it called dedicated microservices each backed by their own data store — PostgreSQL for relational data, DynamoDB for high-throughput key-value lookups like user preferences and saved searches.

Async communication used SQS and SNS. For example, when a dealer updated a listing, an SNS topic fanned out to SQS queues consumed by the search indexer, pricing service, and analytics pipeline. AWS Lambda handled lightweight event-driven tasks like image resizing and notification triggers.

Deployments ran on ECS with Elastic Beanstalk for some legacy services, CI/CD through GitHub Actions. Monitoring was New Relic for APM and Kibana for log aggregation.

**Trade-off:** The aggregation layer was powerful for orchestration but became a bottleneck — every search request funneled through it. If I redesigned it, I'd explore a BFF (Backend for Frontend) pattern to distribute that load more evenly.
