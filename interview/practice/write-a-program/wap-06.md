**6.** Write a program to reverse a list without using built-in reverse function.

```Java
class ReverseList {
    static List<Integer> reverseAList(List<Integer> list) {
        if (list == null || list.size() <= 1) {
            return list;
        }
        for (Integer i = 0, j = list.size() -1 ; i < j ; i++, j--) {
            swap(list, i , j);
        }
        return list;
    }
    
    static void swap(List<Integer> list, Integer i, Integer j) {
        Integer tmp = list.get(i);
        list.set(i, list.get(j));
        list.set(j, tmp);
    }
}
```

========== THIS SECTION IS THE ANSWER ==========

## Solution: Reverse a List Without Built-in Reverse

### Approach: Two Pointers (Optimal, In-place)

**Pattern:** Two pointers — one from start, one from end, swap and move inward.

```java
import java.util.List;

public class ReverseList {

    static List<Integer> reverseAList(List<Integer> list) {
        if (list == null || list.size() <= 1) {
            return list;
        }
        for (int i = 0, j = list.size() - 1; i < j; i++, j--) {
            Integer tmp = list.get(i);
            list.set(i, list.get(j));
            list.set(j, tmp);
        }
        return list;
    }
}
```

### Alternative: New list (not in-place)

```java
static List<Integer> reverseNewList(List<Integer> list) {
    if (list == null) return null;
    List<Integer> result = new ArrayList<>(list.size());
    for (int i = list.size() - 1; i >= 0; i--) {
        result.add(list.get(i));
    }
    return result;
}
```

### Complexity

| Approach | Time | Space |
|----------|------|-------|
| Two pointers (in-place) | O(n) | O(1) |
| New list | O(n) | O(n) |

### Edge Cases
- Null list -> return null
- Single element -> return as-is
- Empty list -> return empty list
- Even/odd length -> both work (two pointers meet in middle)

### Key Insight
Two-pointer swap is the classic in-place reversal pattern used for arrays, strings, and linked lists. Total swaps = n/2. This is the same pattern used in palindrome checking.
