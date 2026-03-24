**4.** What is the most challenging feature you have built? What made it difficult?

========== THIS SECTION IS THE ANSWER ==========

The most challenging was building the natural language search feature using AWS Bedrock. Users could type things like "red SUV under $30k near me" instead of using filters. The difficulty was threefold: integrating an LLM (Bedrock) into a latency-sensitive search flow, parsing unstructured queries into structured Solr search parameters, and handling the unpredictable nature of AI responses.

The LLM call added 1-2 seconds of latency, which was unacceptable for our p99 target of 500ms. We solved it by making the AI call async — show traditional search results immediately, then enhance results when the AI response came back. We also had to handle cases where the AI misinterpreted queries (e.g., "Mustang" as the animal, not the car).

What I learned: never put an LLM in the critical path of a latency-sensitive request. Use it to enhance, not block. We deployed it behind a feature flag, starting with 5% of users, and monitored search conversion rates before full rollout.
