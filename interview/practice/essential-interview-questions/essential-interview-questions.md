# 500 Essential Interview Questions (50 per Topic)

## Topic 1: Java Core (50 Questions)

### OOP Concepts (1-10)
1. What are the 4 principles of OOP? Explain each with examples.
2. What is the difference between abstraction and encapsulation?
3. What is the difference between interface and abstract class?
4. When would you use an abstract class vs an interface?
5. What is polymorphism? Give real-world examples.
6. What is the difference between method overloading and method overriding?
7. Can you override a static method? Why or why not?
8. What is the difference between composition and inheritance?
9. What is covariant return type?
10. What is solid principle and give example?

### Java Basics (11-20)
11. Is Java pass-by-value or pass-by-reference? Explain.
12. What is the difference between == and equals()?
13. What is the contract between hashCode() and equals()?
14. Why is String immutable in Java?
15. What is the difference between String, StringBuilder, and StringBuffer?
16. What are wrapper classes? Why are they needed?
17. What is autoboxing and unboxing?
18. What is the difference between final, finally, and finalize?
19. Can you declare a constructor as final?
20. What is a marker interface? Give examples.

### Collections Framework (21-30)
21. What is the difference between List, Set, and Map?
22. What is the difference between ArrayList and LinkedList?
23. What is the difference between HashMap and Hashtable?
24. How does HashMap work internally?
25. What is the difference between HashMap and ConcurrentHashMap?
26. What is the difference between HashSet and TreeSet?
27. What is the difference between Comparable and Comparator?
28. What is the difference between fail-fast and fail-safe iterators?
29. What is the initial capacity of ArrayList and HashMap?
30. What happens when HashMap exceeds its load factor?

### Java 8+ Features (31-40)
31. What are the benefits of Java 8?
32. What is a functional interface? Give examples.
33. What is a lambda expression?
34. What is the difference between intermediate and terminal operations in Streams?
35. What is the difference between map() and flatMap()?
36. What is Optional? Why was it introduced?
37. What is the difference between findFirst() and findAny()?
38. What are default methods in interfaces?
39. What is the method reference syntax (::)?
40. What are the new Date/Time API classes in Java 8?

### Advanced Java (41-50)
41. What is the difference between checked and unchecked exceptions?
42. What is exception propagation?
43. What is try-with-resources?
44. What is the volatile keyword?
45. What is the synchronized keyword?
46. How do we shared data between threads ?
47. What is a thread pool? Why use it?
48. What is the ExecutorService framework?
49. What is the difference between Runnable and Callable?
50. What is Thread Local ?

---

## Topic 2: Spring Framework (50 Questions)

### Spring Core (1-12)
1. What is dependency injection? What are its benefits?
2. What are the types of dependency injection?
3. What is Inversion of Control (IoC)?
4. What is a Spring Bean?
5. What are the different bean scopes in Spring?
6. What is the default bean scope in Spring?
7. What is the difference between @Component and @Bean?
8. What is @Autowired? How does it work?
9. What is the difference between @Autowired and @Resource?
10. What is @Qualifier annotation used for?
11. What is @Primary annotation used for?
12. What is the bean lifecycle in Spring?

### Spring Boot (13-22)
13. What is Spring Boot? How is it different from Spring?
14. What are the advantages of Spring Boot?
15. What is @SpringBootApplication annotation?
16. What is auto-configuration in Spring Boot?
17. How does @EnableAutoConfiguration work?
18. What is application.properties vs application.yml?
19. How do you handle different environments (dev, test, prod) in Spring Boot?
20. What is @Value annotation?
21. What is @ConfigurationProperties?
22. What are Spring Boot starters?

### Spring MVC & REST (23-32)
23. What is the DispatcherServlet?
24. Explain the request flow in Spring MVC.
25. What is @RestController vs @Controller?
26. What is @RequestBody vs @ResponseBody?
27. What is @PathVariable vs @RequestParam?
28. What are the different HTTP methods in REST?
29. What is the difference between PUT and PATCH?
30. What HTTP status codes do you commonly use?
31. How do you handle exceptions in Spring Boot?
32. What is @ControllerAdvice?

### Spring Data JPA (33-40)
33. What is Spring Data JPA?
34. What is the difference between JPA and Hibernate?
35. What is @Entity annotation?
36. What is @Repository annotation?
37. What is JpaRepository?
38. How do you write custom queries in Spring Data JPA?
39. What is @Query annotation?
40. What is the N+1 problem? How do you solve it?

