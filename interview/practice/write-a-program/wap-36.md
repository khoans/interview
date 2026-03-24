**36.** Sort an array of 0s, 1s, and 2s (Dutch National Flag).



========== THIS SECTION IS THE ANSWER ==========

## Optimal: Dutch National Flag Algorithm (Three-way Partition)

**Pattern:** Three Pointers

```java
public class DutchNationalFlag {

    public static void sort012(int[] arr) {
        if (arr == null || arr.length <= 1) return;

        int low = 0;
        int mid = 0;
        int high = arr.length - 1;

        while (mid <= high) {
            switch (arr[mid]) {
                case 0:
                    swap(arr, low, mid);
                    low++;
                    mid++;
                    break;
                case 1:
                    mid++;
                    break;
                case 2:
                    swap(arr, mid, high);
                    high--;
                    // don't increment mid — swapped element needs checking
                    break;
            }
        }
    }

    private static void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }

    public static void main(String[] args) {
        int[] arr = {0, 1, 2, 0, 1, 2, 1, 0};
        sort012(arr);
        for (int num : arr) {
            System.out.print(num + " ");  // 0 0 0 1 1 1 2 2
        }
    }
}
```

## How It Works
- **low:** boundary for 0s (everything before `low` is 0)
- **mid:** current element being examined
- **high:** boundary for 2s (everything after `high` is 2)
- Elements between `low` and `mid` are 1s

**Invariant at all times:**
- `arr[0..low-1]` = all 0s
- `arr[low..mid-1]` = all 1s
- `arr[mid..high]` = unprocessed
- `arr[high+1..n-1]` = all 2s

## Brute Force Alternative
Simply count occurrences of 0, 1, 2 and overwrite — but requires two passes.

## Complexity
- **Time:** O(n) — single pass
- **Space:** O(1) — in-place
- **Stable:** No

## Edge Cases
- Null or empty array
- All same elements
- Already sorted
- Only two distinct values
