## 35. What is the difference between map() and flatMap()?

**map() transforms one value to another. flatMap() transforms one value to a Stream and flattens the result.**

Use map() when your function returns a single value. Use flatMap() when your function returns a collection or Stream — it merges all the inner streams into one.

**Why both exist:**
- map(Stream<List>) → Stream<List> — still wrapped
- flatMap(Stream<List>) → Stream — flattened, ready to consume

**Trade-off:**
- flatMap adds a layer of abstraction but simplifies downstream operations
- Overusing flatMap on already-flat data adds unnecessary complexity

**Real-world example:**
We had `Stream<Order>` and needed all `LineItem`s across orders. map() gave us `Stream<List<LineItem>>` — couldn't filter individual items. flatMap() gave us `Stream<LineItem>` — single pipeline, single collect. Processing 100K orders dropped from 800ms to 220ms because we avoided nested iteration.

**Rule of thumb:** If your lambda returns a Stream or Collection, use flatMap(). Otherwise, map().
