# ⬆ Increment & Decrement Operators

---

## Basics

```text
++   Increment (value + 1)
--   Decrement (value - 1)
```

Ye variable ki value ko **1 se increase/decrease** karte hain.

---

## 1. Pre-Increment `++x`

> **Pehle increment karo → phir use karo**

```java
int x = 5;
int y = ++x;
```

**Step-by-step:**
```text
Step 1: x increment → x = 6
Step 2: y = updated value → y = 6
```

**Result:**
```text
x = 6
y = 6
```

---

## 2. Post-Increment `x++`

> **Pehle use karo → phir increment karo**

```java
int x = 5;
int y = x++;
```

**Step-by-step:**
```text
Step 1: y = current value → y = 5
Step 2: x increment → x = 6
```

**Result:**
```text
x = 6
y = 5
```

---

## 3. Pre-Decrement `--x`

```java
int x = 5;
int y = --x;
```

**Result:**
```text
x = 4
y = 4
```

---

## 4. Post-Decrement `x--`

```java
int x = 5;
int y = x--;
```

**Result:**
```text
x = 4
y = 5
```

---

## Summary Table

| Expression | x (before = 5) | y | x (after) |
|-----------|----------------|---|-----------|
| `y = ++x` | — | **6** | **6** |
| `y = x++` | — | **5** | **6** |
| `y = --x` | — | **4** | **4** |
| `y = x--` | — | **5** | **4** |

### Easy Memory Trick

```text
PRE  → ++ pehle hai, toh increment PEHLE hoga
POST → ++ baad me hai, toh increment BAAD ME hoga
```

---

## Note: Tokenization Gotchas — `+++` Expressions

Java me `+` characters ko **lexer** left-to-right greedy matching se tokens me break karta hai.

### Example: `x++ + ++y`

```java
int x = 5, y = 5;
int result = x++ + ++y;
```

**Step-by-step:**
```text
x++ → use 5, then x becomes 6
++y → y becomes 6, use 6
result = 5 + 6 = 11
```

### Note: Confusing Expression: `x+++++x`

```java
x+++++x
```

Java lexer ise parse karta hai as:
```text
x++ ++ +x    →    Error! (x++ returns a value, not a variable — ++ can't apply to it)
```

> ** Best Practice:** Hamesha spacing clearly use karo. `x++ + ++x` likhna better hai than `x++++x`.

---

## Where `++`/`--` Work Nahi Karte

| Type | Works? |
|------|--------|
| `int`, `long`, `byte`, `short` | Yes |
| `float`, `double` | Yes |
| `char` | Yes |
| `boolean` | No |
| `String` | No |
| Literal values (`5++`) | No |
| Expressions (`(a+b)++`) | No |

```java
boolean flag = true;
flag++;         // Compile error

String s = "hi";
s++;            // Compile error

5++;            // Not a variable
```

---

## Tricky Interview Examples

### Example 1
```java
int x = 10;
x = x++;
System.out.println(x);   // Output: 10  (NOT 11!)
```
**Why?** `x++` returns old value (10), which gets assigned back to `x`. Then increment happens, but assignment already overwrote it.

### Example 2
```java
int x = 5;
int y = x++ + ++x;
System.out.println(y);   // Output: 12
```
**Why?**
```text
x++ → use 5, x becomes 6
++x → x becomes 7, use 7
y = 5 + 7 = 12
```

### Example 3
```java
int x = 3;
System.out.println(x++ + x++ + x++);  // Output: 12
```
**Why?**
```text
First  x++ → use 3, x becomes 4
Second x++ → use 4, x becomes 5
Third  x++ → use 5, x becomes 6
Result = 3 + 4 + 5 = 12
```

---

[Back to Index](./README.md) | [Next: Bitwise & Shift Operators](./11-bitwise-and-shift.md)
