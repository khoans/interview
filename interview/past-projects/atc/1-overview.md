# Project Overview

The project is a vehicle marketplace that connects private sellers, car dealerships, and customers. It is one of the largest automotive marketplaces in the United States, operating across multiple brands including Autotrader, Kelley Blue Book (KBB), and Ford Blue Advantage, with around 30 million user visits per month.

The system is very large, and my scope is tied to the customer-facing sites, specifically the Vehicle Search Results Page on both frontend and backend.

On the backend, we use Java with Spring Boot in a microservices architecture. The main search service is an aggregation service that interacts directly with the Apache Solr search engine and satellite services, which use PostgreSQL as the database management system. The system is heavily integrated with AWS — almost all major services are deployed in two regions (us-east-1 and us-west-2) to prevent region outages. Many of our components and services use serverless platforms such as AWS Lambda, AWS SQS, and AWS SNS.

On the frontend, we use a microsite architecture with different technologies, including Next.js with a custom Node.js framework wrapper and WordPress. We leverage Next.js's server-side rendering for better SEO support.

For data storage, we use DynamoDB and PostgreSQL as our database management systems.
