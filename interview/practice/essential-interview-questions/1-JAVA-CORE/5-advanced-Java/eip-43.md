43. What is try-with-resources?

**Try-with-resources automatically closes resources that implement AutoCloseable — no finally block needed.**

Introduced in Java 7. Any resource opened in the try parenthesis is closed automatically when the block exits, even if an exception occurs.

**Trade-offs:**
- Cleaner code: No boilerplate finally blocks, reduces 10-15 lines to 3-4
- Safer: Resources close in reverse order, even on exception
- Multiple resources: All close properly, suppressed exceptions preserved
- Only works with AutoCloseable — most Java resources implement it (streams, connections, readers)

**Real-world example:**
Our file upload service originally had 200 lines of try-catch-finally for closing FileInputStream, FileOutputStream, and ZipInputStream. Each finally block checked null, caught IOException on close. Refactored to try-with-resources — down to 50 lines. One edge case: we had a bug where close() threw and masked the original exception. Try-with-resources uses addSuppressed() — we could see both the real error and the close failure.

**Key insight:** Always use try-with-resources for I/O, database connections, network sockets. The only exception: when you need the resource to stay open beyond the block — then manage lifecycle explicitly.
