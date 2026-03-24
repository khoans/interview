**17.** Check if a number is a perfect square.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Check if a Number is a Perfect Square

### Approach 1: Math.sqrt (Simple)

```java
public class PerfectSquareChecker {

    static boolean isPerfectSquare(int n) {
        if (n < 0) return false;
        int sqrt = (int) Math.sqrt(n);
        return sqrt * sqrt == n;
    }

    public static void main(String[] args) {
        System.out.println(isPerfectSquare(16));  // true (4*4)
        System.out.println(isPerfectSquare(25));  // true (5*5)
        System.out.println(isPerfectSquare(14));  // false
        System.out.println(isPerfectSquare(0));   // true (0*0)
        System.out.println(isPerfectSquare(1));   // true (1*1)
    }
}
```

### Approach 2: Binary Search (No floating point)

**Pattern:** Binary search for the square root in range [0, n].

```java
static boolean isPerfectSquareBinarySearch(long n) {
    if (n < 0) return false;
    if (n == 0) return true;

    long lo = 1, hi = n;
    while (lo <= hi) {
        long mid = lo + (hi - lo) / 2;
        long square = mid * mid;
        if (square == n) return true;
        else if (square < n) lo = mid + 1;
        else hi = mid - 1;
    }
    return false;
}
```

### Approach 3: Newton's Method (Integer square root)

```java
static boolean isPerfectSquareNewton(long n) {
    if (n < 0) return false;
    long x = n;
    while (x * x > n) {
        x = (x + n / x) / 2;
    }
    return x * x == n;
}
```

### Complexity

| Approach | Time | Space |
|----------|------|-------|
| Math.sqrt | O(1) | O(1) |
| Binary search | O(log n) | O(1) |
| Newton's method | O(log n) | O(1) |

### Edge Cases
- Negative numbers -> return false
- 0 -> true (0 * 0 = 0)
- 1 -> true (1 * 1 = 1)
- Large numbers -> use `long` to avoid overflow in `mid * mid`
- Floating point precision -> `Math.sqrt` may have rounding issues for very large numbers

### Key Insight
`Math.sqrt` is simplest but can have floating point precision issues for very large numbers. Binary search avoids floating point entirely. Always verify by squaring the result: `sqrt * sqrt == n`. For interviews, mention the binary search approach to show you understand the potential floating point pitfall.
