**1. What is CI/CD?**
Explain the concepts of Continuous Integration, Continuous Delivery, and Continuous Deployment. What are the differences between them?

========== THIS SECTION IS THE ANSWER ==========

CI is automatically building and testing code every time someone pushes — catching integration issues early. CD has two flavors: Continuous Delivery means code is always in a deployable state but requires manual approval to go to production; Continuous Deployment removes that gate and auto-deploys every passing build.

In my project, we used GitHub Actions for CI — every PR triggered build, lint, unit tests, and SonarCloud scan. For CD, we used Continuous Delivery with manual approval before production because we served 30M users/month and needed a human checkpoint before releases. Staging deployed automatically though.

The trade-off: Continuous Deployment gives faster feedback loops, but requires very mature test coverage and monitoring. We weren't comfortable going fully automated to production because a bad deploy could affect millions of users. Manual gate added ~15 minutes but saved us from shipping broken search results twice.
