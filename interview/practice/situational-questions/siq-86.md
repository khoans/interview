**86.** How did you handle pagination in your APIs? Offset vs cursor-based?

========== THIS SECTION IS THE ANSWER ==========

We used both offset-based and cursor-based pagination depending on the use case.

For our vehicle search results page, we used offset-based pagination (page/size parameters) because users needed to jump to arbitrary pages and Solr natively supports start/rows. Typical page size was 25 results, and we capped maximum offset at 10,000 to prevent deep pagination performance issues — Solr degrades significantly beyond that. For our dealer leads API, which had real-time data and infinite scroll UI, we used cursor-based pagination with a composite cursor of (created_at, lead_id) encoded as a Base64 token. This was more efficient for PostgreSQL because it used a WHERE clause (created_at < ? OR (created_at = ? AND id < ?)) with our composite index instead of OFFSET which scans and discards rows. The cursor approach also handled data consistency better — new leads arriving between pages didn't cause duplicates or missed items.

Trade-off: cursor-based is more performant but doesn't support "jump to page 5." For search results where users expect traditional page numbers, offset was the right UX choice despite being less efficient. For feeds and infinite scroll, cursor was clearly better.
