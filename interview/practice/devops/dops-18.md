**18. Docker Image Registry**
What is a container registry? How do you manage image tagging and versioning? What is the danger of using the `latest` tag?

========== THIS SECTION IS THE ANSWER ==========

A container registry stores and distributes Docker images — like a package repository for containers. Examples: Docker Hub, AWS ECR, GitHub Container Registry. You tag images to identify versions.

In our project, we tagged images with the git commit SHA (`search-service:a1b2c3d`) and also the branch name for non-production (`search-service:feature-xyz`). Production always deployed a specific SHA, never `latest`. We used ECR with lifecycle policies to auto-delete untagged images after 14 days.

The danger of `latest`: it's mutable — two deploys using `latest` might get different images. If you rollback to `latest`, you're not rolling back to anything specific. We had an incident where staging and production both pulled `latest` but got different versions because someone pushed in between. After that, `latest` was banned from all deployment configs.
