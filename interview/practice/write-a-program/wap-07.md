**7.** Write a program to find the sum of all elements in a list.

```Java
public class SumOfAllElement {
    static Integer sumOfAll(List<Integer> list) {
        if (list == null) {
            return null;
        }
        return list.stream().filter(Objects::nonNull).reduce(0,(curr, val) -> curr + val);
    }
}
```

========== THIS SECTION IS THE ANSWER ==========

## Solution: Find the Sum of All Elements in a List

### Approach 1: Stream API

```java
import java.util.*;

public class SumOfAllElement {

    static Integer sumOfAll(List<Integer> list) {
        if (list == null) return null;
        return list.stream()
            .filter(Objects::nonNull)
            .reduce(0, Integer::sum);
    }
}
```

### Approach 2: Enhanced for loop

```java
static int sumLoop(List<Integer> list) {
    if (list == null) return 0;
    int sum = 0;
    for (Integer num : list) {
        if (num != null) sum += num;
    }
    return sum;
}
```

### Approach 3: mapToInt (avoids boxing)

```java
static int sumMapToInt(List<Integer> list) {
    if (list == null) return 0;
    return list.stream()
        .filter(Objects::nonNull)
        .mapToInt(Integer::intValue)
        .sum();
}
```

### Complexity

| Metric | Value |
|--------|-------|
| Time | O(n) |
| Space | O(1) |

### Edge Cases
- Null list -> return null or 0
- Empty list -> return 0
- Null elements -> skip
- Integer overflow -> use `long` for large sums

### Key Insight
`mapToInt().sum()` is more efficient than `reduce()` because it avoids auto-boxing. For production code, consider using `long` to avoid integer overflow.
