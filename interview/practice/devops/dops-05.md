**5. Pipeline as Code**
What does "pipeline as code" mean? What are the advantages over GUI-configured pipelines?

========== THIS SECTION IS THE ANSWER ==========

Pipeline as code means defining your CI/CD pipeline in a version-controlled file (like `.github/workflows/deploy.yml` or `Jenkinsfile`) rather than configuring it through a UI. It lives alongside your application code.

In our project, all pipelines were GitHub Actions YAML files checked into the repo. When we needed to add a security scanning step, it was a normal PR — reviewed, tested, merged. We could also see the pipeline history in git blame.

The trade-off: YAML pipelines have a learning curve and debugging is harder than clicking through a GUI. But the benefits are massive — versioning, code review, reproducibility, and no "who changed the Jenkins job" mysteries. We once lost a Jenkins config due to a server crash; after that, pipeline-as-code became non-negotiable.
