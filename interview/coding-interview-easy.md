# 50 Easy Coding Interview Questions (Most Common)

## Questions 1-10: Number Operations

**1. Palindrome Number**
Given an integer x, return true if x is a palindrome integer. An integer is a palindrome when it reads the same backward as forward. For example, 121 is a palindrome while 123 is not.

**2. Two Sum**
Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.

**3. Plus One**
Given a large integer represented as an array of digits, where each digits[i] is the ith digit of the integer, add one to the integer.

**4. Reverse Integer**
Given a signed 32-bit integer x, return x with its digits reversed. If reversing x causes the value to go outside the signed 32-bit integer range, return 0.

**5. Roman to Integer**
Given a roman numeral, convert it to an integer. (I=1, V=5, X=10, L=50, C=100, D=500, M=1000)

**6. Integer to Roman**
Given an integer, convert it to a roman numeral.

**7. Power of Two**
Given an integer n, return true if it is a power of two. Otherwise, return false.

**8. Factorial**
Given a non-negative integer n, return the factorial of n.

**9. Fibonacci Number**
Given an integer n, return the nth Fibonacci number. (F(0)=0, F(1)=1, F(n)=F(n-1)+F(n-2))

**10. Count Digits**
Given a positive integer, count the number of digits in it.

---

## Questions 11-20: String Manipulation

**11. Valid Anagram**
Given two strings s and t, return true if t is an anagram of s, and false otherwise.

**12. Valid Palindrome**
Given a string s, return true if it is a palindrome, considering only alphanumeric characters and ignoring cases.

**13. Reverse String**
Write a function that reverses a string. The input string is given as an array of characters.

**14. Longest Common Prefix**
Write a function to find the longest common prefix string amongst an array of strings.

**15. Is Subsequence**
Given two strings s and t, return true if s is a subsequence of t, or false otherwise.

**16. Implement strStr()**
Return the index of the first occurrence of needle in haystack, or -1 if needle is not part of haystack.

**17. Length of Last Word**
Given a string s consisting of words and spaces, return the length of the last word in the string.

**18. Convert to Lowercase**
Given a string s, return the string after replacing every uppercase letter with the same lowercase letter.

**19. Count Vowels**
Given a string, count the number of vowels (a, e, i, o, u) in it.

**20. Remove Duplicates from String**
Given a string, remove all duplicate characters from it.

---

## Questions 21-30: Array Operations

**21. Maximum Subarray**
Given an integer array nums, find the contiguous subarray which has the largest sum and return its sum.

**22. Best Time to Buy and Sell Stock**
Given an array prices where prices[i] is the price of a given stock on the ith day, find the maximum profit you can achieve from one transaction.

**23. Contains Duplicate**
Given an integer array nums, return true if any value appears at least twice in the array, and false if every element is distinct.

**24. Move Zeroes**
Given an integer array nums, move all 0's to the end of it while maintaining the relative order of the non-zero elements.

**25. Remove Duplicates from Sorted Array**
Given a sorted array nums, remove the duplicates in-place such that each element appears only once and return the new length.

**26. Merge Sorted Array**
Given two sorted integer arrays nums1 and nums2, merge nums2 into nums1 as one sorted array.

**27. Find Maximum in Array**
Given an array of integers, find the maximum element.

**28. Find Minimum in Array**
Given an array of integers, find the minimum element.

**29. Array Sum**
Given an array of integers, return the sum of all elements.

**30. Second Largest Element**
Given an array of integers, return the second largest element. (Same as question 45 in original list)

---

## Questions 31-40: Linked List Basics

**31. Reverse Linked List**
Given the head of a singly linked list, reverse the list and return the reversed list.

**32. Middle of Linked List**
Given the head of a singly linked list, return the middle node of the linked list.

**33. Linked List Cycle**
Given head, the head of a linked list, determine if the linked list has a cycle in it.

**34. Delete Node in Linked List**
Write a function to delete a given node in a singly linked list (node is not the tail).

**35. Merge Two Sorted Lists**
Merge two sorted linked lists and return it as a sorted list.

**36. Remove Linked List Elements**
Remove all elements from a linked list that have a value equal to val.

**37. Palindrome Linked List**
Given the head of a singly linked list, return true if it is a palindrome.

**38. Count Nodes in Linked List**
Given the head of a linked list, count the number of nodes.

**39. Find Nth Node from End**
Given a linked list, find the nth node from the end.

**40. Insert at Beginning**
Given a linked list and a value, insert the value at the beginning of the list.

---

## Questions 41-50: Miscellaneous Easy Problems

**41. Valid Parentheses**
Given a string s containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

**42. Climbing Stairs**
You are climbing a staircase. It takes n steps to reach the top. Each time you can either climb 1 or 2 steps. How many distinct ways can you climb to the top?

