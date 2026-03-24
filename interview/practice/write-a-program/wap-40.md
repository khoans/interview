**40.** Merge two sorted arrays into one sorted array.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Two Pointer Merge

**Pattern:** Two Pointers (same technique used in merge sort's merge step)

```java
public class MergeSortedArrays {

    public static int[] merge(int[] a, int[] b) {
        if (a == null) return b;
        if (b == null) return a;

        int[] result = new int[a.length + b.length];
        int i = 0, j = 0, k = 0;

        while (i < a.length && j < b.length) {
            if (a[i] <= b[j]) {
                result[k++] = a[i++];
            } else {
                result[k++] = b[j++];
            }
        }

        // Copy remaining elements
        while (i < a.length) {
            result[k++] = a[i++];
        }
        while (j < b.length) {
            result[k++] = b[j++];
        }

        return result;
    }

    public static void main(String[] args) {
        int[] a = {1, 3, 5, 7};
        int[] b = {2, 4, 6, 8, 10};
        int[] merged = merge(a, b);
        for (int num : merged) {
            System.out.print(num + " ");  // 1 2 3 4 5 6 7 8 10
        }
    }
}
```

## In-place Variant (Merge into first array — LeetCode 88 style)

```java
// arr1 has enough space at end to hold arr2
public static void mergeInPlace(int[] arr1, int m, int[] arr2, int n) {
    int i = m - 1;
    int j = n - 1;
    int k = m + n - 1;

    while (i >= 0 && j >= 0) {
        if (arr1[i] > arr2[j]) {
            arr1[k--] = arr1[i--];
        } else {
            arr1[k--] = arr2[j--];
        }
    }
    while (j >= 0) {
        arr1[k--] = arr2[j--];
    }
}
```

## Complexity
- **Time:** O(m + n)
- **Space:** O(m + n) for new array version, O(1) for in-place version

## Edge Cases
- One or both arrays null
- One or both arrays empty
- Arrays of different lengths
- Duplicate elements across arrays
- One array entirely smaller than the other
