**22.** Reverse a string without built-in reverse.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Reverse a String Without Built-in Reverse

### Approach 1: Two Pointers with char array (Optimal)

**Pattern:** Convert to char array, swap from both ends.

```java
public class ReverseString {

    static String reverse(String s) {
        if (s == null || s.length() <= 1) return s;

        char[] chars = s.toCharArray();
        int left = 0, right = chars.length - 1;
        while (left < right) {
            char temp = chars[left];
            chars[left] = chars[right];
            chars[right] = temp;
            left++;
            right--;
        }
        return new String(chars);
    }

    public static void main(String[] args) {
        System.out.println(reverse("hello"));   // "olleh"
        System.out.println(reverse("ab"));      // "ba"
        System.out.println(reverse("a"));       // "a"
        System.out.println(reverse(""));        // ""
    }
}
```

### Approach 2: Iterate backwards

```java
static String reverseBackward(String s) {
    if (s == null) return null;
    StringBuilder sb = new StringBuilder(s.length());
    for (int i = s.length() - 1; i >= 0; i--) {
        sb.append(s.charAt(i));
    }
    return sb.toString();
}
```

### Approach 3: Recursive

```java
static String reverseRecursive(String s) {
    if (s == null || s.length() <= 1) return s;
    return s.charAt(s.length() - 1) + reverseRecursive(s.substring(0, s.length() - 1));
}
```

### Complexity

| Approach | Time | Space |
|----------|------|-------|
| Two pointers (char array) | O(n) | O(n) for char array |
| Iterate backwards | O(n) | O(n) for StringBuilder |
| Recursive | O(n^2) | O(n) stack + substring copies |

### Edge Cases
- Null -> return null
- Empty string -> return empty
- Single character -> return as-is
- Palindrome -> same string returned
- Unicode/emoji -> char-level reversal may break surrogate pairs

### Key Insight
Strings in Java are immutable, so you always need O(n) space for the result. The two-pointer swap on a char array is the most interview-friendly approach. Avoid the recursive approach in production — `substring()` creates new String objects making it O(n^2). Use `StringBuilder` for building strings iteratively.
