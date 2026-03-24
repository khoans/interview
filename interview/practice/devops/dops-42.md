**42. Git Merge vs Rebase**
What is the difference between merge and rebase? When should you use each? What are the risks of rebasing?

========== THIS SECTION IS THE ANSWER ==========

Merge creates a merge commit preserving both branch histories. Rebase replays your commits on top of the target branch, creating a linear history. Merge is safer (no history rewriting), rebase is cleaner (straight line of commits).

In our project, the convention was: rebase your feature branch onto main before opening a PR (to get a clean linear history), then use a merge commit when merging the PR into main (so we could see where features were integrated). `git pull --rebase` was the standard to avoid unnecessary merge commits during local development.

The key risk: never rebase commits that have been pushed and shared with others — it rewrites history and creates duplicate commits when teammates pull. We had a developer rebase a shared branch, causing 3 people to spend an hour untangling the mess. After that, the rule was: rebase only your own unpushed commits.
