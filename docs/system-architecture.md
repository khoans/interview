# Asking AI - System Architecture

## Overview

This document describes the architecture of the Asking AI interview preparation system. Since this is a learning/documentation platform rather than a software system, the "architecture" refers to how the content, tools, and workflows are organized to deliver an effective learning experience.

## System Goals

1. **Scalable Learning** — Support 400+ questions across 9 technical domains
2. **Self-Paced Progression** — Enable flexible study paths (4-day intensive or 2-week customized)
3. **Automated Feedback** — Provide instant validation via Qwen AI integration
4. **Progress Visibility** — Track completion and identify weak areas
5. **Knowledge Retention** — Reinforce learning through multiple exposures and approaches

## Architecture Layers

### Layer 1: Content Organization

**Purpose:** Structure and categorize 400+ questions for easy discovery and study

**Components:**
- **Domain Structure:** 9 technical domains (Java, Spring, Database, Architecture, etc.)
- **Topic Hierarchy:** Multi-level folders (Domain → Topic → Subtopic)
- **Reference Guides:** Domain summaries and quick-access guides
- **Professional Context:** Resume and project case studies

**Data Flow:**
```
User explores domain
    ↓
Selects topic within domain
    ↓
Accesses individual practice question
    ↓
Reads question and setup
    ↓
Solves problem (writes answer in file)
```

### Layer 2: Practice Question System

**Purpose:** Deliver individual practice questions with space for candidate response and solutions

**Components:**
- **Question Files:** 453+ individual .md files with standard structure
- **Answer Templates:** Consistent format for solutions and explanations
- **Validation Markers:** Separator between candidate space and official answer
- **Reference Fields:** Time complexity, space complexity, trade-offs

**Data Flow:**
```
Practice question file
    ├── Question text (read by candidate)
    ├── Setup/Context (schema, constraints, examples)
    ├── Answer space (15-20 lines for candidate solution)
    ├── Separator (========== THIS SECTION IS THE ANSWER ==========)
    └── Solution (code, explanation, alternatives, trade-offs)
```

**File Structure Pattern:**
```markdown
**N.** Question text

[Setup/Context if needed]

[15-20 blank lines for candidate answer]

========== THIS SECTION IS THE ANSWER ==========
[Complete solution with:]
- Main approach
- Code example (if applicable)
- Time/Space complexity
- Alternative approaches
- Key insights and trade-offs
```

### Layer 3: AI-Assisted Validation

**Purpose:** Provide instant feedback on candidate answers using Qwen AI commands

**Components:**
- **4 Custom Commands:** `/check-answer`, `/check-code`, `/check-lld`, `/answer-interview`
- **Response Guidelines:** Instructions for AI behavior in `.qwen/instructions.md`
- **Integration Points:** Command definitions in `.qwen/commands/`

**Validation Flow:**
```
Candidate writes answer in practice file
    ↓
Runs Qwen command: /check-answer <file>
    ↓
Qwen AI validates against:
    - Syntax correctness
    - Logic correctness
    - Edge case handling
    - Best practices compliance
    - Performance considerations
    ↓
Returns feedback with:
    - Corrections (if needed)
    - Suggestions for improvement
    - Reference to official solution
    ↓
Candidate reviews, learns, and iterates
```

**Command Mapping:**

| Command | Input | Validation Type | Feedback Type |
|---------|-------|---|---|
| `/check-answer sql.md` | SQL query | Syntax, Logic, NULL handling | Specific corrections |
| `/check-code wap.md` | Java/Algorithm | Correctness, Complexity, Edge cases | Test cases, optimization |
| `/check-lld lld.md` | Design pattern | SOLID, Extensibility, Design | Refactoring suggestions |
| `/answer-interview "Q?"` | Text question | N/A (Generation mode) | Senior-level answer |

### Layer 4: Progress Tracking

**Purpose:** Track completion, identify weak areas, and maintain learner motivation

**Components:**
- **Master Checklist:** 200-question completion tracker in `interview/checklist.md`
- **Category Breakdown:** Progress by technical domain
- **Daily Tracking:** Per-day completion statistics
- **Weak Areas Section:** Notes on topics requiring more study

