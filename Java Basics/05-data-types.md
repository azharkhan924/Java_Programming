# Data Types in Java

---

## Two Categories of Data Types

```text
Java Data Types
├── Primitive Types (8 types)
│ ├── Integer → byte, short, int, long
│ ├── Floating → float, double
│ ├── Character → char
│ └── Boolean → boolean
│
└── Reference Types
 ├── String
 ├── Arrays
 ├── Classes
 └── Interfaces
```

---

## Integer Data Types

| Type | Size | Range | Default Value |
|------|------|-------|---------------|
| `byte` | 1 byte (8 bits) | **-128** to **127** | `0` |
| `short` | 2 bytes (16 bits) | **-32,768** to **32,767** | `0` |
| `int` | 4 bytes (32 bits) | **-2³¹** to **2³¹ - 1** (~±2.1 billion) | `0` |
| `long` | 8 bytes (64 bits) | **-2⁶³** to **2⁶³ - 1** | `0L` |

### General Signed n-bit Range Formula

Two's complement representation me:

```text
Range = -2^(n-1) to 2^(n-1) - 1

Where: 1 byte = 8 bits
```

### Quick Memory Trick

```text
byte → 1 byte → -128 to 127 → "byte = 1 byte" 
short → 2 bytes → ~±32K → "short story = 2 pages"
int → 4 bytes → ~±2 billion → "int = default integer choice"
long → 8 bytes → ~±9.2 quintillion → "long = long number needs 8 bytes"
```

> ** Tip:** Integer literal by default `int` hota hai. `long` ke liye suffix `L` lagao: `long x = 100L;`

---

## Floating-Point Data Types

| Type | Size | Precision | Range (approx.) | Default Value |
|------|------|-----------|-----------------|---------------|
| `float` | 4 bytes | ~6–7 significant digits | `~1.4E-45` to `~3.4E38` | `0.0f` |
| `double` | 8 bytes | ~15–16 significant digits | `~4.9E-324` to `~1.8E308` | `0.0d` |

### Note: Default Type Rule

> Decimal floating-point literal by default **`double`** hota hai!

```java
double x = 10.25; // OK — 10.25 is double by default

float x = 10.25; // Error — trying to assign double to float
float x = 10.25f; // OK — suffix 'f' makes it float
float x = (float) 10.25; // OK — explicit cast
```

### Float vs Double — When to Use?

| Use Case | Recommended |
|----------|-------------|
| General calculations | `double` (default, more precision) |
| Memory-constrained (e.g., large arrays) | `float` |
| Financial calculations | **Neither** — use `BigDecimal` |

---

## Character Data Type — `char`

| Property | Value |
|----------|-------|
| **Size** | 2 bytes (16 bits) |
| **Range** | 0 to 65,535 |
| **Encoding** | UTF-16 code unit |
| **Default Value** | `'\u0000'` (null character) |
| **Signed?** | **No** — `char` is unsigned |

```java
char ch = 'A'; // Single character in single quotes
char ch = 65; // Same as 'A' (Unicode value)
char ch = '\u0041'; // Same as 'A' (Unicode escape)
```

---

## Boolean Data Type

| Property | Value |
|----------|-------|
| **Size** | JVM-dependent (not precisely defined by spec) |
| **Values** | `true` or `false` only |
| **Default Value** | `false` |

```java
boolean isJavaFun = true;
boolean isPythonHard = false;
```

> Note: Java me `boolean` me `0`/`1` assign nahi kar sakte (unlike C/C++)

---

## Complete Data Types Summary

```text
┌──────────┬──────────┬─────────────────────────┬───────────┐
│ Type │ Size │ Range │ Default │
├──────────┼──────────┼─────────────────────────┼───────────┤
│ byte │ 1 byte │ -128 to 127 │ 0 │
│ short │ 2 bytes │ -32,768 to 32,767 │ 0 │
│ int │ 4 bytes │ -2³¹ to 2³¹-1 │ 0 │
│ long │ 8 bytes │ -2⁶³ to 2⁶³-1 │ 0L │
│ float │ 4 bytes │ ~±3.4E38 │ 0.0f │
│ double │ 8 bytes │ ~±1.8E308 │ 0.0d │
│ char │ 2 bytes │ 0 to 65,535 │ '\u0000' │
│ boolean │ varies │ true / false │ false │
└──────────┴──────────┴─────────────────────────┴───────────┘
```

---

[ Back to Index](./README.md) | [Next: Variables & Naming](./06-variables-and-naming.md)
