**9. Rollback Strategy**
How do you design a rollback strategy for a failed deployment? What are the challenges with database rollbacks?

========== THIS SECTION IS THE ANSWER ==========

For application rollback, keep previous artifacts available and make switching fast — either swap load balancer targets (blue-green), redeploy the previous Docker image, or revert the git commit and re-run the pipeline. The key is: never delete the old version until the new one is proven stable.

In our project, we kept the last 5 Docker images in ECR. If a deploy went wrong, we'd trigger a redeploy of the previous image tag — took about 3 minutes. For database rollbacks, we used backward-compatible migrations only: add columns but never remove them in the same release. Drop the old column in a later release after confirming no rollback needed.

The hardest lesson: we once deployed a migration that renamed a column. When we had to rollback the application, the old code couldn't find the old column name. After that, our rule became: every migration must work with both the old and new application version.
