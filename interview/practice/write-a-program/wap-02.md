**2.** Write a program to find the second smallest element in a list.

```Java
public class FindSecondSmallest {
    static Integer findSecondSmallest(List<Integer> list){
        if (list == null || list.size() < 2) {
            return null;
        }
        
        Integer first = Integer.MAX_VALUE;
        Integer second = Integer.MAX_VALUE;
        
        for (Integer num : list) {
            if ( num == null) {
                continue;
            }
            
            if (num < first) {
                second = first;
                first = num;
            } else if (num > first && num < second) {
                second = num;
            }
        }
        return second == Integer.MAX_VALUE ? null : second;
    }
}
```
