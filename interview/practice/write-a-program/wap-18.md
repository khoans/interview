**18.** Count the number of digits in an integer.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Count the Number of Digits in an Integer

### Approach 1: Division Loop (Classic)

```java
public class DigitCounter {

    static int countDigits(int n) {
        if (n == 0) return 1;
        n = Math.abs(n); // handle negatives
        int count = 0;
        while (n > 0) {
            count++;
            n /= 10;
        }
        return count;
    }

    public static void main(String[] args) {
        System.out.println(countDigits(12345));  // 5
        System.out.println(countDigits(0));      // 1
        System.out.println(countDigits(-987));   // 3
        System.out.println(countDigits(7));      // 1
    }
}
```

### Approach 2: String conversion

```java
static int countDigitsString(int n) {
    return String.valueOf(Math.abs(n)).length();
}
```

### Approach 3: Logarithm

```java
static int countDigitsLog(int n) {
    if (n == 0) return 1;
    return (int) Math.floor(Math.log10(Math.abs(n))) + 1;
}
```

### Complexity

| Approach | Time | Space |
|----------|------|-------|
| Division loop | O(d) where d = digits | O(1) |
| String conversion | O(d) | O(d) |
| Logarithm | O(1) | O(1) |

### Edge Cases
- n = 0 -> return 1 (zero has one digit)
- Negative numbers -> use absolute value, count digits of magnitude
- Integer.MIN_VALUE -> careful with Math.abs (overflow), use long
- Single digit -> return 1

### Key Insight
The division loop (`n /= 10`) is the most common interview approach. Each division removes one digit. The logarithm approach is O(1) but has floating point precision risks. For `Integer.MIN_VALUE`, `Math.abs()` overflows since `|-2147483648| > 2147483647` — cast to `long` first.