### Spring Advanced (41-50)
41. What is AOP (Aspect-Oriented Programming)?
42. What are cross-cutting concerns?
43. What is @Transactional annotation?
44. What are the propagation levels in @Transactional?
45. What is the isolation level in @Transactional?
46. What happens when a transactional method calls another transactional method?
47. What is Spring Security?
48. What is @PreAuthorize and @PostAuthorize?
49. What is Spring Actuator?
50. How do you implement caching in Spring?

---

## Topic 3: Database/SQL Theory (50 Questions)
*(Excluding DB design and SQL coding - covered in separate files)*

### Indexing (1-12)
1. What is a database index? How does it work?
2. What are the types of indexes in SQL?
3. What is the difference between clustered and non-clustered index?
4. When should you create an index?
5. When should you NOT create an index?
6. Can a table have multiple clustered indexes?
7. How many non-clustered indexes can a table have?
8. What is a composite index?
9. What is index selectivity?
10. What is a covering index?
11. What is index fragmentation? How do you fix it?
12. Why might an index NOT be used by a query?

### Query Optimization (13-22)
13. How do you identify slow queries?
14. What is an execution plan?
15. How do you read an execution plan?
16. What is a full table scan? When is it acceptable?
17. What is the difference between WHERE and HAVING?
18. What is the difference between UNION and UNION ALL?
19. What is the difference between INNER JOIN and LEFT JOIN?
20. What is a correlated subquery?
21. What is query caching?
22. What are query hints?

### Transactions & ACID (23-32)
23. What is a database transaction?
24. What are ACID properties? Explain each.
25. What is the difference between Atomicity and Consistency?
26. What are transaction isolation levels?
27. What is READ UNCOMMITTED isolation level?
28. What is READ COMMITTED isolation level?
29. What is REPEATABLE READ isolation level?
30. What is SERIALIZABLE isolation level?
31. What is a dirty read?
32. What is a phantom read?

### Normalization (33-38)
33. What is normalization?
34. What are the benefits of normalization?
35. What is 1NF (First Normal Form)?
36. What is 2NF (Second Normal Form)?
37. What is 3NF (Third Normal Form)?
38. When would you denormalize?

### Advanced Concepts (39-50)
39. What is the difference between DELETE and TRUNCATE?
40. What is the difference between DROP and TRUNCATE?
41. What is a view? When would you use it?
42. What is a stored procedure?
43. What is a trigger? When would you use it?
44. What is a cursor?
45. What is database partitioning?
46. What is sharding?
47. What is replication?
48. What is the difference between vertical and horizontal scaling?
49. What is connection pooling?
50. What is the difference between optimistic and pessimistic locking?

---

## Topic 4: System Architecture (50 Questions)

### Architecture Basics (1-10)
1. What is monolithic architecture?
2. What is microservices architecture?
3. What are the advantages of microservices?
4. What are the disadvantages of microservices?
5. When would you choose monolith over microservices?
6. What is service-oriented architecture (SOA)?
7. What is event-driven architecture?
8. What is layered architecture?
9. What is hexagonal architecture (ports and adapters)?
10. What is CQRS pattern?

### Microservices Communication (11-20)
11. How do microservices communicate with each other?
12. What is synchronous vs asynchronous communication?
13. What is REST? What are its constraints?
14. What is gRPC? When would you use it?
15. What is GraphQL?
16. What is a message broker?
17. What is the difference between RabbitMQ and Kafka?
18. What is event sourcing?
19. What is saga pattern?
20. What is API Gateway pattern?

### Resilience & Fault Tolerance (21-30)
21. What happens if one microservice is down?
22. What is circuit breaker pattern?
23. What is retry pattern?
24. What is bulkhead pattern?
25. What is fallback mechanism?
26. What is rate limiting?
27. What is service discovery?
28. What is health check endpoint?
29. What is graceful degradation?
30. What is chaos engineering?

### Scalability (31-40)
31. What is horizontal vs vertical scaling?
32. What is load balancing?
33. What are load balancing algorithms?
34. What is caching strategy?
35. What is CDN (Content Delivery Network)?
36. What is database replication?
37. What is read replica?
38. What is database sharding?
39. What is consistent hashing?
40. What is CAP theorem?

### Design Principles (41-50)
41. What is SOLID principles?
42. What is Single Responsibility Principle?
43. What is Open/Closed Principle?
44. What is 12-Factor App methodology?
45. What is domain-driven design (DDD)?
46. What is bounded context?
47. What is eventual consistency?
48. What is strong consistency?
49. What is two-phase commit?
50. What is distributed tracing?

---

