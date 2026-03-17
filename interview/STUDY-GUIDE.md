# Interview Study Guide
## Personalized Preparation Plan Based on Your Resume

**Generated:** March 17, 2026  
**Target Role:** Senior Java Backend Developer  
**Interview Focus:** Essential Interview Questions + Kafka Basics

---

## 📊 Your Profile Summary

| Area | Your Experience | Interview Relevance |
|------|-----------------|---------------------|
| **Java/Spring** | 5+ years, Spring Boot, Spring Security, Hibernate | ⭐⭐⭐ Core Focus |
| **Microservices** | Decoupled monolith, multi-region deployment | ⭐⭐⭐ High Priority |
| **Databases** | PostgreSQL, MySQL, DynamoDB | ⭐⭐⭐ High Priority |
| **Message Queue** | 2+ years AWS SQS | ⭐⭐ Kafka Basics |
| **Frontend** | React, Next.js, Node.js | ⭐ Bonus |
| **DevOps** | GitHub Actions, Elastic Beanstalk, CodeDeploy | ⭐⭐ Medium Priority |
| **Scale** | 30M users/month | ⭐⭐ System Design |

---

## 🎯 Priority Matrix

### Priority 1: Core Java & Spring (Questions 1-100)
**Study Time:** 40% of total preparation

| Question Range | Topic | Interview Frequency | Your Experience Match |
|----------------|-------|---------------------|----------------------|
| 1-10 | OOP Concepts | High (Q39-40 in questions.md) | ✅ Daily use |
| 11-20 | Java Basics | Medium | ✅ Daily use |
| 21-30 | Collections | High | ✅ Daily use |
| 31-40 | Java 8 Features | High (Q11 in questions.md) | ✅ Daily use |
| 41-50 | Advanced Java (Concurrency) | Medium | ✅ Backend work |
| 51-62 | Spring Core | **Very High** (Q15, 27, 38) | ✅ Daily use |
| 63-72 | Spring Boot | High (Q22-25) | ✅ Daily use |
| 73-82 | Spring MVC & REST | High | ✅ API development |
| 83-90 | Spring Data JPA | **Very High** (Q16, 28-29) | ✅ Daily use |
| 91-100 | Spring Advanced | High (Q93-96 @Transactional) | ✅ Payment features |

**Must-Master Questions:**
- Q24: How does HashMap work internally?
- Q25: HashMap vs ConcurrentHashMap
- Q34: Intermediate vs terminal operations (asked directly in questions.md Q11)
- Q55-56: Bean scopes (asked 3 times in questions.md)
- Q90: N+1 problem (asked in questions.md Q16, 28-29)
- Q93-96: @Transactional (critical for your payment work)

---

### Priority 2: Database & Indexing (Questions 101-150)
**Study Time:** 25% of total preparation

| Question Range | Topic | Interview Frequency | Your Experience Match |
|----------------|-------|---------------------|----------------------|
| 101-112 | Indexing | **Very High** (Q17, 31, 50) | ✅ PostgreSQL, MySQL |
| 113-122 | Query Optimization | High (Q32) | ✅ Performance tuning |
| 123-132 | Transactions & ACID | High (Q49) | ✅ Payment processing |
| 133-138 | Normalization | Medium (Q49) | ✅ DB design |
| 139-150 | Advanced Concepts | Medium | ✅ Multi-DB experience |

**Must-Master Questions:**
- Q101-112: All indexing questions (asked 4 times in questions.md)
- Q104-105: When to create / NOT create index
- Q113-116: Slow query identification (asked in questions.md Q32)
- Q123-132: ACID properties (asked in questions.md Q49)
- Q149: Connection pooling (asked in questions.md Q9)

---

### Priority 3: System Architecture (Questions 151-200)
**Study Time:** 20% of total preparation

