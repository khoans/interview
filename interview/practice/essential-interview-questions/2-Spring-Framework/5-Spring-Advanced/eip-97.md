## 97. What is Spring Security?

**Spring Security is a framework for authentication and authorization in Spring applications.**

It handles who can access what — login, logout, role-based access, method-level security, OAuth2, SSO.

**How it works:**
- Filter chain intercepts every request
- Authenticates user (password, token, certificate)
- Authorizes access based on roles or expressions
- Integrates with LDAP, OAuth2, JWT, databases

**Why it matters:**
- Security is hard to get right — Spring Security is battle-tested
- Centralized — no scattered `if (!user.isAdmin())` checks
- Flexible: URL-based, method-level (`@PreAuthorize`), or custom access decisions

**Trade-off:**
- Powerful but complex — steep learning curve, verbose configuration
- Performance overhead: 5-15ms per request for security filter chain
- Over-engineering risk: using OAuth2 for a simple internal API

**Real-world example:**
Our internal dashboard had hardcoded admin checks in 40 controllers. When we added contractor roles, we spent 3 days finding and updating every check. Migrated to Spring Security with role-based access control. New roles took 10 minutes — just update the config. But our first config was too permissive — contractors could access admin endpoints. Fixed with method-level `@PreAuthorize("hasRole('ADMIN')")`. Zero security incidents in 14 months.

**Rule of thumb:** Start with URL-based security for simple apps. Add method-level security for fine-grained control. Test security configs explicitly — don't assume defaults are safe.