**43. Single Number**
Given a non-empty array of integers nums, every element appears twice except for one. Find that single one.

**44. Intersection of Two Arrays**
Given two integer arrays, return their intersection (each element in the result must be unique).

**45. FizzBuzz**
Given an integer n, return a string array answer where answer[i] is "FizzBuzz" if i is divisible by 3 and 5, "Fizz" if divisible by 3, "Buzz" if divisible by 5, and the number as string otherwise.

**46. Count Primes**
Given an integer n, return the number of prime numbers less than n.

**47. Happy Number**
Write an algorithm to determine if a number n is happy. A happy number is defined by replacing the number by the sum of the squares of its digits, until it equals 1 or loops endlessly in a cycle.

**48. Missing Number**
Given an array nums containing n distinct numbers in the range [0, n], return the only number in the range that is missing from the array.

**49. Majority Element**
Given an array nums of size n, return the majority element (the element that appears more than n/2 times).

**50. Excel Sheet Column Title**
Given an integer columnNumber, return its corresponding column title as it appears in an Excel sheet. (1->A, 2->B, ..., 26->Z, 27->AA, 28->AB, ...)

---

## Tips for Solving These Problems

1. **Understand the problem**: Read carefully and clarify edge cases
2. **Start simple**: Write a brute force solution first if needed
3. **Use examples**: Work through small examples by hand
4. **Think about edge cases**: Empty input, single element, negative numbers, null/undefined
5. **Practice regularly**: Consistency is key for interview preparation
6. **Explain your approach**: In interviews, talk through your thinking process
7. **Test your code**: Verify with multiple test cases before submitting

---

## Common Patterns to Recognize

| Pattern | When to Use |
|---------|-------------|
| Two Pointers | Sorted arrays, finding pairs |
| Sliding Window | Subarray/substring problems |
| Fast & Slow Pointers | Linked list cycles, middle element |
| Hash Map | Finding duplicates, two sum |
| Stack | Parentheses, valid expressions |
| Recursion | Tree, linked list, divide & conquer |

---

## Questions 51-70: More Array Problems

**51. Contains Duplicate II**
Given an integer array nums and an integer k, return true if there are two distinct indices i and j such that nums[i] == nums[j] and abs(i - j) <= k.

**52. Third Maximum Number**
Given an integer array nums, return the third distinct maximum number. If third maximum does not exist, return the maximum number.

**53. Find All Numbers Disappeared in an Array**
Given an array nums of n integers where nums[i] is in the range [1, n], return an array of all the integers in the range [1, n] that do not appear in nums.

**54. Maximum Product of Three Numbers**
Given an integer array nums, find three numbers whose product is maximum and return the maximum product.

**55. Array Partition I**
Given an integer array nums of 2n integers, group these integers into n pairs such that the sum of min(ai, bi) for all i is maximized. Return the maximized sum.

**56. Find Pivot Index**
Given an array of integers nums, calculate the pivot index. The pivot index is the index where the sum of all the numbers to the left is equal to the sum of all the numbers to the right.

**57. Largest Number At Least Twice of Others**
Given an integer array nums, return the index of the largest element if it is at least twice as much as every other number in the array. If not, return -1.

**58. Plus One Linked List**
Given a non-negative integer represented as a linked list of digits, plus one to the integer.

**59. Shuffle an Array**
Given an integer array nums, design an algorithm to randomly shuffle the array. All permutations of the array should be equally likely.

**60. Find the Difference**
Given two strings s and t where t is generated by shuffling s and adding one more letter, return the letter that was added to t.

**61. Teemo Attacking**
Given a time series and attack duration, calculate the total time the target is poisoned.

**62. Minimum Moves to Equal Array Elements**
Given an integer array nums of size n, return the minimum number of moves required to make all array elements equal (incrementing n-1 elements by 1).

**63. Range Addition II**
Given an m x n matrix initialized with zeros and operations, count the maximum integers in the matrix after performing all operations.

**64. Non-decreasing Array**
Given an array nums with n integers, check if it can become non-decreasing by modifying at most one element.

**65. Shortest Unsorted Continuous Subarray**
Given an integer array nums, find one continuous subarray that if you only sort this subarray, the whole array will be sorted in non-decreasing order.

**66. Degree of an Array**
Given a non-empty array of non-negative integers, find the smallest possible length of a contiguous subarray that has the same degree as the array.

**67. Image Smoother**
Given a 2D integer matrix representing an image, design a smoother to make the grayscale of each cell become the average grayscale of itself and its 8 surrounding cells.

**68. Reshape the Matrix**
Given a 2D array representing a matrix and two integers r and c, reshape the matrix into a new one with r rows and c columns.

