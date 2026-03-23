# Asking AI - Project Overview & PDR

## Executive Summary

**Asking AI** is a comprehensive, self-paced interview preparation repository designed for senior-level Java backend developer interviews. It provides 400+ structured practice questions across 9 technical domains, combined with custom AI commands for automated answer validation and feedback.

## Project Goals

1. **Accelerate Interview Readiness** — Enable job seekers to master 400+ technical questions through structured, AI-assisted learning
2. **Self-Paced Learning** — Flexible study paths (4-day intensive or 2-week customized plans)
3. **Automated Feedback** — Custom AI commands provide instant validation and improvement suggestions
4. **Knowledge Retention** — Progress tracking via checklist to identify weak areas and ensure comprehensive coverage
5. **Real-World Relevance** — Questions grounded in actual interview scenarios and production experience (30M user-scale systems, enterprise Java/Spring stack)

## Target Audience

- **Primary:** Java backend developers with 3-5+ years of experience preparing for senior-level interviews
- **Experience Level:** Intermediate to advanced developers with:
  - Strong Java fundamentals (Spring Boot, Hibernate, multi-threading)
  - Database experience (SQL, indexing, transaction handling)
  - Microservices architecture knowledge
  - CI/CD and DevOps familiarity

## Project Scope

### Content Categories (9 Domains)

| Domain | Question Count | Focus Areas |
|--------|---|---|
| **Java Core** | ~50 | OOP, Collections, Streams, Concurrency, Generics |
| **Spring Framework** | ~50 | Beans, DI, Boot, MVC, JPA, Advanced topics |
| **Database** | ~50 | Indexing, Query Optimization, ACID, Normalization |
| **System Architecture** | ~50 | Microservices, Communication, Resilience, Scalability, Design Principles |
| **DevOps** | ~32 | CI/CD, Docker, Git, Monitoring |
| **Design Patterns** | ~50 | Creational, Structural, Behavioral patterns |
| **Testing** | ~50 | Unit testing, Mocking, Integration testing, Best Practices |
| **Security** | ~50 | Authentication, Authorization, Spring Security, Best Practices |
| **Kafka** | ~50 | Basics, Producers/Consumers, Internals, Guarantees, Advanced |
| **SQL Practice** | 50 | SELECT, JOINs, Aggregation, Subqueries, Advanced DML |
| **Write a Program** | 50 | Arrays, Strings, Numbers, Sorting, Algorithms |
| **Database Design** | 50 | Entity relationships, Normalization, Schema design |
| **Coding Interview Easy** | 50 | Algorithm fundamentals |
| **Low-Level Design** | 50 | Class design, Design patterns in practice |
| **Situational Questions** | 52 | Behavioral interview prep |
| **Past Projects** | 5 files | Resume and deep-dive on 30M-user automotive platform |

**Total:** 400+ questions across 453+ markdown files

### Study Pathways

**4-Day Intensive Plan:**
- 200 core questions (SQL, Coding, DB Design, LLD)
- 48-52 hours of focused preparation
- Day-by-day schedule in `interview/4-day-study-plan.md`

**2-Week Personalized Plan:**
- Priority-weighted based on candidate background
- Adaptive focus: Java/Spring 40%, DB 25%, Architecture 20%, Kafka 10%, DevOps 5%
- Detailed in `interview/STUDY-GUIDE.md`

## Key Features

### 1. Custom AI Commands (Qwen Integration)

| Command | Purpose | Input | Output |
|---------|---------|-------|--------|
| `/check-answer <file>` | SQL validator | SQL practice files | Syntax check, logic review, edge case feedback |
| `/check-code <file>` | Code solution checker | Program files | Correctness, complexity analysis, optimization suggestions |
| `/check-lld <file>` | LLD design reviewer | Design files | Pattern appropriateness, SOLID compliance, refactoring advice |
| `/answer-interview <question>` | Senior-level answers | Plain text question | Professional answer with trade-offs, examples |

### 2. Progress Tracking

- **Master Checklist** (`interview/checklist.md`): Track 200-question completion
- **Daily Progress Tables:** By category and difficulty
- **Weak Areas Section:** Identify and monitor challenging topics

### 3. Complete Solutions

- All questions include detailed answers
- Code examples for programming questions
- SQL solutions with multiple valid approaches
- Design pattern implementations with trade-off analysis

## Technology Stack

