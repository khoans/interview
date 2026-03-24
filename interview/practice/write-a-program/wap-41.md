**41.** Print the Fibonacci series up to n terms.



========== THIS SECTION IS THE ANSWER ==========

## Approach 1: Iterative (Optimal)

```java
public class Fibonacci {

    public static void printFibonacci(int n) {
        if (n <= 0) return;

        long a = 0, b = 1;
        for (int i = 0; i < n; i++) {
            System.out.print(a + " ");
            long next = a + b;
            a = b;
            b = next;
        }
        System.out.println();
    }

    public static void main(String[] args) {
        printFibonacci(10);
        // 0 1 1 2 3 5 8 13 21 34
    }
}
```

## Approach 2: Recursive (for interview discussion)

```java
public static long fibonacci(int n) {
    if (n <= 0) return 0;
    if (n == 1) return 1;
    return fibonacci(n - 1) + fibonacci(n - 2);
}
```

## Approach 3: Memoized Recursive (Top-down DP)

```java
import java.util.HashMap;
import java.util.Map;

public static long fibMemo(int n, Map<Integer, Long> memo) {
    if (n <= 0) return 0;
    if (n == 1) return 1;
    if (memo.containsKey(n)) return memo.get(n);

    long result = fibMemo(n - 1, memo) + fibMemo(n - 2, memo);
    memo.put(n, result);
    return result;
}
```

## Complexity

| Approach | Time | Space |
|----------|------|-------|
| Iterative | O(n) | O(1) |
| Recursive | O(2^n) | O(n) call stack |
| Memoized | O(n) | O(n) |

## Edge Cases
- n = 0 → print nothing
- n = 1 → print "0"
- n = 2 → print "0 1"
- Large n → use `long` instead of `int` (overflow at ~47th term for int)
- Negative n → handle gracefully

## Interview Tip
Always mention the exponential time complexity of naive recursion and how memoization or iteration fixes it. This shows understanding of dynamic programming.
