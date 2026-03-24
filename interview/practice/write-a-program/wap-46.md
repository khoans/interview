**46.** Check if parentheses in a string are balanced.



========== THIS SECTION IS THE ANSWER ==========

## Solution: Stack-based

**Pattern:** Stack — push opening brackets, pop and match on closing brackets

```java
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.Map;

public class BalancedParentheses {

    public static boolean isBalanced(String str) {
        if (str == null) return true;

        Deque<Character> stack = new ArrayDeque<>();
        Map<Character, Character> pairs = Map.of(
            ')', '(',
            ']', '[',
            '}', '{'
        );

        for (char c : str.toCharArray()) {
            if (c == '(' || c == '[' || c == '{') {
                stack.push(c);
            } else if (c == ')' || c == ']' || c == '}') {
                if (stack.isEmpty() || stack.pop() != pairs.get(c)) {
                    return false;
                }
            }
            // ignore non-bracket characters
        }

        return stack.isEmpty();  // any unmatched opening brackets?
    }

    public static void main(String[] args) {
        System.out.println(isBalanced("()"));          // true
        System.out.println(isBalanced("({[]})"));      // true
        System.out.println(isBalanced("(]"));           // false
        System.out.println(isBalanced("((())"));        // false
        System.out.println(isBalanced("a*(b+c)"));      // true
        System.out.println(isBalanced(""));              // true
    }
}
```

## Simplified version (only round parentheses)

```java
public static boolean isBalancedSimple(String str) {
    int count = 0;
    for (char c : str.toCharArray()) {
        if (c == '(') count++;
        else if (c == ')') count--;
        if (count < 0) return false;  // closing before opening
    }
    return count == 0;
}
```

## Complexity
- **Time:** O(n)
- **Space:** O(n) worst case (all opening brackets)

## Edge Cases
- Null or empty string → true (no brackets to balance)
- String with no brackets → true
- Only closing brackets → false
- Only opening brackets → false
- Nested brackets of different types: `([{}])`
- Interleaved incorrectly: `([)]`
- Single bracket → false

## Interview Tip
This is a classic stack problem. Mentioning that you use `ArrayDeque` over `Stack` (which is legacy and synchronized) shows Java knowledge.
