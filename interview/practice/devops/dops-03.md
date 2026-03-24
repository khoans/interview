**3. Build Artifacts**
What is a build artifact? How should artifacts be managed and versioned in a CI/CD pipeline?

========== THIS SECTION IS THE ANSWER ==========

A build artifact is the output of your build process — JAR files, Docker images, compiled bundles. It's the immutable, versioned package that gets deployed to every environment.

In our project, we built Docker images tagged with the git commit SHA (e.g., `search-service:abc1234`) and pushed them to AWS ECR. The same image deployed to staging and production — no rebuilding per environment. Configuration differences were handled by environment variables, not different builds.

The trade-off: tagging with commit SHA gives traceability but creates many images. We set up ECR lifecycle policies to auto-delete images older than 30 days. The key principle is: build once, deploy many — if you rebuild for production, you're not deploying what you tested.