| Question Range | Topic | Interview Frequency | Your Experience Match |
|----------------|-------|---------------------|----------------------|
| 151-160 | Architecture Basics | Medium | ✅ Monolith → Microservices |
| 161-170 | Microservices Communication | **Very High** (Q20) | ✅ Aggregation services |
| 171-180 | Resilience & Fault Tolerance | **Very High** (Q21) | ✅ Multi-region deployment |
| 181-190 | Scalability | High | ✅ 30M users/month |
| 191-200 | Design Principles | Medium | ✅ SOLID in code reviews |

**Must-Master Questions:**
- Q152-155: Microservices vs Monolith (your decoupling experience)
- Q161-162: Service communication (asked in questions.md Q20)
- Q171-172: Service failure handling (asked in questions.md Q21)
- Q184: Caching strategy (you use ElastiCache)
- Q197-198: Eventual vs Strong consistency (multi-region)

---

### Priority 4: Kafka Basics (Questions 401-438)
**Study Time:** 10% of total preparation

> **Note:** Interview scope says "Kafka but not go in-depth" — focus on concepts only

| Question Range | Topic | Interview Frequency | Your Experience Match |
|----------------|-------|---------------------|----------------------|
| 401-410 | Kafka Basics | Medium | ⚠️ SQS experience maps here |
| 411-420 | Producers & Consumers | Medium | ⚠️ SQS producers/consumers |
| 421-430 | Kafka Internals | Low-Medium | ⚠️ Conceptual only |
| 431-438 | Delivery Guarantees | Medium | ⚠️ Compare with SQS |

**Must-Master Questions:**
- Q401-408: Kafka core concepts (topics, partitions, brokers)
- Q413: Consumer groups (similar to SQS consumer groups)
- Q416-417: acks configuration (delivery guarantees)
- Q434-437: Delivery semantics (at-least-once, exactly-once)

**Skip:** Q439-450 (Kafka Streams, KSQL, MirrorMaker, tuning)

---

### Priority 5: DevOps & CI/CD (Questions 201-224, 235-250)
**Study Time:** 5% of total preparation

| Question Range | Topic | Interview Frequency | Your Experience Match |
|----------------|-------|---------------------|----------------------|
| 201-212 | CI/CD | High (Q12-13) | ✅ GitHub Actions, Beanstalk |
| 213-224 | Docker | High (Q14) | ✅ Docker for dev environment |
| 235-242 | Git Flow | High (Q36-37) | ✅ Git, Bitbucket, GitHub |
| 243-250 | Monitoring | Medium | ✅ New Relic, Kibana, SonarCloud |

**Must-Master Questions:**
- Q201-204: CI/CD concepts (asked in questions.md Q12)
- Q210-212: Deployment strategies (blue-green, canary)
- Q217-219: Dockerfile optimization (asked in questions.md Q14)
- Q235-238: Git workflows (asked in questions.md Q36-37)

---

### Priority 6: Design Patterns (Questions 251-290)
**Study Time:** 5% of total preparation

| Question Range | Topic | Interview Frequency | Your Experience Match |
|----------------|-------|---------------------|----------------------|
| 251-262 | Creational Patterns | High (Q26-27 Singleton) | ✅ Applied in code reviews |
| 263-274 | Structural Patterns | Medium | ✅ Adapter, Decorator |
| 275-290 | Behavioral Patterns | Medium (Q43 Listener) | ✅ Observer pattern |

**Must-Master Questions:**
- Q251-253: Singleton (asked in questions.md Q26-27)
- Q254-256: Factory, Builder (you mentioned in resume)
- Q276-277: Observer pattern (asked as "Listener" in questions.md Q43)

---

## 📋 Direct Mapping: questions.md → essential-interview-questions.md

### Guaranteed Questions (From questions.md)

