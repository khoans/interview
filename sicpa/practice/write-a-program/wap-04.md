**4.** Write a program to find all duplicate elements in a list.

```Java
import java.util.ArrayList;import java.util.HashSet;
class FindAllDuplicate {
    static List<Integer> allDuplicate(List<Integer> list) {
        if (list == null || list.size() < 2) {
            return null;
        }
        Set<Integer> seen = new HashSet<>();
        Set<Integer> duplicates = new HashSet<>();
        for (Integer num : list) {
            if (num == null) {
                continue;
            }
            if (!seen.add(num)) {
                duplicates.add(num);
            }
        }
        
        return new ArrayList<>(duplicates);
        
    }
}
```
