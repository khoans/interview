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
