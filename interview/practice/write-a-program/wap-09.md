**9.** Find the maximum element without built-in max.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Find the Maximum Element Without Built-in Max

### Approach: Linear Scan (Optimal)

**Pattern:** Initialize with first element, compare each subsequent element.

```java
import java.util.List;

public class FindMax {

    static Integer findMax(List<Integer> list) {
        if (list == null || list.isEmpty()) {
            return null;
        }

        Integer max = null;
        for (Integer num : list) {
            if (num == null) continue;
            if (max == null || num > max) {
                max = num;
            }
        }
        return max;
    }
}
```

### Alternative: Using reduce

```java
static Integer findMaxReduce(List<Integer> list) {
    if (list == null || list.isEmpty()) return null;
    return list.stream()
        .filter(java.util.Objects::nonNull)
        .reduce(Integer.MIN_VALUE, (a, b) -> a > b ? a : b);
}
```

### Complexity

| Metric | Value |
|--------|-------|
| Time | O(n) |
| Space | O(1) |

### Edge Cases
- Null list -> return null
- Empty list -> return null
- All nulls -> return null
- Single element -> return that element
- All negative numbers -> works correctly
- Integer.MIN_VALUE in list -> works correctly

### Key Insight
Initializing `max` to `null` instead of `Integer.MIN_VALUE` avoids the edge case where all elements are null and you'd incorrectly return MIN_VALUE. Always iterate through every element — you cannot skip any since the max could be anywhere.
