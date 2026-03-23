# Asking AI — Interview Preparation Repository

Comprehensive interview preparation platform with 400+ practice questions across 9 technical domains, custom AI commands for automated answer validation, and structured study plans for efficient learning.

## Quick Start

### 1. Start a Study Session

**First time?** Begin with the 2-week personalized study plan:

```bash
open interview/STUDY-GUIDE.md
```

**Short on time?** Try the 4-day intensive plan:

```bash
open interview/4-day-study-plan.md
```

### 2. Open a Practice Question

Navigate to any practice question and read it:

```bash
interview/practice/essential-interview-questions/1-JAVA-CORE/1-OOP-Concepts/eiq-01.md
interview/practice/sql/sql-0.md
interview/practice/write-a-program/wap-01.md
```

### 3. Solve & Get Feedback

Write your answer in the blank space, then validate with Qwen:

```
/check-answer interview/practice/sql/sql-0.md
/check-code interview/practice/write-a-program/wap-01.md
/check-lld interview/practice/low-level-design/lld-01.md
```

### 4. Track Progress

Update your progress in the master checklist:

```bash
open interview/checklist.md
```

## Content Overview

### 400+ Questions Across 9 Domains

| Domain | Questions | Topics | Level |
|--------|-----------|--------|-------|
| **Java Core** | 44 | OOP, Collections, Streams, Concurrency | Intermediate |
| **Spring Framework** | 38 | Beans, Boot, MVC, JPA, Security | Intermediate |
| **Database** | 39 | Indexing, Query Optimization, ACID, Normalization | Intermediate |
| **System Architecture** | 50 | Microservices, Resilience, Scalability, Design | Advanced |
| **DevOps** | 32 | CI/CD, Docker, Git, Monitoring | Intermediate |
| **Design Patterns** | 50 | Creational, Structural, Behavioral | Intermediate |
| **Testing** | 50 | Unit, Mocking, Integration, Best Practices | Intermediate |
| **Security** | 50 | Authentication, Authorization, Spring Security | Intermediate |
| **Kafka** | 50 | Basics, Producers, Internals, Advanced | Intermediate |

### Practice Problem Banks

| Category | Count | Format |
|----------|-------|--------|
| SQL Practice | 50 | Write and validate SQL queries |
| Write a Program | 50 | Implement Java algorithms and data structures |
| Database Design | 50 | Design schemas and entity relationships |
| Low-Level Design | 50 | Apply design patterns and SOLID principles |
| Coding Interview Easy | 50 | Algorithm fundamentals |
| Situational Questions | 52 | Behavioral interview preparation |

**Total: 400+ questions**

## Study Pathways

### 4-Day Intensive (48-52 hours)

Perfect for rapid preparation before an interview:

- **Day 1:** SQL fundamentals + Coding basics (50 + 25 = 75 questions, 12-13h)
- **Day 2:** Coding problems + Database design intro (25 + 25 + 15 = 65 questions, 12-13h)
- **Day 3:** Write-a-program + Advanced database design (25 + 25 = 50 questions, 12-13h)
- **Day 4:** Full review + Mock interview (35 questions, 12-13h)

**Total:** 225 questions in 4 focused days

See `interview/4-day-study-plan.md` for detailed schedule.

### 2-Week Personalized (40+ hours)

Tailored learning based on your experience:

- **Priority 1 (40%):** Java/Spring fundamentals — 100+ questions
- **Priority 2 (25%):** Database design and optimization — 89+ questions
- **Priority 3 (20%):** System architecture and microservices — 50 questions
- **Priority 4 (10%):** Kafka essentials — 50 questions
- **Priority 5 (5%):** DevOps practices — 32 questions

**Total:** 321+ questions over 2 weeks

See `interview/STUDY-GUIDE.md` for full breakdown.

### Domain-Focused Learning

Pick any technical area and master it systematically:

```
interview/practice/essential-interview-questions/
├── 1-JAVA-CORE/
├── 2-Spring-Framework/
├── 3-Database/
├── 4-System-Architecture/
├── 5-DevOps/
├── 6-Design-Patterns/
├── 7-Testing/
├── 8-Security/
└── 9-Kafka/
```

## Custom Qwen AI Commands

### /check-answer — SQL Validator

Validate SQL practice answers with syntax and logic checking:

```
/check-answer interview/practice/sql/sql-5.md
```

