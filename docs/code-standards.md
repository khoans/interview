# Asking AI - Content Standards & Conventions

## Overview

This document defines the standards for creating, formatting, and maintaining content in the Asking AI repository. Since this is a Markdown-based learning platform, the standards focus on content organization, question formatting, and answer structure rather than code style.

## Markdown Formatting Standards

### File Headers

All practice question files should begin with minimal headers for clarity:

```markdown
**N.** Question text here
```

Format:
- Use bold (`**N.**`) for the question number
- Keep question text on the same line or continue naturally
- Leave one blank line before code blocks or setup

### Section Separators

Use the answer separator to divide candidate answer space from the official solution:

```markdown
========== THIS SECTION IS THE ANSWER ==========
```

This marks the start of the provided solution. Use exactly as shown (47 equal signs for consistency).

### Code Blocks

**Java Code:**
```markdown
```java
// Your Java code here
public class Example {
    // ...
}
```
```

**SQL Code:**
```markdown
```sql
SELECT * FROM table WHERE condition;
```
```

**SQL in Question Setup:**
```markdown
```sql
TABLE employee
    id INTEGER NOT NULL PRIMARY KEY
    name VARCHAR(255) NOT NULL
```
```

**General/Pseudo Code:**
````markdown
```
// Pseudo code or algorithm description
```
````

**Rules:**
- Always specify language (java, sql, python, etc.)
- Indent code blocks with 4 spaces or backticks
- Keep code lines under 100 characters where possible
- Use meaningful variable names

### Inline Code

Use backticks for inline code references:

```markdown
The `HashMap` class uses `hashCode()` and `equals()` methods...
```

### Lists

**Ordered Lists** (for steps):
```markdown
1. First step
2. Second step
3. Third step
```

**Unordered Lists** (for options/points):
```markdown
- First point
- Second point
- Third point
```

**Definition Lists** (for concepts):
```markdown
- **Term:** Definition and explanation
- **Another Term:** More details
```

### Tables

Use pipe tables for structured comparisons:

```markdown
| Term | Definition | Example |
|------|-----------|---------|
| Row 1 | Description | Code |
| Row 2 | Description | Code |
```

**Rules:**
- Header row with `| --- |` separator
- Align columns with spaces for readability
- Use when comparing 3+ related items

### Emphasis

- **Bold** (`**text**`): Important concepts, keyword definitions
- *Italic* (`*text*`): Examples, edge cases, emphasis
- ~~Strikethrough~~ (`~~text~~`): Deprecated or incorrect approaches (rare)

## Question File Structure

### Complete Example: SQL Question

```markdown
**0.** Given the following table containing employees, some of which are managers:

```sql
TABLE employee
    id INTEGER NOT NULL PRIMARY KEY
    managerId INTEGER REFERENCES employee(id)
    name VARCHAR(255) NOT NULL
```

Which quer(ies) return the list of employees who are not managers?

[15-20 blank lines for candidate answer]



========== THIS SECTION IS THE ANSWER ==========

**Approach 1: Using NOT IN**
```sql
SELECT * FROM employee
WHERE id NOT IN (SELECT managerId FROM employee WHERE managerId IS NOT NULL)
```
=> Explanation: An employee is not a manager if their `id` never appears in the `managerId` column

**Approach 2: Using NOT EXISTS**
```sql
SELECT * FROM employee e
WHERE NOT EXISTS (SELECT 1 FROM employee m WHERE m.managerId = e.id)
```
=> Explanation: An employee is not a manager if there does not exist any employee whose `managerId` equals that employee's `id`

**Approach 3: Using LEFT JOIN**
```sql
SELECT e.* FROM employee e
LEFT JOIN employee m ON e.id = m.managerId
WHERE m.managerId IS NULL
```
=> Explanation: An employee is not a manager if there is no match in the join
```

### Complete Example: Programming Question

```markdown
**1.** Write a function to find all unique pairs in an array that sum to a target value.

**Input:** `[1, 5, 7, -1, 5]`, target = `6`
**Output:** `[[1, 5], [-1, 7]]`

[Blank lines for candidate solution]

========== THIS SECTION IS THE ANSWER ==========

```java
public List<List<Integer>> findPairs(int[] nums, int target) {
    Set<List<Integer>> result = new HashSet<>();
    Set<Integer> seen = new HashSet<>();

    for (int num : nums) {
        int complement = target - num;
        if (seen.contains(complement)) {
            result.add(Arrays.asList(Math.min(num, complement), Math.max(num, complement)));
        }
        seen.add(num);
    }

    return new ArrayList<>(result);
}
```

**Time Complexity:** O(n)
**Space Complexity:** O(n)

**Key Insights:**
- Use a HashSet to track seen numbers
- Use another HashSet to avoid duplicate pairs
- Calculate complement for each number
```

### Complete Example: Design Pattern Question

