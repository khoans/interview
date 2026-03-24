**50.** Find the intersection of two sorted arrays.



========== THIS SECTION IS THE ANSWER ==========

## Approach 1: Two Pointers (Optimal)

**Pattern:** Two Pointers — similar to merge step of merge sort

```java
import java.util.ArrayList;
import java.util.List;

public class ArrayIntersection {

    public static List<Integer> intersection(int[] a, int[] b) {
        List<Integer> result = new ArrayList<>();
        if (a == null || b == null) return result;

        int i = 0, j = 0;
        while (i < a.length && j < b.length) {
            if (a[i] == b[j]) {
                // Avoid duplicates in result
                if (result.isEmpty() || result.get(result.size() - 1) != a[i]) {
                    result.add(a[i]);
                }
                i++;
                j++;
            } else if (a[i] < b[j]) {
                i++;
            } else {
                j++;
            }
        }
        return result;
    }

    public static void main(String[] args) {
        int[] a = {1, 2, 3, 4, 5, 6};
        int[] b = {2, 4, 6, 8};
        System.out.println(intersection(a, b));  // [2, 4, 6]

        int[] c = {1, 2, 2, 3};
        int[] d = {2, 2, 3, 4};
        System.out.println(intersection(c, d));  // [2, 3]
    }
}
```

## Approach 2: With duplicates allowed (return all common occurrences)

```java
public static List<Integer> intersectionWithDuplicates(int[] a, int[] b) {
    List<Integer> result = new ArrayList<>();
    if (a == null || b == null) return result;

    int i = 0, j = 0;
    while (i < a.length && j < b.length) {
        if (a[i] == b[j]) {
            result.add(a[i]);
            i++;
            j++;
        } else if (a[i] < b[j]) {
            i++;
        } else {
            j++;
        }
    }
    return result;
}
// Input: [1,2,2,3], [2,2,3,4] → Output: [2,2,3]
```

## Approach 3: Binary Search (when one array is much larger)

```java
public static List<Integer> intersectionBinarySearch(int[] small, int[] large) {
    List<Integer> result = new ArrayList<>();
    for (int num : small) {
        if (java.util.Arrays.binarySearch(large, num) >= 0) {
            if (result.isEmpty() || result.get(result.size() - 1) != num) {
                result.add(num);
            }
        }
    }
    return result;
}
```

## Complexity

| Approach | Time | Space |
|----------|------|-------|
| Two Pointers | O(m + n) | O(min(m,n)) for result |
| Binary Search | O(m log n) | O(min(m,n)) |

Use binary search when `m << n` (one array is much smaller).

## Edge Cases
- One or both arrays null/empty
- No common elements
- Identical arrays
- Duplicate elements (clarify: unique intersection or include duplicates?)
- One array is a subset of the other
- Arrays of very different sizes
