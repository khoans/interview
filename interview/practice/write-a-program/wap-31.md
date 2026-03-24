**31.** Implement bubble sort.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Bubble Sort with Early Termination

**Pattern:** Comparison-based sorting, repeatedly swap adjacent elements

```java
public class BubbleSort {

    public static void bubbleSort(int[] arr) {
        if (arr == null || arr.length <= 1) return;

        int n = arr.length;
        for (int i = 0; i < n - 1; i++) {
            boolean swapped = false;
            for (int j = 0; j < n - 1 - i; j++) {
                if (arr[j] > arr[j + 1]) {
                    int temp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = temp;
                    swapped = true;
                }
            }
            if (!swapped) break;  // array already sorted
        }
    }

    public static void main(String[] args) {
        int[] arr = {64, 34, 25, 12, 22, 11, 90};
        bubbleSort(arr);
        for (int num : arr) {
            System.out.print(num + " ");  // 11 12 22 25 34 64 90
        }
    }
}
```

## How It Works
1. Compare each pair of adjacent elements
2. Swap them if they are in the wrong order
3. After each pass, the largest unsorted element "bubbles up" to its correct position
4. Optimization: if no swaps in a pass, array is sorted — break early

## Complexity
- **Time:** O(n^2) worst/average, O(n) best (already sorted with early termination)
- **Space:** O(1) — in-place sorting
- **Stable:** Yes (equal elements preserve their relative order)

## Edge Cases
- Null or empty array
- Single element array
- Already sorted array (early termination kicks in)
- Reverse sorted array (worst case)
- Array with duplicates
