**47.** Find the middle element of a linked list.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Fast and Slow Pointers

**Pattern:** Two Pointers — slow moves 1 step, fast moves 2 steps

```java
public class MiddleOfLinkedList {

    static class ListNode {
        int val;
        ListNode next;
        ListNode(int val) { this.val = val; }
    }

    public static ListNode findMiddle(ListNode head) {
        if (head == null) return null;

        ListNode slow = head;
        ListNode fast = head;

        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }

        return slow;  // slow is at middle when fast reaches end
    }

    public static void main(String[] args) {
        // 1 -> 2 -> 3 -> 4 -> 5
        ListNode head = new ListNode(1);
        head.next = new ListNode(2);
        head.next.next = new ListNode(3);
        head.next.next.next = new ListNode(4);
        head.next.next.next.next = new ListNode(5);

        System.out.println(findMiddle(head).val);  // 3

        // Even length: 1 -> 2 -> 3 -> 4
        ListNode head2 = new ListNode(1);
        head2.next = new ListNode(2);
        head2.next.next = new ListNode(3);
        head2.next.next.next = new ListNode(4);

        System.out.println(findMiddle(head2).val);  // 3 (second middle)
    }
}
```

## Why It Works
When fast pointer reaches the end (or null), slow pointer is exactly at the middle. Fast travels 2x the speed, so when fast covers the full list, slow covers half.

- **Odd length list:** slow lands on exact middle
- **Even length list:** slow lands on second middle node

## Alternative: Count length first (two-pass)

```java
public static ListNode findMiddleTwoPass(ListNode head) {
    int length = 0;
    ListNode current = head;
    while (current != null) {
        length++;
        current = current.next;
    }

    current = head;
    for (int i = 0; i < length / 2; i++) {
        current = current.next;
    }
    return current;
}
```

## Complexity

| Approach | Time | Space |
|----------|------|-------|
| Two Pointers | O(n) single pass | O(1) |
| Count first | O(n) two passes | O(1) |

## Edge Cases
- Null head → return null
- Single node → return that node
- Two nodes → return second node
- Even vs odd length (clarify which middle for even)
