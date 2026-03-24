**50.** Tell me about a project that failed or was cancelled. What did you learn from it?

========== THIS SECTION IS THE ANSWER ==========

Not every project fails dramatically — some just get deprioritized, and the lesson is still valuable.

On the automotive marketplace, we started building a "smart recommendations" engine that would suggest vehicles based on user browsing history. I spent about 3 weeks designing the data pipeline — capturing click events via SQS, storing user profiles in DynamoDB, and building a recommendation service. We had a working prototype, but then the business shifted priority to the natural language search feature (AWS Bedrock integration) because it had a more direct impact on conversion rates. The recommendation project was shelved indefinitely.

What I learned: validate business commitment before deep technical investment. I should have pushed for a simpler MVP — maybe just "recently viewed" with basic collaborative filtering — that could ship in one sprint and prove value. Instead, I over-engineered the architecture for a feature that hadn't yet proven its business case. Now I always ask: "What's the simplest version we can ship to validate demand?" before designing the full system.
