**12. Dockerfile Best Practices**
How do you optimize a Dockerfile for production? Explain multi-stage builds, layer caching, and image size reduction.

========== THIS SECTION IS THE ANSWER ==========

Three key optimizations: multi-stage builds to separate build dependencies from runtime (build in a JDK image, run in JRE), layer ordering to maximize cache hits (copy dependency files first, then source code — so dependency layer is cached when only code changes), and using slim/distroless base images to reduce attack surface and size.

In our project, our Java service Dockerfile went from 800MB to 200MB by switching to multi-stage: first stage used Maven to build the JAR, second stage copied just the JAR into `eclipse-temurin:17-jre-alpine`. We also copied `pom.xml` before `src/` so Maven dependencies were cached unless pom changed.

The lesson: we initially used `openjdk:17` as base — it had shell, package managers, everything a hacker could want. Switching to a minimal base image not only cut size by 4x but also eliminated CVEs from unused OS packages that kept showing up in our security scans.
