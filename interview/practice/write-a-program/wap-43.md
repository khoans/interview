**43.** Check if a linked list has a cycle.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Floyd's Cycle Detection (Tortoise and Hare)

**Pattern:** Two Pointers — slow (1 step) and fast (2 steps)

```java
public class LinkedListCycle {

    static class ListNode {
        int val;
        ListNode next;
        ListNode(int val) { this.val = val; }
    }

    public static boolean hasCycle(ListNode head) {
        if (head == null || head.next == null) return false;

        ListNode slow = head;
        ListNode fast = head;

        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;

            if (slow == fast) {
                return true;  // cycle detected
            }
        }
        return false;  // fast reached end, no cycle
    }

    // Bonus: find the start of the cycle
    public static ListNode detectCycleStart(ListNode head) {
        if (head == null || head.next == null) return null;

        ListNode slow = head;
        ListNode fast = head;

        // Phase 1: detect cycle
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast) break;
        }

        if (fast == null || fast.next == null) return null;

        // Phase 2: find entry point
        slow = head;
        while (slow != fast) {
            slow = slow.next;
            fast = fast.next;
        }
        return slow;
    }

    public static void main(String[] args) {
        ListNode n1 = new ListNode(1);
        ListNode n2 = new ListNode(2);
        ListNode n3 = new ListNode(3);
        ListNode n4 = new ListNode(4);
        n1.next = n2; n2.next = n3; n3.next = n4;
        n4.next = n2;  // cycle: 4 -> 2

        System.out.println(hasCycle(n1));          // true
        System.out.println(detectCycleStart(n1).val); // 2
    }
}
```

## Why It Works
- If there's no cycle, fast pointer reaches null
- If there's a cycle, fast catches up to slow (they must meet inside the cycle)
- For finding cycle start: when slow and fast meet, distance from head to cycle start equals distance from meeting point to cycle start

## Complexity
- **Time:** O(n)
- **Space:** O(1)

## Alternative: HashSet (simpler but uses space)
```java
public static boolean hasCycleHashSet(ListNode head) {
    Set<ListNode> visited = new HashSet<>();
    while (head != null) {
        if (!visited.add(head)) return true;
        head = head.next;
    }
    return false;
}
```
- Time: O(n), Space: O(n)

## Edge Cases
- Null head
- Single node, no cycle
- Single node pointing to itself
- Cycle at the head
- Cycle at the tail
