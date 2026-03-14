# Interview Preparation Checklist (200 Questions)

## 📋 Progress Summary

| Category | Total | Completed | Percentage |
|----------|-------|-----------|------------|
| SQL Practice | 50 | [ ] / 50 | __% |
| Write a Program | 50 | [ ] / 50 | __% |
| DB Design | 50 | [ ] / 50 | __% |
| Coding Interview Easy | 50 | [ ] / 50 | __% |
| **TOTAL** | **200** | **[ ] / 200** | **__%** |

---

## 📝 SQL Practice (50 Questions)
*Source: `sicpa/sql-practice.md`*

### Questions 1-10: Basic SELECT and Filtering
- [ ] 1. Find all employees with salary greater than 5000
- [ ] 2. Find all employees whose name starts with 'A'
- [ ] 3. Find all employees whose salary is between 3000 and 7000
- [ ] 4. Find all employees who do not have a department_id (NULL)
- [ ] 5. Find all employees whose name contains the letter 'o'
- [ ] 6. Return distinct department_ids from employees table
- [ ] 7. Find all employees ordered by salary descending
- [ ] 8. Find the top 5 highest paid employees
- [ ] 9. Find all employees whose department_id is 1, 3, or 5
- [ ] 10. Find all employees whose name ends with 'son'

### Questions 11-20: Aggregation and GROUP BY
- [ ] 11. Count the total number of employees
- [ ] 12. Find the average salary of all employees
- [ ] 13. Find the total sum of all salaries
- [ ] 14. Find the minimum and maximum salary
- [ ] 15. Count the number of employees in each department
- [ ] 16. Find the average salary per department
- [ ] 17. Find departments that have more than 5 employees
- [ ] 18. Find the department with the highest average salary
- [ ] 19. Count how many employees have NULL department_id
- [ ] 20. Find total salary budget per department (ordered by budget desc)

### Questions 21-30: JOINs
- [ ] 21. List all employees with their department names
- [ ] 22. List all departments with their employees (include dept with no employees)
- [ ] 23. Find all employees who work in the 'IT' department
- [ ] 24. Find departments that have no employees
- [ ] 25. Count employees in each department (including zero employees)
- [ ] 26. Find all employees working on the 'Website Redesign' project
- [ ] 27. Find all projects that have no employees assigned
- [ ] 28. Count how many projects each employee is working on
- [ ] 29. Find employees who work on more than 2 projects
- [ ] 30. List employees with department name and project names

### Questions 31-40: Subqueries and Self-Joins
- [ ] 31. Find employees who earn more than the average salary
- [ ] 32. Find the employee with the highest salary in each department
- [ ] 33. Find departments where average salary is above company-wide average
- [ ] 34. List all employees with their manager's name
- [ ] 35. Find all managers (employees who have at least one person reporting)
- [ ] 36. Find employees who earn more than their managers
- [ ] 37. Find the second highest salary in the company
- [ ] 38. Find employees who have the same salary as at least one other employee
- [ ] 39. Find departments that have employees with salary above 10000
- [ ] 40. Find employees who work in the same department as 'John Smith'

### Questions 41-50: Advanced Filtering and Data Manipulation
- [ ] 41. Update all employees' salary by 10% for those in department_id = 1
- [ ] 42. Delete all employees who do not have a department_id assigned
- [ ] 43. Insert a new employee with name 'Jane Doe', salary 6000, department_id 3
- [ ] 44. Find duplicate names in the employees table
- [ ] 45. Find employees whose salary is NULL
- [ ] 46. Use CASE to categorize employees as 'Low', 'Medium', 'High' by salary
- [ ] 47. Find the 3rd highest salary without using LIMIT/OFFSET
- [ ] 48. Find employees who joined in the last 6 months (hire_date column)
- [ ] 49. Find consecutive employees (by id) who work in the same department
- [ ] 50. Pivot data: show each department as a column with count of employees

---

## 💻 Write a Program (50 Questions)
*Source: `sicpa/write-a-program.md`*

### Questions 1-10: Array/List Operations
- [ ] 1. Find the second largest element in a list
- [ ] 2. Find the second smallest element in a list
- [ ] 3. Find the third largest element in a list
- [ ] 4. Find all duplicate elements in a list
- [ ] 5. Remove all duplicates from a list
- [ ] 6. Reverse a list without using built-in reverse function
- [ ] 7. Find the sum of all elements in a list
- [ ] 8. Find the product of all elements in a list
- [ ] 9. Find the maximum element without using built-in max function
- [ ] 10. Find the minimum element without using built-in min function

### Questions 11-20: Number Programs
- [ ] 11. Check if a number is prime
- [ ] 12. Print all prime numbers in a given range
- [ ] 13. Check if a number is an Armstrong number
- [ ] 14. Check if a number is a perfect number
- [ ] 15. Find the GCD of two numbers
- [ ] 16. Find the LCM of two numbers
- [ ] 17. Check if a number is a perfect square
- [ ] 18. Count the number of digits in a given integer
- [ ] 19. Find the sum of digits of a given number
- [ ] 20. Reverse a given integer

