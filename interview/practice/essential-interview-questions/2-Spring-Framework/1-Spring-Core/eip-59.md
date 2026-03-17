59. What is the difference between @Autowired and @Resource?

**@Autowired is Spring's annotation that injects by type. @Resource is JSR-250 standard that injects by name — it's part of Java, not Spring.**

@Autowired looks for a matching bean type in the context. @Resource looks for a bean with a matching name (field name or specified name), then falls back to type. @Autowired requires Spring; @Resource works with any JSR-250 container.

**Trade-offs:**
- @Autowired: Spring-specific, more features (required=false, @Qualifier), but couples you to Spring
- @Resource: Standard Java, name-based injection (useful for multiple implementations), portable across frameworks
- Ambiguity: @Autowired + multiple beans = error; @Resource + name = picks specific bean
- Team preference: Spring shops use @Autowired; enterprise/portable code uses @Resource

**Real-world example:**
We had a legacy app using @Resource — injected DataSource by name "primaryDataSource". Worked fine. Migrated to Spring Boot, added @Autowired everywhere. Then we needed to switch between MySQL and PostgreSQL datasources. @Autowired couldn't distinguish — same type. Switched back to @Resource(name="mysqlDataSource") for clarity. For services, kept @Autowired — cleaner with @Qualifier.

**Key insight:** Both work, but they signal different things. @Autowired says "I need a bean of this type." @Resource says "I need this specific bean by name." In pure Spring apps, use @Autowired with @Qualifier. In multi-framework or portable code, @Resource is safer.
