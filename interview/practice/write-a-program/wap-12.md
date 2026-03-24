**12.** Print all prime numbers in a given range.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Print All Prime Numbers in a Given Range

### Approach 1: Iterate and Check (Simple)

```java
import java.util.*;

public class PrimesInRange {

    static boolean isPrime(int n) {
        if (n < 2) return false;
        if (n == 2) return true;
        if (n % 2 == 0) return false;
        for (int i = 3; i * i <= n; i += 2) {
            if (n % i == 0) return false;
        }
        return true;
    }

    static List<Integer> primesInRange(int start, int end) {
        List<Integer> primes = new ArrayList<>();
        for (int i = Math.max(2, start); i <= end; i++) {
            if (isPrime(i)) {
                primes.add(i);
            }
        }
        return primes;
    }
}
```

### Approach 2: Sieve of Eratosthenes (Optimal for large ranges)

**Pattern:** Mark all composites, remaining are primes.

```java
static List<Integer> sieveOfEratosthenes(int start, int end) {
    if (end < 2) return Collections.emptyList();

    boolean[] isComposite = new boolean[end + 1];
    isComposite[0] = true;
    isComposite[1] = true;

    for (int i = 2; i * i <= end; i++) {
        if (!isComposite[i]) {
            for (int j = i * i; j <= end; j += i) {
                isComposite[j] = true;
            }
        }
    }

    List<Integer> primes = new ArrayList<>();
    for (int i = Math.max(2, start); i <= end; i++) {
        if (!isComposite[i]) {
            primes.add(i);
        }
    }
    return primes;
}
```

### Complexity

| Approach | Time | Space |
|----------|------|-------|
| Iterate + isPrime | O((end - start) * sqrt(end)) | O(1) + output |
| Sieve of Eratosthenes | O(n log log n) | O(n) |

### Edge Cases
- start > end -> return empty list
- Range includes negatives -> start from 2
- start = end = prime -> return that single prime
- Very large range -> Sieve is much faster

### Key Insight
The Sieve of Eratosthenes is the classic algorithm for finding all primes up to n. It works by iteratively marking multiples of each prime starting from 2. Starting inner loop at `i * i` (not `2 * i`) is an optimization since smaller multiples were already marked by smaller primes. For interview, mention both approaches and explain when each is appropriate.
