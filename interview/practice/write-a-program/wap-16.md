**16.** Find the LCM of two numbers.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Find the LCM (Least Common Multiple) of Two Numbers

### Approach: Using GCD (Optimal)

**Pattern:** `LCM(a, b) = |a * b| / GCD(a, b)`

```java
public class LCM {

    static int gcd(int a, int b) {
        a = Math.abs(a);
        b = Math.abs(b);
        while (b != 0) {
            int temp = b;
            b = a % b;
            a = temp;
        }
        return a;
    }

    static long lcm(int a, int b) {
        if (a == 0 || b == 0) return 0;
        // Divide first to avoid overflow: a / gcd * b instead of a * b / gcd
        return Math.abs((long) a / gcd(a, b) * b);
    }

    public static void main(String[] args) {
        System.out.println(lcm(12, 8));   // 24
        System.out.println(lcm(4, 6));    // 12
        System.out.println(lcm(3, 7));    // 21 (coprime -> a * b)
        System.out.println(lcm(0, 5));    // 0
    }
}
```

### Alternative: Brute force

```java
static int lcmBrute(int a, int b) {
    a = Math.abs(a);
    b = Math.abs(b);
    if (a == 0 || b == 0) return 0;
    int max = Math.max(a, b);
    for (int i = max; ; i += max) {
        if (i % a == 0 && i % b == 0) return i;
    }
}
```

### Complexity

| Approach | Time | Space |
|----------|------|-------|
| Using GCD | O(log(min(a,b))) | O(1) |
| Brute force | O(a * b / gcd) | O(1) |

### Edge Cases
- One or both numbers are 0 -> LCM is 0
- Negative numbers -> use absolute values
- Equal numbers -> LCM equals that number
- Coprime numbers -> LCM = a * b
- Overflow risk -> divide before multiply: `a / gcd(a,b) * b`

### Key Insight
The formula `LCM(a, b) = |a * b| / GCD(a, b)` leverages the Euclidean GCD algorithm. To avoid integer overflow, always divide first: `a / gcd(a,b) * b` instead of `a * b / gcd(a,b)`. Using `long` for the return type adds extra safety.
