**49.** Print all permutations of a string.



========== THIS SECTION IS THE ANSWER ==========

## Approach 1: Backtracking (Classic)

**Pattern:** Backtracking — swap, recurse, un-swap

```java
import java.util.ArrayList;
import java.util.List;

public class StringPermutations {

    public static List<String> permutations(String str) {
        List<String> result = new ArrayList<>();
        if (str == null) return result;

        backtrack(str.toCharArray(), 0, result);
        return result;
    }

    private static void backtrack(char[] chars, int index, List<String> result) {
        if (index == chars.length) {
            result.add(new String(chars));
            return;
        }

        for (int i = index; i < chars.length; i++) {
            swap(chars, index, i);
            backtrack(chars, index + 1, result);
            swap(chars, index, i);  // backtrack
        }
    }

    private static void swap(char[] chars, int i, int j) {
        char temp = chars[i];
        chars[i] = chars[j];
        chars[j] = temp;
    }

    public static void main(String[] args) {
        List<String> perms = permutations("ABC");
        perms.forEach(System.out::println);
        // ABC, ACB, BAC, BCA, CBA, CAB
    }
}
```

## Approach 2: Handle duplicates

```java
import java.util.HashSet;
import java.util.Set;

private static void backtrackUnique(char[] chars, int index,
                                     List<String> result) {
    if (index == chars.length) {
        result.add(new String(chars));
        return;
    }

    Set<Character> used = new HashSet<>();
    for (int i = index; i < chars.length; i++) {
        if (used.contains(chars[i])) continue;  // skip duplicate
        used.add(chars[i]);
        swap(chars, index, i);
        backtrackUnique(chars, index + 1, result);
        swap(chars, index, i);
    }
}
```

## How Backtracking Works (for "ABC")
```
Fix A → permute BC → ABC, ACB
Fix B → permute AC → BAC, BCA
Fix C → permute BA → CBA, CAB
```

At each position, try every remaining character, then recurse on the rest.

## Complexity
- **Time:** O(n! * n) — n! permutations, each takes O(n) to build string
- **Space:** O(n) recursion depth + O(n! * n) to store results

## Edge Cases
- Null or empty string
- Single character → one permutation
- Duplicate characters: "AAB" → {AAB, ABA, BAA} (use Set-based approach)
- All same characters → only one permutation

## Total Permutations
- n distinct characters → n! permutations
- With duplicates → n! / (k1! * k2! * ... ) where ki is frequency of each char
