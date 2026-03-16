# 11. Write a program to check if a number is prime.

## ✅ Complete Solution

```java
import java.util.*;

public class PrimeChecker {
    
    /**
     * Check if a number is prime
     * Time: O(√n), Space: O(1)
     */
    static boolean isPrime(int n) {
        // Edge case: numbers less than 2 are not prime
        if (n < 2) {
            return false;
        }
        
        // Edge case: 2 is prime
        if (n == 2) {
            return true;
        }
        
        // Edge case: even numbers (except 2) are not prime
        if (n % 2 == 0) {
            return false;
        }
        
        // Check odd divisors up to sqrt(n)
        for (int i = 3; i * i <= n; i += 2) {
            if (n % i == 0) {
                return false;  // Found a divisor, not prime
            }
        }
        
        return true;  // No divisors found, is prime
    }
    
    // Test cases
    public static void main(String[] args) {
        System.out.println("=== Prime Number Checker ===\n");
        
        // Test 1: Prime numbers
        System.out.println("Prime numbers:");
        System.out.println("isPrime(2) = " + isPrime(2));    // true ✅
        System.out.println("isPrime(3) = " + isPrime(3));    // true ✅
        System.out.println("isPrime(5) = " + isPrime(5));    // true ✅
        System.out.println("isPrime(7) = " + isPrime(7));    // true ✅
        System.out.println("isPrime(11) = " + isPrime(11));  // true ✅
        System.out.println("isPrime(97) = " + isPrime(97));  // true ✅
        
        // Test 2: Not prime numbers
        System.out.println("\nNot prime numbers:");
        System.out.println("isPrime(0) = " + isPrime(0));    // false ✅
        System.out.println("isPrime(1) = " + isPrime(1));    // false ✅
        System.out.println("isPrime(4) = " + isPrime(4));    // false ✅
        System.out.println("isPrime(6) = " + isPrime(6));    // false ✅
        System.out.println("isPrime(9) = " + isPrime(9));    // false ✅
        System.out.println("isPrime(100) = " + isPrime(100));  // false ✅
        
        // Test 3: Negative numbers
        System.out.println("\nNegative numbers:");
        System.out.println("isPrime(-5) = " + isPrime(-5));  // false ✅
        System.out.println("isPrime(-1) = " + isPrime(-1));  // false ✅
    }
}
```

---

## 📊 How It Works

### Example 1: Prime Number (n = 17)

```
Input: n = 17

STEP 1: Edge case checks
┌──────────────────────────┐
│ n < 2?    → 17 < 2? No   │
│ n == 2?   → 17 == 2? No  │
│ n % 2 == 0? → 17 % 2 = 1? No (odd) │
└──────────────────────────┘
→ Continue to loop

STEP 2: Check odd divisors up to √17 ≈ 4.12
┌──────┬───────┬──────────────┬────────┬─────────────────┐
│  i   │ i * i │ i * i <= 17? │ 17 % i │     Action      │
├──────┼───────┼──────────────┼────────┼─────────────────┤
│  3   │   9   │ Yes (9 ≤ 17) │   2    │ Continue        │
│  5   │  25   │ No (25 > 17) │   -    │ Stop loop!      │
└──────┴───────┴──────────────┴────────┴─────────────────┘

STEP 3: No divisors found
→ Return true ✅

RESULT: 17 is PRIME ✅
```

### Example 2: Not Prime (n = 9)

```
Input: n = 9

STEP 1: Edge case checks
┌──────────────────────────┐
│ n < 2?    → 9 < 2? No    │
│ n == 2?   → 9 == 2? No   │
│ n % 2 == 0? → 9 % 2 = 1? No (odd) │
└──────────────────────────┘
→ Continue to loop

STEP 2: Check odd divisors up to √9 = 3
┌──────┬───────┬──────────────┬────────┬─────────────────┐
│  i   │ i * i │ i * i <= 9?  │  9 % i │     Action      │
├──────┼───────┼──────────────┼────────┼─────────────────┤
│  3   │   9   │ Yes (9 ≤ 9)  │   0    │ Found divisor!  │
└──────┴───────┴──────────────┴────────┴─────────────────┘

STEP 3: Divisor found (3)
→ Return false ❌

RESULT: 9 is NOT PRIME ❌
```

