**19.** Find the sum of digits of a number.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Find the Sum of Digits of a Number

### Approach 1: Modulo and Division (Optimal)

**Pattern:** Extract last digit with `% 10`, remove it with `/ 10`.

```java
public class DigitSum {

    static int sumOfDigits(int n) {
        n = Math.abs(n);
        int sum = 0;
        while (n > 0) {
            sum += n % 10;
            n /= 10;
        }
        return sum;
    }

    public static void main(String[] args) {
        System.out.println(sumOfDigits(12345)); // 15 (1+2+3+4+5)
        System.out.println(sumOfDigits(0));     // 0
        System.out.println(sumOfDigits(-987));  // 24 (9+8+7)
        System.out.println(sumOfDigits(100));   // 1
    }
}
```

### Approach 2: Recursive

```java
static int sumOfDigitsRecursive(int n) {
    n = Math.abs(n);
    if (n < 10) return n;
    return n % 10 + sumOfDigitsRecursive(n / 10);
}
```

### Approach 3: String and char arithmetic

```java
static int sumOfDigitsString(int n) {
    return String.valueOf(Math.abs(n)).chars()
        .map(c -> c - '0')
        .sum();
}
```

### Complexity

| Approach | Time | Space |
|----------|------|-------|
| Iterative | O(d) | O(1) |
| Recursive | O(d) | O(d) stack |
| String | O(d) | O(d) |

### Edge Cases
- n = 0 -> return 0
- Negative numbers -> use absolute value
- Single digit -> return the digit itself
- Large numbers -> no overflow risk since max digit sum for int is 9*10 = 90

### Key Insight
The `% 10` / `/ 10` pattern is fundamental for digit manipulation. It appears in many problems: digit sum, reverse number, palindrome check, Armstrong number. The iterative approach is preferred over recursive for O(1) space.
