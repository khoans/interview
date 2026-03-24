**7.** How do you handle code reviews? Have you ever rejected a PR? What was the reason?

========== THIS SECTION IS THE ANSWER ==========

I review PRs daily — I focus on correctness first (does it do what it claims?), then design (is the approach right?), then readability. I don't nitpick formatting — that's what linters are for. I always try to explain *why* something should change, not just *what* to change.

Yes, I've requested changes on PRs multiple times. One significant case: a teammate added a new API endpoint that returned the full user object including email and internal IDs — a data leak risk for a site with 30M users. I flagged it, suggested using a DTO to expose only necessary fields, and linked to our API response guidelines. It was a 2-line fix but could have been a serious security issue.

The approach: be direct but respectful. "This could expose PII — can we use a DTO here?" works better than "This is wrong." I also make sure to call out good code in reviews — not just problems. People are more receptive to feedback when they know you also notice their good work.
