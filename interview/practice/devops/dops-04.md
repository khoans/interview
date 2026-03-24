**4. Branching Strategy for CI/CD**
How does your branching strategy (GitFlow, trunk-based, GitHub Flow) affect your CI/CD pipeline design?

========== THIS SECTION IS THE ANSWER ==========

Your branching strategy directly shapes how your pipeline triggers and what it deploys. GitFlow has long-lived branches needing multiple pipeline configs; trunk-based has one main branch with short-lived feature branches, simpler pipelines but requires feature flags; GitHub Flow is a middle ground — feature branches merge to main via PRs.

We used GitHub Flow. Every PR triggered CI (build + test), merging to main triggered deploy to staging automatically, and production deploy was manual. This kept things simple — one main pipeline, one deployable branch.

The lesson: we tried GitFlow early on with develop/release/hotfix branches, and pipeline maintenance became painful — three separate pipeline configs drifting apart. Switching to GitHub Flow with feature flags reduced our pipeline code by half and made hotfixes much faster.
