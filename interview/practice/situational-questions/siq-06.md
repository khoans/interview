**6.** Have you ever disagreed with a technical decision made by your team lead? What did you do?

========== THIS SECTION IS THE ANSWER ==========

Yes. Our tech lead wanted to use GraphQL for the new search API. I disagreed because our team had no GraphQL experience, the search results page had a fixed structure (we didn't need flexible queries), and we'd need to rewrite our existing REST clients. Adding GraphQL would increase complexity without clear benefit for our use case.

I didn't just say "I disagree." I prepared a comparison: REST vs GraphQL for our specific scenario — listing maintenance cost, learning curve, migration effort, and where GraphQL actually shines (mobile apps with varied data needs, not our case). I presented it in a tech discussion meeting, not in a 1-on-1 where it might feel confrontational.

The outcome: the tech lead agreed to keep REST for the search API but we adopted GraphQL for a new internal dashboard that had genuinely flexible query needs. The lesson: disagree with data, not opinions. Propose an alternative, not just "no."
