# Deployment Strategy

## Infrastructure Overview

All major services — the Next.js frontend applications (via bonnet-next), the LSC (Listing Search Composition), and many of the satellite services — are deployed using **AWS Elastic Beanstalk** running on **EC2 instances**.

Each service has a **Dockerfile**. When the CI/CD pipeline is triggered, it:

1. Builds the Docker image from the Dockerfile
2. Pushes the image to **AWS ECR** (Elastic Container Registry)
3. Deploys the image to Elastic Beanstalk, which provisions the EC2 instances

### Autoscaling

Given the scale of the platform (~30 million monthly visits), the high-traffic services are deployed on **EC2 extra-large instances** with aggressive autoscaling:

- **LSC (Listing Search Composition):** autoscales from **20 to 100 instances**
- **find-cars (Next.js + bonnet-next):** autoscales from **20 to 100 instances**
- Other satellite services scale based on their own traffic patterns

All major services are deployed across **two AWS regions** (us-east-1 and us-west-2) for high availability and region failover.

## Three Environments: PR → Staging → Production

What makes this CI/CD strategy unique is that we have **three tiers of environments**, not just the typical staging and production.

### 1. Pull Request Environment

With around **200 developers** working across these services, we invested heavily in developer experience. Every time a developer creates a pull request, the CI/CD pipeline automatically:

1. Builds **only the services/applications that contain changes** in that PR
2. Deploys them to a dedicated PR environment
3. Provisions a **subdomain containing the PR number** (e.g., `pr-1234.dev.autotrader.com`) for easy testing and review

This means reviewers and QA can test the exact changes in isolation without waiting for a shared staging environment to be free.

**Why this works without data conflicts:**

Since our services are primarily **read-heavy (search)** — a separate department handles inventory updates, pricing writes, and other data mutations — there is no risk of conflicting writes across PR environments. All non-prod environments (PR and staging) share the same **standardized non-prod dataset**, which mirrors production data patterns without containing real customer data.

### 2. Staging Environment

After a PR is merged, the changes flow into the staging environment for integration testing. This environment mirrors the production topology — same service mesh, same autoscaling configuration (at a smaller scale), same data sources.

### 3. Production Environment

Production deployments go through Elastic Beanstalk's rolling deployment strategy to ensure zero downtime. Services are deployed across both AWS regions.

## CI/CD Pipeline Summary

```
Developer creates PR
        │
        ▼
┌──────────────────────────┐
│   CI/CD Pipeline         │
│                          │
│  1. Detect changed       │
│     services             │
│  2. Build Docker image   │
│  3. Push to AWS ECR      │
│  4. Deploy to PR env     │
│     (pr-XXXX.dev.*)      │
└────────────┬─────────────┘
             │
        PR merged
             │
             ▼
┌──────────────────────────┐
│   Staging Deployment     │
│                          │
│  1. Build Docker image   │
│  2. Push to AWS ECR      │
│  3. Deploy to staging    │
│  4. Integration tests    │
└────────────┬─────────────┘
             │
        Approved
             │
             ▼
┌──────────────────────────┐
│  Production Deployment   │
│                          │
│  1. Deploy to us-east-1  │
│  2. Deploy to us-west-2  │
│  3. Rolling deployment   │
│     (zero downtime)      │
└──────────────────────────┘
```

## Serverless and Cron Job Deployments

For **serverless services** (AWS Lambda functions like NLP Search, Pricing, SEO) and **scheduled cron jobs** (Solr index ingestion, cache refresh, pricing calculations), we use **Infrastructure as Code (IaC)** rather than Elastic Beanstalk:

- **Terraform** — used for some services to define and provision the Lambda functions, event triggers, IAM roles, and associated infrastructure
- **AWS CDK** — used for other services, providing the same IaC approach but with the advantage of writing infrastructure definitions in a familiar programming language (TypeScript/Java)

The choice between Terraform and AWS CDK varies by team and service — both coexist across the project. The CI/CD pipeline for these services packages the Lambda code, runs `terraform apply` or `cdk deploy`, and provisions the infrastructure alongside the application code in a single pipeline.

These serverless services and cron jobs also follow the same **PR environment practice** — when a developer creates a pull request, the pipeline provisions a dedicated set of Lambda functions and infrastructure for that PR, allowing isolated testing before merging.

## Post-Deployment Monitoring

We use **New Relic** as our APM (Application Performance Monitoring) tool to monitor every release. After each deployment, we can:

- Track **error rates** in real time and compare them against the pre-release baseline
- Identify **which PRs are included in a given release**, allowing us to quickly narrow down which change introduced a regression
- Correlate performance degradations or error spikes to specific code changes, significantly reducing the time to diagnose and resolve issues

This tight feedback loop between releases and observability is critical at our scale — with ~200 developers shipping changes frequently, being able to pinpoint the offending PR in a release makes incident response fast and targeted.

## Key Design Decisions

| Decision | Reasoning |
|---|---|
| **PR environments** | With ~200 developers, shared staging would be a bottleneck. PR environments allow parallel testing of isolated changes. |
| **Build only changed services** | Avoids rebuilding the entire system for every PR, keeping pipeline times fast. |
| **Shared non-prod data** | Since services are read-heavy (search), all non-prod environments safely share the same standardized dataset with no write conflicts. |
| **Elastic Beanstalk + Docker** | Provides managed autoscaling and deployment orchestration without the operational overhead of managing Kubernetes. |
| **Dual-region deployment** | Protects against regional AWS outages for a platform with 30M monthly visits. |
