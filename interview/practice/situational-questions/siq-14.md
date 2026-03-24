**14.** If you had to redesign your system from scratch, what would you change? Why?

========== THIS SECTION IS THE ANSWER ==========

Three main changes. First, I would replace the central aggregation service with a BFF (Backend for Frontend) pattern. Our aggregation layer became a god service that every frontend request had to go through, creating a bottleneck. A BFF per client type (web, mobile) would distribute that orchestration load and let each team optimize their own data-fetching logic.

Second, I would adopt an event-driven architecture more aggressively from the start. We used SQS/SNS for some async flows, but a lot of inter-service communication was still synchronous REST calls, which created tight coupling. Something like Kafka with event sourcing for listing updates, pricing changes, and dealer inventory would give us better decoupling and a natural audit trail.

Third, I would push for Elasticsearch over Apache Solr. Solr served us well, but its operational overhead — manual shard management, ZooKeeper coordination — was painful. Elasticsearch (or OpenSearch on AWS) provides better cluster management, easier scaling, and richer analytics capabilities out of the box.

**Lesson:** These aren't things we got "wrong" — they were reasonable decisions at the time given team size and business constraints. The real takeaway is that architecture evolves with scale. What works at 5M users/month doesn't necessarily work at 30M.
