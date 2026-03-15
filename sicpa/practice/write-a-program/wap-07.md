**7.** Write a program to find the sum of all elements in a list.

```Java
public class SumOfAllElement {
    static Integer sumOfAll(List<Integer> list) {
        if (list == null) {
            return null;
        }
        return list.stream().filter(Objects::nonNull).reduce(0,(curr, val) -> curr + val);
    }
}
```
