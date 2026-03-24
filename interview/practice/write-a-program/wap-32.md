**32.** Implement selection sort.



========== THIS SECTION IS THE ANSWER ==========

## Solution

**Pattern:** Find minimum element in unsorted portion, swap with first unsorted position

```java
public class SelectionSort {

    public static void selectionSort(int[] arr) {
        if (arr == null || arr.length <= 1) return;

        int n = arr.length;
        for (int i = 0; i < n - 1; i++) {
            int minIdx = i;
            for (int j = i + 1; j < n; j++) {
                if (arr[j] < arr[minIdx]) {
                    minIdx = j;
                }
            }
            if (minIdx != i) {
                int temp = arr[i];
                arr[i] = arr[minIdx];
                arr[minIdx] = temp;
            }
        }
    }

    public static void main(String[] args) {
        int[] arr = {64, 25, 12, 22, 11};
        selectionSort(arr);
        for (int num : arr) {
            System.out.print(num + " ");  // 11 12 22 25 64
        }
    }
}
```

## How It Works
1. Divide array into sorted (left) and unsorted (right) portions
2. Find minimum element in unsorted portion
3. Swap it with the first element of unsorted portion
4. Move boundary one position right
5. Repeat until entire array is sorted

## Complexity
- **Time:** O(n^2) for all cases (always scans entire unsorted portion)
- **Space:** O(1) — in-place
- **Stable:** No (swapping can change relative order of equal elements)

## Edge Cases
- Null or empty array
- Single element
- Already sorted array (still O(n^2) — no early termination)
- All elements equal

## Comparison with Bubble Sort
- Selection sort does fewer swaps: O(n) swaps vs O(n^2) for bubble sort
- Bubble sort can terminate early if sorted; selection sort cannot
- Both are O(n^2) but selection sort is better when writes are expensive
