**56.** How did you store passwords? What hashing algorithm did you use and why?

========== THIS SECTION IS THE ANSWER ==========

We used BCrypt via Spring Security's BCryptPasswordEncoder with a work factor of 12.

On the automotive marketplace, password hashing was handled by Spring Security's PasswordEncoder interface. We chose BCrypt because it's deliberately slow (adaptive cost factor), making brute-force attacks impractical even if the database is compromised. The work factor of 12 meant each hash took roughly 200-300ms — acceptable for login but expensive for an attacker trying millions of combinations. We also enforced password complexity at the API level (minimum 8 characters, at least one number and one special character) and implemented account lockout after 5 failed attempts, stored as a counter in PostgreSQL with a 15-minute cooldown.

The trade-off: BCrypt is CPU-intensive, which matters at scale. At 30M users/month, login traffic was significant, but since hashing only happens on login (not every API call), the load was manageable. If we needed even stronger protection, we'd have moved to Argon2 (memory-hard, resistant to GPU attacks), but BCrypt was the right balance of security and compatibility with our Spring Security setup.
