**16. Container Security**
What are the security best practices for running containers in production? (non-root user, image scanning, secrets management)

========== THIS SECTION IS THE ANSWER ==========

Run containers as non-root user, use minimal base images, scan images for CVEs, never bake secrets into images, and use read-only file systems where possible.

In our project, we added `USER 1001` in every Dockerfile so containers never ran as root. We used AWS ECR image scanning on push — it flagged critical CVEs before images reached production. Secrets came from AWS Secrets Manager injected as environment variables at runtime, never in the image layers.

The lesson: early on, a developer committed an API key into the Dockerfile's ENV instruction. Even after removing it, the key was still visible in the image layer history (`docker history`). After that, we enforced: secrets only via runtime injection, never in build-time. We also added a pre-commit hook scanning for secret patterns.