**Checks:**
- SQL syntax correctness
- Query logic validity
- NULL handling edge cases
- Column/table name accuracy
- Performance considerations

### /check-code — Code Solution Reviewer

Analyze Java code solutions for correctness and quality:

```
/check-code interview/practice/write-a-program/wap-03.md
```

**Checks:**
- Algorithm correctness
- Edge case handling
- Time/Space complexity analysis
- Code quality and style
- Test case coverage

### /check-lld — Design Pattern Checker

Review low-level design solutions:

```
/check-lld interview/practice/low-level-design/lld-02.md
```

**Checks:**
- Design pattern appropriateness
- SOLID principles compliance
- Extensibility and maintainability
- UML diagram accuracy
- Code implementation quality

### /answer-interview — Senior-Level Answers

Generate professional interview answers:

```
/answer-interview "Difference between Singleton and Factory patterns?"
```

**Provides:**
- Concise direct answer
- Trade-offs and considerations
- Real-world engineering examples
- When to use vs. alternatives

## Directory Structure

```
asking-ai/
├── interview/                       # Main content
│   ├── STUDY-GUIDE.md              # 2-week personalized plan
│   ├── 4-day-study-plan.md         # Intensive 4-day schedule
│   ├── checklist.md                # 200-question progress tracker
│   ├── questions.md                # 52 core interview Q&A
│   ├── practice/                   # 400+ practice questions
│   │   ├── essential-interview-questions/   # 350+ questions (9 domains)
│   │   ├── sql/                    # SQL practice (50 questions)
│   │   ├── write-a-program/        # Code problems (50 questions)
│   │   ├── low-level-design/       # Design patterns (50 questions)
│   │   ├── database-design/        # Schema design (50 questions)
│   │   └── situational-questions/  # Behavioral prep (52 questions)
│   └── past-projects/              # Professional case study
│       ├── resume.md               # Full resume/CV
│       └── atc/                    # Automotive marketplace (30M users)
├── docs/                           # Documentation
│   ├── project-overview-pdr.md     # Project goals and requirements
│   ├── codebase-summary.md         # Directory structure and navigation
│   ├── code-standards.md           # Content standards and conventions
│   ├── system-architecture.md      # Learning system architecture
│   └── project-roadmap.md          # Development roadmap and status
├── .qwen/                          # Qwen AI integration
│   ├── commands/                   # Custom command definitions
│   └── instructions.md             # AI response guidelines
└── .claude/                        # Claude Code configuration
```

## Getting Started

### Prerequisites

- **IDE:** IntelliJ IDEA (or any Markdown viewer)
- **AI Assistant:** Qwen Code or Claude with custom commands
- **Time:** 4-52 hours depending on chosen pathway

### Setup

1. **Clone or download repository**
   ```bash
   git clone <repository-url>
   cd asking-ai
   ```

2. **Open in IntelliJ IDEA**
   - File → Open → select `asking-ai` folder
   - Navigate to `interview/` folder
   - Open `STUDY-GUIDE.md` to start

3. **Set up Qwen commands** (optional but recommended)
   - Copy `.qwen/` configuration to your Qwen AI setup
   - Verify custom commands appear in command palette

### Usage Workflow

```
1. Read study guide (interview/STUDY-GUIDE.md or interview/4-day-study-plan.md)
   ↓
2. Navigate to a practice question in interview/practice/
   ↓
3. Read the question (before the separator line)
   ↓
4. Write your answer in the blank space provided
   ↓
5. Run Qwen validation command: /check-answer, /check-code, or /check-lld
   ↓
6. Read feedback and review the official solution
   ↓
7. Update progress in interview/checklist.md
   ↓
8. Move to next question
```

## Content Quality

### Verified Solutions
- All 400+ questions have complete, tested answers
- Multiple valid approaches shown where applicable
- Time/Space complexity analysis included
- Trade-offs and key insights documented

### Testing
- All SQL queries execute correctly
- All Java code compiles and runs
- All design patterns follow SOLID principles
- All solutions peer-reviewed

### Consistency
- Uniform formatting across all questions
- Consistent file naming conventions
- Standard answer structure (question → blank space → separator → solution)
- Aligned with Qwen AI validation standards

## Progress Tracking

### Master Checklist

Track your progress through 200 core questions:

```bash
open interview/checklist.md
```

Features:
- Per-category progress (SQL, Code, DB Design, LLD)
- Daily tracking tables
- Weak areas identification
- Final pre-interview checklist

