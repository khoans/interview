**5.** Write a program to remove all duplicates from a list.

```Java
import java.util.ArrayList;import java.util.HashSet;public class removeAllDuplicates {
    static List<Integer> removeAllDuplicate(List<Integer> list) {
        Set<Integer> set = new HashSet<>(list);
        return new ArrayList<>(set);

    }
}
```

========== THIS SECTION IS THE ANSWER ==========

## Solution: Remove All Duplicates from a List

### Approach 1: LinkedHashSet (Preserves order)

```java
import java.util.*;

public class RemoveAllDuplicates {

    static List<Integer> removeDuplicates(List<Integer> list) {
        if (list == null) return null;
        return new ArrayList<>(new LinkedHashSet<>(list));
    }
}
```

### Approach 2: Stream API

```java
static List<Integer> removeDuplicatesStream(List<Integer> list) {
    if (list == null) return null;
    return list.stream().distinct().collect(Collectors.toList());
}
```

### Approach 3: Manual (No built-in set)

```java
static List<Integer> removeDuplicatesManual(List<Integer> list) {
    if (list == null) return null;
    List<Integer> result = new ArrayList<>();
    Set<Integer> seen = new HashSet<>();
    for (Integer num : list) {
        if (seen.add(num)) {
            result.add(num);
        }
    }
    return result;
}
```

### Complexity

| Approach | Time | Space | Preserves Order? |
|----------|------|-------|-----------------|
| HashSet | O(n) | O(n) | No |
| LinkedHashSet | O(n) | O(n) | Yes |
| Stream distinct() | O(n) | O(n) | Yes |
| Manual with Set | O(n) | O(n) | Yes |

### Edge Cases
- Null list -> return null
- Empty list -> return empty list
- All duplicates -> return list with single element
- Null elements in list -> preserved (HashSet allows one null)

### Key Insight
`LinkedHashSet` is preferred over `HashSet` because it preserves insertion order. `stream().distinct()` also preserves order. If order doesn't matter, plain `HashSet` is fine.
