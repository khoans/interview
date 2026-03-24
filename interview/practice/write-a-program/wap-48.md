**48.** Swap two numbers without a temporary variable.



========== THIS SECTION IS THE ANSWER ==========

## Approach 1: Arithmetic (Addition/Subtraction)

```java
public class SwapNumbers {

    public static void main(String[] args) {
        int a = 5, b = 10;

        a = a + b;   // a = 15
        b = a - b;   // b = 15 - 10 = 5
        a = a - b;   // a = 15 - 5 = 10

        System.out.println("a = " + a + ", b = " + b);  // a = 10, b = 5
    }
}
```

## Approach 2: XOR (Bitwise — no overflow risk)

```java
public static void swapXOR() {
    int a = 5, b = 10;

    a = a ^ b;   // a = 0101 ^ 1010 = 1111
    b = a ^ b;   // b = 1111 ^ 1010 = 0101 = 5
    a = a ^ b;   // a = 1111 ^ 0101 = 1010 = 10

    System.out.println("a = " + a + ", b = " + b);  // a = 10, b = 5
}
```

## Approach 3: Multiplication/Division (avoid if zero possible)

```java
public static void swapMulDiv() {
    int a = 5, b = 10;

    a = a * b;   // a = 50
    b = a / b;   // b = 50 / 10 = 5
    a = a / b;   // a = 50 / 5 = 10

    System.out.println("a = " + a + ", b = " + b);
}
```

## Comparison

| Approach | Overflow Risk | Zero Safe | Readable |
|----------|--------------|-----------|----------|
| Addition | Yes (large values) | Yes | Good |
| XOR | No | Yes | Less intuitive |
| Multiplication | Yes | No (division by zero) | Good |

## Edge Cases
- Both values are the same (XOR gives 0, then 0 — works but tricky)
- One value is zero (arithmetic works, multiplication fails)
- Integer overflow with large values (addition/multiplication)
- Negative numbers (all approaches work)

## Interview Note
In practice, just use a temp variable — it is clearer, safer, and modern compilers optimize it anyway. This question tests understanding of XOR properties and arithmetic manipulation.

**XOR properties to remember:**
- `a ^ a = 0`
- `a ^ 0 = a`
- XOR is commutative and associative
