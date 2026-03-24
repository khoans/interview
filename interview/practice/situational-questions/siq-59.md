**59.** How did you handle XSS (Cross-Site Scripting) vulnerabilities?

========== THIS SECTION IS THE ANSWER ==========

Defense in depth: output encoding, Content Security Policy headers, and input sanitization — in that priority order.

On the automotive marketplace, the primary XSS risk was in user-generated content — vehicle descriptions, dealer names, and search queries displayed on the page. On the backend, we used Spring's built-in HTML escaping for any user input rendered in server-side templates. On the Next.js frontend, React's JSX automatically escapes interpolated values, which handles most reflected XSS. For cases where we needed to render HTML (like formatted dealer descriptions), we used a whitelist-based sanitizer (DOMPurify) that stripped everything except basic formatting tags. We also set Content-Security-Policy headers to block inline scripts and restrict script sources to our own domains. Additionally, all cookies were marked HttpOnly and Secure, preventing JavaScript access to session tokens.

The trade-off: strict CSP can break legitimate functionality (e.g., third-party analytics scripts, inline styles from CMS content). We had to maintain a CSP allowlist and test it with report-only mode before enforcing. The lesson: React/JSX handles 90% of XSS, but you still need CSP headers and sanitization for the remaining 10% where raw HTML is involved.