**69. Toeplitz Matrix**
Given an m x n matrix, return true if the matrix is Toeplitz (every diagonal from top-left to bottom-right has the same elements).

**70. Transpose Matrix**
Given a 2D integer array matrix, return the transpose of the matrix.

---

## Questions 71-90: More String Problems

**71. Reverse Words in a String III**
Given a string s, reverse the order of characters in each word within a sentence while still preserving whitespace and initial word order.

**72. Student Attendance Record I**
Given a string representing student attendance records, return true if the student is eligible for an attendance award.

**73. Robot Return to Origin**
Given a sequence of moves (U, D, L, R), determine if the robot returns to the origin after completing all moves.

**74. Judge Route Circle**
Initially at (0, 0), given a sequence of moves, determine if the robot ends up at (0, 0) after completing all moves.

**75. Valid Word Abbreviation**
Given a string word and an abbreviation abbr, return whether the string matches the given abbreviation.

**76. Add Binary**
Given two binary strings a and b, return their sum as a binary string.

**77. Implement strStr() - II**
Return the index of the first occurrence of needle in haystack using a different approach than Q16.

**78. Count and Say**
The count-and-say sequence is generated by reading off the previous term. Given n, return the nth term.

**79. Long Pressed Name**
Given two strings name and typed, return true if typed could be a long-pressed version of name.

**80. Goat Latin**
Convert a sentence to "Goat Latin" following specific rules (similar to Pig Latin).

**81. Buddy Strings**
Given two strings s and goal, return true if you can swap two letters in s so the result equals goal.

**82. Unique Morse Code Words**
Given a list of words, return the number of different transformations among all words in Morse code.

**83. To Lower Case - II**
Implement toLowerCase() without using built-in function (alternative approach to Q18).

**84. Reverse Only Letters**
Given a string s, reverse only all the English letters (lowercase or uppercase) in it, keeping other characters in place.

**85. Backspace String Compare**
Given two strings s and t, return true if they are equal when both are typed into empty text editors (# means backspace).

**86. Goal Parser Interpretation**
Given a command string with "G", "()", and "(al)", parse and return the interpreted string.

**87. Check if Two Strings Are Equivalent**
Given two string arrays word1 and word2, return true if they represent the same string when concatenated.

**88. Defanging an IP Address**
Given a valid IPv4 address, return a defanged version where every "." is replaced with "[.]".

**89. Shuffle String**
Given a string s and an integer array indices, shuffle the string such that the character at indices[i] moves to position i.

**90. Split a String in Balanced Strings**
Given a balanced string s (equal number of L and R), split it into the maximum number of balanced substrings.

---

## Questions 91-100: More Math & Logic Problems

**91. Add Digits**
Given an integer num, repeatedly add all its digits until the result has only one digit, and return it.

**92. Ugly Number**
Given an integer n, return true if n is an ugly number (only prime factors are 2, 3, and 5).

**93. Self Dividing Numbers**
A self-dividing number is divisible by every digit it contains. Given left and right, return a list of all self-dividing numbers in the range.

**94. Perfect Number**
Given a positive integer num, return true if it is a perfect number (equal to the sum of its positive divisors excluding itself).

**95. Sum of Square Numbers**
Given a non-negative integer c, decide whether there're two integers a and b such that a² + b² = c.

**96. Construct the Rectangle**
Given a target area, design a rectangular web page with length L and width W where L >= W and L - W is minimized.

**97. Base 7**
Given an integer num, return a string of its base 7 representation.

**98. Hamming Distance**
Given two integers x and y, return the Hamming distance (number of positions where corresponding bits differ).

**99. Number Complement**
Given a positive integer, return its complement number (flip all bits in its binary representation).

**100. Reverse Bits**
Given a 32-bit unsigned integer, return the integer with its bits reversed.

---

## Tips for Solving These Problems

1. **Understand the problem**: Read carefully and clarify edge cases
2. **Start simple**: Write a brute force solution first if needed
3. **Use examples**: Work through small examples by hand
4. **Think about edge cases**: Empty input, single element, negative numbers, null/undefined
5. **Practice regularly**: Consistency is key for interview preparation
6. **Explain your approach**: In interviews, talk through your thinking process
7. **Test your code**: Verify with multiple test cases before submitting

---

## Common Patterns to Recognize

| Pattern | When to Use |
|---------|-------------|
| Two Pointers | Sorted arrays, finding pairs |
| Sliding Window | Subarray/substring problems |
| Fast & Slow Pointers | Linked list cycles, middle element |
| Hash Map | Finding duplicates, two sum |
| Stack | Parentheses, valid expressions |
| Recursion | Tree, linked list, divide & conquer |
| Bit Manipulation | XOR problems, checking powers of 2 |
| Prefix Sum | Range sum queries, pivot index |

Good luck with your interview preparation! 🍀
