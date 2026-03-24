**62.** How did you handle data backup and recovery? What was your RPO and RTO?

========== THIS SECTION IS THE ANSWER ==========

Automated daily snapshots with point-in-time recovery. Our RPO was 5 minutes (max data loss), RTO was under 30 minutes (max downtime).

On the automotive marketplace, PostgreSQL ran on AWS RDS with automated backups enabled — daily snapshots retained for 14 days, plus continuous WAL archiving for point-in-time recovery to any second within the retention window. DynamoDB had point-in-time recovery (PITR) enabled, giving us continuous backups with per-second granularity for the last 35 days. For our Solr search indexes, we didn't back up the indexes themselves — they could be rebuilt from the source-of-truth databases in about 20 minutes via a reindexing job. We tested recovery quarterly: we'd restore an RDS snapshot to a separate instance and verify data integrity. Multi-region deployment (us-east-1 and us-west-2) with read replicas also gave us a hot standby — if one region went down, Route 53 health checks would failover traffic.

The trade-off: lower RPO/RTO means higher cost (more frequent snapshots, cross-region replication bandwidth). For our scale, the cost was justified — even 30 minutes of downtime for a site with 30M monthly visitors translates to significant revenue loss. The lesson: backups are worthless if you never test restores. Our quarterly drills caught issues like missing IAM permissions on the restore role before a real disaster happened.