---

## 🎯 Key Insight: Why Check Only Up to √n?

### The Mathematical Proof

```
┌─────────────────────────────────────────────────────────┐
│  THEOREM: If n is composite, it has a factor ≤ √n       │
│                                                         │
│  PROOF:                                                 │
│  ───────                                                │
│  If n is composite, then n = a × b for some a, b > 1    │
│                                                         │
│  CASE 1: Both a and b are ≤ √n                          │
│          → We'll find a (the smaller one)               │
│                                                         │
│  CASE 2: Both a and b are > √n                          │
│          → Then a × b > √n × √n = n                     │
│          → Contradiction! (a × b should equal n)        │
│                                                         │
│  CASE 3: One is ≤ √n, one is > √n                       │
│          → We'll find the smaller one (≤ √n)            │
│                                                         │
│  CONCLUSION: At least one factor must be ≤ √n           │
│  → We only need to check up to √n!                      │
└─────────────────────────────────────────────────────────┘
```

### Visual Example

```
Example: n = 100

All factor pairs:
┌──────────────────────────────────────────┐
│  1 × 100  ← 1 ≤ √100 (10) ✓             │
│  2 × 50   ← 2 ≤ 10 ✓                     │
│  4 × 25   ← 4 ≤ 10 ✓                     │
│  5 × 20   ← 5 ≤ 10 ✓                     │
│ 10 × 10   ← 10 ≤ 10 ✓ (√100)            │
│ ────────────────────────                  │
│ 20 × 5    ← Repeats! (already found 5)   │
│ 25 × 4    ← Repeats! (already found 4)   │
│ 50 × 2    ← Repeats! (already found 2)   │
│ 100 × 1   ← Repeats! (already found 1)   │
└──────────────────────────────────────────┘

Key Observation:
- After √n, factors repeat in reverse order!
- If we check up to √n and find no factors, n is prime!
- This reduces complexity from O(n) to O(√n)!
```

### Complexity Comparison

```
┌─────────────────────────────────────────────────────────┐
│  Approach          │ Checks For n=100 │ Time Complexity │
├─────────────────────────────────────────────────────────┤
│  Brute Force       │ 99 checks        │ O(n)            │
│  Up to n/2         │ 49 checks        │ O(n/2) = O(n)   │
│  Up to √n          │ 9 checks         │ O(√n) ✅        │
│  Up to √n (odd)    │ 4 checks         │ O(√n/2) = O(√n) │
└─────────────────────────────────────────────────────────┘

For large n (e.g., n = 1,000,000):
- Brute Force: 999,999 checks ❌
- Up to √n: Only 999 checks ✅ (1000x faster!)
```

---

## 🎯 Alternative Solutions

### Option 1: Brute Force (Simple but Slower) ❌

```java
static boolean isPrime(int n) {
    if (n < 2) {
        return false;
    }
    
    // Check ALL numbers from 2 to n-1
    for (int i = 2; i < n; i++) {
        if (n % i == 0) {
            return false;
        }
    }
    
    return true;
}
```

| Metric | Value |
|--------|-------|
| Time | O(n) - checks all numbers |
| Space | O(1) |
| For n=100 | 98 iterations |
| For n=1,000,000 | 999,998 iterations ❌ |

**Why not use this:** Too slow for large numbers!

---

### Option 2: Check Up to n/2 (Better but Still Slow) ⚠️

```java
static boolean isPrime(int n) {
    if (n < 2) {
        return false;
    }
    
    // No factor can be greater than n/2
    for (int i = 2; i <= n / 2; i++) {
        if (n % i == 0) {
            return false;
        }
    }
    
    return true;
}
```

