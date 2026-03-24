**22. Immutable Infrastructure**
What is immutable infrastructure? How does it differ from mutable infrastructure? What problems does it solve?

========== THIS SECTION IS THE ANSWER ==========

Immutable infrastructure means you never modify a running server. Instead of SSH-ing in to patch or update, you build a new image with the changes and replace the old instance entirely. Mutable infrastructure is the opposite — you update servers in place (apt-get upgrade, deploy new code on the same box).

In our project, every deployment created fresh ECS tasks from new Docker images. We never patched running containers. If we needed a JVM flag change, we updated the Dockerfile, built a new image, and deployed it. Old containers were terminated after the new ones passed health checks.

The trade-off: immutable infrastructure eliminates configuration drift (where servers slowly become different from each other due to ad-hoc patches), but deployments take longer since you're replacing instead of updating. For us, the consistency was worth the extra 2-3 minutes. We once had a "works on server A but not server B" bug caused by a manual hotfix someone applied months ago — immutable infrastructure prevents that entirely.
