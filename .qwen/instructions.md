# Custom Instructions for SQL Practice Answer Checker

## /check-answer Command

When the user invokes `/check-answer` with a file path, follow this checklist:

### 1. Read the File
- Read the provided file path
- Extract the question number, table schema, and the user's SQL answer

### 2. Validate the Answer

Check the following:

| Criteria | What to Look For |
|----------|------------------|
| **Correct syntax** | Valid SQL syntax |
| **Correct logic** | Matches the question requirements |
| **Correct operators** | e.g., `>` vs `>=`, `IS NULL` vs `= NULL` |
| **Correct column/table names** | Matches the schema provided |
| **Alias consistency** | If alias is defined, it should be used properly |
| **NULL handling** | Proper use of `IS NULL` / `IS NOT NULL` |
| **Case sensitivity** | Note if `LIKE` vs `ILIKE` vs `LOWER()` matters |

### 3. Provide Feedback

Structure your response as follows:

```markdown
# ✅/❌ [Verdict: Correct / Has Issues / Wrong]

```sql
[User's answer here]
```

| Check | Status |
|-------|--------|
| [Criterion 1] | ✅/❌/⚠️ |
| [Criterion 2] | ✅/❌/⚠️ |
| ... | ... |

---

## 📊 How It Works

[Show example data and how the query filters it]

---

## ⚠️ Issues Found (if any)

[List any bugs or issues with explanations]

---

## ✅ Corrected Answer (if needed)

```sql
[Corrected query]
```

---

## 🎯 Alternative Solutions (optional)

[Show other valid approaches]

---

## 💡 Interview Tips

[Relevant tips for interviews]

---

## 📝 Key Takeaway

[Summary of the main learning point]
```

### 4. Be Encouraging

- If correct: Confirm and encourage
- If has minor issues: Point out gently and explain
- If wrong: Explain the mistake clearly and provide the correct answer

### 5. Keep It Concise

- Use tables for quick visual scanning
- Use code blocks for SQL
- Use emojis for visual cues (✅ ❌ ⚠️ 💡 📊 🎯 📝)

---

## Example Response Format

See previous answer reviews for examples (sql/1.md through sql/5.md).
