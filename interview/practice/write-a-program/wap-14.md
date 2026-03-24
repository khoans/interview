**14.** Check if a number is a perfect number.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Check if a Number is a Perfect Number

A perfect number equals the sum of its proper divisors (excluding itself). Example: 6 = 1 + 2 + 3.

### Approach 1: Check divisors up to sqrt(n) (Optimal)

```java
public class PerfectNumberChecker {

    static boolean isPerfect(int n) {
        if (n <= 1) return false;

        int sum = 1; // 1 is always a divisor
        for (int i = 2; i * i <= n; i++) {
            if (n % i == 0) {
                sum += i;
                if (i != n / i) { // avoid counting sqrt twice
                    sum += n / i;
                }
            }
        }

        return sum == n;
    }

    public static void main(String[] args) {
        System.out.println(isPerfect(6));    // true: 1+2+3 = 6
        System.out.println(isPerfect(28));   // true: 1+2+4+7+14 = 28
        System.out.println(isPerfect(496));  // true
        System.out.println(isPerfect(12));   // false: 1+2+3+4+6 = 16
    }
}
```

### Approach 2: Brute force (Simple)

```java
static boolean isPerfectBrute(int n) {
    if (n <= 1) return false;
    int sum = 0;
    for (int i = 1; i < n; i++) {
        if (n % i == 0) sum += i;
    }
    return sum == n;
}
```

### Complexity

| Approach | Time | Space |
|----------|------|-------|
| Sqrt optimization | O(sqrt(n)) | O(1) |
| Brute force | O(n) | O(1) |

### Edge Cases
- n <= 1 -> return false (1 has no proper divisors except itself)
- n = 0 or negative -> return false
- Perfect squares -> careful not to count sqrt twice

### Key Insight
When finding divisors, if `i` divides `n`, then `n/i` is also a divisor. So we only need to check up to sqrt(n) and add both `i` and `n/i`. Must check `i != n/i` to avoid double-counting when n is a perfect square. Known perfect numbers: 6, 28, 496, 8128.
