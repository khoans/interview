**30.** Check if a string contains only digits.



========== THIS SECTION IS THE ANSWER ==========

## Approach 1: Manual character check

```java
public class OnlyDigits {

    public static boolean containsOnlyDigits(String str) {
        if (str == null || str.isEmpty()) return false;

        for (char c : str.toCharArray()) {
            if (c < '0' || c > '9') {
                return false;
            }
        }
        return true;
    }

    public static void main(String[] args) {
        System.out.println(containsOnlyDigits("12345"));   // true
        System.out.println(containsOnlyDigits("123a5"));   // false
        System.out.println(containsOnlyDigits(""));         // false
        System.out.println(containsOnlyDigits("-123"));     // false
    }
}
```

## Approach 2: Using Character.isDigit()

```java
public static boolean containsOnlyDigitsApi(String str) {
    if (str == null || str.isEmpty()) return false;

    for (char c : str.toCharArray()) {
        if (!Character.isDigit(c)) {
            return false;
        }
    }
    return true;
}
```

## Approach 3: Regex

```java
public static boolean containsOnlyDigitsRegex(String str) {
    if (str == null || str.isEmpty()) return false;
    return str.matches("\\d+");
}
```

## Approach 4: Java 8 Streams

```java
public static boolean containsOnlyDigitsStream(String str) {
    if (str == null || str.isEmpty()) return false;
    return str.chars().allMatch(Character::isDigit);
}
```

## Complexity

| Approach | Time | Space |
|----------|------|-------|
| Manual | O(n) | O(1) |
| Character.isDigit | O(n) | O(1) |
| Regex | O(n) | O(1) |
| Streams | O(n) | O(1) |

## Edge Cases
- Null or empty string → false
- Negative numbers with '-' sign → false (contains non-digit)
- Decimal numbers with '.' → false
- Unicode digit characters (clarify with interviewer)
