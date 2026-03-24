**3.** Write a program to find the nth largest element in a list.

```Java
import java.util.PriorityQueue;

public class NthLargest {
    static Integer findNthLargest(List<Integer> list, int n) {
        if (list == null || list.size() < n) {
            return null;
        }
        
        PriorityQueue<Integer> heap = new PriorityQueue<>(n);
        
        for (Integer num : list) {
            if (num == null) {
                continue;
            }
            if (heap.size() < n) {
                heap.offer(num);
            } else if (num > heap.peek()) {
                heap.poll();
                heap.offer(num);
            }
            
        }
        
        return heap.size() < n ? null : heap.peek();
    }
}
```

Bonus: find the smallest Nth

```Java
import java.util.PriorityQueue;
public class findSmallestNth {
    static Integer findSmallestNth(List<Integer> list, int n) {
        if (list == null || list.size() < n) {
            return null;
        }

        PriorityQueue<Integer> heap = new PriorityQueue<>(n,(a,b) -> Integer.compare(b,a));

        for (Integer num : list) {
            if (num == null) {
                continue;
            }
            if (heap.size() < n) {
                heap.offer(num);
            } else if (num < heap.peek()) {
                heap.poll();
                heap.offer(num);
            }
        }

        return heap.size() < n ? null : heap.peek();
    }
}
```

========== THIS SECTION IS THE ANSWER ==========

## Solution: Find the Nth Largest Element in a List

### Approach 1: Min-Heap of size N (Optimal for streaming)

**Pattern:** Use a min-heap (PriorityQueue) of size n. The top of the heap is the nth largest.

```java
import java.util.*;

public class NthLargest {

    static Integer findNthLargest(List<Integer> list, int n) {
        if (list == null || n <= 0 || list.size() < n) {
            return null;
        }

        PriorityQueue<Integer> heap = new PriorityQueue<>(n); // min-heap

        for (Integer num : list) {
            if (num == null) continue;
            if (heap.size() < n) {
                heap.offer(num);
            } else if (num > heap.peek()) {
                heap.poll();
                heap.offer(num);
            }
        }

        return heap.size() < n ? null : heap.peek();
    }
}
```

### Approach 2: QuickSelect (Optimal average case)

```java
static int quickSelect(int[] arr, int lo, int hi, int k) {
    int pivot = arr[hi];
    int i = lo;
    for (int j = lo; j < hi; j++) {
        if (arr[j] <= pivot) {
            int tmp = arr[i]; arr[i] = arr[j]; arr[j] = tmp;
            i++;
        }
    }
    int tmp = arr[i]; arr[i] = arr[hi]; arr[hi] = tmp;

    if (i == k) return arr[i];
    else if (i < k) return quickSelect(arr, i + 1, hi, k);
    else return quickSelect(arr, lo, i - 1, k);
}

// Usage: nth largest = quickSelect(arr, 0, arr.length - 1, arr.length - n)
```

### Complexity

| Approach | Time (avg) | Time (worst) | Space |
|----------|-----------|-------------|-------|
| Min-Heap | O(n log k) | O(n log k) | O(k) |
| QuickSelect | O(n) | O(n^2) | O(1) |
| Sort | O(n log n) | O(n log n) | O(n) |

### Edge Cases
- n > list size -> return null
- n <= 0 -> return null
- Duplicate elements -> heap handles naturally
- Null elements -> filtered out

### Key Insight
Min-heap of size k keeps the k largest elements seen so far. The root (smallest in the heap) is the kth largest overall. QuickSelect is faster on average but has worse worst-case. For interviews, the heap approach is safer and easier to explain.
