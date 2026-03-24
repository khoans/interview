**37.** Move all negative numbers to the beginning of an array.



========== THIS SECTION IS THE ANSWER ==========

## Approach 1: Two Pointer Partition (Optimal)

**Pattern:** Two Pointers (similar to partition step of quicksort)

```java
public class MoveNegatives {

    public static void moveNegatives(int[] arr) {
        if (arr == null || arr.length <= 1) return;

        int left = 0;
        int right = arr.length - 1;

        while (left <= right) {
            if (arr[left] < 0) {
                left++;
            } else if (arr[right] >= 0) {
                right--;
            } else {
                // arr[left] >= 0 and arr[right] < 0 → swap
                int temp = arr[left];
                arr[left] = arr[right];
                arr[right] = temp;
                left++;
                right--;
            }
        }
    }

    public static void main(String[] args) {
        int[] arr = {-12, 11, -13, -5, 6, -7, 5, -3, -6};
        moveNegatives(arr);
        for (int num : arr) {
            System.out.print(num + " ");
            // All negatives on left, positives on right
        }
    }
}
```

## Approach 2: Stable version (preserves relative order)

```java
public static void moveNegativesStable(int[] arr) {
    if (arr == null || arr.length <= 1) return;

    int insertPos = 0;
    // Collect negatives first
    int[] result = new int[arr.length];
    for (int num : arr) {
        if (num < 0) result[insertPos++] = num;
    }
    for (int num : arr) {
        if (num >= 0) result[insertPos++] = num;
    }
    System.arraycopy(result, 0, arr, 0, arr.length);
}
```

## Complexity

| Approach | Time | Space | Stable |
|----------|------|-------|--------|
| Two Pointer | O(n) | O(1) | No |
| Stable version | O(n) | O(n) | Yes |

## Edge Cases
- Null or empty array
- All negative numbers
- All positive numbers
- Array with zeros (clarify: is zero negative or positive?)
- Single element
