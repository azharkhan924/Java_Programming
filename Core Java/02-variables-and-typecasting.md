# Variables & Type Casting

---

## 1. Variable Declaration & Initialization

### C++ vs Java

C++ me local variable bina initialize kiye use karna **undefined behavior** hai (garbage value milti hai).

Java me:

```java
int x;
System.out.println(x); // Compile-time error
```

```text
variable x might not have been initialized
```

> **Rule:** Java me local variable ko **use karne se pehle initialize karna compulsory hai.**

```java
int x = 10;
System.out.println(x);   // valid
```

### Same Scope Me Re-declaration

```java
int x = 10;
int x = 20;  // variable x is already defined
```

---

## 2. Local Variable vs Instance Variable vs Static Variable

### Local Variable

- Method/block ke andar declared
- **Default value nahi milti** — initialize karna mandatory
- Scope sirf usi method/block tak

```java
void show() {
    int x;
    System.out.println(x);  // ERROR
}
```

### Instance Variable

- Class ke andar but method ke bahar declared
- **Default value automatically milti hai**
- Object-specific — har object ki alag copy

```java
class Student {
    int age;       // default: 0
    String name;   // default: null
}
```

### Static Variable

- `static` keyword ke saath declared
- **Class-level** — sabhi objects share karte hain
- Class name se access karna preferred

```java
class Student {
    static String college = "AITR";
}
// Access: Student.college
```

> Note: Static variable ko **local variable** ke roop me declare nahi kar sakte:
> ```java
> void test() {
>     static int x;  //  invalid in Java
> }
> ```

### Default Values Table

| Type | Default Value |
|------|--------------|
| `byte` | `0` |
| `short` | `0` |
| `int` | `0` |
| `long` | `0L` |
| `float` | `0.0f` |
| `double` | `0.0d` |
| `char` | `'\u0000'` (null character, NOT space) |
| `boolean` | `false` |
| Reference types | `null` |

---

## 3. Integer Data Types & Range

| Data Type | Size | Range |
|-----------|------|-------|
| `byte` | 1 byte | -128 to 127 |
| `short` | 2 bytes | -32,768 to 32,767 |
| `int` | 4 bytes | -2³¹ to 2³¹-1 |
| `long` | 8 bytes | -2⁶³ to 2⁶³-1 |

```java
System.out.println(Long.MAX_VALUE);   // 9223372036854775807
System.out.println(Byte.MIN_VALUE);   // -128
```

> **Why multiple integer types?** Memory ko efficiently use karne ke liye. Small range ke data ke liye `long` use karna wasteful hai.

---

## 4. Integer Literal Rules

**Integer literal by default `int` hota hai.**

```java
long x = 2147483648;    // ERROR — literal exceeds int range
long x = 2147483648L;   // valid L suffix se long literal banta hai
```

> **Tip:** Capital `L` prefer karo — lowercase `l` visually `1` jaisa lagta hai.

**Floating-point literal by default `double` hoti hai.**

```java
float x = 10.5;      // ERROR — 10.5 is double literal
float x = 10.5f;     // valid f suffix se float literal banta hai
```

```java
float x = 10;         // valid int → float widening allowed
System.out.println(x); // 10.0
```

---

## 5. Widening (Implicit) Type Casting

Jab source type **safely** destination type me fit hota hai → **automatic conversion**.

```text
Widening Order:
byte → short → int → long → float → double
```

```java
int x = 10;
long y = x;      // valid int → long (implicit)

byte b = 10;
int i = b;       // valid byte → int (implicit)
```

> Note: **`long → float` technically widening hai**, lekin floating-point representation ki wajah se **precision loss** possible hai.

---

## 6. Narrowing (Explicit) Type Casting

Reverse direction me automatic conversion nahi hota — **explicit cast** chahiye:

```java
double x = 10.5;
int y = (int) x;     // y = 10 — decimal part lost
```

```text
Narrowing Direction (cast required):
double → float → long → int → short → byte
```

> Note: **Data loss ka possibility** hota hai narrowing me.

---

## 7. Binary Numeric Promotion

Arithmetic operations me operands automatically promote hote hain:

```text
Promotion Priority: double > float > long > int
byte, short, char → int me promote (arithmetic ke time)
```

```java
float x = 10;
int y = 3;
System.out.println(x / y);   // 3.3333333 (float result)

double x = 10;
int y = 3;
System.out.println(x / y);   // 3.3333333333333335 (double result)
```

---

## 8. Wrapper Classes & Boxing/Unboxing

| Primitive | Wrapper Class |
|-----------|--------------|
| `byte` | `Byte` |
| `short` | `Short` |
| `int` | `Integer` |
| `long` | `Long` |
| `float` | `Float` |
| `double` | `Double` |
| `char` | `Character` |
| `boolean` | `Boolean` |

### Autoboxing (Primitive → Object)

```java
int x = 10;
Integer obj = x;     // autoboxing
```

### Unboxing (Object → Primitive)

```java
Integer obj = 10;
int x = obj;         // unboxing
```

---

## 9. String → int Conversion

```java
String s1 = "10";
int x = s1;                    // ERROR — incompatible types

int x = Integer.parseInt(s1);  // valid correct way
```

### Invalid String → NumberFormatException

```java
int x = Integer.parseInt("10abc");  // NumberFormatException
```

---

## 10. Two's Complement — Negative Numbers in Binary

### Process

1. Positive number ka binary likho (fixed bit size, e.g., 8-bit)
2. **1's complement** karo (bits flip)
3. **+1** karo → **2's complement**
4. MSB = sign bit (`0` = positive, `1` = negative)

### Example: -5 & -6

```text
-5:  00000101 → 11111010 → 11111011
-6:  00000110 → 11111001 → 11111010

  11111011  (-5)
& 11111010  (-6)
----------
  11111010  = -6

Result: -5 & -6 = -6
```

---

## Interview Quick Questions

| Question | Answer |
|----------|--------|
| Local variable ko default value kyu nahi milti? | Java **definite assignment** require karta hai |
| Integer literal ka default type? | `int` |
| `long x = 2147483648;` error kyu? | Literal `int` treat hota hai — `L` suffix lagao |
| Decimal literal ka default type? | `double` |
| `float x = 10.5;` error kyu? | `10.5` is `double` literal — `f` suffix lagao |
| `String` ko `int` me kaise convert karein? | `Integer.parseInt("100")` |
| Invalid numeric string par kya exception? | `NumberFormatException` |

---

## ⚡ Quick Revision Map

```text
Java Variables
│
├── Local Variable
│   └── Must initialize before use
│
├── Instance Variable
│   ├── Object-specific
│   ├── Default value available
│   └── Access through object
│
└── Static Variable
    ├── Class-level
    ├── Shared
    └── Access using class name
```

```text
Type Conversion
│
├── Implicit / Widening
│   └── Automatic: byte → short → int → long → float → double
│
└── Explicit / Narrowing
    ├── Cast required: (type) value
    └── Data loss possible
```

---

[Previous: Arrays](./01-arrays.md) · [Back to Core Java Index](./README.md) · [Next: Constructors & Instance Blocks](./03-constructors-and-instance-blocks.md)
