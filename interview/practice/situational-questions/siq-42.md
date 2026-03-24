**42.** Tell me about a time you mentored a junior developer. What was your approach?

========== THIS SECTION IS THE ANSWER ==========

My approach is "guided autonomy" — give them real tasks with guardrails, not toy problems.

On the automotive marketplace, a junior developer joined our team and needed to build a new API endpoint for vehicle comparison. Instead of writing it for them, I broke the task into steps: first write the controller with request validation, then the service layer calling our DynamoDB-backed vehicle detail service, then the integration test. I reviewed each step within hours so feedback was tight. When they struggled with DynamoDB's query patterns (partition key vs. sort key), I pair-programmed for 30 minutes rather than explaining abstractly. Within 3 weeks, they were independently handling similar endpoints and contributing to code reviews.

The lesson: resist the urge to just do it yourself because it's faster. The 2x time investment upfront pays back 10x when they become a productive team member. The trade-off is you need to protect your own delivery commitments — I'd block 30 minutes daily for mentoring and keep my own critical path work separate.
