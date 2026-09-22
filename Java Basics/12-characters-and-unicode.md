# Characters & Unicode

---

## `char` Data Type

| Property | Value |
|----------|-------|
| **Type** | Primitive |
| **Size** | 2 bytes (16 bits) |
| **Range** | 0 to 65,535 |
| **Encoding** | UTF-16 code unit |
| **Signed?** | No — `char` is **unsigned** |
| **Default** | `'\u0000'` (null character) |

```java
char ch = 'A';       // Character literal → single quotes
char ch = 65;        // Same as 'A' (Unicode value)
char ch = '\u0041';  // Same as 'A' (Unicode escape)
```

> Note: **Single quotes** = character literal, **Double quotes** = String literal
> ```java
> 'A'    → char
> "A"    → String
> ```

---

## Unicode — The Universal Standard

Unicode ek **universal character encoding standard** hai jo duniya ki almost sabhi languages/scripts ke characters ko represent karta hai.

Java ka `char` **UTF-16** representation ka 16-bit code unit hai.

### Common ASCII / Unicode Values

| Character | Decimal | Hex | Description |
|-----------|---------|-----|-------------|
| `'A'` – `'Z'` | 65 – 90 | 0x41 – 0x5A | Uppercase letters |
| `'a'` – `'z'` | 97 – 122 | 0x61 – 0x7A | Lowercase letters |
| `'0'` – `'9'` | 48 – 57 | 0x30 – 0x39 | Digits |
| `' '` (space) | 32 | 0x20 | Space character |
| `'*'` | 42 | 0x2A | Asterisk |
| `'\n'` | 10 | 0x0A | Newline |
| `'\r'` | 13 | 0x0D | Carriage return |
| `'\t'` | 9 | 0x09 | Tab |

### Quick Memory Tricks

```text
'A' = 65    →  "A for 65, easy to remember"
'a' = 97    →  'a' - 'A' = 32 (difference between upper and lower)
'0' = 48    →  '0' is NOT 0, it's 48!
```

### Example

```java
int x = '1';
System.out.println(x);    // Output: 49 (NOT 1!)
```

> `'1'` ek **character** hai jiska Unicode value **49** hai, integer **1** nahi!

---

## `char` ↔ Integer Conversion

### Integer → char (Explicit Cast Required)

```java
int x = 65;
char ch = (char) x;
System.out.println(ch);    // Output: A
```

### char → int (Implicit Widening)

```java
char ch = 'A';
int x = ch;                // No cast needed
System.out.println(x);     // Output: 65
```

### Note: Constant vs Variable Assignment

```java
// Constant — OK if value fits in char range
char ch = 65;              // valid 65 is compile-time constant, fits in 0-65535

// Variable — Always needs explicit cast
int x = 65;
char ch = x;               // Compile error!
char ch = (char) x;        // valid Explicit cast needed
```

**Why?** Compiler knows constant `65` fits in `char`, but variable `x` could hold any `int` value (even one outside `char` range).

### Another Example

```java
char x = 50;
System.out.println(x);     // Output: 2
// Because Unicode value 50 = character '2'
```

---

## Character Arithmetic

Jab `char` arithmetic me use hota hai, Java **numeric promotion** apply karta hai — `char` automatically `int` me promote hota hai.

### Basic Example

```java
char a = 'A';    // 65
char b = 'B';    // 66

System.out.println(a + b);   // Output: 131 (int result, not char!)
```

### Note: Result Type is `int`, NOT `char`

```java
// char c = 'A' + 'B';        // Error! Result is int
int c = 'A' + 'B';            // Correct
char c = (char)('A' + 'B');   // valid With explicit cast
```

### Interesting Example: `'1' + '0'`

```java
char c = (char)('1' + '0');
System.out.println(c);        // Output: a
```

**Why?**
```text
'1' = 49
'0' = 48
49 + 48 = 97
97 = 'a'
```

---

## Useful Character Operations

### Check if Character is Uppercase

```java
char ch = 'A';
boolean isUpper = (ch >= 'A' && ch <= 'Z');   // true
```

### Convert Uppercase → Lowercase

```java
char upper = 'A';
char lower = (char)(upper + 32);    // 'a'
// Or better:
char lower = (char)(upper + ('a' - 'A'));
```

### Convert Lowercase → Uppercase

```java
char lower = 'a';
char upper = (char)(lower - 32);    // 'A'
```

### Get Numeric Value of Digit Character

```java
char digit = '7';
int value = digit - '0';           // 7
```

> ** Pro Tip:** `Character` wrapper class provides utility methods:
> ```java
> Character.isUpperCase('A');     // true
> Character.isDigit('5');         // true
> Character.toLowerCase('A');     // 'a'
> Character.toUpperCase('a');     // 'A'
> ```

---

## `char` Comparison

`char` ko numeric value ke context me compare kiya ja sakta hai:

```java
System.out.println('A' == 65);       // true
System.out.println('A' == 65.0);     // true  (widening to double)
System.out.println('A' < 'B');       // true  (65 < 66)
System.out.println('a' > 'A');       // true  (97 > 65)
```

---

[Back to Index](./README.md) | [Next: Strings](./13-strings.md)
