# 500 Essential Interview Questions (Continuous Numbering)

## Topic 1: Java Core (Questions 1-50)

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
46. How do we share data between threads?
47. What is a thread pool? Why use it?
48. What is the ExecutorService framework?
49. What is the difference between Runnable and Callable?
50. What is ThreadLocal?

---

## Topic 2: Spring Framework (Questions 51-100)

### Spring Core (51-62)
51. What is dependency injection? What are its benefits?
52. What are the types of dependency injection?
53. What is Inversion of Control (IoC)?
54. What is a Spring Bean?
55. What are the different bean scopes in Spring?
56. What is the default bean scope in Spring?
57. What is the difference between @Component and @Bean?
58. What is @Autowired? How does it work?
59. What is the difference between @Autowired and @Resource?
60. What is @Qualifier annotation used for?
61. What is @Primary annotation used for?
62. What is the bean lifecycle in Spring?

### Spring Boot (63-72)
63. What is Spring Boot? How is it different from Spring?
64. What are the advantages of Spring Boot?
65. What is @SpringBootApplication annotation?
66. What is auto-configuration in Spring Boot?
67. How does @EnableAutoConfiguration work?
68. What is application.properties vs application.yml?
69. How do you handle different environments (dev, test, prod) in Spring Boot?
70. What is @Value annotation?
71. What is @ConfigurationProperties?
72. What are Spring Boot starters?

### Spring MVC & REST (73-82)
73. What is the DispatcherServlet?
74. Explain the request flow in Spring MVC.
75. What is @RestController vs @Controller?
76. What is @RequestBody vs @ResponseBody?
77. What is @PathVariable vs @RequestParam?
78. What are the different HTTP methods in REST?
79. What is the difference between PUT and PATCH?
80. What HTTP status codes do you commonly use?
81. How do you handle exceptions in Spring Boot?
82. What is @ControllerAdvice?

### Spring Data JPA (83-90)
83. What is Spring Data JPA?
84. What is the difference between JPA and Hibernate?
85. What is @Entity annotation?
86. What is @Repository annotation?
87. What is JpaRepository?
88. How do you write custom queries in Spring Data JPA?
89. What is @Query annotation?
90. What is the N+1 problem? How do you solve it?

### Spring Advanced (91-100)
91. What is AOP (Aspect-Oriented Programming)?
92. What are cross-cutting concerns?
93. What is @Transactional annotation?
94. What are the propagation levels in @Transactional?
95. What is the isolation level in @Transactional?
96. What happens when a transactional method calls another transactional method?
97. What is Spring Security?
98. What is @PreAuthorize and @PostAuthorize?
99. What is Spring Actuator?
100. How do you implement caching in Spring?

---

## Topic 3: Database/SQL Theory (Questions 101-150)
*(Excluding DB design and SQL coding - covered in separate files)*

### Indexing (101-112)
101. What is a database index? How does it work?
102. What are the types of indexes in SQL?
103. What is the difference between clustered and non-clustered index?
104. When should you create an index?
105. When should you NOT create an index?
106. Can a table have multiple clustered indexes?
107. How many non-clustered indexes can a table have?
108. What is a composite index?
109. What is index selectivity?
110. Have you encountered scenario where index hasn't increase query speed?
111. What is index fragmentation? How do you fix it?
112. Why might an index NOT be used by a query?

### Query Optimization (113-122)
113. How do you identify slow queries?
114. What is an execution plan?
115. How do you read an execution plan?
116. What is a full table scan? When is it acceptable?
117. What is the difference between WHERE and HAVING?
118. What is the difference between UNION and UNION ALL?
119. What is the difference between INNER JOIN and LEFT JOIN?
120. What is a correlated subquery?
121. What is query caching?
122. What are query hints?

### Transactions & ACID (123-132)
123. What is a database transaction?
124. What are ACID properties? Explain each.
125. What is the difference between Atomicity and Consistency?
126. What are transaction isolation levels?
127. What is READ UNCOMMITTED isolation level?
128. What is READ COMMITTED isolation level?
129. What is REPEATABLE READ isolation level?
130. What is SERIALIZABLE isolation level?
131. What is a dirty read?
132. What is a phantom read?

### Normalization (133-138)
133. What is normalization?
134. What are the benefits of normalization?
135. What is 1NF (First Normal Form)?
136. What is 2NF (Second Normal Form)?
137. What is 3NF (Third Normal Form)?
138. When would you denormalize?

### Advanced Concepts (139-150)
139. What is the difference between DELETE and TRUNCATE?
140. What is the difference between DROP and TRUNCATE?
141. What is a view? When would you use it?
142. What is a stored procedure?
143. What is a trigger? When would you use it?
144. What is a cursor?
145. What is database partitioning?
146. What is sharding?
147. What is replication?
148. What is the difference between vertical and horizontal scaling?
149. What is connection pooling?
150. What is the difference between optimistic and pessimistic locking?

