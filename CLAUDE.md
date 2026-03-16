# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a **documentation-only interview preparation repository** for Java backend developer interviews. It contains 200+ practice questions in Markdown across five categories. There is no build process, no tests, and no runnable code — only Markdown files with questions, answers, and solutions.

## Content Structure

All practice content lives under `interview/`:

- `interview/practice/sql/sql-N.md` — 50 SQL questions (SELECT, JOINs, subqueries, aggregation)
- `interview/practice/write-a-program/wap-NN.md` — 50 coding problems in Java (arrays, strings, sorting, linked lists)
- `interview/practice/low-level-design/lld-NN.md` — LLD/design pattern questions (SOLID, GoF patterns)
- `interview/practice/essential-interview-questions/` — OOP concepts (`eiq-NN.md`) and Java basics (`eip-NN.md`)
- `interview/practice/database-design/` — DB schema design scenarios
- `interview/practice/situational-questions/` — Behavioral interview questions
- `interview/checklist.md` — Master progress tracker for all 200 questions
- `interview/4-day-study-plan.md` — Intensive study schedule

## Custom Slash Commands

Four commands in `.claude/commands/` for practicing:

| Command | Purpose | Input |
|---------|---------|-------|
| `/check-sql-answer` | Validate SQL answers | File path to a `sql-N.md` |
| `/check-wap-code` | Validate coding solutions | File path to a `wap-NN.md` |
| `/check-lld` | Review LLD designs; provides full solution if answer is empty | File path to a `lld-NN.md` |
| `/answer-interview` | Generate senior-level spoken interview answers | A question string |

## Answer File Format

All practice files follow this structure:

```markdown
**N.** Question text here

```java
// User's solution code
```

========== THIS SECTION IS THE ANSWER ==========
[Solution/explanation]
```

## Key Conventions

- **Primary language:** Java (solutions use Java unless otherwise specified)
- **Answer style for `/answer-interview`:** Senior engineer tone — direct answer first, then trade-offs, real-world examples (no code blocks, no textbook definitions, no generic student/animal examples)
- **LLD checker behavior:** If no answer is submitted, it generates a complete solution and **writes it back to the same file**. If an answer exists, it only provides feedback without modifying the file.
- File naming: `sql-N.md` (0-indexed), `wap-NN.md`, `lld-NN.md`, `eiq-NN.md`, `eip-NN.md`
