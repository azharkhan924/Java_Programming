# 🖨️ printf() Formatting & Escape Sequences

---

## 📌 `printf()` — Formatted Output

```java
System.out.printf(formatString, arguments...);
```

`printf()` formatted output print karne ke liye use hota hai — C language ki `printf` se inspired hai.

---

## 📊 Format Specifiers

| Specifier | Use For | Example |
|-----------|---------|---------|
| `%d` | Integer (decimal) | `printf("%d", 42)` → `42` |
| `%o` | Integer (octal) | `printf("%o", 42)` → `52` |
| `%x` / `%X` | Integer (hexadecimal) | `printf("%x", 255)` → `ff` |
| `%f` | Floating-point | `printf("%f", 3.14)` → `3.140000` |
| `%e` / `%E` | Scientific notation | `printf("%e", 3.14)` → `3.140000e+00` |
| `%c` | Character | `printf("%c", 'A')` → `A` |
| `%s` | String | `printf("%s", "Java")` → `Java` |
| `%b` / `%B` | Boolean | `printf("%b", true)` → `true` |
| `%h` / `%H` | Hash code (hex) | `printf("%h", obj)` → hash in hex |
| `%n` | Platform-specific newline | OS ke hisab se newline |
| `%%` | Literal % sign | `printf("100%%")` → `100%` |

### ⚠️ Common Mistakes

```java
// ❌ %b binary ke liye NAHI hai — ye boolean ke liye hai!
System.out.printf("%b", 42);   // Output: true (any non-null = true)

// ✅ Binary print karne ke liye:
System.out.println(Integer.toBinaryString(42));   // 101010

// ❌ %h hex integer ke liye NAHI hai — ye hashCode ke liye hai!
// ✅ Hex integer ke liye %x use karo:
System.out.printf("%x", 255);  // ff
```

---

## 🔢 Floating-Point Formatting

### Default `%f` — 6 digits after decimal

```java
float x = 10 / 3.0f;
System.out.printf("%f", x);     // 3.333333
```

### Precision Control — `%.Nf`

```java
System.out.printf("%.3f", 10.25678);    // 10.257  (3 decimal places)
System.out.printf("%.1f", 3.14159);     // 3.1     (1 decimal place)
System.out.printf("%.0f", 3.7);         // 4       (rounded, no decimal)
System.out.printf("%.10f", 1.0/3);      // 0.3333333333
```

---

## 📏 Width and Padding

### `%Nd` — Minimum Width

```java
System.out.printf("%4d", 10);       //   10   (right-aligned, spaces fill)
System.out.printf("%4d", 12345);    // 12345  (wider than 4, no truncation)
```

```text
%4d with value 10:
┌───┬───┬───┬───┐
│   │   │ 1 │ 0 │    ← right-aligned with spaces
└───┴───┴───┴───┘
```

### `%0Nd` — Zero Padding

```java
System.out.printf("%04d", 10);      // 0010
System.out.printf("%04d", 25);      // 0025
System.out.printf("%05d", 25);      // 00025
System.out.printf("%08d", 12345);   // 00012345
```

```text
%04d with value 25:
┌───┬───┬───┬───┐
│ 0 │ 0 │ 2 │ 5 │    ← zero-padded
└───┴───┴───┴───┘
```

### `%-Nd` — Left-Aligned

```java
System.out.printf("%-10d|", 42);    // 42        |  (left-aligned)
System.out.printf("%10d|", 42);     //         42|  (right-aligned, default)
```

---

## 🔤 Character Printing

### Print as Character

```java
char ch = 'A';
System.out.printf("%c", ch);       // A
```

### Print Character's Numeric Code

```java
System.out.printf("%d", (int)'A');  // 65
```

---

## 📋 Combined Example

```java
String name = "Azhar";
int age = 21;
double cgpa = 8.75;

System.out.printf("Name: %-10s | Age: %3d | CGPA: %.2f%n", name, age, cgpa);
// Output: Name: Azhar      | Age:  21 | CGPA: 8.75
```

---

## 🔙 Escape Sequences

| Escape | Name | Description |
|--------|------|-------------|
| `\n` | Newline | Cursor next line pe jaata hai |
| `\t` | Tab | Horizontal tab space |
| `\r` | Carriage Return | Cursor current line ki **beginning** par |
| `\b` | Backspace | Ek character peeche |
| `\"` | Double Quote | String me `"` print karna |
| `\'` | Single Quote | Char me `'` print karna |
| `\\` | Backslash | Literal backslash |
| `\0` | Null Character | Null character (value 0) |

### Examples

```java
// Double quote inside string
System.out.println("\"Hello\"");       // Output: "Hello"

// Backslash
System.out.println("C:\\Users\\Azhar"); // Output: C:\Users\Azhar

// Tab
System.out.println("Name:\tAzhar");    // Output: Name:	Azhar

// Newline
System.out.println("Line1\nLine2");
// Output:
// Line1
// Line2
```

### Escape Sequence Values

```text
'\n' = 10     (newline)
'\r' = 13     (carriage return)
'\t' = 9      (tab)
'\0' = 0      (null)
'\"' = 34     (double quote)
'\'' = 39     (single quote)
'\\' = 92     (backslash)
'*'  = 42     (asterisk — not an escape, but commonly asked)
```

---

## 📊 Format Specifier Quick Reference

```text
┌────────────┬─────────────────┬──────────────────┐
│ Specifier  │ Type            │ Example Output   │
├────────────┼─────────────────┼──────────────────┤
│ %d         │ int             │ 42               │
│ %04d       │ zero-padded int │ 0042             │
│ %10d       │ width 10 int    │         42       │
│ %-10d      │ left-aligned    │ 42               │
│ %f         │ float/double    │ 3.140000         │
│ %.2f       │ 2 decimal       │ 3.14             │
│ %e         │ scientific      │ 3.14e+00         │
│ %c         │ char            │ A                │
│ %s         │ String          │ Java             │
│ %o         │ octal           │ 52               │
│ %x         │ hex             │ 2a               │
│ %b         │ boolean         │ true             │
│ %n         │ newline         │ (platform-based) │
│ %%         │ literal %       │ %                │
└────────────┴─────────────────┴──────────────────┘
```

---

[⬅️ Back to Index](./README.md) | [Next: Swapping Techniques ➡️](./15-swapping-techniques.md)