---

## Topic 4: System Architecture (Questions 151-200)

### Architecture Basics (151-160)
151. What is monolithic architecture?
152. What is microservices architecture?
153. What are the advantages of microservices?
154. What are the disadvantages of microservices?
155. When would you choose monolith over microservices?
156. What is service-oriented architecture (SOA)?
157. What is event-driven architecture?
158. What is layered architecture?
159. What is hexagonal architecture (ports and adapters)?
160. What is CQRS pattern?

### Microservices Communication (161-170)
161. How do microservices communicate with each other?
162. What is synchronous vs asynchronous communication?
163. What is REST? What are its constraints?
164. What is gRPC? When would you use it?
165. What is GraphQL?
166. What is a message broker?
167. What is the difference between RabbitMQ and Kafka?
168. What is event sourcing?
169. What is saga pattern?
170. What is API Gateway pattern?

### Resilience & Fault Tolerance (171-180)
171. What happens if one microservice is down?
172. What is circuit breaker pattern?
173. What is retry pattern?
174. What is bulkhead pattern?
175. What is fallback mechanism?
176. What is rate limiting?
177. What is service discovery?
178. What is health check endpoint?
179. What is graceful degradation?
180. What is chaos engineering?

### Scalability (181-190)
181. What is horizontal vs vertical scaling?
182. What is load balancing?
183. What are load balancing algorithms?
184. What is caching strategy?
185. What is CDN (Content Delivery Network)?
186. What is database replication?
187. What is read replica?
188. What is database sharding?
189. What is consistent hashing?
190. What is CAP theorem?

### Design Principles (191-200)
191. What is SOLID principles?
192. What is Single Responsibility Principle?
193. What is Open/Closed Principle?
194. What is 12-Factor App methodology?
195. What is domain-driven design (DDD)?
196. What is bounded context?
197. What is eventual consistency?
198. What is strong consistency?
199. What is two-phase commit?
200. What is distributed tracing?

---

## Topic 5: DevOps/Deployment (Questions 201-250)

### CI/CD (201-212)
201. What is CI/CD?
202. What is continuous integration?
203. What is continuous delivery?
204. What is continuous deployment?
205. What is Jenkins?
206. What is a Jenkins pipeline?
207. What is GitHub Actions?
208. What is GitLab CI?
209. What are the stages in a typical CI/CD pipeline?
210. What is blue-green deployment?
211. What is canary deployment?
212. What is rolling deployment?

### Docker (213-224)
213. What is Docker?
214. What is a Docker image?
215. What is a Docker container?
216. What is the difference between image and container?
217. What is a Dockerfile?
218. How do you optimize a Dockerfile?
219. What is multi-stage build?
220. What is Docker Compose?
221. What is Docker volume?
222. What is Docker network?
223. What is the difference between COPY and ADD in Dockerfile?
224. What is .dockerignore?

### Kubernetes (225-234)
225. What is Kubernetes?
226. What is a Pod?
227. What is a Deployment?
228. What is a Service in Kubernetes?
229. What is ConfigMap?
230. What is Secret?
231. What is Namespace?
232. What is Ingress?
233. What is Horizontal Pod Autoscaler?
234. What is liveness and readiness probe?

### Git & Version Control (235-242)
235. What is Git flow?
236. What is feature branch workflow?
237. What is trunk-based development?
238. What is the difference between git merge and git rebase?
239. What is a pull request?
240. What is code review process?
241. What is semantic versioning?
242. What is git cherry-pick?

### Monitoring & Logging (243-250)
243. What is centralized logging?
244. What is ELK stack?
245. What is Prometheus?
246. What is Grafana?
247. What are metrics vs logs vs traces?
248. What is alerting strategy?
249. What is SLA, SLO, SLI?
250. What is infrastructure as code?

---

## Topic 6: Design Patterns (Questions 251-300)
*(Concept-focused, implementation covered in low-level-design.md)*

### Creational Patterns (251-262)
251. What is the Singleton pattern? When would you use it?
252. What are the problems with Singleton?
253. How do you make Singleton thread-safe?
254. What is the Factory pattern?
255. What is the difference between Factory and Abstract Factory?
256. What is the Builder pattern? When would you use it?
257. What is the Prototype pattern?
258. What is the difference between deep copy and shallow copy?
259. When would you use Factory vs Builder?
260. What is Object Pool pattern?
261. What is Lazy Initialization?
262. What is Dependency Injection pattern?

