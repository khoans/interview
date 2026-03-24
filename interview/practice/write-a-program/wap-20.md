**20.** Reverse a given integer.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Reverse a Given Integer

### Approach: Modulo and Division

**Pattern:** Extract last digit with `% 10`, build reversed number with `* 10 +`.

```java
public class ReverseInteger {

    static int reverse(int n) {
        boolean negative = n < 0;
        long reversed = 0;
        long num = Math.abs((long) n);

        while (num > 0) {
            reversed = reversed * 10 + num % 10;
            num /= 10;
        }

        // Check overflow
        if (reversed > Integer.MAX_VALUE) return 0;

        return (int) (negative ? -reversed : reversed);
    }

    public static void main(String[] args) {
        System.out.println(reverse(12345));       // 54321
        System.out.println(reverse(-123));        // -321
        System.out.println(reverse(100));         // 1
        System.out.println(reverse(0));           // 0
        System.out.println(reverse(1534236469));  // 0 (overflow)
    }
}
```

### Alternative: String-based

```java
static int reverseString(int n) {
    boolean negative = n < 0;
    String str = new StringBuilder(String.valueOf(Math.abs((long) n)))
        .reverse().toString();
    try {
        int result = Integer.parseInt(str);
        return negative ? -result : result;
    } catch (NumberFormatException e) {
        return 0; // overflow
    }
}
```

### Complexity

| Approach | Time | Space |
|----------|------|-------|
| Math (modulo) | O(d) | O(1) |
| String | O(d) | O(d) |

### Edge Cases
- n = 0 -> return 0
- Negative numbers -> preserve the sign
- Trailing zeros (e.g., 100) -> reversed is 1 (leading zeros dropped)
- Integer overflow -> return 0 (e.g., reverse of 1534236469 overflows)
- Integer.MIN_VALUE -> handle with long cast

### Key Insight
The critical edge case is **overflow**. Reversing 1534236469 gives 9646324351 which exceeds Integer.MAX_VALUE. Use `long` for the intermediate result and check bounds before casting back to `int`. This is a classic LeetCode problem (#7). Interviewers specifically watch for overflow handling.
