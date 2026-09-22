# BigInteger

---

## 1. Why BigInteger?

Normal integer types ki **fixed limit** hoti hai:

| Type | Size | Max Value |
|------|------|-----------|
| `int` | 32-bit | ~2.1 billion |
| `long` | 64-bit | ~9.2 × 10¹⁸ |

Jab number in se bhi bada ho:

```java
long x = 999999999999999999999999L; // exceeds long range
```

### Solution — BigInteger

```java
BigInteger x = new BigInteger("999999999999999999999999"); // 
```

- **Package:** `java.math`
- **Arbitrary precision** — koi size limit nahi
- **Immutable** class

> `BigInteger` **integer values** ke liye hai. Decimal values ke liye `BigDecimal` use karo.

---

## 2. Creating BigInteger

### Method 1 — `valueOf()` (Small values)

```java
BigInteger x = BigInteger.valueOf(10);
BigInteger y = BigInteger.valueOf(20);
```

> `valueOf()` tab use karo jab value `long` ke andar fit ho.

### Method 2 — Constructor with String (Large values)

```java
BigInteger x = new BigInteger("123456789123456789123456789");
```

### Note: Common Trap

```java
// Wrong — literal exceeds long range
BigInteger x = BigInteger.valueOf(999999999999999999999L);

// Correct — use String
BigInteger x = new BigInteger("999999999999999999999");
```

---

## 3. Arithmetic — Methods, Not Operators

BigInteger me **normal operators directly use nahi** kar sakte:

```java
// Wrong
x + y
x - y
x * y
```

```java
// Correct
x.add(y);
x.subtract(y);
x.multiply(y);
x.divide(y);
x.mod(y);
```

### Example

```java
BigInteger x = BigInteger.valueOf(10);
BigInteger y = BigInteger.valueOf(20);

System.out.println(x.add(y)); // 30
System.out.println(x.subtract(y)); // -10
System.out.println(x.multiply(y)); // 200
System.out.println(y.divide(x)); // 2
System.out.println(x.mod(y)); // 10
```

### Note: Immutable — Result Store Karo

```java
x.add(y); // x change NAHI hota!

x = x.add(y); // result re-assign karo
```

---

## 4. Important Methods

| Method | Purpose | Example |
|--------|---------|---------|
| `add()` | Addition | `x.add(y)` |
| `subtract()` | Subtraction | `x.subtract(y)` |
| `multiply()` | Multiplication | `x.multiply(y)` |
| `divide()` | Division | `x.divide(y)` |
| `mod()` | Remainder | `x.mod(y)` |
| `abs()` | Absolute value | `x.abs()` |
| `pow(int)` | Power | `x.pow(10)` |
| `gcd()` | GCD | `x.gcd(y)` |
| `max()` | Maximum of two | `x.max(y)` |
| `min()` | Minimum of two | `x.min(y)` |
| `compareTo()` | Compare | `x.compareTo(y)` |
| `toString()` | To String | `x.toString()` |
| `intValue()` | To int | `x.intValue()` |
| `longValue()` | To long | `x.longValue()` |

---

## 5. Predefined Constants

```java
BigInteger.ZERO // 0
BigInteger.ONE // 1
BigInteger.TWO // 2 (Java 9+)
BigInteger.TEN // 10
```

---

## 6. Comparison — Don't Use `==`

```java
// Wrong — reference comparison
if (x == y)

// Correct — equals()
if (x.equals(y))

// Correct — compareTo()
if (x.compareTo(y) < 0)
 System.out.println("x is smaller");
```

### `compareTo()` Returns

| Return | Meaning |
|--------|---------|
| `0` | `x == y` |
| `< 0` | `x < y` |
| `> 0` | `x > y` |

---

## 7. BigInteger vs int/long

| Feature | `int` | `long` | `BigInteger` |
|---------|-------|--------|-------------|
| Size | Fixed (32-bit) | Fixed (64-bit) | Arbitrary precision |
| Type | Primitive | Primitive | Class/Object |
| Operators | `+ - * /` | `+ - * /` | Methods only |
| Very large numbers | No | Limited | Yes |
| Package | `java.lang` | `java.lang` | `java.math` |
| Immutable | — | — | Yes |

---

## 8. Use Cases

- Very large mathematical calculations
- Competitive Programming
- Cryptography (RSA, etc.)
- Large factorials / Fibonacci
- Exact integer calculations (no overflow)

---

## Interview Quick Questions

| Question | Answer |
|----------|--------|
| `BigInteger` kaunse package me hai? | `java.math` |
| `BigInteger` immutable hai? | Yes |
| Operators directly use kar sakte hain? | No — methods use karo |
| `x.add(y)` se `x` change hota hai? | No — new object return hota hai |
| `BigInteger.TWO` kab se available? | Java 9+ |
| Comparison kaise karein? | `equals()` ya `compareTo()` |
| Decimal values ke liye kya use karein? | `BigDecimal` |

---

## Quick Reference

```text
BigInteger
→ java.math package
→ Arbitrary precision integers
→ Immutable — results must be stored
→ No operators — use add(), subtract(), multiply(), divide(), mod()
→ Constants: ZERO, ONE, TWO (9+), TEN
→ Comparison: equals() or compareTo()
→ Large values: new BigInteger("string")
→ Small values: BigInteger.valueOf(long)
```

---

[Previous: Strings & String Pool](./05-strings-and-pool.md) · [Back to OOP Index](./README.md) · [Next: Garbage Collection](./07-garbage-collection.md)
