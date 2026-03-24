**50. On-Call and Runbooks**
What makes a good on-call rotation? What is a runbook and how does it help during incidents? How do you reduce mean time to recovery (MTTR)?

========== THIS SECTION IS THE ANSWER ==========

A good on-call rotation is fair (shared equally), sustainable (1 week on, 3+ weeks off), compensated, and has clear escalation paths. A runbook is a step-by-step guide for responding to a specific alert — check X, run Y command, if Z then escalate. It removes guesswork at 3 AM when you're half-asleep.

In our project, we had 5 developers rotating weekly. Each critical alert in PagerDuty linked to a runbook in Confluence with: what the alert means, how to verify the issue, mitigation steps, escalation contacts. Our MTTR dropped from 45 minutes to 15 minutes after introducing runbooks because engineers stopped wasting time figuring out what to check first.

The key to reducing MTTR: automate what you can (auto-scaling, auto-restart), document what you can't (runbooks), and practice regularly (game days). The worst on-call experiences come from alerts with no context — you get paged for "high CPU on search-service" but have no idea what to do. Every alert without a runbook is a ticking time bomb.
