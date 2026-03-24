**1.** Write a program to find the second largest element in a list. (Original question 45)

```java
public class SecondLargestElement {
    
    Integer secondLargest(List<Integer> list) {
        if (list == null || list.size() < 2 ) {
            return null;
        }
        
        Integer first = Integer.MIN_VALUE;
        Integer second = Integer.MIN_VALUE;
        
        for (Integer num : list) {
            if (num == null) {
                continue;
            }
            
            if (num > first) {
                second = first;
                first = num;
            } else if (num > second && num < first) {
                second = num;
            }
            
        }
        return second == Integer.MIN_VALUE ? null : second;
    }
}
```

========== THIS SECTION IS THE ANSWER ==========

## Solution: Find the Second Largest Element in a List

### Approach 1: Single Pass (Optimal)

**Pattern:** Track two variables — first and second largest.

```java
import java.util.List;

public class SecondLargestElement {

    static Integer secondLargest(List<Integer> list) {
        if (list == null || list.size() < 2) {
            return null;
        }

        Integer first = Integer.MIN_VALUE;
        Integer second = Integer.MIN_VALUE;

        for (Integer num : list) {
            if (num == null) continue;

            if (num > first) {
                second = first;
                first = num;
            } else if (num > second && num < first) {
                second = num;
            }
        }
        return second == Integer.MIN_VALUE ? null : second;
    }
}
```

### Approach 2: Sort then pick (Simpler but slower)

```java
import java.util.*;
import java.util.stream.*;

static Integer secondLargestSort(List<Integer> list) {
    if (list == null || list.size() < 2) return null;
    List<Integer> sorted = list.stream()
        .filter(Objects::nonNull)
        .distinct()
        .sorted(Comparator.reverseOrder())
        .collect(Collectors.toList());
    return sorted.size() < 2 ? null : sorted.get(1);
}
```

### Complexity

| Approach | Time | Space |
|----------|------|-------|
| Single pass | O(n) | O(1) |
| Sort | O(n log n) | O(n) |

### Edge Cases
- List with all identical elements (no second largest) -> return null
- List with nulls -> skip nulls
- List with fewer than 2 elements -> return null
- Negative numbers -> works correctly since we compare values

### Key Insight
The single-pass approach is optimal. When `num > first`, the old `first` becomes `second`. The `num < first` check in the else-if prevents duplicates from being counted as second largest.
