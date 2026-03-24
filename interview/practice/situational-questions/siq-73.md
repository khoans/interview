**73.** How did you handle timeout issues in your application? What timeout values did you set?

========== THIS SECTION IS THE ANSWER ==========

We set timeouts at every integration point — no call was allowed to hang indefinitely. We tuned them based on actual P99 latency data from New Relic.

In our search aggregation service, the Solr search call had a 2-second connection timeout and 5-second read timeout — Solr P99 was around 3 seconds, so 5 seconds gave headroom without blocking threads too long. Downstream REST calls to satellite services (pricing, dealer info) had 500ms connection and 2-second read timeouts. The overall API gateway timeout for the search results page was 8 seconds. We configured HikariCP database connection pool with 30-second connection timeout and 2-second validation timeout for PostgreSQL. For SQS message processing, we set visibility timeout to 5 minutes to prevent duplicate processing of slow messages.

The trade-off: setting timeouts too aggressive causes false failures during normal traffic spikes. We initially set Solr timeout to 3 seconds and got frequent timeouts during peak hours. Bumping to 5 seconds based on P99 data eliminated 95% of those false timeouts without meaningfully impacting user experience.
