**49. Backup and Disaster Recovery**
How do you design a backup and disaster recovery strategy? What is RPO (Recovery Point Objective) and RTO (Recovery Time Objective)?

========== THIS SECTION IS THE ANSWER ==========

RPO is how much data you can afford to lose (e.g., "last 1 hour of data"). RTO is how fast you need to recover (e.g., "back online within 30 minutes"). These drive your backup strategy: RPO of 0 requires real-time replication; RPO of 24 hours allows nightly backups.

In our project, our PostgreSQL RDS had automated daily snapshots (RPO: 24 hours for full restore) plus continuous WAL archiving with point-in-time recovery (RPO: ~5 minutes). Cross-region read replica in us-west-2 could be promoted to primary within 10 minutes (RTO: ~15 minutes including DNS failover). We tested the disaster recovery process quarterly by actually failing over to the secondary region.

The lesson: untested backups are not backups. We once discovered during a DR drill that our snapshot restore script had a wrong parameter and would have failed in a real emergency. After that, DR drills became mandatory every quarter with the results documented — restoring the database and verifying data integrity, not just checking that snapshots exist.