### Structural Patterns (263-274)
263. What is the Adapter pattern? Give an example.
264. What is the Decorator pattern? Give an example.
265. What is the difference between Decorator and Inheritance?
266. What is the Facade pattern?
267. What is the Proxy pattern?
268. What are the types of Proxy pattern?
269. What is the Composite pattern?
270. What is the Bridge pattern?
271. What is the Flyweight pattern?
272. What is the difference between Adapter and Facade?
273. What is the difference between Proxy and Decorator?
274. What is the Front Controller pattern?

### Behavioral Patterns (275-290)
275. What is the Strategy pattern? Give an example.
276. What is the Observer pattern? Give an example.
277. What is the difference between Observer and Pub/Sub?
278. What is the Command pattern?
279. What is the State pattern?
280. What is the difference between State and Strategy?
281. What is the Template Method pattern?
282. What is the Iterator pattern?
283. What is the Chain of Responsibility pattern?
284. What is the Mediator pattern?
285. What is the Memento pattern?
286. What is the Visitor pattern?
287. What is the Interpreter pattern?
288. What is the Null Object pattern?
289. What is the Specification pattern?
290. What is the Double Dispatch pattern?

### Pattern Concepts (291-300)
291. What are Gang of Four (GoF) patterns?
292. What is the difference between composition and inheritance in patterns?
293. What is "programming to an interface"?
294. What is "favor composition over inheritance"?
295. What is the Open/Closed Principle in patterns?
296. What is anti-pattern?
297. What is code smell?
298. What is refactoring?
299. What is pattern density?
300. When should you NOT use design patterns?

---

## Topic 7: Testing (Questions 301-350)

### Testing Basics (301-310)
301. What are the different types of testing?
302. What is the testing pyramid?
303. What is unit testing?
304. What is integration testing?
305. What is end-to-end testing?
306. What is functional testing?
307. What is regression testing?
308. What is smoke testing?
309. What is acceptance testing?
310. What is exploratory testing?

### Unit Testing (311-320)
311. What is JUnit?
312. What is @Test annotation?
313. What is @BeforeEach and @AfterEach?
314. What is @BeforeAll and @AfterAll?
315. What is assert vs verify?
316. What is test-driven development (TDD)?
317. What is the red-green-refactor cycle?
318. What makes a good unit test?
319. What should you NOT unit test?
320. What is code coverage?

### Mocking (321-328)
321. What is mocking?
322. What is Mockito?
323. What is the difference between mock and spy?
324. What is @Mock annotation?
325. What is @InjectMocks annotation?
326. What is when().thenReturn()?
327. What is verify() method?
328. What is argument captor?

### Integration Testing (329-336)
329. What is @SpringBootTest?
330. What is @WebMvcTest?
331. What is @DataJpaTest?
332. What is TestContainers?
333. What is context slice ? What is Test Slice?
334. How do you test REST APIs?
335. How do you test real data?
336. What is contract testing?

### Testing Best Practices (337-350)
337. What is AAA pattern (Arrange-Act-Assert)?
338. What is given-when-then pattern?
339. How do you test private methods?
340. How do you test static methods?
341. How do you test exceptions?
342. What is parameterized testing?
343. What is @ValueSource annotation?
344. What is mutation testing?
345. What is flaky test?
346. How do you handle test data?
347. What is test fixture?
348. What is the difference between @Before and @BeforeEach?
349. What is continuous testing?
350. What is shift-left testing?

---

## Topic 8: Security (Questions 351-400)

### Authentication & Authorization (351-362)
351. What is authentication vs authorization?
352. What is OAuth2?
353. Explain the OAuth2 authorization code flow.
354. What is OAuth2 implicit flow?
355. What is OAuth2 client credentials flow?
356. What is OpenID Connect (OIDC)?
357. What is JWT (JSON Web Token)?
358. What are the parts of JWT?
359. What is the difference between session and token-based authentication?
360. What is SSO (Single Sign-On)?
361. What is SAML?
362. What is LDAP?

### Web Security (363-374)
363. What is HTTPS? How does it work?
364. What is SSL/TLS?
365. What is CORS (Cross-Origin Resource Sharing)?
366. What is CSRF (Cross-Site Request Forgery)?
367. How do you prevent CSRF attacks?
368. What is XSS (Cross-Site Scripting)?
369. What are the types of XSS?
370. How do you prevent XSS attacks?
371. What is SQL Injection?
372. How do you prevent SQL Injection?
373. What is Content Security Policy (CSP)?
374. What is SameSite cookie attribute?

### Spring Security (375-384)
375. What is Spring Security?
376. What is SecurityFilterChain?
377. What is AuthenticationManager?
378. What is UserDetailsService?
379. What is @PreAuthorize?
380. What is @Secured annotation?
381. What is method security?
382. What is BCrypt password encoder?
383. What is CSRF token in Spring Security?
384. What is CORS configuration in Spring Security?

