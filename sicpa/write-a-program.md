# 50 "Write a Program" Interview Questions

## Questions 1-10: Array/List Operations

**1.** Write a program to find the second largest element in a list. (Original question 45)

**2.** Write a program to find the second smallest element in a list.

**3.** Write a program to find the third largest element in a list.

**4.** Write a program to find all duplicate elements in a list.

**5.** Write a program to remove all duplicates from a list.

**6.** Write a program to reverse a list without using built-in reverse function.

**7.** Write a program to find the sum of all elements in a list.

**8.** Write a program to find the product of all elements in a list.

**9.** Write a program to find the maximum element in a list without using built-in max function.

**10.** Write a program to find the minimum element in a list without using built-in min function.

---

## Questions 11-20: Number Programs

**11.** Write a program to check if a number is prime.

**12.** Write a program to print all prime numbers in a given range.

**13.** Write a program to check if a number is an Armstrong number. (An Armstrong number is a number that is equal to the sum of its own digits each raised to the power of the number of digits, e.g., 153 = 1³ + 5³ + 3³)

**14.** Write a program to check if a number is a perfect number. (A perfect number is equal to the sum of its proper divisors, e.g., 6 = 1 + 2 + 3)

**15.** Write a program to find the GCD (Greatest Common Divisor) of two numbers.

**16.** Write a program to find the LCM (Least Common Multiple) of two numbers.

**17.** Write a program to check if a number is a perfect square.

**18.** Write a program to count the number of digits in a given integer.

**19.** Write a program to find the sum of digits of a given number.

**20.** Write a program to reverse a given integer.

---

## Questions 21-30: String Programs

**21.** Write a program to check if a string is a palindrome.

**22.** Write a program to reverse a string without using built-in reverse function.

**23.** Write a program to count the number of vowels in a string.

**24.** Write a program to count the number of words in a string.

**25.** Write a program to check if two strings are anagrams of each other.

**26.** Write a program to find the first non-repeated character in a string.

**27.** Write a program to remove all spaces from a string.

**28.** Write a program to convert a string to uppercase without using built-in function.

**29.** Write a program to find the length of a string without using built-in length function.

**30.** Write a program to check if a string contains only digits.

---

## Questions 31-40: Sorting and Searching

**31.** Write a program to implement bubble sort.

**32.** Write a program to implement selection sort.

**33.** Write a program to implement insertion sort.

**34.** Write a program to implement linear search.

**35.** Write a program to implement binary search (assume sorted array).

**36.** Write a program to sort an array of 0s, 1s, and 2s (Dutch National Flag problem).

**37.** Write a program to move all negative numbers to the beginning of an array.

**38.** Write a program to find all pairs in an array that sum to a given target.

**39.** Write a program to rotate an array by k positions.

**40.** Write a program to merge two sorted arrays into one sorted array.

---

## Questions 41-50: Miscellaneous Programs

**41.** Write a program to print the Fibonacci series up to n terms.

**42.** Write a program to find the factorial of a number using recursion.

**43.** Write a program to check if a linked list has a cycle.

**44.** Write a program to implement a stack using arrays.

**45.** Write a program to implement a queue using arrays.

**46.** Write a program to check if parentheses in a string are balanced.

**47.** Write a program to find the middle element of a linked list.

**48.** Write a program to swap two numbers without using a temporary variable.

**49.** Write a program to print all permutations of a string.

**50.** Write a program to find the intersection of two sorted arrays.

---

## Sample Solutions Template

Create a separate file `write-a-program-solutions.md` with your solutions:

```markdown
## Question 1: Find Second Largest Element
```java
public int findSecondLargest(int[] arr) {
    if (arr.length < 2) {
        throw new IllegalArgumentException("Array must have at least 2 elements");
    }
    int first = Integer.MIN_VALUE;
    int second = Integer.MIN_VALUE;
    
    for (int num : arr) {
        if (num > first) {
            second = first;
            first = num;
        } else if (num > second && num != first) {
            second = num;
        }
    }
    return second;
}
```

## Question 2: Find Second Smallest Element
```java
public int findSecondSmallest(int[] arr) {
    // Your solution here
}
```
...
```

---

## Tips for "Write a Program" Questions

1. **Clarify requirements**: Ask about edge cases, input constraints, expected output
2. **Think aloud**: Explain your thought process as you code
3. **Start with brute force**: It's okay to start simple, then optimize
4. **Handle edge cases**: Empty input, single element, null values, negative numbers
5. **Use meaningful variable names**: Make your code readable
6. **Test as you go**: Verify with small examples while coding
7. **Discuss time/space complexity**: Be ready to analyze your solution
8. **Practice common patterns**: Two pointers, sliding window, hash maps, etc.

---

## Common Patterns Reference

| Pattern | Example Questions |
|---------|-------------------|
| Two Pointers | Two sum, reverse string, find pairs |
| Fast & Slow Pointers | Second largest, middle element, cycle detection |
| Hash Map | Find duplicates, anagrams, intersection |
| Sliding Window | Subarray problems |
| Divide & Conquer | Binary search, merge sort |
| Recursion | Factorial, Fibonacci, permutations |

Good luck with your interview preparation! 🍀
