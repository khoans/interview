**44.** Implement a stack using arrays.



========== THIS SECTION IS THE ANSWER ==========

## Solution

```java
public class ArrayStack {

    private int[] data;
    private int top;
    private int capacity;

    public ArrayStack(int capacity) {
        this.capacity = capacity;
        this.data = new int[capacity];
        this.top = -1;
    }

    public void push(int value) {
        if (isFull()) {
            throw new RuntimeException("Stack overflow");
        }
        data[++top] = value;
    }

    public int pop() {
        if (isEmpty()) {
            throw new RuntimeException("Stack underflow");
        }
        return data[top--];
    }

    public int peek() {
        if (isEmpty()) {
            throw new RuntimeException("Stack is empty");
        }
        return data[top];
    }

    public boolean isEmpty() {
        return top == -1;
    }

    public boolean isFull() {
        return top == capacity - 1;
    }

    public int size() {
        return top + 1;
    }

    public static void main(String[] args) {
        ArrayStack stack = new ArrayStack(5);
        stack.push(10);
        stack.push(20);
        stack.push(30);

        System.out.println(stack.peek());  // 30
        System.out.println(stack.pop());   // 30
        System.out.println(stack.pop());   // 20
        System.out.println(stack.size());  // 1
        System.out.println(stack.isEmpty()); // false
    }
}
```

## Dynamic resizing version

```java
public void push(int value) {
    if (top == capacity - 1) {
        resize(capacity * 2);
    }
    data[++top] = value;
}

private void resize(int newCapacity) {
    int[] newData = new int[newCapacity];
    System.arraycopy(data, 0, newData, 0, top + 1);
    data = newData;
    capacity = newCapacity;
}
```

## Complexity

| Operation | Time |
|-----------|------|
| push | O(1) amortized |
| pop | O(1) |
| peek | O(1) |
| isEmpty | O(1) |
| size | O(1) |

**Space:** O(n) where n is capacity

## Key Points
- LIFO (Last In, First Out)
- `top = -1` indicates empty stack
- Throw exceptions on overflow/underflow rather than silent failure
- Java's `java.util.Stack` extends `Vector` (synchronized, legacy). Prefer `Deque<Integer> stack = new ArrayDeque<>()` in practice.

## Edge Cases
- Push on full stack
- Pop/peek on empty stack
- Single element push then pop