### Security Best Practices (385-400)
385. What is the principle of least privilege?
386. What is defense in depth?
387. What is secure by default?
388. What is input validation?
389. What is output encoding?
390. What is password hashing vs encryption?
391. What is salt in password hashing?
392. What is pepper in password hashing?
393. What is rate limiting?
394. What is account lockout policy?
395. What is two-factor authentication (2FA)?
396. What is security headers?
397. What is security audit?
398. What is penetration testing?
399. What is OWASP Top 10?
400. What is security logging and monitoring?

---

## Topic 9: Apache Kafka (Questions 401-450)

### Kafka Basics (401-410)
401. What is Apache Kafka?
402. What is Kafka used for?
403. What are the main components of Kafka?
404. What is a Kafka topic?
405. What is a Kafka partition?
406. Why do we need partitions in Kafka?
407. What is a Kafka broker?
408. What is a Kafka cluster?
409. What is ZooKeeper? What is its role in Kafka?
410. What is Kafka Connect?

### Producers & Consumers (411-420)
411. What is a Kafka producer?
412. What is a Kafka consumer?
413. What is a consumer group?
414. How does Kafka ensure message ordering?
415. What is the difference between a topic and a partition?
416. What is acks configuration in Kafka producer?
417. What is the difference between acks=0, acks=1, and acks=all?
418. What is idempotent producer?
419. What is transactional producer?
420. What is consumer offset?

### Kafka Internals (421-430)
421. How does Kafka store messages?
422. What is log compaction in Kafka?
423. What is the retention policy in Kafka?
424. What is a leader partition?
425. What is a follower partition?
426. What is ISR (In-Sync Replicas)?
427. How does Kafka handle broker failures?
428. What is rebalancing in Kafka?
429. When does consumer rebalancing occur?
430. What is the role of group coordinator?

### Kafka Guarantees (431-438)
431. What guarantees does Kafka provide?
432. How does Kafka ensure durability?
433. How does Kafka ensure availability?
434. What is at-most-once delivery?
435. What is at-least-once delivery?
436. What is exactly-once delivery?
437. How do you achieve exactly-once semantics in Kafka?
438. What is message loss in Kafka? How do you prevent it?

### Kafka Advanced (439-450)
439. What is Kafka Streams?
440. What is KSQL?
441. What is Schema Registry?
442. What is Avro? Why use it with Kafka?
443. What is dead letter queue in Kafka?
444. What is Kafka MirrorMaker?
445. What is multi-cluster replication?
446. How do you monitor Kafka?
447. What are important Kafka metrics?
448. How do you tune Kafka performance?
449. What is backpressure in Kafka?
450. How do you handle poison pill messages in Kafka?

---

## Topic 10: RabbitMQ (Questions 451-500)

### RabbitMQ Basics (451-460)
451. What is RabbitMQ?
452. What is a message broker?
453. What is AMQP protocol?
454. What are the main components of RabbitMQ?
455. What is a producer in RabbitMQ?
456. What is a consumer in RabbitMQ?
457. What is a queue in RabbitMQ?
458. What is an exchange in RabbitMQ?
459. What is a binding in RabbitMQ?
460. What is a virtual host (vhost) in RabbitMQ?

### Exchange Types (461-470)
461. What are the types of exchanges in RabbitMQ?
462. What is a direct exchange?
463. What is a fanout exchange?
464. What is a topic exchange?
465. What is a headers exchange?
466. What is the default exchange in RabbitMQ?
467. What is a routing key?
468. How does topic exchange routing work?
469. What is the difference between * and # in topic routing?
470. When would you use fanout vs direct exchange?

### Message Handling (471-480)
471. What is message acknowledgment in RabbitMQ?
472. What is auto acknowledgment?
473. What is manual acknowledgment?
474. What happens when a message is rejected?
475. What is requeue in RabbitMQ?
476. What is a dead letter exchange?
477. What is TTL (Time-To-Live) for messages?
478. What is message priority in RabbitMQ?
479. What is publisher confirm?
480. What is mandatory flag in RabbitMQ?

### Durability & Reliability (481-488)
481. What is a durable queue?
482. What is a persistent message?
483. What is the difference between durable and persistent?
484. What is quorum queue in RabbitMQ?
485. What is mirrored queue?
486. What is high availability in RabbitMQ?
487. How does RabbitMQ handle broker failures?
488. What is publisher confirms?

### RabbitMQ Advanced (489-500)
489. What is RabbitMQ Federation?
490. What is RabbitMQ Shovel?
491. What is RabbitMQ Streams?
492. What is lazy queue?
493. What is quorum queue vs classic queue?
494. How do you monitor RabbitMQ?
495. What are important RabbitMQ metrics?
496. What is RabbitMQ management plugin?
497. How do you secure RabbitMQ?
498. What is rate limiting in RabbitMQ?
499. What is consumer prefetch?
500. How do you handle poison pill messages in RabbitMQ?

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
