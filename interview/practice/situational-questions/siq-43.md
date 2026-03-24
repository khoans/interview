**43.** How do you handle a situation where requirements change in the middle of a sprint?

========== THIS SECTION IS THE ANSWER ==========

I assess impact first, communicate immediately, and negotiate scope — never silently absorb changes and risk missing everything.

On the automotive marketplace, mid-sprint our PM got a request from business to add a "natural language search" feature to the search results page, which was originally scoped for the next quarter. I spent 2 hours estimating: integrating AWS Bedrock for query interpretation, modifying the Solr query builder, and updating the Next.js frontend. I came back with: "This is a 2-week effort. We can start the backend integration this sprint if we defer the SEO metadata task, or we prototype with a hardcoded prompt and iterate next sprint." We agreed to swap out a lower-priority item and deliver a working prototype that sprint.

The trade-off: being flexible doesn't mean saying yes to everything. The key is making the cost visible — "we can do X, but Y moves out" — so the PM makes an informed decision, not the engineer silently working overtime.
