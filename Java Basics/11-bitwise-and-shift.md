# Bitwise & Shift Operators

---

## Overview

Bitwise operators **individual bits** par operation perform karte hain.

| Operator | Name | Type |
|----------|------|------|
| `&` | Bitwise AND | Binary |
| `\|` | Bitwise OR | Binary |
| `^` | Bitwise XOR | Binary |
| `~` | Bitwise Complement | Unary |
| `<<` | Left Shift | Binary |
| `>>` | Signed Right Shift | Binary |
| `>>>` | Unsigned Right Shift | Binary |

---

## 1. Bitwise AND `&`

> Dono bits `1` hone chahiye result `1` ho.

| A | B | A & B |
|---|---|-------|
| 0 | 0 | **0** |
| 0 | 1 | **0** |
| 1 | 0 | **0** |
| 1 | 1 | **1** |

### Example: `18 & 21`

```text
  18 = 1 0 0 1 0
  21 = 1 0 1 0 1
       ---------
  &  = 1 0 0 0 0  =  16
```

```java
System.out.println(18 & 21);   // Output: 16
```

### Common Uses
- **Check if number is even/odd:** `n & 1` → `0` means even, `1` means odd
- **Masking:** Specific bits extract karna

```java
System.out.println(10 & 1);    // 0 → even
System.out.println(11 & 1);    // 1 → odd
```

---

## 2. Bitwise OR `|`

> Koi bhi ek bit `1` ho to result `1` hai.

| A | B | A \| B |
|---|---|--------|
| 0 | 0 | **0** |
| 0 | 1 | **1** |
| 1 | 0 | **1** |
| 1 | 1 | **1** |

### Example: `18 | 21`

```text
  18 = 1 0 0 1 0
  21 = 1 0 1 0 1
       ---------
  |  = 1 0 1 1 1  =  23
```

```java
System.out.println(18 | 21);   // Output: 23
```

---

## 3. Bitwise XOR `^`

> Dono bits **different** hone chahiye result `1` ho.

| A | B | A ^ B |
|---|---|-------|
| 0 | 0 | **0** |
| 0 | 1 | **1** |
| 1 | 0 | **1** |
| 1 | 1 | **0** |

### Example: `18 ^ 21`

```text
  18 = 1 0 0 1 0
  21 = 1 0 1 0 1
       ---------
  ^  = 0 0 1 1 1  =  7
```

```java
System.out.println(18 ^ 21);   // Output: 7
```

### XOR Properties (Important!)

```text
a ^ a = 0        → XOR with itself = 0
a ^ 0 = a        → XOR with 0 = same value
a ^ b = b ^ a    → Commutative
(a ^ b) ^ c = a ^ (b ^ c)  → Associative
```

> ** Interview Use:** Array me ek number jo ek hi baar aaya hai, baaki sab twice → XOR of all elements = answer!

---

## 4. Bitwise Complement `~`

> Har bit ko **invert** karta hai: `0 → 1`, `1 → 0`

### Formula (for signed integers)

```text
~n = -(n + 1)
```

### Examples

```java
System.out.println(~15);     // -16
System.out.println(~0);      // -1
System.out.println(~(-1));   // 0
System.out.println(~10);     // -11
```

**Why `~15 = -16`?**
```text
15 in binary (32-bit):  00000000 00000000 00000000 00001111
~15:                    11111111 11111111 11111111 11110000
This is -16 in two's complement representation
```

---

## 5. Left Shift `<<`

> Bits ko **left** me shift karta hai. Right side par **0** fill hota hai.

### Formula

```text
x << n = x × 2ⁿ  (for normal positive values, subject to overflow)
```

### Example: `20 << 3`

```text
  20 =    1 0 1 0 0
  20 << 3 = 1 0 1 0 0 0 0 0  =  160

  Verification: 20 × 2³ = 20 × 8 = 160 
```

```java
System.out.println(20 << 3);    // 160
System.out.println(1 << 10);    // 1024  (2¹⁰)
System.out.println(5 << 1);     // 10    (5 × 2)
```

---

## 6. Signed Right Shift `>>`

> Bits ko **right** me shift karta hai. Left side par **sign bit** fill hota hai (sign preserve).

### Formula

```text
x >> n ≈ x / 2ⁿ  (integer division, rounds towards negative infinity)
```

### Example: `20 >> 3`

```text
20 >> 3 ≈ 20 / 8 = 2

Verification:
  20 = 1 0 1 0 0
  20 >> 3 =     1 0  =  2 
```

### Negative Number Example

```java
System.out.println(-20 >> 3);   // -3
// Because sign bit (1) is preserved during shift
```

---

## 7. Unsigned Right Shift `>>>`

> Right shift karta hai, lekin left side par **hamesha 0** fill karta hai (sign ignore).

```java
System.out.println(20 >>> 3);    // 2  (same as >> for positive numbers)
System.out.println(-20 >>> 3);   // 536870909  (very large positive number!)
```

### `>>` vs `>>>` Difference

| | `>>` (Signed) | `>>>` (Unsigned) |
|---|---|---|
| Positive numbers | Same result | Same result |
| Negative numbers | Sign preserved (stays negative) | Sign lost (becomes positive) |
| Left fill | Sign bit | Always `0` |

---

## Bitwise Operators with Boolean

`&`, `|`, `^` **boolean** operands par bhi kaam karte hain:

```java
System.out.println(true & false);    // false
System.out.println(true | false);    // true
System.out.println(true ^ false);    // true
System.out.println(true ^ true);     // false
```

### Note: `&` vs `&&` with Booleans

| | `&` (Bitwise) | `&&` (Logical) |
|---|---|---|
| Short-circuits? | No — always evaluates both sides | Yes |
| Use case | Rare with booleans | Standard for conditions |

```java
// With &&: second condition NOT evaluated if first is false
false && someMethod()     // someMethod() skipped

// With &: BOTH sides ALWAYS evaluated
false & someMethod()      // someMethod() still called!
```

### These Do NOT Work on Boolean

```text
<<   >>   >>>   ~
```

---

## Quick Reference

```text
AND (&):   1 & 1 = 1, rest = 0   → "Both must be 1"
OR  (|):   0 | 0 = 0, rest = 1   → "Either can be 1"
XOR (^):   Same = 0, Diff = 1    → "Must be different"
NOT (~):   ~n = -(n+1)           → "Flip all bits"

<<  : x × 2ⁿ                    → "Multiply by power of 2"
>>  : x / 2ⁿ (sign preserved)   → "Divide by power of 2"
>>> : x / 2ⁿ (unsigned)         → "Divide, ignore sign"
```

---

[Back to Index](./README.md) | [Next: Characters & Unicode](./12-characters-and-unicode.md)
