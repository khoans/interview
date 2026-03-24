**45.** Implement a queue using arrays.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Circular Queue

```java
public class ArrayQueue {

    private int[] data;
    private int front;
    private int rear;
    private int size;
    private int capacity;

    public ArrayQueue(int capacity) {
        this.capacity = capacity;
        this.data = new int[capacity];
        this.front = 0;
        this.rear = -1;
        this.size = 0;
    }

    public void enqueue(int value) {
        if (isFull()) {
            throw new RuntimeException("Queue is full");
        }
        rear = (rear + 1) % capacity;  // circular wrap-around
        data[rear] = value;
        size++;
    }

    public int dequeue() {
        if (isEmpty()) {
            throw new RuntimeException("Queue is empty");
        }
        int value = data[front];
        front = (front + 1) % capacity;  // circular wrap-around
        size--;
        return value;
    }

    public int peek() {
        if (isEmpty()) {
            throw new RuntimeException("Queue is empty");
        }
        return data[front];
    }

    public boolean isEmpty() {
        return size == 0;
    }

    public boolean isFull() {
        return size == capacity;
    }

    public int size() {
        return size;
    }

    public static void main(String[] args) {
        ArrayQueue queue = new ArrayQueue(5);
        queue.enqueue(10);
        queue.enqueue(20);
        queue.enqueue(30);

        System.out.println(queue.peek());     // 10
        System.out.println(queue.dequeue());  // 10
        System.out.println(queue.dequeue());  // 20
        System.out.println(queue.size());     // 1

        // Demonstrate circular behavior
        queue.enqueue(40);
        queue.enqueue(50);
        queue.enqueue(60);
        queue.enqueue(70);
        System.out.println(queue.dequeue());  // 30
    }
}
```

## Why Circular?
A naive (non-circular) array queue wastes space: after dequeuing, front slots are unused. Circular queue reuses those slots via modulo arithmetic.

```
Non-circular: [_, _, 30, 40, 50]  → front moves right, wasted space on left
Circular:     [60, 70, 30, 40, 50] → wraps around, full utilization
```

## Complexity

| Operation | Time |
|-----------|------|
| enqueue | O(1) |
| dequeue | O(1) |
| peek | O(1) |
| isEmpty | O(1) |

**Space:** O(n)

## Edge Cases
- Enqueue on full queue
- Dequeue/peek on empty queue
- Wrap-around scenario (rear < front)
- Single element enqueue then dequeue

## Interview Note
In Java, use `Queue<Integer> q = new LinkedList<>()` or `ArrayDeque<>()` in practice. This implementation demonstrates understanding of FIFO and circular buffer concepts.
