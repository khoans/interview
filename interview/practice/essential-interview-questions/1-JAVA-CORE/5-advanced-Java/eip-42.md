42. What is exception propagation?

**Exception propagation is how an uncaught exception travels up the call stack until it's handled or reaches the JVM.**

When a method throws an exception and doesn't catch it, Java automatically passes it to the caller. That caller can handle it, or pass it further up. This continues until someone catches it or the program crashes.

**How it works:**
- Method A calls B calls C
- C throws an exception
- If C doesn't catch it, B receives it
- If B doesn't catch it, A receives it
- If no one catches it, JVM terminates the thread

**Trade-offs:**
- Propagating up: Keeps error handling centralized, but deep stacks make debugging harder
- Catching early: More context available, but risks swallowing important errors
- Best practice: Catch only where you can meaningfully handle or translate the exception

**Real-world example:**
Our order processing had 8 layers deep — controller → service → repository → database. A `SQLException` in the repository propagated through 4 layers before being caught at the service boundary. We translated it to `OrderProcessingException` there — added business context, hid database details from the API layer. Made error responses consistent and prevented leaking SQL stack traces to users.

**Key insight:** Don't catch exceptions just to log and rethrow. Let them propagate to a layer that can actually do something — retry, translate to business error, or return a user-friendly message.
