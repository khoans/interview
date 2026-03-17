60. What is @Qualifier annotation used for?

**@Qualifier tells Spring which specific bean to inject when multiple beans of the same type exist — it disambiguates by bean name.**

Without @Qualifier, @Autowired injects by type. If two beans match the type, Spring throws NoUniqueBeanDefinitionException. @Qualifier specifies which bean name to use.

**Trade-offs:**
- Explicitness: Clear which implementation you're getting, but adds coupling to bean names
- Refactoring risk: Bean names can change — string-based coupling is fragile
- Alternative: @Primary sets default, @Qualifier overrides for specific cases
- Best practice: Use meaningful bean names, not default lowercase-classname

**Real-world example:**
We had PaymentProcessor interface with StripeProcessor and PayPalProcessor implementations. First @Autowired failed — two beans, same type. Added @Qualifier("stripeProcessor") to the field. Worked, but felt fragile — what if class renamed? Refactored to use @Bean with explicit names ("stripe", "paypal") in config. Now @Qualifier("stripe") is stable, independent of class names.

**Key insight:** @Qualifier solves the "which bean" problem, but it's a string — prone to typos, refactoring issues. Better approach: define bean names explicitly in @Configuration classes, use those names consistently. Or use @Primary for the default, @Qualifier only when you need the non-default.
