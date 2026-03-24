**71.** How did you handle errors in your application? Do you have a global error handler?

========== THIS SECTION IS THE ANSWER ==========

Yes, we used a global error handler with Spring's `@RestControllerAdvice`. It caught all exceptions and mapped them to consistent JSON error responses with proper HTTP status codes, error codes, and user-friendly messages.

For example, in our vehicle search aggregation service, we had a hierarchy: `@ExceptionHandler` for `BusinessException` (400s), `ResourceNotFoundException` (404), `ExternalServiceException` (502), and a catch-all for unexpected errors (500). Each response included a correlation ID that matched our Kibana logs, so when support reported an issue, we could trace it end-to-end in seconds. We also had specific handling for downstream failures — if the Solr search cluster returned an error, we wrapped it with context about which query failed, logged it with New Relic custom attributes, and returned a degraded response rather than a raw 500.

The trade-off: a global handler can mask bugs if you're too aggressive with catch-all. We learned to keep specific handlers for known cases and let truly unexpected errors bubble up with full stack traces in logs, while still returning a clean response to the client.
