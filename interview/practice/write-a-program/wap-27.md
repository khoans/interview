**27.** Remove all spaces from a string.



========== THIS SECTION IS THE ANSWER ==========

## Approach 1: StringBuilder (Optimal)

```java
public class RemoveSpaces {

    public static String removeSpaces(String str) {
        if (str == null) return null;

        StringBuilder sb = new StringBuilder();
        for (char c : str.toCharArray()) {
            if (c != ' ') {
                sb.append(c);
            }
        }
        return sb.toString();
    }

    public static void main(String[] args) {
        System.out.println(removeSpaces("Hello World"));       // HelloWorld
        System.out.println(removeSpaces("  a b c  "));         // abc
        System.out.println(removeSpaces("   "));               // (empty)
    }
}
```

## Approach 2: Using replaceAll (One-liner)

```java
public static String removeSpacesRegex(String str) {
    if (str == null) return null;
    return str.replaceAll("\\s+", "");  // removes all whitespace chars
}
```

## Approach 3: char array in-place style

```java
public static String removeSpacesInPlace(String str) {
    if (str == null) return null;

    char[] chars = str.toCharArray();
    int writeIdx = 0;
    for (int readIdx = 0; readIdx < chars.length; readIdx++) {
        if (chars[readIdx] != ' ') {
            chars[writeIdx++] = chars[readIdx];
        }
    }
    return new String(chars, 0, writeIdx);
}
```

## Complexity

| Approach | Time | Space |
|----------|------|-------|
| StringBuilder | O(n) | O(n) |
| replaceAll | O(n) | O(n) |
| char array | O(n) | O(n) — Java strings are immutable |

## Edge Cases
- Null string
- Empty string
- String with only spaces
- Tabs and other whitespace (clarify: spaces only vs all whitespace)
