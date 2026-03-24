**96.** How did you do load testing? What tools did you use? What did you discover?

========== THIS SECTION IS THE ANSWER ==========

We used Gatling for load testing, running simulations against our staging environment which mirrored production infrastructure.

We had three test scenarios: (1) steady-state — simulated 500 concurrent users doing vehicle searches for 30 minutes, representing normal traffic. (2) Spike — ramped from 100 to 2,000 users in 60 seconds, simulating a marketing campaign launch. (3) Soak — 300 users for 4 hours, checking for memory leaks and connection pool exhaustion. Key discoveries: during the spike test, we found that Solr's query cache hit rate dropped to 15% because the sudden influx of diverse queries overwhelmed the cache. Response time spiked from 450ms to 6 seconds. The fix was increasing Solr's filterCache size and implementing a query warming strategy. During the soak test, we discovered the HikariCP connection pool was leaking connections in a specific error path — a try-with-resources wasn't properly closing the connection when an exception occurred during result set processing. It took 3 hours to manifest, which is why short tests missed it.

Trade-off: realistic load testing requires production-like infrastructure, which costs money. We ran full load tests monthly and before major releases, using spot instances to keep costs down.
