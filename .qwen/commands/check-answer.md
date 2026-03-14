# SQL Practice Answer Checker

**Usage:** `/check-answer <file-path>`

**Example:** `/check-answer sicpa/practice/sql/1.md`

---

## What This Command Does

When you provide a file path containing a SQL practice question and your answer, I will:

1. ✅ **Read the question** and schema from the file
2. ✅ **Analyze your SQL answer** for correctness
3. ✅ **Check for common issues**:
   - Syntax errors
   - Wrong operators (`>` vs `>=`, `IS NULL` vs `= NULL`)
   - Incorrect column/table names
   - Alias consistency
   - NULL handling
   - Case sensitivity
4. ✅ **Provide feedback** with:
   - Clear verdict (Correct / Has Issues / Wrong)
   - Visual table showing what's right/wrong
   - Example data demonstration
   - Corrected answer (if needed)
   - Alternative solutions
   - Interview tips

---

## Supported Question Files

Any file in `sicpa/practice/sql/` format:

```
sicpa/practice/sql/0.md
sicpa/practice/sql/1.md
sicpa/practice/sql/2.md
...
```

---

## Example Usage

```
/check-answer sicpa/practice/sql/1.md
```

**Expected Output:**
- Verdict on correctness
- Table of checks (syntax, logic, operators, etc.)
- Visual example of how the query works
- Alternative solutions (if applicable)
- Interview tips

---

## Quick Reference

| Command | Description |
|---------|-------------|
| `/check-answer <file>` | Check a SQL practice answer |
| `/check-answer 0.md` | Shorthand (if in same directory) |

---

**Ready to check your answers! Just provide the file path.** 🎯
