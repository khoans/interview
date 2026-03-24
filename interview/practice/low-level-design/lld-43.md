**43.** Design a Custom Collection System using the Iterator pattern. Support tree traversal: In-order, Pre-order, Post-order, Level-order.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Custom Collection System — Iterator Pattern

**Pattern:** Iterator (Behavioral)
**Why Iterator?** Expose sequential access to a binary tree without revealing internal node structure. Different traversal orders serve different use cases.

**SOLID Principles Applied:**
- **SRP:** Tree manages nodes; each iterator handles one traversal strategy
- **OCP:** Add new traversals (Zigzag, Reverse) as new iterators
- **ISP:** Client uses `Iterator<T>` — doesn't know about tree internals

### Text UML Diagram

```
┌──────────────────┐     ┌───────────────────────┐
│  BinaryTree<T>   │────→│  <<interface>>         │
│──────────────────│     │  Iterable<T>           │
│ -root            │     └───────────────────────┘
│ +insert()        │
│ +inOrderIter()   │     ┌───────────────────────┐
│ +preOrderIter()  │────→│  <<interface>>         │
│ +postOrderIter() │     │  Iterator<T>           │
│ +levelOrderIter()│     │───────────────────────│
└──────────────────┘     │ +hasNext()             │
                         │ +next()                │
        ┌──────┐         └────────┬──────────────┘
        │ Node │                  │ implements
        │──────│         ┌────────┼──────────┐──────────┐
        │ -val │      InOrder  PreOrder  PostOrder  LevelOrder
        │ -left│      Iterator Iterator  Iterator   Iterator
        │-right│       (stack)  (stack)   (2 stacks)  (queue)
        └──────┘
```

### Key Design Decisions

1. **Lazy iteration** — Iterators don't pre-compute all elements; they traverse on-demand using stack/queue
2. **Implements Java's Iterator** — Works with for-each loops and Stream API
3. **Iterable tree** — Default iteration is in-order; other orders via factory methods
4. **Generic** — Works with any Comparable type

### Java Implementation