| questions.md # | Question | Maps To | Priority |
|----------------|----------|---------|----------|
| 1 | Giới thiệu bản thân | N/A | ⭐⭐⭐ Prepare story |
| 2-3 | Sprint, meetings | N/A | ⭐ Know Agile basics |
| 7 | Handle pressure, adhoc tasks | N/A | ⭐ Prepare examples |
| 9 | Spring pool là gì? | Q149 (Connection pooling) | ⭐⭐⭐ |
| 10 | Pass by reference in Java | Q11 (Pass-by-value vs reference) | ⭐⭐⭐ |
| 11 | Intermediate vs terminal operations | Q34 (Stream operations) | ⭐⭐⭐ |
| 12-13 | CI/CD, Jenkins | Q201-209 (CI/CD concepts) | ⭐⭐ |
| 14 | Docker, optimize Dockerfile | Q217-219 (Dockerfile optimization) | ⭐⭐ |
| 15, 27 | Bean scope | Q55-56 (Bean scopes) | ⭐⭐⭐ |
| 16, 28-29 | Hibernate lazy/eager, N+1 | Q83-90 (JPA, N+1 problem) | ⭐⭐⭐ |
| 17, 31, 50 | DB Index | Q101-112 (Indexing) | ⭐⭐⭐ |
| 20 | How services communicate | Q161-167 (Microservices communication) | ⭐⭐⭐ |
| 21 | What if service is down? | Q171-180 (Resilience patterns) | ⭐⭐⭐ |
| 22-25 | Properties, environments, scripts | Q68-69 (Spring profiles), Q201-212 (CI/CD) | ⭐⭐ |
| 26 | Singleton object | Q251-253 (Singleton pattern) | ⭐⭐⭐ |
| 32 | Slow queries identification | Q113-116 (Query optimization) | ⭐⭐ |
| 34-35 | Unit test, real data | Q301-336 (Testing) | ⭐⭐ |
| 36-37 | Git flow | Q235-238 (Git workflows) | ⭐⭐ |
| 38 | Dependency injection | Q51-52 (DI concepts) | ⭐⭐⭐ |
| 39-40 | Interface vs abstract class, OOP | Q1-10 (OOP Concepts) | ⭐⭐⭐ |
| 41 | Indexes pros/cons | Q101-112 (Indexing) | ⭐⭐⭐ |
| 42 | OAuth2 flow | Q352-353 (OAuth2) | ⭐⭐ |
| 43 | Listener design pattern | Q276-277 (Observer pattern) | ⭐⭐ |
| 44 | Set, List, Array | Q21-30 (Collections) | ⭐⭐ |
| 45-47 | Coding problems | Practice separately | ⭐⭐⭐ |
| 48-49 | SQL, DB design | Practice SQL separately | ⭐⭐⭐ |

---

## 📅 2-Week Study Plan

### Week 1: Foundation (Java, Spring, Database)

| Day | Focus | Questions | Time |
|-----|-------|-----------|------|
| **Day 1** | OOP + Java Basics | Q1-20 | 4-5 hours |
| **Day 2** | Collections + Java 8 | Q21-40 | 4-5 hours |
| **Day 3** | Advanced Java (Concurrency) | Q41-50 | 3-4 hours |
| **Day 4** | Spring Core | Q51-62 | 4-5 hours |
| **Day 5** | Spring Boot + MVC | Q63-82 | 4-5 hours |
| **Day 6** | Spring Data JPA + Transactional | Q83-100 | 4-5 hours |
| **Day 7** | Database Indexing | Q101-122 | 4-5 hours |

### Week 2: Architecture, Kafka, DevOps

| Day | Focus | Questions | Time |
|-----|-------|-----------|------|
| **Day 8** | Transactions + Normalization | Q123-150 | 3-4 hours |
| **Day 9** | Microservices Basics | Q151-170 | 4-5 hours |
| **Day 10** | Resilience + Scalability | Q171-200 | 4-5 hours |
| **Day 11** | Kafka Basics | Q401-438 | 3-4 hours |
| **Day 12** | CI/CD + Docker | Q201-224 | 3-4 hours |
| **Day 13** | Git + Design Patterns | Q235-242, Q251-280 | 3-4 hours |
| **Day 14** | Full Review + Mock Interview | All high-priority | 4-5 hours |

---

