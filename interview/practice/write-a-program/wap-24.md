**24.** Count the number of words in a string.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Count the Number of Words in a String

### Approach 1: Split with regex (Clean)

```java
public class WordCounter {

    static int countWords(String s) {
        if (s == null || s.trim().isEmpty()) return 0;
        return s.trim().split("\\s+").length;
    }

    public static void main(String[] args) {
        System.out.println(countWords("Hello World"));         // 2
        System.out.println(countWords("  Hello   World  "));   // 2
        System.out.println(countWords("one"));                 // 1
        System.out.println(countWords(""));                    // 0
        System.out.println(countWords("   "));                 // 0
    }
}
```

### Approach 2: Manual counting (No regex)

```java
static int countWordsManual(String s) {
    if (s == null || s.trim().isEmpty()) return 0;

    int count = 0;
    boolean inWord = false;
    for (char c : s.toCharArray()) {
        if (Character.isWhitespace(c)) {
            if (inWord) {
                count++;
                inWord = false;
            }
        } else {
            inWord = true;
        }
    }
    if (inWord) count++; // count last word
    return count;
}
```

### Approach 3: Stream API

```java
static long countWordsStream(String s) {
    if (s == null || s.trim().isEmpty()) return 0;
    return java.util.Arrays.stream(s.trim().split("\\s+")).count();
}
```

### Complexity

| Approach | Time | Space |
|----------|------|-------|
| Split | O(n) | O(n) — creates array |
| Manual | O(n) | O(1) |
| Stream | O(n) | O(n) |

### Edge Cases
- Null or empty string -> return 0
- Only whitespace -> return 0
- Leading/trailing spaces -> trim first
- Multiple consecutive spaces -> treated as single delimiter
- Single word -> return 1
- Tabs and newlines -> `\\s+` and `isWhitespace` handle these

### Key Insight
`split("\\s+")` splits on one or more whitespace characters, handling multiple spaces, tabs, and newlines. Always `trim()` first to avoid an empty first element from leading whitespace. The manual approach with a boolean flag is the classic O(1) space solution — track transitions from whitespace to non-whitespace.
