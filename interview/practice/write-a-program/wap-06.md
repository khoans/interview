**6.** Write a program to reverse a list without using built-in reverse function.

```Java
class ReverseList {
    static List<Integer> reverseAList(List<Integer> list) {
        if (list == null || list.size() <= 1) {
            return list;
        }
        for (Integer i = 0, j = list.size() -1 ; i < j ; i++, j--) {
            swap(list, i , j);
        }
        return list;
    }
    
    static void swap(List<Integer> list, Integer i, Integer j) {
        Integer tmp = list.get(i);
        list.set(i, list.get(j));
        list.set(j, tmp);
    }
}
```
