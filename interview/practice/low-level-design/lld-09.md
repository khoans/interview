**9.** HTTP Request Builder — Builder pattern. Request with URL, method, headers, params, body, auth, timeout, retry. Design the classes.



========== THIS SECTION IS THE ANSWER ==========

## HTTP Request Builder — Builder Pattern

### Pattern: Builder (Fluent API)

**Why Builder?** HTTP requests have 1 required field (URL) and 8+ optional fields. Builder provides a type-safe, readable way to construct complex requests without telescoping constructors.

### Text UML

```
┌────────────────────────────┐     ┌────────────────────────┐
│      HttpRequest           │     │   HttpRequestBuilder   │
├────────────────────────────┤     ├────────────────────────┤
│ - url: String              │     │ + url(u): Builder      │
│ - method: HttpMethod       │     │ + method(m): Builder   │
│ - headers: Map             │     │ + header(k,v): Builder │
│ - queryParams: Map         │     │ + queryParam(k,v): B.  │
│ - body: String             │     │ + body(b): Builder     │
│ - auth: AuthConfig         │     │ + bearerToken(t): B.   │
│ - timeout: Duration        │     │ + basicAuth(u,p): B.   │
│ - retryConfig: RetryConfig │     │ + timeout(d): Builder  │
├────────────────────────────┤     │ + retry(max,delay): B. │
│ + execute(): HttpResponse  │     │ + build(): HttpRequest │
└────────────────────────────┘     └────────────────────────┘

┌─────────────────┐  ┌──────────────────┐
│  AuthConfig     │  │  RetryConfig     │
├─────────────────┤  ├──────────────────┤
│ - type: AuthType│  │ - maxRetries: int│
│ - credentials   │  │ - delay: Duration│
└─────────────────┘  │ - backoff: double│
                     └──────────────────┘
```

### Key Design Decisions

1. **Immutable HttpRequest** — Thread-safe, safe to log/cache/retry
2. **Auth abstraction** — Bearer, Basic, API Key handled uniformly via `AuthConfig`
3. **RetryConfig** — Exponential backoff built in; retries only on 5xx or timeout
4. **execute() on request** — Keeps request and execution together; or inject an `HttpClient`

### SOLID Principles Applied

- **SRP**: Builder constructs; HttpRequest holds data; execution is delegated
- **OCP**: Add new auth types (OAuth2, HMAC) without changing builder
- **DIP**: Request doesn't depend on specific HTTP library (OkHttp, Apache HttpClient)

### Java Code

