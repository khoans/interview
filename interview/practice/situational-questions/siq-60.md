**60.** How did you implement CSRF protection? When is it needed and when is it not?

========== THIS SECTION IS THE ANSWER ==========

CSRF protection is needed for cookie-based authentication with state-changing requests. For our JWT-based API, we disabled it in Spring Security — and that was the correct choice.

On the automotive marketplace, our APIs were stateless and authenticated via JWT tokens sent in the Authorization header, not cookies. Since CSRF attacks exploit the browser's automatic cookie sending, and our tokens weren't in cookies, CSRF wasn't a vector. We explicitly disabled CSRF in Spring Security config: http.csrf().disable(). However, for our internal admin dashboard that used session-based auth with cookies, we kept CSRF protection enabled — Spring Security's default CsrfFilter generated a token per session, embedded it in forms via a hidden field, and validated it on every POST/PUT/DELETE. The token was also exposed via a cookie (XSRF-TOKEN) for the Angular-based admin frontend to read and send back as a header.

The trade-off: disabling CSRF for JWT APIs is safe only if tokens are never stored in cookies. If you store JWTs in cookies (which some teams do for convenience), you need CSRF protection back. The lesson: understand the attack vector before applying or removing a security control — blindly enabling everything adds complexity without benefit, and blindly disabling creates real vulnerabilities.