| Metric | Value |
|--------|-------|
| Time | O(n/2) = O(n) |
| Space | O(1) |
| For n=100 | 49 iterations |
| For n=1,000,000 | 499,999 iterations ⚠️ |

**Why not use this:** Still O(n), not optimal!

---

### Option 3: Check Up to √n (Optimal) ✅ RECOMMENDED

```java
static boolean isPrime(int n) {
    if (n < 2) {
        return false;
    }
    
    // Check up to sqrt(n)
    for (int i = 2; i * i <= n; i++) {
        if (n % i == 0) {
            return false;
        }
    }
    
    return true;
}
```

| Metric | Value |
|--------|-------|
| Time | O(√n) ✅ |
| Space | O(1) |
| For n=100 | 9 iterations |
| For n=1,000,000 | 999 iterations ✅ (1000x faster!) |

**Why use this:** Optimal balance of simplicity and performance!

---

### Option 4: Check Up to √n with Even Optimization (Most Efficient) ✅✅

```java
static boolean isPrime(int n) {
    // Edge case: numbers less than 2
    if (n < 2) {
        return false;
    }
    
    // Edge case: 2 is prime
    if (n == 2) {
        return true;
    }
    
    // Edge case: even numbers are not prime
    if (n % 2 == 0) {
        return false;
    }
    
    // Check only ODD divisors up to √n
    for (int i = 3; i * i <= n; i += 2) {
        if (n % i == 0) {
            return false;
        }
    }
    
    return true;
}
```

| Metric | Value |
|--------|-------|
| Time | O(√n/2) = O(√n) ✅ |
| Space | O(1) |
| For n=100 | 4 iterations (only odd: 3, 5, 7, 9) |
| For n=1,000,000 | ~500 iterations ✅ (2000x faster than brute!) |

**Why use this:** Best performance! This is the **recommended solution** for interviews!

---

### Option 5: Using Math.sqrt() (Same Complexity)

```java
static boolean isPrime(int n) {
    if (n < 2) {
        return false;
    }
    
    int sqrt = (int) Math.sqrt(n);
    for (int i = 2; i <= sqrt; i++) {
        if (n % i == 0) {
            return false;
        }
    }
    
    return true;
}
```

| Metric | Value |
|--------|-------|
| Time | O(√n) |
| Space | O(1) |
| Pros | More readable (explicit sqrt) |
| Cons | Slight overhead from Math.sqrt() call |

**Note:** `i * i <= n` is slightly faster than `Math.sqrt()` because it avoids the function call!

---

## 💡 Interview Tips

### Optimization Progression

```
┌─────────────────────────────────────────────────────────┐
│  How to Approach This in an Interview:                  │
│                                                         │
│  STEP 1: Start with brute force                         │
│  ─────────────────────────────                          │
│  "First, I'd check all numbers from 2 to n-1..."        │
│  Time: O(n)                                             │
│                                                         │
│  STEP 2: Optimize to n/2                                │
│  ───────────────────────                                │
│  "Actually, no factor can be greater than n/2..."       │
│  Time: O(n/2) = O(n)                                    │
│                                                         │
│  STEP 3: Optimize to √n (KEY INSIGHT!)                  │
│  ─────────────────────────────                          │
│  "If n = a × b, then at least one factor ≤ √n..."       │
│  Time: O(√n) ✅                                         │
│                                                         │
│  STEP 4: Skip even numbers                              │
│  ─────────────────────────────                          │
│  "After checking 2, we only need odd numbers..."        │
│  Time: O(√n/2) = O(√n) ✅                              │
│                                                         │
│  This shows problem-solving progression!                │
└─────────────────────────────────────────────────────────┘
```

### Edge Cases to Remember