### Questions 21-30: String Programs
- [ ] 21. Check if a string is a palindrome
- [ ] 22. Reverse a string without using built-in reverse function
- [ ] 23. Count the number of vowels in a string
- [ ] 24. Count the number of words in a string
- [ ] 25. Check if two strings are anagrams of each other
- [ ] 26. Find the first non-repeated character in a string
- [ ] 27. Remove all spaces from a string
- [ ] 28. Convert a string to uppercase without using built-in function
- [ ] 29. Find the length of a string without using built-in length function
- [ ] 30. Check if a string contains only digits

### Questions 31-40: Sorting and Searching
- [ ] 31. Implement bubble sort
- [ ] 32. Implement selection sort
- [ ] 33. Implement insertion sort
- [ ] 34. Implement linear search
- [ ] 35. Implement binary search (assume sorted array)
- [ ] 36. Sort an array of 0s, 1s, and 2s (Dutch National Flag)
- [ ] 37. Move all negative numbers to the beginning of an array
- [ ] 38. Find all pairs in an array that sum to a given target
- [ ] 39. Rotate an array by k positions
- [ ] 40. Merge two sorted arrays into one sorted array

### Questions 41-50: Miscellaneous Programs
- [ ] 41. Print the Fibonacci series up to n terms
- [ ] 42. Find the factorial of a number using recursion
- [ ] 43. Check if a linked list has a cycle
- [ ] 44. Implement a stack using arrays
- [ ] 45. Implement a queue using arrays
- [ ] 46. Check if parentheses in a string are balanced
- [ ] 47. Find the middle element of a linked list
- [ ] 48. Swap two numbers without using a temporary variable
- [ ] 49. Print all permutations of a string
- [ ] 50. Find the intersection of two sorted arrays

---

## 🗄️ Database Design (50 Questions)
*Source: `sicpa/db-design.md`*

### Questions 1-10: Basic Entity Relationships
- [ ] 1. University: Students and Classes (M:N)
- [ ] 2. Library: Books, Authors, Members, Borrowing
- [ ] 3. E-commerce: Customers, Orders, Products
- [ ] 4. Hospital: Doctors, Patients, Medical Tests
- [ ] 5. Movie Theater: Movies, Showtimes, Ticket Bookings
- [ ] 6. Gym: Members, Classes, Trainers
- [ ] 7. Restaurant: Menu Items, Categories, Orders, Waiters, Tables
- [ ] 8. Hotel: Hotels, Rooms, Customers, Bookings
- [ ] 9. Car Rental: Customers, Cars, Rentals, Maintenance Records
- [ ] 10. School: Teachers, Subjects, Students, Classes

### Questions 11-20: One-to-Many Relationships
- [ ] 11. Blog: Users, Posts, Comments
- [ ] 12. Company: Departments, Employees, Projects
- [ ] 13. Music Streaming: Artists, Albums, Songs, Users, Playlists
- [ ] 14. Real Estate: Agents, Properties, Viewings, Buyers
- [ ] 15. Bank: Customers, Accounts, Transactions, Branches, Employees
- [ ] 16. Flight Booking: Airports, Flights, Passengers, Bookings
- [ ] 17. University Research: Professors, Projects, Researchers
- [ ] 18. Warehouse: Warehouses, Products, Suppliers
- [ ] 19. Social Media: Users, Photos, Comments, Follows
- [ ] 20. Conference: Conferences, Sessions, Speakers, Attendees

### Questions 21-30: Many-to-Many Relationships
- [ ] 21. Course Enrollment: Students, Courses, Prerequisites
- [ ] 22. Pet Adoption: Pets, Vaccinations, Volunteers, Adopters
- [ ] 23. Film Production: Actors, Movies, Directors, Genres
- [ ] 24. Supply Chain: Manufacturers, Products, Retailers, Locations
- [ ] 25. Tournament: Teams, Players, Matches, Referees
- [ ] 26. Subscription: Users, Magazines, Issues, Articles
- [ ] 27. Skill Management: Employees, Skills, Projects
- [ ] 28. Event Management: Events, Sponsors, Attendees
- [ ] 29. Recipe Sharing: Recipes, Ingredients, Users
- [ ] 30. Equipment Rental: Customers, Equipment, Rentals, Categories

### Questions 31-40: Advanced Design Scenarios
- [ ] 31. Version Control: Repositories, Branches, Commits, Files
- [ ] 32. LMS: Courses, Modules, Lessons, Students, Progress Tracking
- [ ] 33. Ride-Sharing: Drivers, Passengers, Rides, Stops
- [ ] 34. Food Delivery: Restaurants, Menu Items, Orders, Drivers
- [ ] 35. Task Management: Projects, Tasks, Users, Subtasks, Status, Priority
- [ ] 36. Healthcare Appointment: Patients, Doctors, Appointments, Follow-ups
- [ ] 37. Crowdfunding: Users, Campaigns, Backers, Reward Tiers
- [ ] 38. Job Portal: Companies, Job Listings, Candidates, Skills
- [ ] 39. Parking Management: Parking Lots, Spots, Reservations, Time Slots
- [ ] 40. Donation Management: Donors, Donations, Charities, Programs

