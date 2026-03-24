**36. SLA, SLO, and SLI**
Define SLA, SLO, and SLI. Give an example of each for a REST API service.

========== THIS SECTION IS THE ANSWER ==========

SLI (Service Level Indicator) is the metric you measure — e.g., "percentage of requests completing under 500ms." SLO (Service Level Objective) is the target — e.g., "99.9% of requests under 500ms." SLA (Service Level Agreement) is the contract with consequences — e.g., "if we drop below 99.5%, the customer gets a credit."

In our project, our search API's SLIs were: availability (successful responses / total requests), latency (p99 response time), and error rate. The SLO was 99.9% availability and p99 under 500ms. We didn't have formal SLAs since it was an internal service, but the product team treated the SLO as a hard commitment.

The lesson: setting SLOs too aggressively (99.99%) means your team can't deploy anything risky without burning the error budget. We learned to set realistic SLOs that balance reliability with development velocity. An error budget of 0.1% per month gave us room for about 43 minutes of downtime — enough for risky deployments without panic.
