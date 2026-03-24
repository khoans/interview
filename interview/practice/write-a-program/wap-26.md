**26.** Find the first non-repeated character in a string.



========== THIS SECTION IS THE ANSWER ==========

## Approach 1: LinkedHashMap (Optimal)

**Pattern:** Hash Map + Order Preservation

```java
import java.util.LinkedHashMap;
import java.util.Map;

public class FirstNonRepeatedChar {

    public static Character firstNonRepeated(String str) {
        if (str == null || str.isEmpty()) {
            return null;
        }

        Map<Character, Integer> countMap = new LinkedHashMap<>();
        for (char c : str.toCharArray()) {
            countMap.put(c, countMap.getOrDefault(c, 0) + 1);
        }

        for (Map.Entry<Character, Integer> entry : countMap.entrySet()) {
            if (entry.getValue() == 1) {
                return entry.getKey();
            }
        }
        return null;
    }

    public static void main(String[] args) {
        System.out.println(firstNonRepeated("swiss"));    // w
        System.out.println(firstNonRepeated("aabbcc"));   // null
        System.out.println(firstNonRepeated("abcab"));    // c
    }
}
```

## Approach 2: indexOf / lastIndexOf

```java
public static Character firstNonRepeatedSimple(String str) {
    if (str == null || str.isEmpty()) return null;

    for (int i = 0; i < str.length(); i++) {
        char c = str.charAt(i);
        if (str.indexOf(c) == str.lastIndexOf(c)) {
            return c;
        }
    }
    return null;
}
```

## Complexity

| Approach | Time | Space |
|----------|------|-------|
| LinkedHashMap | O(n) | O(n) |
| indexOf/lastIndexOf | O(n^2) | O(1) |

## Edge Cases
- Null or empty string
- All characters repeated
- Single character string
- Case sensitivity (treat 'A' and 'a' differently unless specified)
