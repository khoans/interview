# Write a Program - Code Answer Checker

**Usage:** `/check-code <file-path>`

**Example:** `/check-code sicpa/practice/write-a-program/1.md`

---

## What This Command Does

When you provide a file path containing a "Write a Program" practice question and your answer, I will:

1. ✅ **Read the question** and requirements from the file
2. ✅ **Analyze your code** for correctness
3. ✅ **Check for common issues**:
   - Syntax errors
   - Logic errors
   - Edge case handling (null, empty, negative, single element)
   - Time complexity issues
   - Space complexity issues
   - Code style and readability
4. ✅ **Provide feedback** with:
   - Clear verdict (Correct / Has Issues / Wrong)
   - Table of checks (logic, edge cases, complexity, style)
   - Example input/output demonstration
   - Corrected answer (if needed)
   - Alternative solutions
   - Interview tips

---

## Supported Question Files

Any file in `sicpa/practice/write-a-program/` format:

```
sicpa/practice/write-a-program/1.md
sicpa/practice/write-a-program/2.md
sicpa/practice/write-a-program/3.md
...
```

---

## Evaluation Criteria

### 1. Correctness (Most Important)
- [ ] Does the code solve the problem?
- [ ] Does it pass all test cases?
- [ ] Are there any logic errors?

### 2. Edge Cases
- [ ] Empty input handling
- [ ] Null/undefined handling
- [ ] Single element handling
- [ ] Negative numbers (if applicable)
- [ ] Duplicate values (if applicable)
- [ ] Maximum/minimum values

### 3. Time Complexity
- [ ] Is the solution efficient?
- [ ] Can it be optimized?
- [ ] What is the Big O notation?

### 4. Space Complexity
- [ ] Is extra space used efficiently?
- [ ] Can space be optimized?
- [ ] What is the space complexity?

### 5. Code Quality
- [ ] Meaningful variable names
- [ ] Clean, readable code
- [ ] Proper error handling
- [ ] Comments (if needed)

---

## Example Usage

```
/check-code sicpa/practice/write-a-program/1.md
```

**Expected Output:**
- Verdict on correctness
- Table of checks (logic, edge cases, complexity, style)
- Visual example of how the code works
- Time/Space complexity analysis
- Alternative solutions (if applicable)
- Interview tips

---

## Language Support

| Language | Support Level |
|----------|---------------|
| Java | ✅ Full support |
| Python | ✅ Full support |
| JavaScript | ✅ Full support |
| C++ | ✅ Full support |
| C# | ✅ Full support |
| Other | ✅ Basic support |

---

## Common Patterns Reference

| Pattern | Questions | Key Insight |
|---------|-----------|-------------|
| **Two Pointers** | Two sum, reverse, pairs | Use two indices from different ends |
| **Fast & Slow Pointers** | Middle element, cycle detection | One pointer moves faster than other |
| **Hash Map/Set** | Duplicates, anagrams, lookup | O(1) lookup time |
| **Sliding Window** | Subarray, substring | Maintain a window that slides |
| **Binary Search** | Sorted array search | Divide and conquer on sorted data |
| **Recursion** | Factorial, Fibonacci, tree | Base case + recursive case |
| **In-place** | Reverse, rotate, swap | Modify input without extra space |

---

## Complexity Cheat Sheet

| Operation | Time Complexity | Space Complexity |
|-----------|-----------------|------------------|
| Array access | O(1) | O(1) |
| Array search (unsorted) | O(n) | O(1) |
| Array search (sorted) | O(log n) | O(1) |
| Hash Map lookup | O(1) average | O(n) |
| Sorting | O(n log n) | O(log n) or O(n) |
| Nested loops | O(n²) | O(1) |
| Recursion (depth n) | O(n) or O(2ⁿ) | O(n) stack space |

---

## Quick Reference

| Command | Description |
|---------|-------------|
| `/check-code <file>` | Check a coding practice answer |
| `/check-code 1.md` | Shorthand (if in same directory) |
| `/check-code` | List all available questions |

---

## Sample Output Format

```markdown
# ✅/❌ [Verdict: Correct / Has Issues / Wrong]

```java
// Your code here
```

| Check | Status |
|-------|--------|
| Logic | ✅/❌ |
| Edge Cases | ✅/❌ |
| Time Complexity | O(n) ✅ |
| Space Complexity | O(1) ✅ |
| Code Style | ✅/❌ |

---

## 📊 How It Works

[Visual example with sample input/output]

---

## ⚠️ Issues Found (if any)

[List any bugs or issues with explanations]

---

## ✅ Corrected Answer (if needed)

```java
// Corrected code
```

---

## 🎯 Alternative Solutions

[Show other valid approaches with trade-offs]

---

## 💡 Interview Tips

[Relevant tips for this type of problem]

---

## 📝 Key Takeaway

[Summary of the main learning point]
```

---

## Tips for "Write a Program" Questions

### 1. Clarify Requirements First
- Ask about input constraints
- Ask about expected output format
- Ask about edge cases

### 2. Think Aloud
- Explain your thought process
- Discuss trade-offs before coding
- Mention time/space complexity

### 3. Start Simple
- Brute force is okay to start
- Optimize after it works
- Explain optimization思路

### 4. Test as You Go
- Use small examples
- Verify edge cases
- Walk through your code

### 5. Write Clean Code
- Meaningful variable names
- Consistent formatting
- Add comments for complex logic

---

**Ready to check your coding answers! Just provide the file path.** 🎯
