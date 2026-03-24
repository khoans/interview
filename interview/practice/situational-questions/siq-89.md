**89.** How did you handle connection pooling? What settings did you tune?

========== THIS SECTION IS THE ANSWER ==========

We used HikariCP (Spring Boot's default) for PostgreSQL connection pooling and tuned it based on production load patterns from New Relic.

Our search satellite services had: maximumPoolSize=20, minimumIdle=5, connectionTimeout=30000ms, idleTimeout=600000ms (10 min), maxLifetime=1800000ms (30 min). The pool size formula we followed was: connections = (core_count * 2) + effective_spindle_count — for our 4-vCPU ECS tasks, that meant about 10 connections per instance. We set it to 20 to handle burst traffic, with 5 minimum idle to reduce cold-start latency. For Redis, we used Lettuce (Spring Boot default) with a connection pool of 8 max active connections. We also tuned the Solr HTTP client connection pool: maxTotalConnections=100, maxConnectionsPerHost=20, with connection keep-alive to reduce TCP handshake overhead. We monitored pool metrics through New Relic — specifically pool wait time and active connection count. When wait time spiked above 100ms, it signaled we needed to either increase pool size or optimize query performance.

Lesson: more connections isn't always better. We once bumped PostgreSQL pool to 50 per instance, and with 12 ECS instances that meant 600 connections to the database, which actually degraded performance due to context switching on the DB server. Right-sizing to 20 per instance performed better.
