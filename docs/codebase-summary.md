# Asking AI - Codebase Summary

## Directory Structure Overview

```
asking-ai/ (root)
├── interview/                          # Main content directory
│   ├── 4-day-study-plan.md            # Intensive 4-day prep schedule
│   ├── STUDY-GUIDE.md                 # 2-week personalized study plan
│   ├── checklist.md                   # 200-question progress tracker
│   ├── coding-interview-easy.md        # 50 easy coding problems (reference)
│   ├── db-design.md                    # 50 DB design questions (reference)
│   ├── low-level-design.md             # 50 LLD questions (reference)
│   ├── questions.md                    # 52 core interview Q&A
│   ├── past-projects/                  # Professional experience docs
│   │   ├── resume.md                   # Full resume/CV
│   │   └── atc/                        # Automotive marketplace deep-dive
│   │       ├── 1-overview.md           # Project scope and architecture
│   │       ├── 2-Responsibilities.md   # Role and contributions
│   │       ├── 3-Details-of-feature.md # Feature implementation examples
│   │       ├── 4-Architecture.md       # System design and patterns
│   │       └── 5-Deployment-Strategy.md # DevOps and scaling strategy
│   └── practice/                       # 400+ practice questions (9 domains)
│       ├── database-design/            # 50+ DB design questions
│       │   └── 0.md, 1.md, ...        # Individual practice files
│       ├── essential-interview-questions/ # ~350 questions across 9 domains
│       │   ├── 1-JAVA-CORE/           # ~45 Java core questions
│       │   │   ├── 1-OOP-Concepts/    # eiq-01 to eiq-10 (7 files)
│       │   │   ├── 2-Java-Basics/     # eip-11 to eip-20 (10 files)
│       │   │   ├── 3-Collections/     # eip-21 to eip-28 (8 files)
│       │   │   ├── 4-java8/           # eip-31 to eip-40 (9 files)
│       │   │   └── 5-advanced-Java/   # eip-41 to eip-50 (10 files)
│       │   ├── 2-Spring-Framework/    # ~38 Spring questions
│       │   │   ├── 1-Spring-Core/     # eip-51+ (12 files)
│       │   │   ├── 2-Spring-Boot/     # (8 files)
│       │   │   ├── 3-Spring-MVC-and-REST/ # (5 files)
│       │   │   ├── 4-Spring-Data-JPA/ # (3 files)
│       │   │   └── 5-Spring-Advanced/ # (10 files)
│       │   ├── 3-Database/            # ~39 DB questions
│       │   │   ├── 1-Indexing/        # eip-101+ (12 files)
│       │   │   ├── 2-Query-Optimization/ # (10 files)
│       │   │   ├── 3-Transactions-ACID/ # (10 files)
│       │   │   ├── 4-Normalization/   # (6 files)
│       │   │   └── 5-Advanced-Concepts/ # (1 file)
│       │   ├── 4-System-Architecture/ # ~50 architecture questions
│       │   │   ├── 1-Architecture-Basics/ # (10 files)
│       │   │   ├── 2-Microservices-Communication/ # (10 files)
│       │   │   ├── 3-Resilience-Fault-Tolerance/ # (10 files)
│       │   │   ├── 4-Scalability/     # (10 files)
│       │   │   └── 5-Design-Principles/ # (10 files)
│       │   ├── 5-DevOps/              # ~32 DevOps questions
│       │   │   ├── 1-CI-CD/           # eip-301+ (12 files)
│       │   │   ├── 2-Docker/          # (12 files)
│       │   │   ├── 4-Git-Version-Control/ # (4 files)
│       │   │   └── 5-Monitoring/      # (4 files)
│       │   ├── 6-Design-Patterns/     # ~50 pattern questions
│       │   │   ├── 1-Creational/      # eip-401+ (12 files)
│       │   │   ├── 2-Structural/      # (12 files)
│       │   │   └── 3-Behavioral/      # (16 files)
│       │   ├── 7-Testing/             # ~50 testing questions
│       │   │   ├── 1-Testing-Basics/  # eip-501+ (10 files)
│       │   │   ├── 2-Unit-Test/       # (10 files)
│       │   │   ├── 3-Mocking/         # (8 files)
│       │   │   ├── 4-Integration-Test/ # (8 files)
│       │   │   └── 5-Best-Practice/   # (14 files)
│       │   ├── 8-Security/            # ~50 security questions
│       │   │   ├── 1-Authentication/ # eip-601+ (12 files)
│       │   │   ├── 2-Web-Security/    # (12 files)
│       │   │   ├── 3-Spring-Security/ # (10 files)
│       │   │   └── 4-Best-Practices/  # (16 files)
│       │   └── 9-Kafka/               # ~50 Kafka questions
│       │       ├── 1-Kafka-Basics/    # eip-701+ (10 files)
│       │       ├── 2-Producers-Consumers/ # (10 files)
│       │       ├── 3-Kafka-Internals/ # (10 files)
│       │       ├── 4-Kafka-Guarantees/ # (8 files)
│       │       └── 5-Kafka-Advanced/  # (12 files)
│       ├── low-level-design/          # 50 LLD design questions
│       │   ├── lld-01.md to lld-04.md # Individual practice files
│       │   └── low-level-design.md    # Reference guide
│       ├── situational-questions/     # 52 behavioral questions
│       │   ├── situational.md         # Reference guide
│       │   └── image files (.png)     # Visual aids
│       ├── sql/                       # 50 SQL practice questions
│       │   ├── sql-0.md to sql-40.md  # Individual practice files
│       │   └── sql-practice.md        # Reference guide
│       └── write-a-program/           # 50 coding problems
│           ├── wap-01.md to wap-11.md # Individual practice files
│           └── write-a-program.md     # Reference guide
├── .qwen/                             # Qwen AI integration
│   ├── commands/                      # Custom command definitions
│   │   ├── answer-interview.md        # /answer-interview command
│   │   ├── check-lld.md               # /check-lld command
│   │   ├── check-sql-answer.md        # /check-answer command
│   │   └── check-wap-code.md          # /check-code command
│   └── instructions.md                # AI response guidelines
├── .claude/                           # Claude Code configuration
├── docs/                              # Documentation (this directory)
│   ├── project-overview-pdr.md        # Project overview and PDR
│   ├── codebase-summary.md            # This file
│   ├── code-standards.md              # Content standards and conventions
│   ├── system-architecture.md         # System architecture and workflows
│   └── project-roadmap.md             # Project roadmap and status
├── .idea/                             # IntelliJ IDEA configuration
├── asking-ai.iml                      # IntelliJ module file
├── QWEN.md                            # Project context for Qwen AI
├── README.md                          # Root-level quick start guide
└── .gitignore                         # Git configuration
```

