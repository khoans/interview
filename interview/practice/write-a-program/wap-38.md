**38.** Find all pairs in an array that sum to a given target.



========== THIS SECTION IS THE ANSWER ==========

## Approach 1: HashSet (Optimal)

**Pattern:** Hash Map / Complement Lookup

```java
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

public class PairSum {

    public static List<int[]> findPairs(int[] arr, int target) {
        List<int[]> result = new ArrayList<>();
        if (arr == null || arr.length < 2) return result;

        Set<Integer> seen = new HashSet<>();
        Set<Integer> usedComplements = new HashSet<>();

        for (int num : arr) {
            int complement = target - num;
            if (seen.contains(complement) && !usedComplements.contains(num)) {
                result.add(new int[]{Math.min(num, complement),
                                     Math.max(num, complement)});
                usedComplements.add(num);
                usedComplements.add(complement);
            }
            seen.add(num);
        }
        return result;
    }

    public static void main(String[] args) {
        int[] arr = {1, 5, 7, -1, 5};
        List<int[]> pairs = findPairs(arr, 6);
        for (int[] pair : pairs) {
            System.out.println(pair[0] + ", " + pair[1]);
            // Output: 1, 5  and  -1, 7
        }
    }
}
```

## Approach 2: Sorting + Two Pointers

```java
import java.util.Arrays;

public static List<int[]> findPairsTwoPointer(int[] arr, int target) {
    List<int[]> result = new ArrayList<>();
    if (arr == null || arr.length < 2) return result;

    Arrays.sort(arr);
    int left = 0, right = arr.length - 1;

    while (left < right) {
        int sum = arr[left] + arr[right];
        if (sum == target) {
            result.add(new int[]{arr[left], arr[right]});
            left++;
            right--;
            // Skip duplicates
            while (left < right && arr[left] == arr[left - 1]) left++;
            while (left < right && arr[right] == arr[right + 1]) right--;
        } else if (sum < target) {
            left++;
        } else {
            right--;
        }
    }
    return result;
}
```

## Complexity

| Approach | Time | Space |
|----------|------|-------|
| HashSet | O(n) | O(n) |
| Two Pointers | O(n log n) | O(1) — excluding sort |

## Edge Cases
- Null or empty array
- No valid pairs exist
- Duplicate elements (e.g., [3,3] with target 6)
- Negative numbers
- Single element array
