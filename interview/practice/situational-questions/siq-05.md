**5.** Tell me about a time you had to learn a new technology quickly for a project. How did you approach it?

========== THIS SECTION IS THE ANSWER ==========

When we needed to build the natural language search feature, I had to learn AWS Bedrock and prompt engineering in about 2 weeks. I had no prior experience with LLMs in production.

My approach: first, I read the official AWS Bedrock documentation and built a small proof-of-concept in a sandbox environment — just calling the API and seeing how different models responded to car search queries. Then I studied how others integrated LLMs into search systems (read engineering blogs from Algolia and Elasticsearch). Finally, I pair-programmed with a teammate who had some ML experience to design the prompt template and response parsing logic.

The key lesson: don't try to learn everything — focus on what you need to ship the feature. I didn't need to understand transformer architecture; I needed to know how to call the API, craft effective prompts, handle timeouts, and manage costs. Within 2 weeks I had a working prototype, and within a month it was in production behind a feature flag.
