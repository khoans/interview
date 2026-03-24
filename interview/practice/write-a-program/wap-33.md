**33.** Implement insertion sort.



========== THIS SECTION IS THE ANSWER ==========

## Solution

**Pattern:** Build sorted portion one element at a time by inserting each element into its correct position

```java
public class InsertionSort {

    public static void insertionSort(int[] arr) {
        if (arr == null || arr.length <= 1) return;

        for (int i = 1; i < arr.length; i++) {
            int key = arr[i];
            int j = i - 1;

            // Shift elements greater than key to the right
            while (j >= 0 && arr[j] > key) {
                arr[j + 1] = arr[j];
                j--;
            }
            arr[j + 1] = key;
        }
    }

    public static void main(String[] args) {
        int[] arr = {12, 11, 13, 5, 6};
        insertionSort(arr);
        for (int num : arr) {
            System.out.print(num + " ");  // 5 6 11 12 13
        }
    }
}
```

## How It Works
1. Start from the second element (index 1)
2. Store current element as `key`
3. Compare `key` with elements in the sorted portion (left side)
4. Shift larger elements one position right
5. Insert `key` at its correct position

## Complexity
- **Time:** O(n^2) worst/average, O(n) best (already sorted)
- **Space:** O(1) — in-place
- **Stable:** Yes
- **Adaptive:** Yes — runs faster on nearly sorted data

## Edge Cases
- Null or empty array
- Single element
- Already sorted (best case — O(n), just comparisons, no shifts)
- Reverse sorted (worst case — maximum shifts)

## Why Insertion Sort Matters
- Best among simple O(n^2) sorts for nearly-sorted data
- Used as the base case in hybrid sorts (e.g., TimSort uses insertion sort for small subarrays)
- Online algorithm — can sort as elements arrive
