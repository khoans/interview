**41. Git Branching Strategies**
Compare GitFlow, GitHub Flow, and trunk-based development. Which would you recommend for a team of 10 developers and why?

========== THIS SECTION IS THE ANSWER ==========

GitFlow has long-lived develop/release/hotfix branches — good for scheduled releases but complex. GitHub Flow is simpler: feature branches off main, PR to merge, main is always deployable. Trunk-based development has everyone committing to main directly (or very short-lived branches) — requires feature flags and strong CI.

For a team of 10, I'd recommend GitHub Flow. In our project, we used it — developers created feature branches, opened PRs with mandatory code review and CI checks, merged to main which auto-deployed to staging. Simple, everyone understood it, and we rarely had merge conflicts because branches lived less than a week.

The trade-off: GitFlow suits teams with formal release cycles (mobile apps, enterprise software). Trunk-based is ideal for mature teams with excellent test coverage and feature flags. GitHub Flow is the sweet spot for most web backend teams — structured enough to catch issues in PR review, simple enough that nobody gets confused about which branch to use.
