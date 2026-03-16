# Asking AI - Interview Preparation Repository

## Project Overview

**Asking AI** is a comprehensive interview preparation repository designed for Java backend developer interviews (specifically targeting SICPA). It contains **200+ practice questions** across four key technical areas, along with custom Qwen CLI commands for automated answer checking and feedback.

### Purpose
- Structured interview preparation with 200 technical questions
- Self-paced learning with automated feedback via custom commands
- Complete solutions and explanations for review
- 4-day intensive study plan for rapid preparation

### Main Technologies & Tools
- **Language:** Java (primary), SQL
- **IDE:** IntelliJ IDEA (configured with Checkstyle, Google Java Format, JavaDocs)
- **AI Assistant:** Qwen Code with custom commands for answer validation
- **Documentation:** Markdown-based Q&A repository

---

## Directory Structure

```
asking-ai/
├── .qwen/                          # Qwen AI custom commands and instructions
│   ├── commands/
│   │   ├── answer-interview.md     # /answer-interview: Senior-level interview answers
│   │   ├── check-lld.md            # /check-lld: Low-level design checker
│   │   ├── check-sql-answer.md     # /check-answer: SQL answer validator
│   │   └── check-wap-code.md       # /check-code: Code solution checker
│   └── instructions.md             # Custom instructions for AI responses
├── sicpa/                          # Main interview preparation content
│   ├── 4-day-study-plan.md         # Intensive 4-day preparation schedule
│   ├── checklist.md                # 200-question progress tracker
│   ├── coding-interview-easy.md    # 50 easy coding problems
│   ├── db-design.md                # 50 database design questions
│   ├── low-level-design.md         # 50 LLD questions (Java class design)
│   ├── questions.md                # 52 common interview Q&A
│   └── practice/                   # Practice questions with solutions
│       ├── database-design/        # DB design practice
│       ├── essential-interview-questions/
│       │   ├── 1-OOP-Concepts/     # OOP interview questions
│       │   └── 2-Java-Basics/      # Java fundamentals
│       ├── low-level-design/       # LLD solutions (lld-01.md to lld-XX.md)
│       ├── sql/                    # SQL practice (sql-0.md to sql-50.md)
│       ├── situational-questions/  # Behavioral questions
│       └── write-a-program/        # Coding solutions (wap-01.md to wap-XX.md)
├── .idea/                          # IntelliJ IDEA project configuration
├── asking-ai.iml                   # IntelliJ module file
└── QWEN.md                         # This file - project context for AI
```

---

## Content Categories

### 1. SQL Practice (50 Questions)
**Location:** `sicpa/practice/sql/` and `sicpa/sql-practice.md`

| Topic | Questions |
|-------|-----------|
| Basic SELECT and Filtering | Q1-10 |
| Aggregation and GROUP BY | Q11-20 |
| JOINs | Q21-30 |
| Subqueries and Self-Joins | Q31-40 |
| Advanced Filtering & DML | Q41-50 |

**Key Skills:** SELECT, WHERE, JOINs, GROUP BY, subqueries, NULL handling, CASE, window functions

---

### 2. Write a Program (50 Questions)
**Location:** `sicpa/practice/write-a-program/`

| Topic | Questions |
|-------|-----------|
| Array/List Operations | Q1-10 |
| Number Programs | Q11-20 |
| String Programs | Q21-30 |
| Sorting and Searching | Q31-40 |
| Miscellaneous Programs | Q41-50 |

**Key Skills:** Two pointers, hash maps, recursion, sorting algorithms, linked lists, stacks/queues

---

### 3. Database Design (50 Questions)
**Location:** `sicpa/db-design.md` and `sicpa/practice/database-design/`

| Topic | Questions |
|-------|-----------|
| Basic Entity Relationships | Q1-10 |
| One-to-Many Relationships | Q11-20 |
| Many-to-Many Relationships | Q21-30 |
| Advanced Design Scenarios | Q31-40 |
| Special Design Considerations | Q41-50 |

**Key Skills:** ER diagrams, normalization, relationships (1:1, 1:N, M:N), indexing, constraints

---

