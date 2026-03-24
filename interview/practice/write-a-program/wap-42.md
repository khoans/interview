**42.** Find the factorial of a number using recursion.



========== THIS SECTION IS THE ANSWER ==========

## Approach 1: Recursion

```java
public class Factorial {

    public static long factorial(int n) {
        if (n < 0) throw new IllegalArgumentException("Negative number");
        if (n == 0 || n == 1) return 1;
        return n * factorial(n - 1);
    }

    public static void main(String[] args) {
        System.out.println(factorial(5));   // 120
        System.out.println(factorial(0));   // 1
        System.out.println(factorial(10));  // 3628800
    }
}
```

## Approach 2: Tail-recursive style (iterative for comparison)

```java
public static long factorialIterative(int n) {
    if (n < 0) throw new IllegalArgumentException("Negative number");

    long result = 1;
    for (int i = 2; i <= n; i++) {
        result *= i;
    }
    return result;
}
```

## Approach 3: Using BigInteger for large numbers

```java
import java.math.BigInteger;

public static BigInteger factorialBig(int n) {
    if (n < 0) throw new IllegalArgumentException("Negative number");

    BigInteger result = BigInteger.ONE;
    for (int i = 2; i <= n; i++) {
        result = result.multiply(BigInteger.valueOf(i));
    }
    return result;
}
```

## Complexity
- **Time:** O(n)
- **Space:** O(n) recursive (call stack), O(1) iterative

## Edge Cases
- n = 0 → return 1 (0! = 1 by definition)
- n = 1 → return 1
- Negative numbers → throw exception
- Large n → `long` overflows at n = 21 (use BigInteger)
- Stack overflow for very large n with recursion

## Interview Tip
Note that Java does NOT optimize tail recursion. For production code, iterative is preferred. The recursive version is shown because the question specifically asks for it.
