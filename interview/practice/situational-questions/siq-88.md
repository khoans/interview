**88.** How did you optimize your Docker images for faster deployment?

========== THIS SECTION IS THE ANSWER ==========

We used multi-stage builds and layer caching to keep images small and builds fast.

Our Java services used a two-stage Dockerfile: the first stage used a Maven image to build the JAR (with .dockerignore excluding unnecessary files), and the second stage used eclipse-temurin:17-jre-alpine as the runtime — no JDK, no build tools. This brought image size from ~800MB (full JDK + Maven) down to ~180MB. We also ordered Dockerfile instructions carefully: COPY pom.xml and dependency resolution first, then COPY source code — so dependency layers were cached and only source code changes triggered a rebuild. For our Next.js frontend, we used standalone output mode which only included necessary files, reducing the image from ~1.2GB to ~150MB. In GitHub Actions, we used Docker layer caching with actions/cache and pushed to ECR with --cache-from to speed up CI builds from 8 minutes to about 3 minutes.

Trade-off: Alpine-based images are smaller but can have compatibility issues with native libraries (e.g., glibc vs musl). We hit this once with a PDF generation library. For that specific service, we switched to debian-slim base image — slightly larger but fully compatible.