### Repository Infrastructure
- **Format:** Markdown-based Q&A documentation
- **IDE Configuration:** IntelliJ IDEA with:
  - Java formatting (Google Java Format)
  - Code linting (Checkstyle)
  - JavaDocs generation (4.0.1)
- **Version Control:** Git (IntelliJ `.idea/` configuration included)

### Technologies Covered in Content

| Category | Techs |
|----------|-------|
| **Backend** | Java, Spring Boot, Spring Security, Hibernate, JPA |
| **Databases** | PostgreSQL, MySQL, DynamoDB, Indexing, Query Optimization |
| **Message Queue** | Apache Kafka (basics through advanced) |
| **DevOps** | Docker, CI/CD, GitHub Actions, Git workflows |
| **Testing** | JUnit, Mockito, Integration testing frameworks |
| **Architecture** | Microservices, REST APIs, Event-driven design |

## Functional Requirements

### FR1: Question Curation & Organization
- [x] Organize 400+ questions into 9+ technical domains
- [x] Provide hierarchical folder structure for easy navigation
- [x] Support multiple practice pathways (4-day, 2-week customized)

### FR2: Answer Validation System
- [x] Implement 4 custom AI commands for automated feedback
- [x] Validate SQL syntax and logic
- [x] Analyze algorithm correctness and complexity
- [x] Review design pattern implementations
- [x] Generate senior-level interview answers

### FR3: Progress Tracking
- [x] Master checklist covering 200 core questions
- [x] Category-wise progress breakdowns
- [x] Weak areas identification and tracking
- [x] Daily completion tracking

### FR4: Content Quality
- [x] Include complete, verified solutions for all questions
- [x] Provide multiple valid approaches where applicable
- [x] Cover edge cases and best practices
- [x] Reference production experience and real-world scenarios

### FR5: Self-Paced Learning
- [x] Support flexible study schedules (intensive and customized)
- [x] Enable practice in any order or focused sequencing
- [x] Provide immediate feedback via AI commands

## Non-Functional Requirements

### NFR1: Usability
- Intuitive file structure that mirrors interview domains
- Clear naming conventions for easy file discovery
- Consistent answer format across all questions
- Quick-reference tables and study guides

### NFR2: Accessibility
- All questions in plain Markdown (no build required)
- Works in any Markdown viewer or IDE
- Compatible with IntelliJ IDEA and Qwen Code
- No external dependencies or complex tooling

### NFR3: Maintainability
- Modular structure allows independent question updates
- Consistent formatting enables automated parsing
- Clear file naming prevents version confusion
- Version control integration for change tracking

### NFR4: Comprehensiveness
- Coverage of 400+ questions across 9 domains
- Multiple difficulty levels (easy to senior)
- Real-world examples from production systems
- Progressive learning paths

## Success Metrics

### Adoption & Usage
- Questions completed per study session
- Progress through designated study plans
- AI command usage frequency

### Learning Effectiveness
- Questions skipped vs. attempted
- Answer improvement between attempts
- Weak area identification and follow-up focus

### Content Quality
- Question clarity and relevance feedback
- Answer completeness and correctness
- AI validator feedback accuracy

## Acceptance Criteria

- [ ] 400+ questions organized in 9+ domains
- [ ] All questions have complete, verified answers
- [ ] 4 custom AI commands fully functional
- [ ] Master checklist provides clear progress tracking
- [ ] 4-day and 2-week study plans documented
- [ ] Complete file navigation guide
- [ ] All past projects documented (resume + ATC deep-dive)
- [ ] Documentation covers all content categories

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | Mar 2026 | Initial documentation created |

## Related Documentation

- **Study Guide:** `interview/STUDY-GUIDE.md` — Personalized 2-week plan
- **4-Day Plan:** `interview/4-day-study-plan.md` — Intensive preparation schedule
- **Checklist:** `interview/checklist.md` — 200-question progress tracker
- **AI Commands:** `.qwen/instructions.md` — Response guidelines for Qwen

## Owner Profile (Context)

**Senior Java Backend Developer**
- 5+ years Java/Spring experience
- 30M user-scale automotive marketplace (production experience)
- Skills: Java, Spring Boot, PostgreSQL, MySQL, DynamoDB, React, Next.js, AWS
- Previous: EdTech projects (Django, Odoo, MongoDB)
- Target: Senior backend roles requiring Java, microservices, and system design knowledge
