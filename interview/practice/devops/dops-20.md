**20. Docker in CI/CD**
How do you integrate Docker into a CI/CD pipeline? Explain the build, test, push, and deploy flow for containerized applications.

========== THIS SECTION IS THE ANSWER ==========

The flow: build the Docker image → run tests inside the container (or against it) → if tests pass, push the image to a registry → deploy that exact image to the target environment.

In our pipeline: GitHub Actions checked out code → built the Docker image with commit SHA tag → ran unit tests inside the build stage (multi-stage Dockerfile) → pushed to ECR → triggered ECS deployment using the new image tag. The same image went to staging first, then production after approval.

The key lesson: always test the actual container, not just the code. We once had tests passing in CI but the app failing in production because the Dockerfile was missing an environment variable. After that, we added a smoke test step that started the actual Docker container and hit the health endpoint before pushing to the registry.