```java
┌─────────────────────────────────────────────────────────┐
│  Test Case           │ Expected │ Why                   │
├─────────────────────────────────────────────────────────┤
│  isPrime(0)          │ false    │ Less than 2           │
│  isPrime(1)          │ false    │ Less than 2           │
│  isPrime(2)          │ true     │ Smallest prime        │
│  isPrime(3)          │ true     │ Prime                 │
│  isPrime(4)          │ false    │ Even (divisible by 2) │
│  isPrime(9)          │ false    │ 9 = 3 × 3             │
│  isPrime(17)         │ true     │ Prime                 │
│  isPrime(20)         │ false    │ Even                  │
│  isPrime(97)         │ true     │ Prime                 │
│  isPrime(100)        │ false    │ Even                  │
│  isPrime(-5)         │ false    │ Negative              │
│  isPrime(-1)         │ false    │ Negative              │
└─────────────────────────────────────────────────────────┘
```

### Common Mistakes

```java
┌─────────────────────────────────────────────────────────┐
│  Mistake                  │ Why Wrong    │ Fix          │
├─────────────────────────────────────────────────────────┤
│  if (n <= 2) return false │ 2 is prime!  │ Use n < 2    │
│  i * i < n                │ Misses squares│ Use <=      │
│  Check all to n           │ Too slow O(n)│ Check to √n  │
│  Check even numbers       │ Unnecessary  │ Skip evens   │
│  No edge case handling    │ Wrong for 0,1│ Add checks   │
└─────────────────────────────────────────────────────────┘
```

### Complexity Cheat Sheet

```
┌─────────────────────────────────────────────────────────┐
│  Operation              │ Time      │ Space             │
├─────────────────────────────────────────────────────────┤
│  Brute Force (to n)     │ O(n)      │ O(1)              │
│  Check to n/2           │ O(n)      │ O(1)              │
│  Check to √n            │ O(√n) ✅  │ O(1)              │
│  Check to √n (odd only) │ O(√n) ✅  │ O(1)              │
│  Math.sqrt() call       │ O(1)      │ O(1)              │
│  Modulo operation (%)   │ O(1)      │ O(1)              │
└─────────────────────────────────────────────────────────┘

For n = 1,000,000:
- O(n)      = 1,000,000 operations ❌
- O(√n)     = 1,000 operations ✅ (1000x faster!)
- O(√n/2)   = 500 operations ✅ (2000x faster!)
```

---

## 📝 Key Takeaway

```
┌─────────────────────────────────────────────────────────┐
│  CHECK IF NUMBER IS PRIME - SUMMARY                     │
│                                                         │
│  ✅ OPTIMAL SOLUTION:                                   │
│  ───────────────────                                    │
│  1. Handle edge cases (n < 2, n = 2, even numbers)      │
│  2. Check odd divisors only up to √n                    │
│  3. Time: O(√n), Space: O(1)                            │
│                                                         │
│  🔑 KEY INSIGHT:                                        │
│  ───────────────                                        │
│  If n = a × b, then min(a, b) ≤ √n                      │
│  → Only need to check divisors up to √n!                │
│  → Reduces O(n) to O(√n) - 1000x faster for large n!    │
│                                                         │
│  ⚡ OPTIMIZATIONS:                                      │
│  ──────────────                                         │
│  1. Skip even numbers (after checking 2)                │
│  2. Use i * i <= n instead of Math.sqrt()               │
│  3. Handle small cases (0, 1, 2) separately             │
│                                                         │
│  🎯 INTERVIEW TIPS:                                     │
│  ──────────────                                         │
│  - Start with brute force, then optimize                │
│  - Explain the √n insight clearly                       │
│  - Mention edge cases (0, 1, 2, negatives)              │
│  - Discuss time/space complexity                        │
│                                                         │
│  This is a CLASSIC interview question!                  │
│  Memorize this pattern!                                 │
└─────────────────────────────────────────────────────────┘
```

---

## 🧪 Practice Problems

Try these variations to master the pattern:

1. **Print all primes up to n** (Sieve of Eratosthenes)
2. **Find the nth prime number**
3. **Count primes in a range [a, b]**
4. **Check if a number is semi-prime** (product of exactly 2 primes)
5. **Find all prime factors of a number**

---

**Good luck with your interview preparation! 🍀**
