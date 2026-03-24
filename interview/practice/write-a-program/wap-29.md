**29.** Find the length of a string without built-in length.



========== THIS SECTION IS THE ANSWER ==========

## Approach 1: Convert to char array

```java
public class StringLength {

    public static int stringLength(String str) {
        if (str == null) return 0;

        int count = 0;
        for (char c : str.toCharArray()) {
            count++;
        }
        return count;
    }

    public static void main(String[] args) {
        System.out.println(stringLength("hello"));  // 5
        System.out.println(stringLength(""));        // 0
        System.out.println(stringLength(null));       // 0
    }
}
```

## Approach 2: Using charAt with exception

```java
public static int stringLengthCharAt(String str) {
    if (str == null) return 0;

    int count = 0;
    try {
        while (true) {
            str.charAt(count);
            count++;
        }
    } catch (IndexOutOfBoundsException e) {
        // reached end of string
    }
    return count;
}
```

## Approach 3: Using toCharArray().length (simplest workaround)

```java
public static int stringLengthArray(String str) {
    if (str == null) return 0;
    return str.toCharArray().length;
}
```

## Complexity
- **Time:** O(n) for all approaches
- **Space:** O(n) for toCharArray-based, O(1) for charAt-based

## Edge Cases
- Null string
- Empty string
- String with spaces (spaces count as characters)

## Note
In a real interview, clarify what "without built-in length" means — does `toCharArray()` count as built-in? The interviewer usually wants to see you iterate character by character.
