**23.** Count the number of vowels in a string.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Count the Number of Vowels in a String

### Approach 1: Set Lookup (Clean)

```java
import java.util.Set;

public class VowelCounter {

    static int countVowels(String s) {
        if (s == null || s.isEmpty()) return 0;

        Set<Character> vowels = Set.of('a', 'e', 'i', 'o', 'u',
                                        'A', 'E', 'I', 'O', 'U');
        int count = 0;
        for (char c : s.toCharArray()) {
            if (vowels.contains(c)) {
                count++;
            }
        }
        return count;
    }

    public static void main(String[] args) {
        System.out.println(countVowels("hello world"));  // 3
        System.out.println(countVowels("AEIOU"));        // 5
        System.out.println(countVowels("bcdfg"));        // 0
        System.out.println(countVowels(""));             // 0
    }
}
```

### Approach 2: String contains check

```java
static int countVowelsSimple(String s) {
    if (s == null) return 0;
    int count = 0;
    for (char c : s.toLowerCase().toCharArray()) {
        if ("aeiou".indexOf(c) >= 0) {
            count++;
        }
    }
    return count;
}
```

### Approach 3: Stream API

```java
static long countVowelsStream(String s) {
    if (s == null) return 0;
    return s.chars()
        .mapToObj(c -> (char) c)
        .filter(c -> "aeiouAEIOU".indexOf(c) >= 0)
        .count();
}
```

### Complexity

| Approach | Time | Space |
|----------|------|-------|
| Set lookup | O(n) | O(1) — set is constant size |
| String indexOf | O(n) | O(n) — toLowerCase creates new string |
| Stream | O(n) | O(1) |

### Edge Cases
- Null or empty string -> return 0
- No vowels -> return 0
- All vowels -> return string length
- Mixed case -> handle both upper and lower
- Special characters/numbers -> not vowels, ignored

### Key Insight
Using a `Set` for vowel lookup gives O(1) per character check. Converting to lowercase first simplifies the check but creates a new String object. For interviews, the Set approach is cleanest and most extensible.
