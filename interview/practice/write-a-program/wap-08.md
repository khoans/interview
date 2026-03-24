**8.** Write a program to find the product of all elements in a list.


```Java
import java.util.Objects;public class FindProductOfAll {
    static Integer findProductOfAllElements(List<Integer> list) {
        if (list == null) {
            return null;
        }
        return list.stream().filter(Objects::nonNull).reduce(1,(a,b) -> a*b);
    }
}
```

========== THIS SECTION IS THE ANSWER ==========

## Solution: Find the Product of All Elements in a List

### Approach 1: Stream API

```java
import java.util.*;

public class FindProductOfAll {

    static Integer findProductOfAllElements(List<Integer> list) {
        if (list == null) return null;
        return list.stream()
            .filter(Objects::nonNull)
            .reduce(1, (a, b) -> a * b);
    }
}
```

### Approach 2: Loop

```java
static long productLoop(List<Integer> list) {
    if (list == null) return 0;
    long product = 1;
    for (Integer num : list) {
        if (num != null) product *= num;
    }
    return product;
}
```

### Complexity

| Metric | Value |
|--------|-------|
| Time | O(n) |
| Space | O(1) |

### Edge Cases
- Null list -> return null
- Empty list -> return 1 (identity for multiplication)
- Contains zero -> product is 0
- Null elements -> skip
- Integer overflow -> use `long` or `BigInteger` for large products
- Negative numbers -> handled correctly (product sign follows multiplication rules)

### Key Insight
Identity element for multiplication is 1, not 0. If using `reduce(0, ...)` the result would always be 0. Overflow is a bigger risk with products than sums — consider `long` or `BigInteger` for production code.