## Topic 5: DevOps/Deployment (50 Questions)

### CI/CD (1-12)
1. What is CI/CD?
2. What is continuous integration?
3. What is continuous delivery?
4. What is continuous deployment?
5. What is Jenkins?
6. What is a Jenkins pipeline?
7. What is GitHub Actions?
8. What is GitLab CI?
9. What are the stages in a typical CI/CD pipeline?
10. What is blue-green deployment?
11. What is canary deployment?
12. What is rolling deployment?

### Docker (13-24)
13. What is Docker?
14. What is a Docker image?
15. What is a Docker container?
16. What is the difference between image and container?
17. What is a Dockerfile?
18. How do you optimize a Dockerfile?
19. What is multi-stage build?
20. What is Docker Compose?
21. What is Docker volume?
22. What is Docker network?
23. What is the difference between COPY and ADD in Dockerfile?
24. What is .dockerignore?

### Kubernetes (25-34)
25. What is Kubernetes?
26. What is a Pod?
27. What is a Deployment?
28. What is a Service in Kubernetes?
29. What is ConfigMap?
30. What is Secret?
31. What is Namespace?
32. What is Ingress?
33. What is Horizontal Pod Autoscaler?
34. What is liveness and readiness probe?

### Git & Version Control (35-42)
35. What is Git flow?
36. What is feature branch workflow?
37. What is trunk-based development?
38. What is the difference between git merge and git rebase?
39. What is a pull request?
40. What is code review process?
41. What is semantic versioning?
42. What is git cherry-pick?

### Monitoring & Logging (43-50)
43. What is centralized logging?
44. What is ELK stack?
45. What is Prometheus?
46. What is Grafana?
47. What are metrics vs logs vs traces?
48. What is alerting strategy?
49. What is SLA, SLO, SLI?
50. What is infrastructure as code?

---

## Topic 6: Design Patterns (50 Questions)
*(Concept-focused, implementation covered in low-level-design.md)*

### Creational Patterns (1-12)
1. What is the Singleton pattern? When would you use it?
2. What are the problems with Singleton?
3. How do you make Singleton thread-safe?
4. What is the Factory pattern?
5. What is the difference between Factory and Abstract Factory?
6. What is the Builder pattern? When would you use it?
7. What is the Prototype pattern?
8. What is the difference between deep copy and shallow copy?
9. When would you use Factory vs Builder?
10. What is Object Pool pattern?
11. What is Lazy Initialization?
12. What is Dependency Injection pattern?

### Structural Patterns (13-24)
13. What is the Adapter pattern? Give an example.
14. What is the Decorator pattern? Give an example.
15. What is the difference between Decorator and Inheritance?
16. What is the Facade pattern?
17. What is the Proxy pattern?
18. What are the types of Proxy pattern?
19. What is the Composite pattern?
20. What is the Bridge pattern?
21. What is the Flyweight pattern?
22. What is the difference between Adapter and Facade?
23. What is the difference between Proxy and Decorator?
24. What is the Front Controller pattern?

### Behavioral Patterns (25-40)
25. What is the Strategy pattern? Give an example.
26. What is the Observer pattern? Give an example.
27. What is the difference between Observer and Pub/Sub?
28. What is the Command pattern?
29. What is the State pattern?
30. What is the difference between State and Strategy?
31. What is the Template Method pattern?
32. What is the Iterator pattern?
33. What is the Chain of Responsibility pattern?
34. What is the Mediator pattern?
35. What is the Memento pattern?
36. What is the Visitor pattern?
37. What is the Interpreter pattern?
38. What is the Null Object pattern?
39. What is the Specification pattern?
40. What is the Double Dispatch pattern?

### Pattern Concepts (41-50)
41. What are Gang of Four (GoF) patterns?
42. What is the difference between composition and inheritance in patterns?
43. What is "programming to an interface"?
44. What is "favor composition over inheritance"?
45. What is the Open/Closed Principle in patterns?
46. What is anti-pattern?
47. What is code smell?
48. What is refactoring?
49. What is pattern density?
50. When should you NOT use design patterns?

---

## Topic 7: Testing (50 Questions)

### Testing Basics (1-10)
1. What are the different types of testing?
2. What is the testing pyramid?
3. What is unit testing?
4. What is integration testing?
5. What is end-to-end testing?
6. What is functional testing?
7. What is regression testing?
8. What is smoke testing?
9. What is acceptance testing?
10. What is exploratory testing?

