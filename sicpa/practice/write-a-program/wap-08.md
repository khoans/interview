**8.** Write a program to find the product of all elements in a list.


```Java
import java.util.Objects;public class FindProductOfAll {
    static Integer findProductOfAllElements(List<Integer> list) {
        if (list == null) {
            return null;
        }
        return list.stream().filter(Objects::nonNull).reduce(1,(a,b) -> a*b);
    }
}
```
