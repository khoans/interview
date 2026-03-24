**28.** Design a Sorting System using the Strategy pattern. Switch between Bubble, Quick, Merge, and Insertion sort at runtime.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Sorting System — Strategy Pattern

**Pattern:** Strategy (Behavioral)
**Why Strategy?** Encapsulate each sorting algorithm as an interchangeable object. Client picks the algorithm at runtime based on data characteristics.

**SOLID Principles Applied:**
- **SRP:** Each strategy class has one job — one sorting algorithm
- **OCP:** Add new sorting algorithms without modifying existing code
- **DIP:** Sorter depends on SortStrategy interface, not concrete algorithms

### Text UML Diagram

```
┌─────────────────┐       ┌───────────────────┐
│     Sorter      │──────→│  <<interface>>     │
│─────────────────│       │   SortStrategy     │
│ -strategy       │       │───────────────────│
│ +setStrategy()  │       │ +sort(int[])       │
│ +sort(int[])    │       │ +getName()         │
│ +autoSelect()   │       └────────┬──────────┘
└─────────────────┘                │ implements
                         ┌────────┼────────┬──────────┐
                         │        │        │          │
                    ┌────▼──┐ ┌──▼───┐ ┌──▼───┐ ┌───▼──────┐
                    │Bubble │ │Quick │ │Merge │ │Insertion │
                    │Sort   │ │Sort  │ │Sort  │ │Sort      │
                    └───────┘ └──────┘ └──────┘ └──────────┘
```

### Key Design Decisions

1. **Auto-select strategy** — Choose algorithm based on array size (small arrays favor Insertion, large arrays favor Merge/Quick)
2. **Strategy returns name** — For logging/debugging which algorithm was used
3. **In-place vs new array** — Strategy interface sorts in-place for consistency; Merge sort copies internally
4. **Comparable support** — Generic version for any Comparable type

### Java Implementation

```java
import java.util.Arrays;

// ============================================================
// STRATEGY INTERFACE
// ============================================================

interface SortStrategy {
    void sort(int[] array);
    String getName();
}

// ============================================================
// CONCRETE STRATEGIES
// ============================================================

class BubbleSortStrategy implements SortStrategy {
    @Override
    public void sort(int[] array) {
        int n = array.length;
        for (int i = 0; i < n - 1; i++) {
            boolean swapped = false;
            for (int j = 0; j < n - i - 1; j++) {
                if (array[j] > array[j + 1]) {
                    int temp = array[j];
                    array[j] = array[j + 1];
                    array[j + 1] = temp;
                    swapped = true;
                }
            }
            if (!swapped) break; // Optimization: already sorted
        }
    }

    @Override
    public String getName() { return "BubbleSort O(n^2)"; }
}

class InsertionSortStrategy implements SortStrategy {
    @Override
    public void sort(int[] array) {
        for (int i = 1; i < array.length; i++) {
            int key = array[i];
            int j = i - 1;
            while (j >= 0 && array[j] > key) {
                array[j + 1] = array[j];
                j--;
            }
            array[j + 1] = key;
        }
    }

    @Override
    public String getName() { return "InsertionSort O(n^2)"; }
}

class QuickSortStrategy implements SortStrategy {
    @Override
    public void sort(int[] array) {
        quickSort(array, 0, array.length - 1);
    }

    private void quickSort(int[] arr, int low, int high) {
        if (low < high) {
            int pivot = partition(arr, low, high);
            quickSort(arr, low, pivot - 1);
            quickSort(arr, pivot + 1, high);
        }
    }

    private int partition(int[] arr, int low, int high) {
        int pivot = arr[high];
        int i = low - 1;
        for (int j = low; j < high; j++) {
            if (arr[j] <= pivot) {
                i++;
                int temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp;
            }
        }
        int temp = arr[i + 1];
        arr[i + 1] = arr[high];
        arr[high] = temp;
        return i + 1;
    }

    @Override
    public String getName() { return "QuickSort O(n log n) avg"; }
}

class MergeSortStrategy implements SortStrategy {
    @Override
    public void sort(int[] array) {
        if (array.length <= 1) return;
        int[] sorted = mergeSort(array, 0, array.length - 1);
        System.arraycopy(sorted, 0, array, 0, array.length);
    }

    private int[] mergeSort(int[] arr, int left, int right) {
        if (left == right) return new int[]{arr[left]};
        int mid = (left + right) / 2;
        int[] leftArr = mergeSort(arr, left, mid);
        int[] rightArr = mergeSort(arr, mid + 1, right);
        return merge(leftArr, rightArr);
    }

    private int[] merge(int[] a, int[] b) {
        int[] result = new int[a.length + b.length];
        int i = 0, j = 0, k = 0;
        while (i < a.length && j < b.length) {
            result[k++] = (a[i] <= b[j]) ? a[i++] : b[j++];
        }
        while (i < a.length) result[k++] = a[i++];
        while (j < b.length) result[k++] = b[j++];
        return result;
    }

    @Override
    public String getName() { return "MergeSort O(n log n) stable"; }
}

// ============================================================
// CONTEXT — uses strategy, supports auto-selection
// ============================================================

class Sorter {
    private SortStrategy strategy;

    public Sorter(SortStrategy strategy) {
        this.strategy = strategy;
    }

    public void setStrategy(SortStrategy strategy) {
        this.strategy = strategy;
    }

    public void sort(int[] array) {
        System.out.println("Sorting " + array.length + " elements with " + strategy.getName());
        long start = System.nanoTime();
        strategy.sort(array);
        long elapsed = System.nanoTime() - start;
        System.out.println("Completed in " + elapsed / 1000 + " microseconds");
    }

    /**
     * Auto-select best strategy based on array characteristics.
     * Real-world: Java's Arrays.sort uses dual-pivot QuickSort for primitives
     * and TimSort (merge+insertion hybrid) for objects.
     */
    public void autoSelectStrategy(int[] array) {
        if (array.length < 20) {
            this.strategy = new InsertionSortStrategy();
        } else if (array.length < 1000) {
            this.strategy = new QuickSortStrategy();
        } else {
            this.strategy = new MergeSortStrategy(); // stable, predictable O(n log n)
        }
        System.out.println("Auto-selected: " + strategy.getName());
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class SortingDemo {
    public static void main(String[] args) {
        int[] data = {64, 34, 25, 12, 22, 11, 90};

        // Explicit strategy selection
        Sorter sorter = new Sorter(new BubbleSortStrategy());
        sorter.sort(data.clone());

        // Switch strategy at runtime
        sorter.setStrategy(new QuickSortStrategy());
        sorter.sort(data.clone());

        sorter.setStrategy(new MergeSortStrategy());
        sorter.sort(data.clone());

        // Auto-select based on data size
        int[] largeData = new int[5000];
        for (int i = 0; i < largeData.length; i++) {
            largeData[i] = (int) (Math.random() * 10000);
        }
        sorter.autoSelectStrategy(largeData);
        sorter.sort(largeData);

        System.out.println("First 10: " + Arrays.toString(Arrays.copyOf(largeData, 10)));
    }
}
```

### Interview Talking Points

- **Why Strategy over if-else?** Each algorithm is complex enough to deserve its own class. Adding HeapSort means one new class, zero changes to Sorter.
- **Java's own usage:** `Arrays.sort()` internally uses a strategy-like approach — InsertionSort for small arrays (<47), dual-pivot QuickSort for primitives, TimSort for objects.
- **When NOT to use Strategy:** If algorithms are trivial (1-2 lines), strategy adds unnecessary indirection. Inline logic is fine.