### 4. Coding Interview Easy (50 Questions)
**Location:** `sicpa/coding-interview-easy.md`

| Topic | Questions |
|-------|-----------|
| Number Operations | Q1-10 |
| String Manipulation | Q11-20 |
| Array Operations | Q21-30 |
| Linked List Basics | Q31-40 |
| Miscellaneous Easy Problems | Q41-50 |

**Key Skills:** Basic algorithms, string manipulation, array operations, linked lists

---

### 5. Low-Level Design (50 Questions)
**Location:** `sicpa/low-level-design.md` and `sicpa/practice/low-level-design/`

| Category | Questions |
|----------|-----------|
| Creational Patterns | Q1-12 |
| Structural Patterns | Q13-25 |
| Behavioral Patterns | Q26-38 |
| Miscellaneous Design | Q39-50 |

**Key Patterns:** Singleton, Factory, Builder, Strategy, Observer, Command, State, Decorator, Composite

---

### 6. Essential Interview Questions
**Location:** `sicpa/questions.md` and `sicpa/practice/essential-interview-questions/`

- **OOP Concepts:** Encapsulation, Inheritance, Polymorphism, Abstraction
- **Java Basics:** Collections, Streams, Spring Framework, Hibernate
- **System Design:** Microservices, CI/CD, Docker, Database optimization

---

## Custom Qwen Commands

This project includes **4 custom commands** for automated answer checking:

### `/check-answer <file-path>`
**Purpose:** Validate SQL practice answers

**Usage:**
```
/check-answer sicpa/practice/sql/1.md
```

**Checks:**
- Correct SQL syntax
- Correct logic and operators
- Proper NULL handling (`IS NULL` vs `= NULL`)
- Correct column/table names
- Alias consistency

---

### `/check-code <file-path>`
**Purpose:** Validate "Write a Program" solutions

**Usage:**
```
/check-code sicpa/practice/write-a-program/1.md
```

**Checks:**
- Correctness (logic, test cases)
- Edge case handling (null, empty, single element)
- Time/Space complexity
- Code quality and style

---

### `/check-lld <file-path>`
**Purpose:** Review Low-Level Design solutions

**Usage:**
```
/check-lld sicpa/practice/low-level-design/1.md
```

**Checks:**
- Design pattern appropriateness
- SOLID principles adherence
- Extensibility and scalability
- UML diagram review
- Code quality

**Special:** If no answer is submitted, provides a complete solution and writes it to the file.

---

### `/answer-interview <question>`
**Purpose:** Generate senior-level interview answers

**Usage:**
```
/answer-interview "What's the difference between Singleton and Factory?"
```

**Guidelines:**
- Direct answer first (1-2 sentences)
- Focus on trade-offs, not just solutions
- No code blocks — describe verbally
- Real-world engineering examples (not generic student/bird examples)

---

## Building and Running

This is a **documentation and practice repository** — there is no build process.

### Setup for Practice

1. **Open in IntelliJ IDEA**
   - Open the `asking-ai` folder in IntelliJ
   - Java configurations are pre-configured (JDK 25, Checkstyle, Google Java Format)

2. **Using Qwen Commands**
   - In Qwen Code chat, use the custom commands:
     - `/check-answer <file>` for SQL
     - `/check-code <file>` for programming
     - `/check-lld <file>` for design
     - `/answer-interview <question>` for interview prep

3. **Practice Workflow**
   ```
   1. Read a question from the practice files
   2. Write your answer in the same file (below the question)
   3. Run the appropriate check command
   4. Review feedback and corrections
   5. Study the solution if needed
   ```

---

## Development Conventions

### File Naming
- **SQL:** `sql-N.md` (e.g., `sql-0.md`, `sql-1.md`)
- **Write a Program:** `wap-NN.md` (e.g., `wap-01.md`, `wap-02.md`)
- **Low-Level Design:** `lld-NN.md` (e.g., `lld-01.md`, `lld-02.md`)
- **OOP Questions:** `eiq-NN.md` (e.g., `eiq-01.md`)
- **Java Basics:** `eip-NN.md` (e.g., `eip-11.md`)