```java
import java.time.Duration;
import java.util.*;

// --- Enums ---
public enum HttpMethod { GET, POST, PUT, DELETE, PATCH }

public enum AuthType { NONE, BASIC, BEARER, API_KEY }

// --- Auth Config ---
public class AuthConfig {
    private final AuthType type;
    private final Map<String, String> credentials;

    private AuthConfig(AuthType type, Map<String, String> credentials) {
        this.type = type;
        this.credentials = credentials;
    }

    public static AuthConfig bearer(String token) {
        return new AuthConfig(AuthType.BEARER, Map.of("token", token));
    }

    public static AuthConfig basic(String username, String password) {
        return new AuthConfig(AuthType.BASIC, Map.of("username", username, "password", password));
    }

    public static AuthConfig apiKey(String headerName, String key) {
        return new AuthConfig(AuthType.API_KEY, Map.of("header", headerName, "key", key));
    }

    public static AuthConfig none() {
        return new AuthConfig(AuthType.NONE, Map.of());
    }

    public AuthType getType() { return type; }
    public String get(String key) { return credentials.get(key); }
}

// --- Retry Config ---
public class RetryConfig {
    private final int maxRetries;
    private final Duration initialDelay;
    private final double backoffMultiplier;

    public RetryConfig(int maxRetries, Duration initialDelay, double backoffMultiplier) {
        this.maxRetries = maxRetries;
        this.initialDelay = initialDelay;
        this.backoffMultiplier = backoffMultiplier;
    }

    public int getMaxRetries() { return maxRetries; }
    public Duration getInitialDelay() { return initialDelay; }
    public double getBackoffMultiplier() { return backoffMultiplier; }
}

// --- Immutable HTTP Request ---
public class HttpRequest {
    private final String url;
    private final HttpMethod method;
    private final Map<String, String> headers;
    private final Map<String, String> queryParams;
    private final String body;
    private final AuthConfig auth;
    private final Duration timeout;
    private final RetryConfig retryConfig;

    private HttpRequest(Builder builder) {
        this.url = builder.url;
        this.method = builder.method;
        this.headers = Collections.unmodifiableMap(builder.headers);
        this.queryParams = Collections.unmodifiableMap(builder.queryParams);
        this.body = builder.body;
        this.auth = builder.auth;
        this.timeout = builder.timeout;
        this.retryConfig = builder.retryConfig;
    }

    // Getters
    public String getUrl() { return url; }
    public HttpMethod getMethod() { return method; }
    public Map<String, String> getHeaders() { return headers; }
    public Map<String, String> getQueryParams() { return queryParams; }
    public String getBody() { return body; }
    public AuthConfig getAuth() { return auth; }
    public Duration getTimeout() { return timeout; }
    public RetryConfig getRetryConfig() { return retryConfig; }

    public String getFullUrl() {
        if (queryParams.isEmpty()) return url;
        StringJoiner params = new StringJoiner("&");
        queryParams.forEach((k, v) -> params.add(k + "=" + v));
        return url + "?" + params;
    }

    public static Builder builder(String url) {
        return new Builder(url);
    }

    // --- Builder ---
    public static class Builder {
        private final String url;
        private HttpMethod method = HttpMethod.GET;
        private final Map<String, String> headers = new LinkedHashMap<>();
        private final Map<String, String> queryParams = new LinkedHashMap<>();
        private String body;
        private AuthConfig auth = AuthConfig.none();
        private Duration timeout = Duration.ofSeconds(30);
        private RetryConfig retryConfig;

        private Builder(String url) {
            this.url = Objects.requireNonNull(url, "URL is required");
        }

        public Builder method(HttpMethod method) {
            this.method = method;
            return this;
        }

        public Builder get() { return method(HttpMethod.GET); }
        public Builder post() { return method(HttpMethod.POST); }
        public Builder put() { return method(HttpMethod.PUT); }
        public Builder delete() { return method(HttpMethod.DELETE); }

        public Builder header(String name, String value) {
            this.headers.put(name, value);
            return this;
        }

        public Builder contentType(String type) {
            return header("Content-Type", type);
        }

        public Builder accept(String type) {
            return header("Accept", type);
        }

        public Builder queryParam(String key, String value) {
            this.queryParams.put(key, value);
            return this;
        }

        public Builder body(String body) {
            this.body = body;
            return this;
        }

        public Builder jsonBody(String json) {
            this.body = json;
            return contentType("application/json");
        }

        public Builder bearerToken(String token) {
            this.auth = AuthConfig.bearer(token);
            return this;
        }

        public Builder basicAuth(String username, String password) {
            this.auth = AuthConfig.basic(username, password);
            return this;
        }

        public Builder apiKey(String headerName, String key) {
            this.auth = AuthConfig.apiKey(headerName, key);
            return this;
        }

        public Builder timeout(Duration timeout) {
            this.timeout = timeout;
            return this;
        }

        public Builder retry(int maxRetries, Duration delay) {
            this.retryConfig = new RetryConfig(maxRetries, delay, 2.0);
            return this;
        }

        public Builder retry(int maxRetries, Duration delay, double backoff) {
            this.retryConfig = new RetryConfig(maxRetries, delay, backoff);
            return this;
        }

        public HttpRequest build() {
            if (body != null && (method == HttpMethod.GET || method == HttpMethod.DELETE)) {
                throw new IllegalStateException("GET/DELETE requests should not have a body");
            }
            return new HttpRequest(this);
        }
    }
}
```

### Usage

```java
// Simple GET
HttpRequest getRequest = HttpRequest.builder("https://api.example.com/users")
    .get()
    .bearerToken("eyJhbGciOiJIUzI1NiJ9...")
    .queryParam("page", "1")
    .queryParam("size", "20")
    .timeout(Duration.ofSeconds(10))
    .build();

// POST with JSON body and retry
HttpRequest postRequest = HttpRequest.builder("https://api.example.com/orders")
    .post()
    .jsonBody("{\"item\":\"laptop\",\"qty\":1}")
    .bearerToken("token123")
    .timeout(Duration.ofSeconds(30))
    .retry(3, Duration.ofSeconds(1))
    .build();

System.out.println(getRequest.getFullUrl());
// https://api.example.com/users?page=1&size=20
```

### Interview Talking Points

- **Mirrors real libraries**: OkHttp's `Request.Builder`, Spring's `WebClient.RequestBodySpec` — same pattern
- **Validation in build()**: Catch misconfigurations early (body on GET, missing URL)
- **RetryConfig**: Exponential backoff is essential for production HTTP clients — prevents thundering herd on downstream failures
- **Immutability**: Built request is thread-safe — can be shared across threads for parallel execution
