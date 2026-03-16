**1.** Write a program to find the second largest element in a list. (Original question 45)

```java
public class SecondLargestElement {
    
    Integer secondLargest(List<Integer> list) {
        if (list == null || list.size() < 2 ) {
            return null;
        }
        
        Integer first = Integer.MIN_VALUE;
        Integer second = Integer.MIN_VALUE;
        
        for (Integer num : list) {
            if (num == null) {
                continue;
            }
            
            if (num > first) {
                second = first;
                first = num;
            } else if (num > second && num < first) {
                second = num;
            }
            
        }
        return second == Integer.MIN_VALUE ? null : second;
    }
}
```
