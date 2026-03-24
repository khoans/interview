**34. Alerting Strategy**
How do you design an effective alerting strategy? What is alert fatigue and how do you prevent it?

========== THIS SECTION IS THE ANSWER ==========

Alert on symptoms (high error rate, slow responses) not causes (high CPU). Use severity levels: critical (pages someone at 3 AM, user-facing impact), warning (needs attention during business hours), info (for dashboards only). Alert fatigue is when too many noisy alerts make the team ignore all of them — including real ones.

In our project, we had 200+ alerts initially and the on-call engineer ignored most of them. We cleaned up ruthlessly: deleted alerts that never led to action, merged duplicates, raised thresholds on flappy alerts. Got it down to ~30 meaningful alerts. Critical alerts went to PagerDuty, warnings went to Slack.

The rule we followed: every alert must have a runbook link and a clear action. If you can't answer "what should I do when this fires?" — delete the alert. We also reviewed alert history monthly and removed any alert that fired more than 10 times without requiring human action — those became auto-remediation candidates.
