**39. Log Levels and Structured Logging**
What are the standard log levels (TRACE, DEBUG, INFO, WARN, ERROR, FATAL)? What is structured logging and why is it preferred?

========== THIS SECTION IS THE ANSWER ==========

Log levels from least to most severe: TRACE (extremely detailed), DEBUG (diagnostic info), INFO (normal operations), WARN (something unexpected but not broken), ERROR (something failed), FATAL (application is crashing). Structured logging outputs logs as JSON key-value pairs instead of plain text — making them machine-parseable and searchable.

In our project, we used SLF4J with Logback configured to output JSON. Instead of `log.info("User 123 searched for Honda Civic")`, we wrote `log.info("search_executed", Map.of("userId", 123, "query", "Honda Civic"))`. In Kibana, we could filter by `userId=123` or aggregate searches by query — impossible with plain text logs.

The trade-off: structured logs are more verbose (more bytes) and slightly more effort to write, but the search and aggregation capabilities are worth it. Plain text logs require regex to parse — which breaks the moment someone changes the log format. At scale with millions of log entries per hour, structured logging is non-negotiable.
