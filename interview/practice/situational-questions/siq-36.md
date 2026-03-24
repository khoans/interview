**36.** Explain your git branching strategy. How do you handle hotfixes?

========== THIS SECTION IS THE ANSWER ==========

We used a simplified GitFlow approach with three key branches: main (production), develop (integration), and feature branches.

**Normal flow:** Developers created feature branches from develop (e.g., feature/natural-language-search). After completing work, they opened a PR to develop. GitHub Actions ran CI checks, at least one reviewer approved, then it merged. Develop was deployed to staging automatically. When staging was validated, we merged develop to main, which triggered production deployment.

**Hotfix flow:** For critical production issues, we branched directly from main (hotfix/fix-pricing-500-error). The fix was minimal — only the specific bug fix, no new features. After PR review and CI passing, we merged the hotfix into both main (immediate production deployment) and develop (so the fix wasn't lost on the next regular release). This was the only time we bypassed the staging cycle, and even then, we still required PR review and CI green.

**Release cadence:** We deployed to production roughly twice a week. For coordinated releases involving multiple services, we used release branches (release/v2.5) cut from develop, where we did final testing before merging to main.

**Branch naming conventions:** feature/*, bugfix/*, hotfix/*, release/*. This made it easy to set up CI rules — for example, only feature/* and hotfix/* branches triggered the full test suite.

**Lesson:** The hotfix process must be fast but not reckless. We once had a developer push a hotfix that accidentally included unfinished feature code because they branched from develop instead of main. After that, we added a CI check that verified hotfix branches contained only the intended changes by checking the diff size.
