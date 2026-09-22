# Number Literals in Java

---

## What are Literals?

Literals wo **fixed values** hain jo directly code me likhi jaati hain.

```java
int x = 100; // 100 is a literal
double pi = 3.14; // 3.14 is a literal
char ch = 'A'; // 'A' is a literal
```

---

## Integer Literals in Different Bases

Java me integer literals ko **4 different number bases** me likh sakte hain:

| Base | Prefix | Digits Allowed | Example | Decimal Value |
|------|--------|----------------|---------|---------------|
| **Decimal** (Base 10) | None | `0-9` | `100` | 100 |
| **Octal** (Base 8) | `0` | `0-7` | `0114` | 76 |
| **Hexadecimal** (Base 16) | `0x` or `0X` | `0-9`, `A-F` | `0xAC` | 172 |
| **Binary** (Base 2) | `0b` or `0B` | `0-1` | `0b1011` | 11 |

---

### 8⃣ Octal Literals

Prefix: `0` (zero)

```java
int x = 0114; // Octal → Decimal: 76
int y = 077; // Octal → Decimal: 63
int z = 010; // Octal → Decimal: 8 (NOT 10!)
```

**Conversion: Octal → Decimal**
```text
0114 = 1×8² + 1×8¹ + 4×8⁰
 = 64 + 8 + 4
 = 76
```

> Note: **Common Mistake:** `058` → **Error!** Octal me sirf digits `0-7` allowed hain. `8` aur `9` invalid hain!

---

### Hexadecimal Literals

Prefix: `0x` or `0X`

```java
int x = 0xAC; // Hex → Decimal: 172
int y = 0xFF; // Hex → Decimal: 255
int z = 0x1A3; // Hex → Decimal: 419
```

**Conversion: Hex → Decimal**
```text
0xAC = A×16¹ + C×16⁰
 = 10×16 + 12×1
 = 160 + 12
 = 172
```

**Hex Digit Values:**
```text
0-9 → 0-9
A/a → 10
B/b → 11
C/c → 12
D/d → 13
E/e → 14
F/f → 15
```

---

### Binary Literals (Java 7+)

Prefix: `0b` or `0B`

```java
int x = 0b1011; // Binary → Decimal: 11
int y = 0b11111111; // Binary → Decimal: 255
int z = 0b1010; // Binary → Decimal: 10
```

**Conversion: Binary → Decimal**
```text
0b1011 = 1×2³ + 0×2² + 1×2¹ + 1×2⁰
 = 8 + 0 + 2 + 1
 = 11
```

---

## Underscore in Numeric Literals (Java 7+)

Readability improve karne ke liye numbers me `_` (underscore) use kar sakte hain:

```java
int million = 1_000_000; // 1000000
long creditCard = 1234_5678_9012_3456L;
int binary = 0b1010_1011_1100; // Readable binary
int hex = 0xFF_EC_DE; // Readable hex
```

### Invalid Underscore Placement

```java
int x = _100; // Cannot start with _ (this is a variable name!)
int x = 100_; // Cannot end with _
int x = 0_x1A; // Cannot be adjacent to prefix
float f = 10._5f; // Cannot be adjacent to decimal point
long l = 100_L; // Cannot be adjacent to type suffix
```

### Valid Underscore Placement

```java
int x = 1_000_000; // 
int x = 10_20; // → value is 1020
double d = 10.25_50; // → value is 10.2550
```

---

## Long and Float Suffixes

| Type | Suffix | Example |
|------|--------|---------|
| `long` | `L` or `l` | `long x = 100L;` |
| `float` | `F` or `f` | `float x = 10.5f;` |
| `double` | `D` or `d` (optional) | `double x = 10.5d;` |

> ** Best Practice:** `L` use karo (uppercase), kyunki lowercase `l` digit `1` se confuse ho sakta hai.

---

[ Back to Index](./README.md) | [Next: Type Casting](./08-type-casting.md)