**Tracking Data:**
```
Master Checklist (interview/checklist.md)
├── Total Questions: 200 (5 categories × 40 core questions)
├── Category 1: SQL (50 questions)
│   └── Progress: [ ] [ ] [ ] ... (40 questions tracked)
├── Category 2: Write a Program (50 questions)
│   └── Progress: [ ] [ ] [ ] ... (40 questions tracked)
├── Category 3: Database Design (50 questions)
│   └── Progress: [ ] [ ] [ ] ... (40 questions tracked)
├── Category 4: Low-Level Design (50 questions)
│   └── Progress: [ ] [ ] [ ] ... (40 questions tracked)
├── Daily Tracking: Date, Question, Time spent, Difficulty
├── Weak Areas: Topics requiring additional study
└── Statistics: % Complete, Trending topics
```

### Layer 5: Study Planning

**Purpose:** Provide structured, goal-oriented study paths with clear milestones

**Components:**
- **4-Day Intensive Plan:** `interview/4-day-study-plan.md` — 200 core questions in 48-52 hours
- **2-Week Customized Plan:** `interview/STUDY-GUIDE.md` — Priority-based learning path
- **Question References:** Domain summaries (sql-practice.md, write-a-program.md, etc.)
- **Core Q&A Guide:** `interview/questions.md` — 52 frequently-asked questions

**Study Path Structure:**
```
Choose Study Duration
    ├─→ 4-Day Intensive
    │   ├─→ Day 1: SQL + Coding Foundations (50 questions, 12-13h)
    │   ├─→ Day 2: Coding + DB Design Intro (65 questions, 12-13h)
    │   ├─→ Day 3: Write Program + DB Design (50 questions, 12-13h)
    │   └─→ Day 4: Full Review + Mock Interview (35 questions, 12-13h)
    │
    └─→ 2-Week Customized
        ├─→ Priority 1: Java/Spring (40% of time, 100+ questions)
        ├─→ Priority 2: Database (25% of time, 89+ questions)
        ├─→ Priority 3: Architecture (20% of time, 50 questions)
        ├─→ Priority 4: Kafka (10% of time, 50 questions)
        └─→ Priority 5: DevOps (5% of time, 32 questions)
```

### Layer 6: Knowledge Integration

**Purpose:** Connect individual questions to broader knowledge areas and professional experience

**Components:**
- **Essential Interview Questions:** 350+ questions across 9 domains
- **Professional Case Study:** 5-file deep-dive on automotive marketplace (30M user-scale)
- **Resume Documentation:** Full candidate background and experience
- **Cross-References:** Links between related concepts and questions

**Integration Points:**
```
Core Technical Knowledge
├── Java Core (44 files)
│   └── Referenced by: Spring questions, Algorithm questions
├── Spring Framework (38 files)
│   └── Referenced by: Architecture questions, Security questions
├── Database (39 files)
│   └── Referenced by: Design pattern questions (DAO), Query optimization
├── System Architecture (50 files)
│   └── Referenced by: Design patterns, Microservices approaches
├── DevOps (32 files)
│   └── Referenced by: Deployment, CI/CD practices
├── Design Patterns (50 files)
├── Testing (50 files)
├── Security (50 files)
└── Kafka (50 files)

Professional Experience (Case Study)
├── Automotive Marketplace (30M users/month)
├── System design decisions
├── Scaling approaches
├── Technology choices
└── Lessons learned
```

## Workflow Architectures

### Workflow 1: Question-Focused Learning

**Used for:** Deep dive into specific topics during study sessions

```
1. Learner navigates to domain/topic
   Example: interview/practice/essential-interview-questions/3-Database/1-Indexing/

2. Opens practice question file
   Example: eip-101.md (Indexing question 1)

3. Reads question and setup
   - Table structure
   - Problem statement
   - Example data

4. Solves problem independently
   - Writes answer in file
   - Uses 15-20 blank lines provided
   - References no external materials

5. (Optional) Runs Qwen validation
   /check-answer eip-101.md

6. Reviews feedback and solution
   - Reads provided answer
   - Compares approaches
   - Notes key insights

7. Updates progress checklist
   - Marks question as complete
   - Records time spent
   - Notes if weak area

8. Moves to next question
```

### Workflow 2: Domain-Based Learning

**Used for:** Systematic coverage of a technical area

```
1. Learner selects domain
   Example: 1-JAVA-CORE

2. Reviews domain overview
   - Core concepts
   - Question distribution
   - Recommended order

3. Starts with subtopic 1
   Example: 1-OOP-Concepts (eiq-01 to eiq-10)

4. Works through all questions in order
   - Progressive difficulty
   - Builds foundational understanding
   - All prerequisites covered by sequential order

5. Completes domain assessment
   - All subtopics completed
   - Weak areas identified
   - Ready for integration with other domains

6. Moves to next domain
```

