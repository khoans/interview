**85.** How did you handle large file processing? Streaming, chunking, async?

========== THIS SECTION IS THE ANSWER ==========

We used streaming and async processing for large file operations — primarily dealer inventory bulk uploads (CSV files with 10,000-50,000 vehicle listings).

Dealers uploaded inventory files through an S3 presigned URL. Once uploaded, S3 triggered an SNS notification, which put a message on SQS. A Lambda function (or ECS task for very large files) processed the CSV using streaming — we used Java's BufferedReader with Spring Batch to read and process in chunks of 500 records. Each chunk was validated, transformed, and written to PostgreSQL in a batch INSERT. For files over 100,000 records, we split the file into partitions and processed them in parallel using multiple SQS messages. The dealer got real-time progress updates via a status endpoint that read from DynamoDB (updated after each chunk). The entire pipeline was async — the upload API returned immediately with a job ID, and the dealer polled for status.

Trade-off: async processing means the dealer doesn't see results immediately. We added WebSocket notifications (via API Gateway WebSocket) so dealers got a push notification when processing completed, instead of having to poll. The complexity was worth it — processing 50,000 listings synchronously would have taken 3+ minutes and timed out the HTTP connection.
