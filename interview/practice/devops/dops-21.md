**21. Infrastructure as Code (IaC)**
What is Infrastructure as Code? Compare Terraform, CloudFormation, and Pulumi. What are the benefits over manual provisioning?

========== THIS SECTION IS THE ANSWER ==========

IaC means defining your infrastructure (servers, databases, networks, load balancers) in code files that are version-controlled, reviewed, and applied automatically. No more clicking through AWS console. Terraform is cloud-agnostic using HCL syntax; CloudFormation is AWS-native with JSON/YAML; Pulumi lets you use real programming languages like TypeScript or Python.

In our project, we used a combination — CloudFormation for core AWS resources (VPC, RDS, ECS clusters) and Terraform for cross-service resources. Infrastructure changes went through the same PR review process as application code.

The trade-off: Terraform has broader cloud support but managing state files is a headache (who stores the `.tfstate`? S3 with DynamoDB locking). CloudFormation is tightly integrated with AWS but verbose and AWS-only. The biggest benefit of IaC over manual: we could spin up a complete staging environment identical to production in 20 minutes instead of 2 days.