### Workflow 3: Intensive Study Plan

**Used for:** 4-day rapid preparation before interview

```
1. Learner starts 4-day study plan
   (interview/4-day-study-plan.md)

2. Day 1: SQL + Coding Foundations
   - SQL practice (sql-0.md to sql-50.md): 50 questions
   - Coding interview easy (coding-interview-easy.md): 25 questions
   - Focus on fundamentals
   - Time: 12-13 hours

3. Day 2: Coding + DB Design Intro
   - Write a program (wap-01.md to wap-25.md): 25 questions
   - Database design (db-design.md 1-25): 25 questions
   - Database questions (essential-interview-questions): 15 questions
   - Time: 12-13 hours

4. Day 3: Write Program + DB Design
   - Write a program (wap-26.md to wap-50.md): 25 questions
   - Database design (db-design.md 26-50): 25 questions
   - Time: 12-13 hours

5. Day 4: Full Review + Mock Interview
   - Review weak areas from previous days
   - Complete remaining questions
   - Full mock interview simulation
   - Time: 12-13 hours

6. Total: 200 questions in 48-52 hours
```

### Workflow 4: Personalized Study Path

**Used for:** 2-week preparation with priority weighting

```
1. Learner reviews personalized guide
   (interview/STUDY-GUIDE.md)

2. Allocates time by priority:
   - Priority 1 (40%): Java/Spring — 100+ questions
     Questions eiq-01 to eip-100 range
     Estimated: 16-20 hours

   - Priority 2 (25%): Database — 89+ questions
     Questions eip-101 to eip-138 range + SQL practice
     Estimated: 10-12 hours

   - Priority 3 (20%): Architecture — 50 questions
     Questions eip-151 to eip-200 range
     Estimated: 8-10 hours

   - Priority 4 (10%): Kafka — 50 questions
     Questions eip-701 to eip-750 range
     Estimated: 4-5 hours

   - Priority 5 (5%): DevOps — 32 questions
     Questions eip-301 to eip-332 range
     Estimated: 2-3 hours

3. Studies in priority order within 2-week window
4. Uses AI commands for validation as needed
5. Adjusts priorities based on weak areas
```

### Workflow 5: AI-Assisted Feedback Loop

**Used for:** Getting help from Qwen AI within any workflow

```
1. Learner completes practice question

2. Runs appropriate Qwen command
   - SQL: /check-answer practice/sql/sql-5.md
   - Code: /check-code practice/write-a-program/wap-03.md
   - Design: /check-lld practice/low-level-design/lld-02.md
   - Interview: /answer-interview "What's a Singleton?"

3. Qwen AI analyzes answer against standards:
   - Syntax correctness
   - Logic validity
   - Edge case handling
   - Performance considerations
   - Best practices alignment

4. Receives feedback:
   - ✅ Correct with suggestions for optimization
   - ❌ Incorrect with specific corrections
   - ⚠️ Partially correct with clarifications

5. Iterates on answer:
   - Updates file with improvements
   - Re-runs validation if needed
   - Compares final answer to official solution

6. Continues to next question
```

## Data Organization Patterns

### Domain Hierarchy Pattern

```
practice/
└── essential-interview-questions/
    ├── 1-JAVA-CORE/
    │   ├── 1-OOP-Concepts/
    │   │   └── eiq-01.md, eiq-02.md, ... (7 files)
    │   ├── 2-Java-Basics/
    │   │   └── eip-11.md, eip-12.md, ... (10 files)
    │   └── ... (5 subtopics total)
    ├── 2-Spring-Framework/
    │   ├── 1-Spring-Core/
    │   │   └── eip-51.md, eip-52.md, ... (12 files)
    │   └── ... (5 subtopics)
    ├── 3-Database/
    ├── 4-System-Architecture/
    ├── 5-DevOps/
    ├── 6-Design-Patterns/
    ├── 7-Testing/
    ├── 8-Security/
    └── 9-Kafka/
```

**Navigation Principle:** User can navigate by:
- Domain (9 main folders)
- Topic within domain (3-5 subfolders per domain)
- Individual question (filename with sequential number)

### Question File Pattern

```markdown
**N.** Question text

[Optional: Setup/Context/Constraints]

[15-20 blank lines for candidate answer]

========== THIS SECTION IS THE ANSWER ==========

**Approach 1:** [Description]
```[code]
[Code example]
```
=> [Explanation]

**Approach 2:** [Alternative]
[...Alternative solution...]

**Key Insights:**
- [Insight 1]
- [Insight 2]

**Complexity:** O(n) time, O(1) space
```