```markdown
**1.** What is the Singleton pattern? When would you use it? What are its trade-offs?

[Blank lines for candidate answer]

========== THIS SECTION IS THE ANSWER ==========

**Definition:** The Singleton pattern ensures a class has only one instance and provides a global point of access to it.

**When to Use:**
- Configuration managers (logging, database connections)
- Thread pool management
- Cache managers
- UI controllers in mobile apps

**Implementation (Thread-Safe):**
```java
public class Singleton {
    private static volatile Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

**Trade-Offs:**
- ✅ Global access point, controlled initialization
- ❌ Hard to test (depends on global state)
- ❌ Can mask design issues (hidden dependencies)
- ❌ Not thread-safe by default (requires synchronization)
```

## Question Format Guidelines

### Question Text

**Rules:**
- Write questions in clear, professional English
- Be specific: avoid ambiguous wording
- Include context when needed (table schemas, problem constraints)
- Break long questions into multiple sentences
- Use proper grammar and punctuation

**Good:** "Given a sorted array of integers and a target sum, find all unique pairs that add to the target."

**Bad:** "find pairs in array that sum to target"

### Answer Space

**Requirements:**
- Provide 15-20 blank lines for candidate response (for practical file format)
- Separator should be clearly visible
- Add a comment above separator if needed: `<!-- Write your answer above this line -->`

### Answer Section

**Must Include:**
1. **Main Answer/Solution** (required)
   - Code example if applicable
   - Multiple approaches if valid alternatives exist
   - Clear step-by-step explanation

2. **Key Insights** (for complex questions)
   - Time/Space complexity
   - Common mistakes
   - Edge cases to consider

3. **Trade-offs** (for design/architecture questions)
   - Pros and cons of the approach
   - When to use vs. alternative approaches
   - Real-world applicability

4. **Related Concepts** (optional but recommended)
   - Links to prerequisite knowledge
   - Advanced extensions
   - Common follow-up questions

## File Naming Conventions

### Practice Questions

| Category | Pattern | Example | Range |
|----------|---------|---------|-------|
| SQL Practice | `sql-N.md` | sql-0.md, sql-1.md | 0-40 |
| Write a Program | `wap-NN.md` | wap-01.md, wap-02.md | 01-11+ |
| Low-Level Design | `lld-NN.md` | lld-01.md, lld-02.md | 01-04+ |
| OOP Concepts | `eiq-NN.md` | eiq-01.md, eiq-02.md | 01-10 |
| Java Basics | `eip-NN.md` | eip-11.md, eip-12.md | 11-20 |

**Naming Rules:**
- Use lowercase only
- Use leading zeros for single digits (wap-01, not wap-1)
- Keep file names short and descriptive (avoid full question text)
- Number sequentially within each category
- Never skip numbers (no gaps in sequence unless intentional)

### Directory Names

Use kebab-case with leading numbers for ordering:

- `1-JAVA-CORE/`
- `2-Spring-Framework/`
- `3-Database/`
- `1-OOP-Concepts/` (within JAVA-CORE)
- `2-Java-Basics/`

**Rules:**
- Start with number for sort ordering
- Capitalize main words
- Separate words with hyphens
- Use consistent capitalization across similar levels

### Reference Guide Names

```markdown
sql-practice.md         # Master reference for SQL
write-a-program.md      # Master reference for coding
low-level-design.md     # Master reference for LLD
db-design.md            # Master reference for DB design
coding-interview-easy.md # Master reference for easy coding
questions.md            # Master reference for 52 core questions
```

**Purpose:** Provide consolidated overview and links to all questions in that category

## Content Quality Standards

### Correctness

- All provided answers must be technically accurate
- Code examples must be syntactically correct
- Multiple approaches should all be valid solutions
- Test answers before adding to repository

**Verification:**
- Compile Java code with javac
- Execute SQL queries against test database
- Trace through algorithm with example inputs
- Have peer review for complex topics

### Completeness

**Every question must include:**
1. Clear problem statement
2. At least one complete solution
3. Explanation of the approach
4. Time/Space complexity (for algorithms)

**Recommended additions:**
- Multiple valid approaches (showing trade-offs)
- Edge cases and how to handle them
- Code examples (unless conceptual question)
- Related concepts or advanced extensions

### Clarity

- Use active voice and imperative mood
- Avoid jargon; explain technical terms
- Use concrete examples instead of abstract descriptions
- Keep sentences concise (under 25 words)
- Organize complex answers with headers or bullet points

**Good:** "Initialize a HashMap to track seen numbers as you iterate through the array."

**Bad:** "A HashMap might be utilized for the purpose of tracking numbers that have been encountered."

### Consistency

**Code Formatting:**
- All Java code uses Google Java Format conventions
- Consistent indentation (4 spaces)
- Consistent naming conventions (camelCase for variables, PascalCase for classes)
- Consistent import ordering

**Terminology:**
- Use consistent vocabulary across all questions
- Define non-standard abbreviations at first use
- Maintain consistent style for similar question types
- Cross-reference related concepts with same naming

## Qwen AI Command Standards

### Command Format

**Pattern:**
```
/{command-name} {parameter}
```

**Standard Commands:**
- `/check-answer <file-path>` — SQL validation
- `/check-code <file-path>` — Code solution checker
- `/check-lld <file-path>` — Design pattern reviewer
- `/answer-interview <question>` — Senior-level answer generation

### Response Guidelines

Defined in `.qwen/instructions.md`:

- **Direct answers first:** Start with concise answer (1-2 sentences)
- **Business language:** Avoid technical jargon unless defining terms
- **Real-world examples:** Use production scenarios, not toy examples
- **Trade-off focus:** Discuss pros/cons, not just one solution
- **No code blocks:** For interview answers, describe verbally

### Integration Points

All practice questions are designed to work with Qwen commands:
- Separators allow easy parsing of question vs. answer
- Code is valid and compilable
- Multiple approaches show trade-offs clearly
- Complexity analysis is explicit

## Versioning & Updates

### Update Protocol

When updating existing questions:

1. **Preserve question number** — Don't renumber files
2. **Update answer only** — Keep original question text unless incorrect
3. **Add note if major change** — "Updated [date]: Added edge case handling"
4. **Maintain consistency** — Don't change style or format
5. **Test thoroughly** — Verify all code examples still work

### Adding New Questions

When adding questions to an existing category:

1. **Find next available number** — Check existing files
2. **Follow naming convention** — Use exact category prefix
3. **Use template** — Follow established format
4. **Test before commit** — Run validation commands
5. **Update reference guide** — Add entry to category reference

## Progress Tracking Standards

### Checklist Format

The `interview/checklist.md` file uses:
- Category headers with question count
- Checkbox format: `- [ ]` (unchecked) or `- [x]` (checked)
- Daily tracking tables
- Progress statistics by category

### Progress Metadata

Keep in the checklist:
- Total questions per category
- Date started/completed
- Time spent
- Weak areas for follow-up
- Notes on particularly challenging topics

## Best Practices

### For Question Authors

1. **Start with a clear problem statement** — Reader should understand the question without guessing
2. **Provide setup/context** — Include table schemas, function signatures, or problem constraints
3. **Include working space** — 15-20 blank lines before the answer section
4. **Show multiple approaches** — When applicable, show different valid solutions
5. **Add complexity analysis** — For algorithmic questions, state time/space complexity
6. **Explain trade-offs** — Compare approaches and when to use each
7. **Test your code** — Compile and run all code examples before committing
8. **Review for clarity** — Have someone else read and verify understanding

### For Learners Using the Repository

1. **Read questions before answers** — Try solving before looking at solution
2. **Understand multiple approaches** — Don't settle for just one solution
3. **Test code locally** — Run examples in your IDE to build muscle memory
4. **Use AI commands** — Get feedback on your attempts
5. **Track progress** — Update checklist regularly
6. **Revisit weak areas** — Schedule time for topics you struggle with

## Reference Standards

### Cross-References

When mentioning related concepts:
- Use section headers: `## Question 5: HashMap vs TreeMap`
- Use file paths: `See write-a-program.md for algorithm basics`
- Use relative links: `[HashMap internals](../1-JAVA-CORE/3-Collections-Framework/eip-24.md)`

### External References

When citing external resources:
- Use full URLs only for stable, authoritative sources
- Prefer official documentation (Java docs, Spring docs)
- Include source title and date if available
- Note if reference may change or require subscription

### Prerequisite Knowledge

For advanced questions, note prerequisites:

```markdown
**Prerequisite:** Understanding of Singleton pattern (see Design Patterns section)
**Related:** Factory pattern, Builder pattern
```

## Accessibility & Readability

### Plain Language

- Use simple sentences (average 15 words)
- Avoid unnecessary jargon
- Define acronyms at first use: "Common Language Runtime (CLR)"
- Use active voice: "The HashMap uses..." not "It is used by..."

### Visual Hierarchy

- Use headers to organize sections
- Use lists for multiple related items
- Use tables for comparisons
- Use code blocks for examples
- Add blank lines between sections

### Rendering

All content must render correctly in:
- GitHub Markdown preview
- Markdown IDE plugins (IntelliJ, VS Code)
- Standard Markdown viewers
- Plain text readers

## Validation Checklist

Before committing a new question:

- [ ] Filename follows naming convention
- [ ] Question is clear and unambiguous
- [ ] 15-20 blank lines provided for answer
- [ ] Separator line is exactly correct
- [ ] Answer section has complete solution
- [ ] All code is syntactically correct
- [ ] Time/Space complexity is stated (for algorithms)
- [ ] Multiple approaches shown (if applicable)
- [ ] Trade-offs are discussed
- [ ] Answer is tested and verified
- [ ] Format is consistent with similar questions
- [ ] No broken links or references
- [ ] Markdown renders correctly

## Summary

These standards ensure:
1. **Consistency** — All questions follow same format and quality
2. **Clarity** — Content is easy to understand and navigate
3. **Correctness** — All answers are verified and accurate
4. **Compatibility** — Questions work with Qwen AI commands
5. **Usability** — Content supports both quick review and deep study
