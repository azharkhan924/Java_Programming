# Type Casting in Java

---

## What is Type Casting?

**Type Casting** = ek data type ki value ko doosre data type me convert karna.

Java me **2 types** ki type conversion hoti hai:

| Type | Direction | Cast Required? | Data Loss? |
|------|-----------|---------------|------------|
| **Widening** (Implicit) | Smaller → Larger | No | Usually No |
| **Narrowing** (Explicit) | Larger → Smaller | Yes | Note: Possible |

---

## 1. Widening / Implicit Type Casting

Smaller type → Larger type. Compiler **automatically** kar deta hai.

### Widening Hierarchy

```text
byte → short → int → long → float → double
                ↑
              char
```

### Examples

```java
int x = 10;
double y = x;           // valid Implicit widening: int → double
System.out.println(y);  // Output: 10.0

byte b = 50;
int i = b;              // valid Implicit widening: byte → int

char c = 'A';
int n = c;              // valid Implicit widening: char → int (65)
```

### Key Points

- JVM/compiler automatically conversion karta hai
- Source aur destination types compatible hone chahiye
- Destination type source type se broader hona chahiye
- Note: **Usually** data loss nahi hota

### Note: Important Exception — `long → float` Precision Loss

```java
long bigNum = 123456789012345L;
float f = bigNum;   // Widening allowed, BUT...
System.out.println(f);          // 1.23456788E14 — digits lost!
System.out.println((long) f);   // 123456789012344 — not exact!
```

> Widening ka matlab sirf **bytes ki ranking** nahi hai. `float` (4 bytes) `long` (8 bytes) se chhota hai, phir bhi `long → float` widening hai kyunki Java spec allows it. But large integer values ke **exact digits lose** ho sakte hain.

---

## 2. Narrowing / Explicit Type Casting

Larger type → Smaller type. **Manually cast** likhna padta hai.

### Syntax

```java
(targetType) value
```

### Narrowing Direction

```text
double → float → long → int → short → byte
                                  ↓
                                char
```

### Examples

```java
double d = 10.75;
int x = (int) d;              // valid Explicit narrowing
System.out.println(x);         // Output: 10 (decimal part discarded!)

float f = (float) 10.5;       // valid double → float

int i = 300;
byte b = (byte) i;            // valid But value wraps around!
System.out.println(b);         // Output: 44 (300 % 256 = 44)
```

### Note: Data Loss Scenarios

| Conversion | What Gets Lost |
|-----------|---------------|
| `double → int` | Decimal part truncated |
| `int → byte` | Higher bits discarded (value wraps) |
| `long → int` | Higher 32 bits discarded |
| `double → float` | Precision reduced |

---

## 3. Numeric Promotion in Expressions

Jab arithmetic operation hota hai, Java **automatically operands ko promote** karta hai:

### Rule

```text
byte / short / char → int   (ALWAYS promoted in arithmetic)
```

Agar operands me wider type hai to result us wider type ka hoga:

```text
result type ≈ max(int, type of x, type of y)
```

### Examples

```java
byte a = 10;
byte b = 20;
// byte c = a + b;    // Error! Result is int, not byte
int c = a + b;        // Correct

char x = 'A';
char y = 'B';
int sum = x + y;      // valid 65 + 66 = 131 (result is int)
```

### Note: Common Gotcha — Compound Assignment vs Normal

```java
byte x = 10;
x += 20;           // valid Valid! (compound assignment has implicit cast)
// Equivalent to: x = (byte)(x + 20);

byte x = 10;
x = x + 20;        // Error! (x + 20 gives int, can't assign to byte)
x = (byte)(x + 20); // valid Explicit cast required
```

> ** Key Insight:** `x += y` is NOT always the same as `x = x + y`. Compound assignment includes an **implicit narrowing cast**!

---

## 4. Assignment Compatibility

```java
// Widening — OK
int x = 10;
double y = x;          // valid

// Narrowing — Error without cast
double x = 10.5;
int y = x;             // Error
int y = (int) x;       // valid

// Constant assignment to smaller type — OK if in range
byte b = 100;          // valid 100 is compile-time constant, fits in byte
byte b = 200;          // 200 exceeds byte range (-128 to 127)

// Variable assignment to smaller type — Always needs cast
int x = 65;
char c = x;            // Error (even though 65 fits in char)
char c = (char) x;     // valid Explicit cast needed
```

---

## Type Casting Cheat Sheet

```text
Smaller → Bigger (WIDENING)
═══════════════════════════
Automatic, No cast needed
byte → short → int → long → float → double
                ↑
              char

Bigger → Smaller (NARROWING)
═══════════════════════════
Manual, Cast required: (type) value
double → float → long → int → short → byte

ARITHMETIC PROMOTION
═══════════════════
byte + byte   →  int
short + short →  int
char + char   →  int
int + long    →  long
int + float   →  float
float + double → double
```

---

[Back to Index](./README.md) | [Next: Operators](./09-operators.md)
