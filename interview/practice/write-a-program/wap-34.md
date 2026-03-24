**34.** Implement linear search.



========== THIS SECTION IS THE ANSWER ==========

## Solution

```java
public class LinearSearch {

    public static int linearSearch(int[] arr, int target) {
        if (arr == null) return -1;

        for (int i = 0; i < arr.length; i++) {
            if (arr[i] == target) {
                return i;
            }
        }
        return -1;  // not found
    }

    // Generic version for any Comparable type
    public static <T> int linearSearch(T[] arr, T target) {
        if (arr == null || target == null) return -1;

        for (int i = 0; i < arr.length; i++) {
            if (target.equals(arr[i])) {
                return i;
            }
        }
        return -1;
    }

    public static void main(String[] args) {
        int[] arr = {10, 20, 80, 30, 60, 50, 110, 100, 130};

        System.out.println(linearSearch(arr, 110));  // 6
        System.out.println(linearSearch(arr, 175));  // -1

        String[] names = {"Alice", "Bob", "Charlie"};
        System.out.println(linearSearch(names, "Bob"));  // 1
    }
}
```

## Complexity
- **Time:** O(n) worst/average, O(1) best (first element)
- **Space:** O(1)

## Edge Cases
- Null array
- Empty array
- Element not present
- Element at first or last position
- Multiple occurrences (returns first index)
- Null target for generic version

## When to Use
- Unsorted data (cannot use binary search)
- Small datasets where O(n) is acceptable
- Linked lists (no random access for binary search)
- Single lookup (sorting + binary search costs O(n log n) overhead)
