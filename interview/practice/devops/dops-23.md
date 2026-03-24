**23. Auto-Scaling**
Explain horizontal vs vertical scaling. How does auto-scaling work in cloud environments? What metrics trigger scaling?

========== THIS SECTION IS THE ANSWER ==========

Vertical scaling means bigger machine (more CPU/RAM). Horizontal scaling means more machines. Auto-scaling automatically adds or removes instances based on metrics like CPU usage, memory, request count, or custom metrics like queue depth.

In our project, our search service on ECS auto-scaled horizontally based on CPU utilization — scale out at 70% CPU, scale in at 30%. We ran minimum 4 tasks across 2 AZs and scaled up to 12 during peak hours (evenings and weekends when car shoppers were most active). We also had scheduled scaling for known traffic patterns like holiday sales.

The trade-off: auto-scaling on CPU is simple but sometimes misleading — a memory leak can keep CPU low while the service is dying. We added a custom CloudWatch metric for request latency (p99) as a secondary scaling trigger. Also, scale-in needs a cooldown period — we set 5 minutes to avoid flapping between scaling up and down.