```java
import java.util.*;

// ============================================================
// TREE NODE
// ============================================================

class TreeNode<T> {
    T value;
    TreeNode<T> left;
    TreeNode<T> right;

    public TreeNode(T value) {
        this.value = value;
    }
}

// ============================================================
// ITERATORS — lazy traversal using stack/queue
// ============================================================

class InOrderIterator<T> implements Iterator<T> {
    private final Deque<TreeNode<T>> stack = new ArrayDeque<>();

    public InOrderIterator(TreeNode<T> root) {
        pushLeft(root);
    }

    private void pushLeft(TreeNode<T> node) {
        while (node != null) {
            stack.push(node);
            node = node.left;
        }
    }

    @Override
    public boolean hasNext() {
        return !stack.isEmpty();
    }

    @Override
    public T next() {
        if (!hasNext()) throw new NoSuchElementException();
        TreeNode<T> node = stack.pop();
        pushLeft(node.right);
        return node.value;
    }
}

class PreOrderIterator<T> implements Iterator<T> {
    private final Deque<TreeNode<T>> stack = new ArrayDeque<>();

    public PreOrderIterator(TreeNode<T> root) {
        if (root != null) stack.push(root);
    }

    @Override
    public boolean hasNext() {
        return !stack.isEmpty();
    }

    @Override
    public T next() {
        if (!hasNext()) throw new NoSuchElementException();
        TreeNode<T> node = stack.pop();
        // Push right first so left is processed first (LIFO)
        if (node.right != null) stack.push(node.right);
        if (node.left != null) stack.push(node.left);
        return node.value;
    }
}

class PostOrderIterator<T> implements Iterator<T> {
    private final Deque<TreeNode<T>> stack = new ArrayDeque<>();
    private final Deque<T> output = new ArrayDeque<>();

    public PostOrderIterator(TreeNode<T> root) {
        // Use two-stack approach: reverse of modified pre-order
        if (root != null) stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode<T> node = stack.pop();
            output.push(node.value);
            if (node.left != null) stack.push(node.left);
            if (node.right != null) stack.push(node.right);
        }
    }

    @Override
    public boolean hasNext() {
        return !output.isEmpty();
    }

    @Override
    public T next() {
        if (!hasNext()) throw new NoSuchElementException();
        return output.pop();
    }
}

class LevelOrderIterator<T> implements Iterator<T> {
    private final Queue<TreeNode<T>> queue = new LinkedList<>();

    public LevelOrderIterator(TreeNode<T> root) {
        if (root != null) queue.add(root);
    }

    @Override
    public boolean hasNext() {
        return !queue.isEmpty();
    }

    @Override
    public T next() {
        if (!hasNext()) throw new NoSuchElementException();
        TreeNode<T> node = queue.poll();
        if (node.left != null) queue.add(node.left);
        if (node.right != null) queue.add(node.right);
        return node.value;
    }
}

// ============================================================
// BINARY SEARCH TREE — Iterable collection
// ============================================================

class BinaryTree<T extends Comparable<T>> implements Iterable<T> {
    private TreeNode<T> root;
    private int size;

    public void insert(T value) {
        root = insertRecursive(root, value);
        size++;
    }

    private TreeNode<T> insertRecursive(TreeNode<T> node, T value) {
        if (node == null) return new TreeNode<>(value);
        int cmp = value.compareTo(node.value);
        if (cmp < 0) node.left = insertRecursive(node.left, value);
        else if (cmp > 0) node.right = insertRecursive(node.right, value);
        return node;
    }

    public int size() { return size; }

    // Default iterator: in-order (sorted)
    @Override
    public Iterator<T> iterator() {
        return new InOrderIterator<>(root);
    }

    // Factory methods for other traversals
    public Iterator<T> preOrderIterator() {
        return new PreOrderIterator<>(root);
    }

    public Iterator<T> postOrderIterator() {
        return new PostOrderIterator<>(root);
    }

    public Iterator<T> levelOrderIterator() {
        return new LevelOrderIterator<>(root);
    }

    // Iterable wrappers for for-each support
    public Iterable<T> preOrder() {
        return () -> new PreOrderIterator<>(root);
    }

    public Iterable<T> postOrder() {
        return () -> new PostOrderIterator<>(root);
    }

    public Iterable<T> levelOrder() {
        return () -> new LevelOrderIterator<>(root);
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class TreeIteratorDemo {
    public static void main(String[] args) {
        /*
         * Build this BST:
         *         5
         *        / \
         *       3   7
         *      / \ / \
         *     2  4 6  8
         */
        BinaryTree<Integer> tree = new BinaryTree<>();
        tree.insert(5);
        tree.insert(3);
        tree.insert(7);
        tree.insert(2);
        tree.insert(4);
        tree.insert(6);
        tree.insert(8);

        // In-order (default): sorted
        System.out.print("In-order:    ");
        for (int val : tree) {
            System.out.print(val + " ");
        }
        System.out.println(); // 2 3 4 5 6 7 8

        // Pre-order: root first
        System.out.print("Pre-order:   ");
        for (int val : tree.preOrder()) {
            System.out.print(val + " ");
        }
        System.out.println(); // 5 3 2 4 7 6 8

        // Post-order: root last
        System.out.print("Post-order:  ");
        for (int val : tree.postOrder()) {
            System.out.print(val + " ");
        }
        System.out.println(); // 2 4 3 6 8 7 5

        // Level-order (BFS): level by level
        System.out.print("Level-order: ");
        for (int val : tree.levelOrder()) {
            System.out.print(val + " ");
        }
        System.out.println(); // 5 3 7 2 4 6 8
    }
}
```

### Interview Talking Points

- **Java's Iterator contract:** `hasNext()` + `next()` + optional `remove()`. Implementing `Iterable<T>` enables for-each loops and `Stream` support via `StreamSupport.stream(spliterator(), false)`.
- **Lazy vs Eager:** InOrder and PreOrder are lazy (O(h) space). PostOrder here is eager (O(n) space) for simplicity. A lazy PostOrder uses a "previous node" tracking approach.
- **Real-world:** Java's `TreeMap` uses in-order iteration. `ResultSet` is an iterator over database rows. Spring Data's `Pageable` is an iterator over pages of results.
- **Why separate from collection?** Iterator encapsulates traversal state. Multiple iterators can traverse the same tree independently (concurrent reads).
