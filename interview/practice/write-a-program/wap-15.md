**15.** Find the GCD of two numbers.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Find the GCD (Greatest Common Divisor) of Two Numbers

### Approach 1: Euclidean Algorithm (Optimal)

**Pattern:** Repeatedly replace the larger number with remainder until one becomes 0.

```java
public class GCD {

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

    public static void main(String[] args) {
        System.out.println(gcd(12, 8));   // 4
        System.out.println(gcd(54, 24));  // 6
        System.out.println(gcd(17, 5));   // 1 (coprime)
        System.out.println(gcd(0, 5));    // 5
    }
}
```

### Approach 2: Recursive Euclidean

```java
static int gcdRecursive(int a, int b) {
    a = Math.abs(a);
    b = Math.abs(b);
    if (b == 0) return a;
    return gcdRecursive(b, a % b);
}
```

### Approach 3: Brute force

```java
static int gcdBrute(int a, int b) {
    a = Math.abs(a);
    b = Math.abs(b);
    int min = Math.min(a, b);
    for (int i = min; i >= 1; i--) {
        if (a % i == 0 && b % i == 0) return i;
    }
    return 1;
}
```

### Complexity

| Approach | Time | Space |
|----------|------|-------|
| Euclidean (iterative) | O(log(min(a,b))) | O(1) |
| Euclidean (recursive) | O(log(min(a,b))) | O(log(min(a,b))) stack |
| Brute force | O(min(a,b)) | O(1) |

### Edge Cases
- One number is 0 -> GCD is the other number
- Both are 0 -> GCD is 0 (by convention)
- Negative numbers -> use absolute values
- Equal numbers -> GCD is that number
- Coprime numbers (GCD = 1) -> e.g., gcd(7, 13) = 1

### Key Insight
The Euclidean algorithm is based on the property that `gcd(a, b) = gcd(b, a % b)`. It converges in O(log n) steps because the remainder decreases by at least half each iteration. This is one of the oldest algorithms in mathematics (circa 300 BC).
