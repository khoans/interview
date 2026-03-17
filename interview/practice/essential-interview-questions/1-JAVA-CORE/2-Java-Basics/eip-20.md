## 20. What is a marker interface? Give examples.

A marker interface is an empty interface with no methods — it exists purely to tag a class with a capability or characteristic that the JVM or framework checks at runtime using instanceof.

**Classic examples:**

- **Serializable** — tells the JVM "this object can be converted to a byte stream." Without it, ObjectOutputStream throws NotSerializableException. This is the most common one you'll encounter — every DTO, every entity that goes over the wire or into a cache needs it.
- **Cloneable** — signals that Object.clone() is allowed. Without it, clone() throws CloneNotSupportedException. Honestly, this is a poorly designed API — most teams avoid clone() entirely and use copy constructors or builder patterns instead.
- **RandomAccess** — tags List implementations like ArrayList that support fast index-based access. Algorithms in Collections check this to decide between index-based iteration versus iterator-based. It's a performance hint.

**Why not just use annotations?** That's the modern approach — and in most cases, annotations have replaced marker interfaces. Spring's @Component, Jackson's @JsonSerialize, JPA's @Entity all do what marker interfaces used to do. The key difference: marker interfaces define a type, so you can use them as method parameter types to enforce compile-time safety. Annotations can't do that. If you have a method that should only accept serializable objects, Serializable as a parameter type catches mistakes at compile time.

**Real-world trade-off:** On a payment processing system, we used a marker interface called Auditable on domain objects that needed audit logging. The AOP interceptor checked instanceof Auditable to decide whether to log changes. We chose an interface over an annotation because our audit repository method accepted only Auditable types — giving us compile-time guarantees that non-auditable objects couldn't accidentally enter the audit pipeline.

**Bottom line:** Marker interfaces are a pre-annotation pattern. Still useful when you need type-level guarantees. For metadata-only tagging, prefer annotations.
