**5.** Write a program to remove all duplicates from a list.

```Java
import java.util.ArrayList;import java.util.HashSet;public class removeAllDuplicates {
    static List<Integer> removeAllDuplicate(List<Integer> list) {
        Set<Integer> set = new HashSet<>(list);
        return new ArrayList<>(set);
        
    }
}
```