## Content Statistics

| Category | Count | Details |
|----------|-------|---------|
| **Total Questions** | 453+ | Across 9 domains |
| **Practice Files** | 453+ | Individual .md files |
| **Domain Areas** | 9 | Java, Spring, DB, Architecture, DevOps, Patterns, Testing, Security, Kafka |
| **SQL Questions** | 50 | sql-0.md to sql-40.md |
| **Program Problems** | 50 | wap-01.md to wap-11.md |
| **DB Design** | 50+ | Database scenario design |
| **LLD Questions** | 50 | lld-01.md to lld-04.md |
| **Java Core** | ~45 | OOP, Collections, Streams, Concurrency |
| **Spring Framework** | ~38 | Beans, Boot, MVC, JPA, Advanced |
| **Database Questions** | ~39 | Indexing, Query Optimization, ACID, Normalization |
| **System Architecture** | ~50 | Microservices, Resilience, Scalability |
| **DevOps** | ~32 | CI/CD, Docker, Git, Monitoring |
| **Design Patterns** | ~50 | Creational, Structural, Behavioral |
| **Testing** | ~50 | Unit, Mocking, Integration, Best Practices |
| **Security** | ~50 | Auth, Spring Security, Best Practices |
| **Kafka** | ~50 | Basics through Advanced |
| **Behavioral** | 52 | Situational interview prep |

## File Naming Conventions