## ⚡ Quick Wins (Highest ROI)

These topics appear **multiple times** in questions.md — master them first:

| Rank | Topic | Question Numbers | Appears In questions.md |
|------|-------|------------------|------------------------|
| 1 | **Bean Scopes** | Q55-56 | 3 times (Q15, 27, 27) |
| 2 | **Hibernate Lazy/N+1** | Q83-90 | 3 times (Q16, 28, 29) |
| 3 | **Indexing** | Q101-112 | 4 times (Q17, 31, 41, 50) |
| 4 | **Service Failure** | Q171-180 | 2 times (Q20, 21) |
| 5 | **Singleton Pattern** | Q251-253 | 2 times (Q26, 27) |
| 6 | **CI/CD** | Q201-212 | 2 times (Q12, 13) |
| 7 | **Stream Operations** | Q34-36 | 2 times (Q11, 34) |

---

## 🚫 Low Priority (Skip or Skim)

| Topic | Question Range | Reason to Skip |
|-------|----------------|----------------|
| Security (except OAuth2) | Q351-400 | Only Q42 (OAuth2) is asked |
| Testing Deep Dive | Q301-350 | Know basics only (Q34-35) |
| Kafka Advanced | Q439-450 | Interview says "not in-depth" |
| RabbitMQ | Q451-500 | You have SQS, Kafka is focus |
| Kubernetes | Q225-234 | You use ECS, not K8s |
| Advanced Design Patterns | Q291-300 | Pattern concepts > advanced patterns |

---

## 🎤 Behavioral Questions (Prepare Stories)

These aren't in the question files but will be asked:

| Topic | Your Experience | Prepare Example |
|-------|-----------------|-----------------|
| **System Design** | 30M users/month, multi-region | Describe search results page architecture |
| **Microservices Migration** | Decoupled monolith | Challenges faced, how you solved them |
| **Performance Optimization** | Web vitals, SEO | Specific metrics improved |
| **Leadership** | Code reviews, stand-ups | Bugs you caught, solutions you proposed |
| **Pressure Handling** | Adhoc tasks, deadlines | questions.md Q7 |
| **Cross-functional** | Frontend + Backend | Full-stack feature you delivered |

---

## 📝 Coding Practice (Separate from Theory)

Based on questions.md Q45-48:

| Type | Practice Topic | File Location |
|------|----------------|---------------|
| **Array/List** | Second largest element | `sicpa/practice/write-a-program/` |
| **Palindrome** | Number palindrome check | `sicpa/coding-interview-easy.md` |
| **SQL** | Self-join (employee-manager) | `sicpa/practice/sql/` |
| **DB Design** | Student-Class M:N relationship | `sicpa/db-design.md` |

---

## 🔗 Useful File References

| File | Purpose |
|------|---------|
| `essential-interview-questions.md` | 500 questions (this guide's reference) |
| `questions.md` | Direct interview questions |
| `STUDY-GUIDE.md` | This file — your personalized plan |
| `practice/essential-interview-questions/` | Individual question files for answers |
| `practice/sql/` | SQL practice with solutions |
| `practice/write-a-program/` | Coding practice with solutions |
| `practice/low-level-design/` | LLD practice with solutions |

---

## ✅ Pre-Interview Checklist

### 1 Day Before
- [ ] Review Bean Scopes (Q55-56)
- [ ] Review Hibernate N+1 (Q83-90)
- [ ] Review Indexing (Q101-112)
- [ ] Review Microservices failure (Q171-180)
- [ ] Prepare "Tell me about yourself" story
- [ ] Prepare 2-3 project stories (STAR format)

### Morning Of
- [ ] Skim Kafka basics (Q401-410)
- [ ] Skim OAuth2 flow (Q352-353)
- [ ] Review your project architecture diagram
- [ ] Have water + notebook ready

---

## 📞 Contact for Mock Interview

Use `/answer-interview @eip-XX.md` command to practice answers.

**Good luck! 🍀**

---

*Last updated: March 17, 2026*
