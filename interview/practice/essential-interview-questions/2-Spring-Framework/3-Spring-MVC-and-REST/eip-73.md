## 73. What is the DispatcherServlet?

**DispatcherServlet is the front controller in Spring MVC — it handles all incoming HTTP requests and routes them to appropriate controllers.**

It's the entry point for every Spring MVC application, configured in web.xml or via Spring Boot auto-configuration.

**How it works:**
- Receives all HTTP requests (single entry point)
- Consults HandlerMapping to find the right controller
- Calls the controller, gets ModelAndView back
- Passes the model to ViewResolver to render the response

**Why it matters:**
- Centralized request handling — one place for cross-cutting concerns (logging, security, locale resolution)
- Decouples controllers from servlet API — easier testing
- Extensible: customize handler mappings, view resolvers, exception handlers

**Trade-off:**
- Single point of failure — misconfiguration breaks all endpoints
- Adds abstraction layer — debugging request flow requires understanding the dispatch chain
- Performance overhead: extra layer of indirection vs. raw servlets (negligible for most apps)

**Real-world example:**
We had a monolith with 200+ endpoints. Each controller handled its own exception logging — inconsistent, 40% of errors went unlogged. Centralized logging in a HandlerInterceptor registered with DispatcherServlet. Now every request is logged uniformly — request ID, timing, response code. Debugging time dropped from 2 hours to 15 minutes. But we added 5ms latency per request due to interceptor chain. For our high-frequency trading service, we bypassed DispatcherServlet with a raw servlet — traded features for 2ms response time.

**Rule of thumb:** DispatcherServlet for 95% of web apps. Raw servlets only when you need every millisecond.