### Essential Interview Questions (eiq/eip prefix)

| Pattern | Location | Example | Range |
|---------|----------|---------|-------|
| `eiq-NN.md` | OOP Concepts | eiq-01.md | 01-10 |
| `eip-NN.md` | Java Basics | eip-11.md to eip-20.md | 11-50 |
| `eip-5NN.md` | Spring Framework | eip-51.md onward | 51-100 |
| `eip-10NN.md` | Database | eip-101.md onward | 101-150 |
| `eip-15NN.md` | System Architecture | eip-151.md onward | 151-200 |
| `eip-30NN.md` | DevOps | eip-301.md onward | 301-400+ |
| `eip-40NN.md` | Design Patterns | eip-401.md onward | 401-450+ |
| `eip-50NN.md` | Testing | eip-501.md onward | 501-550+ |
| `eip-60NN.md` | Security | eip-601.md onward | 601-650+ |
| `eip-70NN.md` | Kafka | eip-701.md onward | 701-750+ |

### Other Categories

| Pattern | Location | Example | Count |
|---------|----------|---------|-------|
| `sql-N.md` | SQL Practice | sql-0.md to sql-40.md | 50 |
| `wap-NN.md` | Write a Program | wap-01.md to wap-11.md | 50 |
| `lld-NN.md` | Low-Level Design | lld-01.md to lld-04.md | 50 |

## Content Organization Architecture

### Hierarchical Structure

The repository uses a **domain-first, topic-second** hierarchy:

```
practice/
├── essential-interview-questions/
│   ├── 1-JAVA-CORE/
│   │   ├── 1-OOP-Concepts/
│   │   │   └── eiq-01.md, eiq-02.md, ...
│   │   ├── 2-Java-Basics/
│   │   │   └── eip-11.md, eip-12.md, ...
│   │   └── ...
│   ├── 2-Spring-Framework/
│   │   ├── 1-Spring-Core/
│   │   │   └── eip-51.md, eip-52.md, ...
│   │   └── ...
│   └── [9 domains total]
├── sql/
│   ├── sql-0.md to sql-40.md
│   └── sql-practice.md (reference guide)
├── write-a-program/
│   ├── wap-01.md to wap-11.md
│   └── write-a-program.md (reference guide)
├── low-level-design/
│   ├── lld-01.md to lld-04.md
│   └── low-level-design.md (reference guide)
└── [other categories]
```

**Benefits:**
- Intuitive navigation by technical area
- Easy to add new topics within existing domains
- Clear progression from fundamentals to advanced
- Supports both domain-focused and sequential study paths

### Answer Format

All practice files follow a consistent structure:

```markdown
**N.** Question text here

[Optional code template or setup]

========== THIS SECTION IS THE ANSWER ==========
[Answer/solution section with:
- Explanation
- Code example (if applicable)
- Multiple approaches (if applicable)
- Trade-offs and considerations]
```

**Example:** `interview/practice/sql/sql-0.md`
- Question: "Find employees who are not managers"
- Answer includes: 3 valid SQL approaches with explanations
- Space: 15+ lines for candidate's own answer before the solution

## Study Pathway Navigation

### Quick Reference Files

| File | Purpose | Location |
|------|---------|----------|
| `interview/checklist.md` | Master progress tracker | Root of interview/ |
| `interview/STUDY-GUIDE.md` | 2-week personalized plan | Root of interview/ |
| `interview/4-day-study-plan.md` | Intensive 4-day schedule | Root of interview/ |
| `interview/questions.md` | 52 core interview Q&A | Root of interview/ |
| `.qwen/instructions.md` | AI command guidelines | Root of .qwen/ |

### Domain Entry Points

- **Java Core:** Start with `1-JAVA-CORE/1-OOP-Concepts/` (eiq-01)
- **Spring Framework:** `2-Spring-Framework/1-Spring-Core/` (eip-51+)
- **Database:** `3-Database/1-Indexing/` (eip-101+)
- **System Architecture:** `4-System-Architecture/1-Architecture-Basics/` (eip-151+)
- **SQL Practice:** `practice/sql/sql-0.md` onward
- **Coding Problems:** `practice/write-a-program/wap-01.md` onward
- **Design Patterns:** `practice/essential-interview-questions/6-Design-Patterns/`

