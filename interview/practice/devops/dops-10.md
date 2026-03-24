**10. Feature Flags**
What are feature flags and how do they relate to deployment strategies? How can they decouple deployment from release?

========== THIS SECTION IS THE ANSWER ==========

Feature flags are runtime toggles that control whether a feature is visible to users. They decouple deployment (putting code on servers) from release (making features available). You can deploy code with the feature turned off, then enable it gradually — no new deployment needed.

In our project, we used feature flags for the natural language search feature powered by AWS Bedrock. We deployed the code to all servers but only enabled it for internal users first, then 10% of users, then everyone. If the AI responses were bad, we'd just flip the flag — no rollback needed.

The trade-off: feature flags create technical debt if not cleaned up. We had flags that stayed in the codebase for 6+ months and nobody knew if they were still needed. The rule became: every flag gets a cleanup ticket created at the same time, with a 30-day expiry reminder.