### Unit Testing (11-20)
11. What is JUnit?
12. What is @Test annotation?
13. What is @BeforeEach and @AfterEach?
14. What is @BeforeAll and @AfterAll?
15. What is assert vs verify?
16. What is test-driven development (TDD)?
17. What is the red-green-refactor cycle?
18. What makes a good unit test?
19. What should you NOT unit test?
20. What is code coverage?

### Mocking (21-28)
21. What is mocking?
22. What is Mockito?
23. What is the difference between mock and spy?
24. What is @Mock annotation?
25. What is @InjectMocks annotation?
26. What is when().thenReturn()?
27. What is verify() method?
28. What is argument captor?

### Integration Testing (29-36)
29. What is @SpringBootTest?
30. What is @WebMvcTest?
31. What is @DataJpaTest?
32. What is TestContainers?
33. What is an embedded database?
34. How do you test REST APIs?
35. What is Postman/Newman?
36. What is contract testing?

### Testing Best Practices (37-50)
37. What is AAA pattern (Arrange-Act-Assert)?
38. What is given-when-then pattern?
39. How do you test private methods?
40. How do you test static methods?
41. How do you test exceptions?
42. What is parameterized testing?
43. What is @ValueSource annotation?
44. What is mutation testing?
45. What is flaky test?
46. How do you handle test data?
47. What is test fixture?
48. What is the difference between @Before and @BeforeEach?
49. What is continuous testing?
50. What is shift-left testing?

---

## Topic 8: Security (50 Questions)

### Authentication & Authorization (1-12)
1. What is authentication vs authorization?
2. What is OAuth2?
3. Explain the OAuth2 authorization code flow.
4. What is OAuth2 implicit flow?
5. What is OAuth2 client credentials flow?
6. What is OpenID Connect (OIDC)?
7. What is JWT (JSON Web Token)?
8. What are the parts of JWT?
9. What is the difference between session and token-based authentication?
10. What is SSO (Single Sign-On)?
11. What is SAML?
12. What is LDAP?

### Web Security (13-24)
13. What is HTTPS? How does it work?
14. What is SSL/TLS?
15. What is CORS (Cross-Origin Resource Sharing)?
16. What is CSRF (Cross-Site Request Forgery)?
17. How do you prevent CSRF attacks?
18. What is XSS (Cross-Site Scripting)?
19. What are the types of XSS?
20. How do you prevent XSS attacks?
21. What is SQL Injection?
22. How do you prevent SQL Injection?
23. What is Content Security Policy (CSP)?
24. What is SameSite cookie attribute?

### Spring Security (25-34)
25. What is Spring Security?
26. What is SecurityFilterChain?
27. What is AuthenticationManager?
28. What is UserDetailsService?
29. What is @PreAuthorize?
30. What is @Secured annotation?
31. What is method security?
32. What is BCrypt password encoder?
33. What is CSRF token in Spring Security?
34. What is CORS configuration in Spring Security?

### Security Best Practices (35-50)
35. What is the principle of least privilege?
36. What is defense in depth?
37. What is secure by default?
38. What is input validation?
39. What is output encoding?
40. What is password hashing vs encryption?
41. What is salt in password hashing?
42. What is pepper in password hashing?
43. What is rate limiting?
44. What is account lockout policy?
45. What is two-factor authentication (2FA)?
46. What is security headers?
47. What is security audit?
48. What is penetration testing?
49. What is OWASP Top 10?
50. What is security logging and monitoring?

---

## Topic 9: Apache Kafka (50 Questions)

### Kafka Basics (1-10)
1. What is Apache Kafka?
2. What is Kafka used for?
3. What are the main components of Kafka?
4. What is a Kafka topic?
5. What is a Kafka partition?
6. Why do we need partitions in Kafka?
7. What is a Kafka broker?
8. What is a Kafka cluster?
9. What is ZooKeeper? What is its role in Kafka?
10. What is Kafka Connect?

### Producers & Consumers (11-20)
11. What is a Kafka producer?
12. What is a Kafka consumer?
13. What is a consumer group?
14. How does Kafka ensure message ordering?
15. What is the difference between a topic and a partition?
16. What is acks configuration in Kafka producer?
17. What is the difference between acks=0, acks=1, and acks=all?
18. What is idempotent producer?
19. What is transactional producer?
20. What is consumer offset?

### Kafka Internals (21-30)
21. How does Kafka store messages?
22. What is log compaction in Kafka?
23. What is the retention policy in Kafka?
24. What is a leader partition?
25. What is a follower partition?
26. What is ISR (In-Sync Replicas)?
27. How does Kafka handle broker failures?
28. What is rebalancing in Kafka?
29. When does consumer rebalancing occur?
30. What is the role of group coordinator?

