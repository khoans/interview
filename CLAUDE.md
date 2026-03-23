# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Asking AI** is a markdown-only interview preparation repository targeting Senior Java Backend Developer roles. It contains 400+ practice questions across 9 technical domains with custom Qwen CLI commands for automated answer validation. There is no build process, no source code to compile, and no tests to run.

## Repository Structure

```
interview/                          # All interview content lives here
├── STUDY-GUIDE.md                 # 2-week personalized study plan
├── 4-day-study-plan.md            # 4-day intensive schedule
├── checklist.md                   # 200-question progress tracker
├── questions.md                   # 52 actual interview questions (Vietnamese)
├── past-projects/                 # Resume + project case studies
│   ├── resume.md
│   └── atc/                       # Automotive marketplace (30M users)
└── practice/                      # 400+ Q&A files
    ├── essential-interview-questions/   # ~350 files in 9 domains
    │   ├── 1-JAVA-CORE/           # OOP, Basics, Collections, Java8, Advanced
    │   ├── 2-Spring-Framework/    # Core, Boot, MVC, JPA, Advanced
    │   ├── 3-Database/            # Indexing, Query Optimization, ACID, Normalization
    │   ├── 4-System-Architecture/ # Basics, Microservices, Resilience, Scalability
    │   ├── 5-DevOps/              # CI/CD, Docker, Git, Monitoring
    │   ├── 6-Design-Patterns/     # Creational, Structural, Behavioral
    │   ├── 7-Testing/             # Unit, Mocking, Integration, Best Practices
    │   ├── 8-Security/            # Auth, Web Security, Spring Security
    │   └── 9-Kafka/               # Basics, Producers, Internals, Guarantees
    ├── sql/                       # 42 SQL practice files
    ├── write-a-program/           # 10 coding solution files
    ├── low-level-design/          # 5 LLD files
    ├── database-design/           # 1 DB design file
    └── situational-questions/     # Behavioral interview prep
```

## File Naming Conventions

- OOP questions: `eiq-NN.md` (e.g., `eiq-01.md`)
- Other essential questions: `eip-NN.md` (e.g., `eip-11.md`)
- SQL: `sql-N.md` (e.g., `sql-0.md`)
- Write a Program: `wap-NN.md` (e.g., `wap-01.md`)
- Low-Level Design: `lld-NN.md` (e.g., `lld-01.md`)

## Q&A File Format

All practice files follow this structure:

```markdown
**N.** Question text here

[blank space for user's answer]

========== THIS SECTION IS THE ANSWER ==========
[Official answer/solution]
```

## Custom AI Commands (Qwen)

Defined in `.qwen/commands/`:
- `/check-answer <file>` — Validate SQL answers (syntax, logic, NULL handling)
- `/check-code <file>` — Review code solutions (correctness, complexity, edge cases)
- `/check-lld <file>` — Check LLD designs (patterns, SOLID, extensibility)
- `/answer-interview <question>` — Generate senior-level interview answers

## Key Context

- Owner is a 5+ year Java/Spring developer who worked on a 30M user/month US automotive marketplace
- Content targets SICPA and similar senior backend interviews
- `interview/questions.md` contains real interview questions in Vietnamese
- The `interview/` directory was originally named `sicpa/` — some internal references may still use the old name
- `QWEN.md` at root contains the full project context document for Qwen AI
