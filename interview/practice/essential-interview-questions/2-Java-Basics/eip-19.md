## 19. Can you declare a constructor as final?

No. The compiler won't allow it. And it makes no sense if you think about what final means for methods.

**Why:** final on a method means "subclasses cannot override this." But constructors are never inherited in the first place — a subclass doesn't inherit the parent's constructor, it calls it via super(). Since there's nothing to override, final has no meaning on a constructor.

Same logic applies to static and abstract — you can't use those on constructors either. Static would imply no instance context, but a constructor's entire purpose is creating an instance. Abstract would mean "no implementation, subclass must provide one," but again, constructors aren't inherited, so there's nothing to force a subclass to implement.

**What interviewers are really testing:** Whether you understand that constructors are not regular methods. They have no return type, they're not inherited, and they can't be overridden. They're a special initialization mechanism that participates in the class hierarchy only through explicit super() chaining.

**If you actually want to prevent subclassing:** Make the class itself final, or make the constructor private and use a static factory method. That's the real-world pattern — we use private constructors with factory methods on configuration objects to control instantiation and enforce validation before object creation.

**Bottom line:** No, and the reason reveals how constructors fundamentally differ from methods in Java's object model.
