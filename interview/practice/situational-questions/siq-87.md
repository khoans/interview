**87.** How did you handle bulk operations efficiently? (e.g., importing 1 million records)

========== THIS SECTION IS THE ANSWER ==========

We processed bulk operations using chunked batch inserts, parallel processing, and async pipelines. The main use case was dealer inventory feeds — some large dealer groups uploaded 500,000+ vehicle listings daily.

We used Spring Batch with chunk-oriented processing: read 500 records from the CSV stream, validate and transform them in memory, then write them to PostgreSQL using JDBC batch insert (rewriteBatchedInserts=true in the connection URL, which collapses multiple INSERTs into a single multi-values statement). This brought insert throughput from ~200 records/second to ~5,000 records/second. For very large feeds, we partitioned the file by dealer ID and processed partitions in parallel across multiple ECS tasks. We used COPY command for initial full loads when validation wasn't needed. For Solr reindexing (which we did monthly for the full 5M vehicle catalog), we used Solr's bulk update API with batches of 1,000 documents and soft commits every 10,000 to balance indexing speed with search availability.

Lesson: always disable constraints and indexes during bulk loads, then rebuild them after. We cut a full PostgreSQL table reload from 45 minutes to 8 minutes by dropping indexes, bulk loading, and recreating indexes. But only safe for full reloads, not incremental updates.
