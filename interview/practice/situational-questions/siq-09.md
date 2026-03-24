**9.** How do you estimate tasks? Have you ever underestimated a task badly? What happened?

========== THIS SECTION IS THE ANSWER ==========

I break tasks into subtasks, estimate each one, then add a buffer. For familiar work (new CRUD API, fix a known bug), I'm usually accurate. For unknowns (new integration, unfamiliar system), I multiply my estimate by 1.5-2x because surprises always happen.

Yes, I badly underestimated the monolith-to-microservice decoupling. I estimated 3 sprints (6 weeks) for extracting the inventory service. It took 5 sprints (10 weeks). The code extraction was fast, but I didn't account for: shared database tables that needed splitting, integration tests that broke when we separated the services, and the deployment pipeline needing reconfiguration for the new service.

What I learned: the code is often the easy part. Estimate the migration, the testing, the deployment changes, and the "what we didn't know we didn't know." Now when I estimate large tasks, I explicitly list assumptions and risks in the ticket — so the PM knows what could blow up the timeline.