### Kafka Guarantees (31-38)
31. What guarantees does Kafka provide?
32. How does Kafka ensure durability?
33. How does Kafka ensure availability?
34. What is at-most-once delivery?
35. What is at-least-once delivery?
36. What is exactly-once delivery?
37. How do you achieve exactly-once semantics in Kafka?
38. What is message loss in Kafka? How do you prevent it?

### Kafka Advanced (39-50)
39. What is Kafka Streams?
40. What is KSQL?
41. What is Schema Registry?
42. What is Avro? Why use it with Kafka?
43. What is dead letter queue in Kafka?
44. What is Kafka MirrorMaker?
45. What is multi-cluster replication?
46. How do you monitor Kafka?
47. What are important Kafka metrics?
48. How do you tune Kafka performance?
49. What is backpressure in Kafka?
50. How do you handle poison pill messages in Kafka?

---

## Topic 10: RabbitMQ (50 Questions)

### RabbitMQ Basics (1-10)
1. What is RabbitMQ?
2. What is a message broker?
3. What is AMQP protocol?
4. What are the main components of RabbitMQ?
5. What is a producer in RabbitMQ?
6. What is a consumer in RabbitMQ?
7. What is a queue in RabbitMQ?
8. What is an exchange in RabbitMQ?
9. What is a binding in RabbitMQ?
10. What is a virtual host (vhost) in RabbitMQ?

### Exchange Types (11-20)
11. What are the types of exchanges in RabbitMQ?
12. What is a direct exchange?
13. What is a fanout exchange?
14. What is a topic exchange?
15. What is a headers exchange?
16. What is the default exchange in RabbitMQ?
17. What is a routing key?
18. How does topic exchange routing work?
19. What is the difference between * and # in topic routing?
20. When would you use fanout vs direct exchange?

### Message Handling (21-30)
21. What is message acknowledgment in RabbitMQ?
22. What is auto acknowledgment?
23. What is manual acknowledgment?
24. What happens when a message is rejected?
25. What is requeue in RabbitMQ?
26. What is a dead letter exchange?
27. What is TTL (Time-To-Live) for messages?
28. What is message priority in RabbitMQ?
29. What is publisher confirm?
30. What is mandatory flag in RabbitMQ?

### Durability & Reliability (31-38)
31. What is a durable queue?
32. What is a persistent message?
33. What is the difference between durable and persistent?
34. What is quorum queue in RabbitMQ?
35. What is mirrored queue?
36. What is high availability in RabbitMQ?
37. How does RabbitMQ handle broker failures?
38. What is publisher confirms?

### RabbitMQ Advanced (39-50)
39. What is RabbitMQ Federation?
40. What is RabbitMQ Shovel?
41. What is RabbitMQ Streams?
42. What is lazy queue?
43. What is quorum queue vs classic queue?
44. How do you monitor RabbitMQ?
45. What are important RabbitMQ metrics?
46. What is RabbitMQ management plugin?
47. How do you secure RabbitMQ?
48. What is rate limiting in RabbitMQ?
49. What is consumer prefetch?
50. How do you handle poison pill messages in RabbitMQ?

---

## Study Priority Guide

### Week 1-2: Foundation
- Java Core (50 questions)
- Database/SQL Theory (50 questions)

### Week 3-4: Framework
- Spring Framework (50 questions)
- Testing (50 questions)

### Week 5-6: Architecture & Patterns
- System Architecture (50 questions)
- Design Patterns (50 questions)

### Week 7-8: Operations & Security
- DevOps/Deployment (50 questions)
- Security (50 questions)

### Week 9-10: Message Brokers (Optional/Specialized)
- Apache Kafka (50 questions)
- RabbitMQ (50 questions)

---

## Quick Reference

| Topic | Question Count | Priority |
|-------|---------------|----------|
| Java Core | 50 | ⭐⭐⭐ |
| Spring Framework | 50 | ⭐⭐⭐ |
| Database/SQL Theory | 50 | ⭐⭐⭐ |
| System Architecture | 50 | ⭐⭐ |
| DevOps/Deployment | 50 | ⭐⭐ |
| Design Patterns | 50 | ⭐⭐ |
| Testing | 50 | ⭐ |
| Security | 50 | ⭐ |
| **Apache Kafka** | **50** | **⭐⭐ (Specialized)** |
| **RabbitMQ** | **50** | **⭐⭐ (Specialized)** |

**Total: 500 Questions**

---

Good luck with your interview preparation! 🍀
