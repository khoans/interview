**39.** Rotate an array by k positions.



========== THIS SECTION IS THE ANSWER ==========

## Approach 1: Reversal Algorithm (Optimal)

**Pattern:** Three reversals — reverse all, reverse first k, reverse rest

```java
public class RotateArray {

    // Right rotation by k positions
    public static void rotate(int[] arr, int k) {
        if (arr == null || arr.length <= 1) return;

        int n = arr.length;
        k = k % n;  // handle k > n
        if (k == 0) return;

        reverse(arr, 0, n - 1);       // reverse entire array
        reverse(arr, 0, k - 1);       // reverse first k elements
        reverse(arr, k, n - 1);       // reverse remaining elements
    }

    private static void reverse(int[] arr, int start, int end) {
        while (start < end) {
            int temp = arr[start];
            arr[start] = arr[end];
            arr[end] = temp;
            start++;
            end--;
        }
    }

    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5, 6, 7};
        rotate(arr, 3);
        for (int num : arr) {
            System.out.print(num + " ");  // 5 6 7 1 2 3 4
        }
    }
}
```

## Approach 2: Extra Array

```java
public static void rotateWithExtra(int[] arr, int k) {
    if (arr == null || arr.length <= 1) return;

    int n = arr.length;
    k = k % n;
    int[] temp = new int[n];

    for (int i = 0; i < n; i++) {
        temp[(i + k) % n] = arr[i];
    }
    System.arraycopy(temp, 0, arr, 0, n);
}
```

## How Reversal Works (Example: rotate right by 3)
```
Original:  [1, 2, 3, 4, 5, 6, 7]
Reverse all: [7, 6, 5, 4, 3, 2, 1]
Reverse [0..2]: [5, 6, 7, 4, 3, 2, 1]
Reverse [3..6]: [5, 6, 7, 1, 2, 3, 4]
```

## Complexity

| Approach | Time | Space |
|----------|------|-------|
| Reversal | O(n) | O(1) |
| Extra array | O(n) | O(n) |

## Edge Cases
- Null or empty array
- k = 0 (no rotation)
- k = n (full rotation, same as original)
- k > n (use k % n)
- Negative k (left rotation: convert to right rotation as `k = n - |k|`)