### Statistics

Monitor your progress with:
- Questions completed per day
- Domains with highest/lowest completion
- Time spent per category
- Weak areas for targeted review

## Professional Experience

### Automotive Marketplace Case Study

Learn from a real 30M user-scale platform:

```
interview/past-projects/atc/
├── 1-overview.md           # Project scope and tech stack
├── 2-Responsibilities.md   # Role and contributions
├── 3-Details-of-feature.md # Feature implementation examples
├── 4-Architecture.md       # System design and patterns
└── 5-Deployment-Strategy.md # DevOps and scaling strategies
```

### Resume Context

Understand the candidate background:

```bash
open interview/past-projects/resume.md
```

- 5+ years Java/Spring experience
- 30M user-scale platform production experience
- Microservices, PostgreSQL, MySQL, DynamoDB
- AWS, CI/CD, multi-region deployment
- Frontend: React, Next.js, Node.js

## Documentation

All documentation is in `docs/` directory:

| Document | Purpose |
|----------|---------|
| `project-overview-pdr.md` | Project goals, scope, and requirements |
| `codebase-summary.md` | Directory structure and content organization |
| `code-standards.md` | Content formatting and quality standards |
| `system-architecture.md` | How the learning system works |
| `project-roadmap.md` | Planned features and development timeline |

## FAQ

### How should I use this repository?

**Start with:** `interview/STUDY-GUIDE.md` (personalized) or `interview/4-day-study-plan.md` (intensive)

**For specific topics:** Navigate to `interview/practice/essential-interview-questions/{domain}/` and start with basic questions

**Track progress:** Mark questions as complete in `interview/checklist.md`

### Should I solve problems before looking at solutions?

Yes. The format provides blank space before the answer separator line (`=====...`). Try solving independently first, then review the solution.

### How do I validate my answers?

Use Qwen AI commands:
- SQL: `/check-answer <file-path>`
- Code: `/check-code <file-path>`
- Design: `/check-lld <file-path>`
- Generic: `/answer-interview <question>`

### Can I use this offline?

Yes! All content is local Markdown files. No internet required after cloning the repository.

### How many questions should I do per day?

**4-Day Plan:** 50-65 questions/day (see `interview/4-day-study-plan.md`)
**2-Week Plan:** 15-20 questions/day (see `interview/STUDY-GUIDE.md`)
**Self-Paced:** 10-15 questions/day for balanced learning

### Which study path should I choose?

- **< 1 week to interview:** Use 4-day intensive plan
- **1-2 weeks to interview:** Use 2-week personalized plan
- **4+ weeks to interview:** Use domain-focused learning
- **Just reviewing:** Use checklist to identify weak areas

### How do I contribute new questions?

See `docs/code-standards.md` for naming conventions and formatting. Follow the template and test with Qwen commands before committing.

## Support & Feedback

### Reporting Issues

Found an error or unclear question?
1. Document the exact file and issue
2. Note what you expected vs. what you found
3. Suggest a fix if possible
4. Create an issue in the repository

### Feature Requests

Want a new feature? See `docs/project-roadmap.md` for planned work.

### Contact

**Maintainer:** Khoa Nguyen
**Email:** [contact information]
**GitHub:** [repository URL]

## License

Educational resource for interview preparation. Free to use for personal study.

## Version

**Version:** 1.0
**Last Updated:** March 2026
**Total Content:** 400+ questions, 453+ files, 50+ MB

---

## Quick Links

- **Study Plans:** [4-Day Plan](interview/4-day-study-plan.md) | [2-Week Plan](interview/STUDY-GUIDE.md)
- **Progress:** [Master Checklist](interview/checklist.md)
- **Practice:** [SQL](interview/practice/sql/) | [Code](interview/practice/write-a-program/) | [Design](interview/practice/low-level-design/)
- **Domains:** [Java](interview/practice/essential-interview-questions/1-JAVA-CORE/) | [Spring](interview/practice/essential-interview-questions/2-Spring-Framework/) | [Database](interview/practice/essential-interview-questions/3-Database/) | [Architecture](interview/practice/essential-interview-questions/4-System-Architecture/)
- **Docs:** [Overview](docs/project-overview-pdr.md) | [Architecture](docs/system-architecture.md) | [Roadmap](docs/project-roadmap.md)

---

**Good luck with your interview preparation!**
