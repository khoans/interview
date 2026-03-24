**37. Incident Management**
Describe your incident management process. What happens when a production alert fires? What is a post-mortem?

========== THIS SECTION IS THE ANSWER ==========

When a critical alert fires: acknowledge → assess severity → communicate (Slack channel) → mitigate (rollback, feature flag off, scale up) → root cause → fix → post-mortem. A post-mortem is a blameless review of what happened, why, and what to change to prevent recurrence.

In our project, the flow was: PagerDuty alert → on-call engineer acknowledges within 5 minutes → opens an incident Slack channel → assesses impact (how many users affected?) → mitigates first (usually rollback or disable feature flag) → investigates root cause after traffic is restored. Post-mortem happened within 48 hours with the whole team.

The key principle: mitigate first, investigate later. We once spent 45 minutes debugging a slow query while users were getting timeouts — should have rolled back immediately, then investigated offline. After that incident, our rule became: if you can't identify the root cause in 10 minutes, rollback and debug in staging.
