**2.** Write a program to find the second smallest element in a list.

```Java
public class FindSecondSmallest {
    static Integer findSecondSmallest(List<Integer> list){
        if (list == null || list.size() < 2) {
            return null;
        }
        
        Integer first = Integer.MAX_VALUE;
        Integer second = Integer.MAX_VALUE;
        
        for (Integer num : list) {
            if ( num == null) {
                continue;
            }
            
            if (num < first) {
                second = first;
                first = num;
            } else if (num > first && num < second) {
                second = num;
            }
        }
        return second == Integer.MAX_VALUE ? null : second;
    }
}
```

========== THIS SECTION IS THE ANSWER ==========

## Solution: Find the Second Smallest Element in a List

### Approach: Single Pass (Optimal)

**Pattern:** Mirror of second-largest — track first and second smallest.

```java
import java.util.List;

public class FindSecondSmallest {

    static Integer findSecondSmallest(List<Integer> list) {
        if (list == null || list.size() < 2) {
            return null;
        }

        Integer first = Integer.MAX_VALUE;
        Integer second = Integer.MAX_VALUE;

        for (Integer num : list) {
            if (num == null) continue;

            if (num < first) {
                second = first;
                first = num;
            } else if (num > first && num < second) {
                second = num;
            }
        }
        return second == Integer.MAX_VALUE ? null : second;
    }
}
```

### Complexity

| Metric | Value |
|--------|-------|
| Time | O(n) |
| Space | O(1) |

### Edge Cases
- All identical elements -> return null (no distinct second smallest)
- Null elements -> skipped
- Fewer than 2 elements -> return null
- Negative numbers -> handled correctly

### Key Insight
Same pattern as second-largest but reversed comparisons. When `num < first`, the old first becomes the new second. The `num > first` check prevents duplicates.
