**10.** Find the minimum element without built-in min.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Find the Minimum Element Without Built-in Min

### Approach: Linear Scan (Optimal)

**Pattern:** Mirror of find-max — initialize with first element, compare each subsequent element.

```java
import java.util.List;

public class FindMin {

    static Integer findMin(List<Integer> list) {
        if (list == null || list.isEmpty()) {
            return null;
        }

        Integer min = null;
        for (Integer num : list) {
            if (num == null) continue;
            if (min == null || num < min) {
                min = num;
            }
        }
        return min;
    }
}
```

### Alternative: Using reduce

```java
static Integer findMinReduce(List<Integer> list) {
    if (list == null || list.isEmpty()) return null;
    return list.stream()
        .filter(java.util.Objects::nonNull)
        .reduce(Integer.MAX_VALUE, Math::min);
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
- All positive numbers -> works correctly
- Integer.MAX_VALUE in list -> works correctly

### Key Insight
Same pattern as find-max with reversed comparison. Must check every element. Initializing with `null` is safer than `Integer.MAX_VALUE` to correctly handle edge cases where all elements are null.
