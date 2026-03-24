**4.** Write a program to find all duplicate elements in a list.

```Java
import java.util.ArrayList;import java.util.HashSet;
class FindAllDuplicate {
    static List<Integer> allDuplicate(List<Integer> list) {
        if (list == null || list.size() < 2) {
            return null;
        }
        Set<Integer> seen = new HashSet<>();
        Set<Integer> duplicates = new HashSet<>();
        for (Integer num : list) {
            if (num == null) {
                continue;
            }
            if (!seen.add(num)) {
                duplicates.add(num);
            }
        }
        
        return new ArrayList<>(duplicates);

    }
}
```

========== THIS SECTION IS THE ANSWER ==========

## Solution: Find All Duplicate Elements in a List

### Approach: HashSet (Optimal)

**Pattern:** Use two sets — `seen` for tracking visited elements, `duplicates` for collecting duplicates.

```java
import java.util.*;

public class FindAllDuplicate {

    static List<Integer> allDuplicate(List<Integer> list) {
        if (list == null || list.size() < 2) {
            return Collections.emptyList();
        }

        Set<Integer> seen = new HashSet<>();
        Set<Integer> duplicates = new HashSet<>();

        for (Integer num : list) {
            if (num == null) continue;
            if (!seen.add(num)) {  // add() returns false if already present
                duplicates.add(num);
            }
        }

        return new ArrayList<>(duplicates);
    }
}
```

### Alternative: Using frequency map

```java
static List<Integer> findDuplicatesWithMap(List<Integer> list) {
    Map<Integer, Integer> freq = new HashMap<>();
    for (Integer num : list) {
        if (num != null) {
            freq.merge(num, 1, Integer::sum);
        }
    }
    return freq.entrySet().stream()
        .filter(e -> e.getValue() > 1)
        .map(Map.Entry::getKey)
        .collect(Collectors.toList());
}
```

### Complexity

| Approach | Time | Space |
|----------|------|-------|
| Two HashSets | O(n) | O(n) |
| Frequency map | O(n) | O(n) |

### Edge Cases
- No duplicates -> return empty list
- All identical elements -> return list with that single element
- Null elements -> skipped
- Empty/null list -> return empty list

### Key Insight
`Set.add()` returns `false` if the element already exists — this is the cleanest way to detect duplicates in one pass. Using a `Set<Integer>` for duplicates ensures each duplicate is reported only once even if it appears 3+ times.
