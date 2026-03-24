**28.** Convert a string to uppercase without built-in function.



========== THIS SECTION IS THE ANSWER ==========

## Solution: ASCII Manipulation

**Key Insight:** In ASCII, lowercase 'a'-'z' are 97-122 and uppercase 'A'-'Z' are 65-90. The difference is 32.

```java
public class ToUpperCase {

    public static String toUpperCase(String str) {
        if (str == null) return null;

        char[] chars = str.toCharArray();
        for (int i = 0; i < chars.length; i++) {
            if (chars[i] >= 'a' && chars[i] <= 'z') {
                chars[i] = (char) (chars[i] - 32);
            }
        }
        return new String(chars);
    }

    public static void main(String[] args) {
        System.out.println(toUpperCase("hello"));       // HELLO
        System.out.println(toUpperCase("Hello World")); // HELLO WORLD
        System.out.println(toUpperCase("abc123!@#"));   // ABC123!@#
    }
}
```

## Alternative: Using bit manipulation

```java
public static String toUpperCaseBit(String str) {
    if (str == null) return null;

    char[] chars = str.toCharArray();
    for (int i = 0; i < chars.length; i++) {
        if (chars[i] >= 'a' && chars[i] <= 'z') {
            chars[i] = (char) (chars[i] & 0x5F);  // clear bit 5
        }
    }
    return new String(chars);
}
```

## Complexity
- **Time:** O(n)
- **Space:** O(n) — Java strings are immutable, need new char array

## Edge Cases
- Null or empty string
- Already uppercase characters
- Non-alphabetic characters (digits, symbols, spaces) — leave unchanged
- Unicode characters beyond ASCII range
