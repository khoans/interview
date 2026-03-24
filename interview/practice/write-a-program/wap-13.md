**13.** Check if a number is an Armstrong number.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Check if a Number is an Armstrong Number

An Armstrong number (narcissistic number) is a number where the sum of its digits each raised to the power of the number of digits equals the number itself. Example: 153 = 1^3 + 5^3 + 3^3.

### Approach: Digit Extraction

```java
public class ArmstrongChecker {

    static boolean isArmstrong(int n) {
        if (n < 0) return false;

        int original = n;
        int numDigits = String.valueOf(n).length();
        int sum = 0;

        int temp = n;
        while (temp > 0) {
            int digit = temp % 10;
            sum += Math.pow(digit, numDigits);
            temp /= 10;
        }

        return sum == original;
    }

    public static void main(String[] args) {
        // Armstrong numbers: 0, 1, 2, ..., 9, 153, 370, 371, 407, 1634
        System.out.println(isArmstrong(153));  // true: 1^3 + 5^3 + 3^3 = 153
        System.out.println(isArmstrong(370));  // true: 3^3 + 7^3 + 0^3 = 370
        System.out.println(isArmstrong(9474)); // true: 9^4 + 4^4 + 7^4 + 4^4 = 9474
        System.out.println(isArmstrong(123));  // false
    }
}
```

### Alternative: Without String conversion for digit count

```java
static boolean isArmstrongPure(int n) {
    if (n < 0) return false;

    int numDigits = 0;
    int temp = n;
    while (temp > 0) {
        numDigits++;
        temp /= 10;
    }
    if (n == 0) numDigits = 1;

    long sum = 0;
    temp = n;
    while (temp > 0) {
        int digit = temp % 10;
        sum += (long) Math.pow(digit, numDigits);
        temp /= 10;
    }

    return sum == n;
}
```

### Complexity

| Metric | Value |
|--------|-------|
| Time | O(d) where d = number of digits |
| Space | O(1) |

### Edge Cases
- Single digit numbers (0-9) are all Armstrong numbers
- Negative numbers -> return false (by convention)
- 0 -> true (0^1 = 0)
- Large numbers -> use `long` for sum to avoid overflow

### Key Insight
The key operations are: (1) count digits, (2) extract each digit using `% 10` and `/ 10`, (3) raise to power and sum. The `% 10` and `/ 10` pattern for digit extraction is fundamental and reused in many number-based problems.