### Questions 41-50: Special Design Considerations
- [ ] 41. Multi-tenant SaaS Application (tenant isolation)
- [ ] 42. Historical Salary Tracking (effective date tracking)
- [ ] 43. Product Catalog with Variants (size, color, price, stock)
- [ ] 44. Audit Logging System (who, what, when changes)
- [ ] 45. Role-Based Access Control (RBAC): Users, Roles, Permissions
- [ ] 46. Notification System: Users, Notifications, Types, Read Status
- [ ] 47. Review and Rating System: Users, Products, Reviews, Votes
- [ ] 48. Shipping and Tracking: Orders, Shipments, Tracking Events, Items
- [ ] 49. CMS: Pages, Versions, Tags, Users, Edits
- [ ] 50. Recommendation System: User Preferences, Interactions, Similar Items

---

## 🧮 Coding Interview Easy (50 Questions)
*Source: `sicpa/coding-interview-easy.md`*

### Questions 1-10: Number Operations
- [ ] 1. Palindrome Number
- [ ] 2. Two Sum
- [ ] 3. Plus One
- [ ] 4. Reverse Integer
- [ ] 5. Roman to Integer
- [ ] 6. Integer to Roman
- [ ] 7. Power of Two
- [ ] 8. Factorial
- [ ] 9. Fibonacci Number
- [ ] 10. Count Digits

### Questions 11-20: String Manipulation
- [ ] 11. Valid Anagram
- [ ] 12. Valid Palindrome
- [ ] 13. Reverse String
- [ ] 14. Longest Common Prefix
- [ ] 15. Is Subsequence
- [ ] 16. Implement strStr()
- [ ] 17. Length of Last Word
- [ ] 18. Convert to Lowercase
- [ ] 19. Count Vowels
- [ ] 20. Remove Duplicates from String

### Questions 21-30: Array Operations
- [ ] 21. Maximum Subarray
- [ ] 22. Best Time to Buy and Sell Stock
- [ ] 23. Contains Duplicate
- [ ] 24. Move Zeroes
- [ ] 25. Remove Duplicates from Sorted Array
- [ ] 26. Merge Sorted Array
- [ ] 27. Find Maximum in Array
- [ ] 28. Find Minimum in Array
- [ ] 29. Array Sum
- [ ] 30. Second Largest Element

### Questions 31-40: Linked List Basics
- [ ] 31. Reverse Linked List
- [ ] 32. Middle of Linked List
- [ ] 33. Linked List Cycle
- [ ] 34. Delete Node in Linked List
- [ ] 35. Merge Two Sorted Lists
- [ ] 36. Remove Linked List Elements
- [ ] 37. Palindrome Linked List
- [ ] 38. Count Nodes in Linked List
- [ ] 39. Find Nth Node from End
- [ ] 40. Insert at Beginning

### Questions 41-50: Miscellaneous Easy Problems
- [ ] 41. Valid Parentheses
- [ ] 42. Climbing Stairs
- [ ] 43. Single Number
- [ ] 44. Intersection of Two Arrays
- [ ] 45. FizzBuzz
- [ ] 46. Count Primes
- [ ] 47. Happy Number
- [ ] 48. Missing Number
- [ ] 49. Majority Element
- [ ] 50. Excel Sheet Column Title

---

## 📅 Daily Tracking

### Day 1
| Session | Target | Completed |
|---------|--------|-----------|
| Morning | SQL Q1-25 | [ ] |
| Afternoon | Coding Easy Q1-25 | [ ] |
| Evening | SQL Q26-50 | [ ] |

### Day 2
| Session | Target | Completed |
|---------|--------|-----------|
| Morning | Coding Easy Q26-50 | [ ] |
| Afternoon | Write Program Q1-25 | [ ] |
| Evening | DB Design Q1-25 | [ ] |

### Day 3
| Session | Target | Completed |
|---------|--------|-----------|
| Morning | Write Program Q26-50 | [ ] |
| Afternoon | DB Design Q26-50 | [ ] |
| Evening | Review Weak Areas | [ ] |

### Day 4
| Session | Target | Completed |
|---------|--------|-----------|
| Morning | SQL + Coding Review | [ ] |
| Afternoon | Program + DB Design Review | [ ] |
| Evening | Mock Interview Simulation | [ ] |

---

## 🎯 Notes & Weak Areas

Use this space to track questions you need to revisit:

### SQL Weak Areas
- 

### Write a Program Weak Areas
- 

### DB Design Weak Areas
- 

### Coding Interview Weak Areas
- 

---

## ✅ Final Checklist (Before Interview)

- [ ] All 200 questions attempted
- [ ] Solutions documented for review
- [ ] Key SQL syntax memorized
- [ ] Common coding patterns reviewed
- [ ] DB design principles internalized
- [ ] Mock interview simulation completed
- [ ] Slept well the night before
- [ ] Ready to ace the interview! 💪

---

**Good luck with your interview preparation! 🍀**
