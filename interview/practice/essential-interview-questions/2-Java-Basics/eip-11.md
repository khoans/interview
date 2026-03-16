## 11. Is Java pass-by-value or pass-by-reference? Explain.

**Java is strictly pass-by-value.**

**Pass-by-value** means the method gets a *copy* of what you pass. Original stays unchanged.

**Pass-by-reference** means the method gets direct access to the original variable. Reassignment affects the original.

**What Java does:**
- Primitives: copies the actual value (`int x = 5` → method gets `5`)
- Objects: copies the *reference* (`User u` → method gets a copy of `u`'s memory address)

**Key distinction:** You can mutate the *object* through the copied reference, but you cannot reassign the *original reference*.

**Trade-off:**
- Pass-by-value: predictable, no surprise reassignments
- Pass-by-reference: more flexible but dangerous — callee can change what your variable points to

**Real-world example:**
We had a `updateOrder(Order order)` method that called `order = new Order()` inside. Caller's order stayed unchanged — wasted 3 days debugging. If Java were pass-by-reference, the reassignment would've worked but introduced hidden coupling. Pass-by-value forces explicit return: `order = updateOrder(order)` — clearer intent, no magic.
