**35.** Implement binary search (assume sorted array).



========== THIS SECTION IS THE ANSWER ==========

## Approach 1: Iterative (Preferred)

**Pattern:** Divide and Conquer

```java
public class BinarySearch {

    public static int binarySearch(int[] arr, int target) {
        if (arr == null || arr.length == 0) return -1;

        int left = 0;
        int right = arr.length - 1;

        while (left <= right) {
            int mid = left + (right - left) / 2;  // avoid integer overflow

            if (arr[mid] == target) {
                return mid;
            } else if (arr[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return -1;
    }

    public static void main(String[] args) {
        int[] arr = {2, 3, 4, 10, 40};
        System.out.println(binarySearch(arr, 10));  // 3
        System.out.println(binarySearch(arr, 5));   // -1
    }
}
```

## Approach 2: Recursive

```java
public static int binarySearchRecursive(int[] arr, int target,
                                         int left, int right) {
    if (left > right) return -1;

    int mid = left + (right - left) / 2;

    if (arr[mid] == target) return mid;
    if (arr[mid] < target) return binarySearchRecursive(arr, target, mid + 1, right);
    return binarySearchRecursive(arr, target, left, mid - 1);
}
```

## Complexity

| Approach | Time | Space |
|----------|------|-------|
| Iterative | O(log n) | O(1) |
| Recursive | O(log n) | O(log n) — call stack |

## Key Details
- **`left + (right - left) / 2`** prevents integer overflow vs `(left + right) / 2`
- **`left <= right`** — use `<=` not `<`, otherwise single-element case fails
- Array MUST be sorted

## Edge Cases
- Null or empty array
- Single element array
- Target smaller than all elements
- Target larger than all elements
- Target not in array
- Duplicate elements (returns any matching index, not necessarily first)
