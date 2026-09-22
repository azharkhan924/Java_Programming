# Swapping Techniques

---

## Problem

Do variables ki values **swap** (interchange) karna.

```text
Before: a = 5, b = 10
After: a = 10, b = 5
```

---

## 1⃣ Using Third Variable (Temp)

> **Safest and most readable approach** 

```java
int temp = a;
a = b;
b = temp;
```

**Step-by-step:**
```text
temp = a → temp = 5
a = b → a = 10
b = temp → b = 5
```

| Pros | Cons |
|------|------|
| Simple, readable | Extra memory (1 variable) |
| Works for all types | — |
| No overflow risk | — |

---

## 2⃣ Addition / Subtraction

> No extra variable needed, but has overflow risk Note: ```java
a = a + b; // a = 15
b = a - b; // b = 5 (original a)
a = a - b; // a = 10 (original b)
```

**Step-by-step:**
```text
a = 5, b = 10
a = 5 + 10 → a = 15
b = 15 - 10 → b = 5
a = 15 - 5 → a = 10
```

| Pros | Cons |
|------|------|
| No extra variable | Note: **Integer overflow** risk for large values |
| | Note: Precision issues with float/double |

---

## 3⃣ Multiplication / Division

> Not recommended for production code 

```java
a = a * b; // a = 50
b = a / b; // b = 5
a = a / b; // a = 10
```

| Pros | Cons |
|------|------|
| No extra variable | **Fails if either value is 0** (divide by zero!) |
| | Note: Integer overflow risk |
| | Note: Precision loss with floating-point |

---

## 4⃣ XOR Swap

> Elegant bitwise approach — works for integers only 

```java
a = a ^ b;
b = a ^ b; // b = original a
a = a ^ b; // a = original b
```

**Step-by-step:**
```text
a = 5 (0101)
b = 10 (1010)

a = 0101 ^ 1010 = 1111 (15)
b = 1111 ^ 1010 = 0101 (5) ← original a
a = 1111 ^ 0101 = 1010 (10) ← original b
```

**Why it works (mathematical proof):**
```text
a' = a ^ b
b' = a' ^ b = (a ^ b) ^ b = a ^ (b ^ b) = a ^ 0 = a 
a'' = a' ^ b' = (a ^ b) ^ a = b ^ (a ^ a) = b ^ 0 = b 
```

| Pros | Cons |
|------|------|
| No extra variable | Only works for integer types |
| No overflow risk | **Fails if both variables point to same location** |
| Looks cool in interviews | Note: Less readable |

> Note: **Gotcha:** Agar `a` aur `b` same variable hain (same memory location), XOR swap se dono **0** ho jaayenge!
> ```java
> int[] arr = {5};
> // Swapping arr[0] with arr[0] using XOR = disaster
> arr[0] = arr[0] ^ arr[0]; // 0!
> ```

---

## Comparison Table

| Method | Extra Memory | Overflow Risk | Zero Safe | Float Safe | Readable |
|--------|-------------|---------------|-----------|------------|----------|
| Temp Variable | 1 variable | None | Yes | Yes | ⭐⭐⭐ |
| Add/Subtract | None | Yes | Yes | Note: | ⭐⭐ |
| Mul/Divide | None | Yes | No | No | ⭐ |
| XOR | None | None | Yes | No | ⭐⭐ |

> ** Best Practice:** Real code me hamesha **temp variable** approach use karo. Baaki methods interviews aur competitive programming ke liye jaano.

---

## Bonus: Java One-Liner Swaps

### Using Arrays (Trick)

```java
a = new int[]{b, b = a}[0];
```

### Using Arithmetic

```java
b = (a + b) - (a = b);
```

> Note: These are clever tricks but **not recommended** for production code — readability matters!

---

[ Back to Index](./README.md) | [Next: Quick Revision](./16-quick-revision.md)