### Answer Format
All practice files follow this structure:
```markdown
**N.** Question text here

```java
// Your solution code
```

========== THIS SECTION IS THE ANSWER ==========
[Answer/solution section]
```

### Code Style (Java)
- **Formatter:** Google Java Format (configured in `.idea/google-java-format.xml`)
- **Linting:** Checkstyle (configured in `.idea/checkstyle-idea.xml`)
- **Documentation:** JavaDocs 4.0.1 (configured in `.idea/intellij-javadocs-4.0.1.xml`)

---

## Study Plans

### 4-Day Intensive Plan
**Location:** `sicpa/4-day-study-plan.md`

| Day | Focus | Questions | Time |
|-----|-------|-----------|------|
| 1 | SQL + Coding Foundations | SQL 1-50, Coding 1-25 | 12-13 hours |
| 2 | Coding + DB Design Intro | Coding 26-50, Write Program 1-25, DB Design 1-25 | 12-13 hours |
| 3 | Write Program + DB Design | Write Program 26-50, DB Design 26-50 | 12-13 hours |
| 4 | Full Review + Mock Interview | All review + mock simulation | 12-13 hours |

**Total:** 200 questions in 4 days (~48-52 hours)

### Progress Tracking
**Location:** `sicpa/checklist.md`

Interactive checklist with:
- Per-category progress (SQL, Write Program, DB Design, Coding)
- Daily tracking tables
- Weak areas notes section
- Final pre-interview checklist

---

## Key Files Reference

| File | Purpose |
|------|---------|
| `sicpa/checklist.md` | Master progress tracker (200 questions) |
| `sicpa/4-day-study-plan.md` | Detailed daily schedule |
| `sicpa/questions.md` | 52 common interview Q&A |
| `sicpa/coding-interview-easy.md` | 50 easy coding problems |
| `sicpa/db-design.md` | 50 database design scenarios |
| `sicpa/low-level-design.md` | 50 LLD questions with patterns |
| `.qwen/instructions.md` | AI response guidelines |

---

## Interview Tips

### Technical Areas to Master
1. **SQL:** JOINs, subqueries, NULL handling, aggregation
2. **Java:** Collections, Streams, OOP principles, Spring Framework
3. **Database Design:** Normalization, relationships, indexing
4. **Low-Level Design:** Design patterns, SOLID principles
5. **Coding:** Arrays, strings, linked lists, basic algorithms

### Common Topics (from `questions.md`)
- Spring: Bean scope, Dependency Injection, Hibernate lazy loading
- Database: Indexing, ACID properties, Normalization, N+1 problem
- Git: Branching strategies, CI/CD pipelines
- System Design: Microservices communication, handling failures
- OOP: 4 principles, Interface vs Abstract class

---

## Tips for Using This Repository

### 1. Start with the Checklist
Use `sicpa/checklist.md` to track your progress across all 200 questions.

### 2. Follow the 4-Day Plan
If you have an interview soon, follow the structured plan in `sicpa/4-day-study-plan.md`.

### 3. Use Custom Commands
Leverage the Qwen commands for instant feedback:
- Write your answer first
- Run the check command
- Review feedback and corrections

### 4. Study Solutions
For questions you get wrong or skip, study the provided solutions in the `practice/` folders.

### 5. Focus on Weak Areas
Use the "Weak Areas" section in `checklist.md` to track and revisit difficult topics.

---

## Project Metadata

- **Project Type:** Interview Preparation Repository (Documentation/Practice)
- **Primary Language:** Java, SQL
- **IDE:** IntelliJ IDEA
- **Total Questions:** 200+ (50 SQL + 50 Coding + 50 DB Design + 50 LLD)
- **Estimated Study Time:** 48-52 hours (4-day intensive plan)

---

## Quick Start

```
1. Open checklist.md to see all 200 questions
2. Pick a category to start (SQL recommended first)
3. Read questions from practice/sql/
4. Write your answer in the file
5. Run: /check-answer sicpa/practice/sql/N.md
6. Review feedback and learn
7. Track progress in checklist.md
```

---

**Good luck with your interview preparation! 🍀**
