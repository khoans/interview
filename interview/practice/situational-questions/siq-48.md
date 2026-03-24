**48.** Your PM asks for a feature that you think is technically a bad idea. What do you do?

========== THIS SECTION IS THE ANSWER ==========

I present alternatives with data, not just opinions. My job is to inform the decision, not block it.

On the automotive marketplace, our PM wanted to add real-time vehicle price alerts by polling our search API every 30 seconds per user. With 30M monthly users, even 1% adoption would mean hundreds of thousands of polling requests per minute — our Solr cluster couldn't handle that. Instead of just saying "no," I prepared two alternatives: (1) an event-driven approach using SQS where price changes trigger notifications, costing roughly $200/month in AWS, or (2) a batch job that checks once per hour, much simpler but less "real-time." I presented the load projections and cost estimates. The PM chose option 2 because "near real-time" was acceptable for the business case.

The lesson: never just say "that's a bad idea." Quantify why it's bad (load numbers, cost, timeline) and offer alternatives with trade-offs. If the PM still insists after seeing the data, document the risk and execute — sometimes business context trumps technical elegance.
