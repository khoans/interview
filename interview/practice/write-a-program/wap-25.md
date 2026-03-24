**25.** Check if two strings are anagrams.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Check if Two Strings are Anagrams

Two strings are anagrams if they contain the same characters with the same frequencies.

### Approach 1: Character Frequency Array (Optimal)

**Pattern:** Count character frequencies; anagrams have identical frequency distributions.

```java
public class AnagramChecker {

    static boolean isAnagram(String s1, String s2) {
        if (s1 == null || s2 == null) return s1 == s2;
        if (s1.length() != s2.length()) return false;

        int[] freq = new int[26]; // assuming lowercase a-z
        for (char c : s1.toLowerCase().toCharArray()) {
            freq[c - 'a']++;
        }
        for (char c : s2.toLowerCase().toCharArray()) {
            freq[c - 'a']--;
        }
        for (int count : freq) {
            if (count != 0) return false;
        }
        return true;
    }

    public static void main(String[] args) {
        System.out.println(isAnagram("listen", "silent")); // true
        System.out.println(isAnagram("hello", "world"));   // false
        System.out.println(isAnagram("Anagram", "nagaram"));// true
        System.out.println(isAnagram("rat", "car"));       // false
    }
}
```

### Approach 2: HashMap (Supports Unicode)

```java
import java.util.*;

static boolean isAnagramMap(String s1, String s2) {
    if (s1 == null || s2 == null) return s1 == s2;
    if (s1.length() != s2.length()) return false;

    Map<Character, Integer> freq = new HashMap<>();
    for (char c : s1.toLowerCase().toCharArray()) {
        freq.merge(c, 1, Integer::sum);
    }
    for (char c : s2.toLowerCase().toCharArray()) {
        freq.merge(c, -1, Integer::sum);
    }
    return freq.values().stream().allMatch(v -> v == 0);
}
```

### Approach 3: Sort and compare (Simple)

```java
static boolean isAnagramSort(String s1, String s2) {
    if (s1 == null || s2 == null) return s1 == s2;
    if (s1.length() != s2.length()) return false;

    char[] a = s1.toLowerCase().toCharArray();
    char[] b = s2.toLowerCase().toCharArray();
    java.util.Arrays.sort(a);
    java.util.Arrays.sort(b);
    return java.util.Arrays.equals(a, b);
}
```

### Complexity

| Approach | Time | Space |
|----------|------|-------|
| Frequency array | O(n) | O(1) — fixed 26-size array |
| HashMap | O(n) | O(n) |
| Sort | O(n log n) | O(n) |

### Edge Cases
- Different lengths -> immediately false (early exit optimization)
- Null strings -> handle consistently
- Case sensitivity -> normalize with toLowerCase()
- Empty strings -> two empty strings are anagrams
- Spaces and special characters -> depends on requirements
- Unicode characters -> use HashMap instead of int[26]

### Key Insight
The length check is the first and cheapest filter. The frequency array approach is optimal for ASCII — O(n) time, O(1) space (fixed 26 slots). Use HashMap when dealing with Unicode. The sort approach is simplest to code but slower at O(n log n). This is LeetCode #242. Interviewers expect the frequency counting approach.
