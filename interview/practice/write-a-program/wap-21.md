**21.** Check if a string is a palindrome.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Check if a String is a Palindrome

### Approach 1: Two Pointers (Optimal)

**Pattern:** Compare characters from both ends moving inward.

```java
public class PalindromeChecker {

    static boolean isPalindrome(String s) {
        if (s == null) return false;
        if (s.length() <= 1) return true;

        int left = 0, right = s.length() - 1;
        while (left < right) {
            if (s.charAt(left) != s.charAt(right)) {
                return false;
            }
            left++;
            right--;
        }
        return true;
    }

    public static void main(String[] args) {
        System.out.println(isPalindrome("racecar")); // true
        System.out.println(isPalindrome("hello"));   // false
        System.out.println(isPalindrome("a"));       // true
        System.out.println(isPalindrome(""));        // true
    }
}
```

### Approach 2: Case-insensitive, alphanumeric only (LeetCode style)

```java
static boolean isPalindromeClean(String s) {
    if (s == null) return false;
    int left = 0, right = s.length() - 1;
    while (left < right) {
        while (left < right && !Character.isLetterOrDigit(s.charAt(left))) left++;
        while (left < right && !Character.isLetterOrDigit(s.charAt(right))) right--;
        if (Character.toLowerCase(s.charAt(left)) != Character.toLowerCase(s.charAt(right))) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
// "A man, a plan, a canal: Panama" -> true
```

### Approach 3: StringBuilder reverse

```java
static boolean isPalindromeReverse(String s) {
    if (s == null) return false;
    return s.equals(new StringBuilder(s).reverse().toString());
}
```

### Complexity

| Approach | Time | Space |
|----------|------|-------|
| Two pointers | O(n) | O(1) |
| Clean (alphanumeric) | O(n) | O(1) |
| StringBuilder reverse | O(n) | O(n) |

### Edge Cases
- Null -> return false
- Empty string -> true (trivially a palindrome)
- Single character -> true
- Even length vs odd length -> both work with two pointers
- Spaces and punctuation -> depends on problem (clean version handles this)
- Case sensitivity -> depends on problem requirements

### Key Insight
Two-pointer technique is the classic O(1) space approach. For interviews, always ask: "Should I ignore case? Ignore non-alphanumeric characters?" The clean version (Approach 2) is LeetCode #125. The two-pointer pattern here is the same one used for reversing arrays and lists.
