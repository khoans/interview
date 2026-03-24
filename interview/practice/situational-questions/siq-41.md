**41.** How do you handle technical debt? How do you convince management to allocate time for it?

========== THIS SECTION IS THE ANSWER ==========

I treat technical debt like financial debt — you track it, prioritize it by interest rate, and pay it down incrementally. I don't ask for a "tech debt sprint." Instead, I attach debt items to related feature work.

On the automotive marketplace, we had a monolithic search service tightly coupled to Apache Solr. Every new feature required touching 5+ files and risked breaking existing queries. I created a tech debt backlog in Jira, tagged items with impact scores (how many incidents or slow deployments they caused), and presented data to our PM: "Last quarter, 40% of our bugs originated from this module, and each fix took 3x longer than average." That got us approval to refactor the Solr query builder into a clean abstraction layer as part of a planned search enhancement feature.

The trade-off: you can't fix all debt at once, and some debt is acceptable. The lesson is — never frame it as "we need to clean up code." Frame it as "this will reduce our bug rate by X% and speed up feature delivery by Y days." Management responds to business impact, not code aesthetics.