## Integration Points

### With Qwen AI
- **Input:** Practice files in Markdown format
- **Commands:** 4 custom validation commands
- **Output:** Feedback, corrections, improvement suggestions
- **Config:** `.qwen/instructions.md` guides AI behavior

### With IntelliJ IDEA
- **Markdown preview:** Built-in support for .md files
- **Configuration:** `.idea/` folder contains:
  - Checkstyle rules
  - Google Java Format settings
  - JavaDocs configuration
  - Project structure metadata

### Version Control
- **Git integration:** Full repository history and branching
- **Tracked files:** All .md files, .qwen/, .claude/ configs
- **Ignored:** `.idea/`, build artifacts, system files

## File Statistics by Category

### Java Core (Essential Interview Questions)
- **1-OOP-Concepts:** 7 files (eiq-01 to eiq-10, missing eiq-05, eiq-06, eiq-07)
- **2-Java-Basics:** 10 files (eip-11 to eip-20)
- **3-Collections:** 8 files (eip-21 to eip-28)
- **4-Java8:** 9 files (eip-31 to eip-40, missing eip-37)
- **5-Advanced-Java:** 10 files (eip-41 to eip-50)
- **Subtotal:** ~44 files

### Spring Framework
- **1-Spring-Core:** 12 files
- **2-Spring-Boot:** 8 files
- **3-Spring-MVC-and-REST:** 5 files
- **4-Spring-Data-JPA:** 3 files
- **5-Spring-Advanced:** 10 files
- **Subtotal:** ~38 files

### Database
- **1-Indexing:** 12 files
- **2-Query-Optimization:** 10 files
- **3-Transactions-ACID:** 10 files
- **4-Normalization:** 6 files
- **5-Advanced-Concepts:** 1 file
- **Subtotal:** ~39 files

### System Architecture
- **All 5 subcategories:** 10 files each = 50 files

### DevOps
- **1-CI-CD:** 12 files
- **2-Docker:** 12 files
- **4-Git-and-Version-Control:** 4 files
- **5-Monitoring:** 4 files
- **Subtotal:** ~32 files

### Design Patterns, Testing, Security, Kafka
- **Design Patterns:** 40 files (12+12+16)
- **Testing:** 50 files (10+10+8+8+14)
- **Security:** 50 files (12+12+10+16)
- **Kafka:** 50 files (10+10+10+8+12)

## Access Patterns

### By Difficulty Level
1. **Easy:** Coding Interview Easy, Write a Program (Q1-25), SQL (Q1-20)
2. **Medium:** Essential Interview Questions (most), Write a Program (Q26-40), SQL (Q21-40)
3. **Hard:** Essential Interview Questions (advanced topics), LLD, DB Design (complex scenarios)

### By Study Duration
1. **Quick Review (1-2 hours):** Reference guides (questions.md, sql-practice.md, etc.)
2. **Daily Practice (1-2 hours):** 10-15 questions + AI validation
3. **4-Day Intensive:** 200 specific questions following 4-day-study-plan.md
4. **2-Week Plan:** Priority-based questions following STUDY-GUIDE.md

### By Interview Focus
1. **Java/Spring Heavy:** Focus on Java Core (44 files) + Spring (38 files)
2. **Database Heavy:** Focus on Database (39 files) + SQL Practice (50 files)
3. **Architecture Heavy:** Focus on System Architecture (50 files) + Design Patterns (40 files)
4. **Full Stack:** Balance across all 9 domains

## Documentation Files (docs/ directory)

| File | Purpose | Size |
|------|---------|------|
| project-overview-pdr.md | Project goals, scope, requirements | ~400 lines |
| codebase-summary.md | Directory structure, naming conventions | This file |
| code-standards.md | Content and formatting standards | ~250 lines |
| system-architecture.md | Learning workflow and architecture | ~300 lines |
| project-roadmap.md | Status, completion, future plans | ~200 lines |