**Parsing Principle:** AI can identify sections by separators; candidates see answer space clearly marked

## Integration Architecture

### Integration Point 1: Qwen AI Commands

**File:** `.qwen/commands/`
- `check-sql-answer.md`: SQL validation logic
- `check-wap-code.md`: Code solution checking logic
- `check-lld.md`: Design pattern review logic
- `answer-interview.md`: Interview answer generation logic

**Integration Flow:**
```
Candidate writes in .md file
    ↓
Runs: /check-answer path/to/file.md
    ↓
Qwen reads file content
    ↓
Parses question vs. candidate answer vs. official solution
    ↓
Applies validation rules from `.qwen/instructions.md`
    ↓
Returns feedback in chat
    ↓
Candidate updates file and re-runs (optional)
```

### Integration Point 2: IntelliJ IDEA

**Files:** `.idea/`
- `checkstyle-idea.xml`: Code style rules
- `google-java-format.xml`: Formatting configuration
- `intellij-javadocs-4.0.1.xml`: Documentation generation

**Integration:**
- Markdown preview for reading questions
- File explorer for navigating domains
- Search/Find for question discovery
- Integrated terminal for Qwen commands

### Integration Point 3: Git Version Control

**Files:** `.gitignore`, Git repository
- All .md files tracked
- Configuration files tracked
- System files ignored (`.idea/` runtime, OS files)

**Integration:**
- Change history for all questions
- Branch support for experimental questions
- Rollback capability if answer changes

## Scalability Considerations

### Current Scale
- **400+ questions** across **9 domains**
- **453+ markdown files**
- **50KB-5MB total storage** (all text)

### Growth Paths

**Horizontal Growth (More Questions):**
- Add new questions to existing domains
- Extend numbering: sql-41.md, wap-12.md, lld-05.md
- Maintain folder structure

**Vertical Growth (More Domains):**
- Add new domain folder: `10-Advanced-Topics/`
- Create subtopics within new domain
- Update reference guides

**Deep Growth (More Resources):**
- Add video links or diagram references
- Create multi-file explanations
- Add practice projects

### Practical Limits
- **Per folder:** 100+ files manageable
- **Total questions:** 1000+ feasible with current structure
- **File size:** Keep under 5MB for IDE performance
- **Navigation depth:** 3 levels optimal (domain/topic/question)

## Performance & Reliability

### Content Delivery
- All content in local Markdown files
- No server dependencies
- Instant access and offline capability
- Git-based version control

### Validation System
- Qwen AI commands work asynchronously
- Feedback generation in < 30 seconds typically
- Can work offline with cached responses
- Multiple validation approaches supported

### Progress Tracking
- Local checklist file in repository
- No cloud sync required
- Manual updates maintain accuracy
- Can use Git to track changes over time

## Security & Privacy

### Data Protection
- All content in local files (no cloud storage)
- Git repository for backup and history
- No personal data collected
- Solutions and answers are canonical (not user-specific)

### Code Safety
- All code examples reviewed and tested
- SQL examples execute safely (read-only or idempotent)
- Java code follows security best practices
- Design pattern examples follow OWASP guidelines

## Extensibility

### Adding New Question Categories
1. Create new folder in `practice/`
2. Define naming convention (prefix)
3. Create reference guide file
4. Add to checklist
5. Document in QWEN.md

### Adding New Qwen Commands
1. Create new command file in `.qwen/commands/`
2. Define command syntax and behavior
3. Document in `.qwen/instructions.md`
4. Test with sample question files

### Adding Validation Rules
1. Update `.qwen/instructions.md`
2. Define what "correct" means for new question type
3. Specify feedback format
4. Document in `code-standards.md`

## Summary

The Asking AI system architecture is designed around **layered content organization** with **AI-assisted validation** and **flexible study workflows**. It scales horizontally (more questions) and vertically (more domains) while remaining simple enough for local management and offline access. The separation of concerns (content, validation, tracking, planning) allows independent evolution of each component.

Key architectural decisions:
- **Markdown-first:** Simple, portable, version-control friendly
- **Qwen-integrated:** Automated feedback without backend services
- **Locally stored:** Privacy, offline access, zero infrastructure
- **Git-tracked:** Full history, branching, collaboration support
- **Structured but flexible:** Supports 4-day intensive and 2-week customized paths
