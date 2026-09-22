# ⚡ Quick Revision — Complete Java Basics Cheat Sheet

---

## Java Basics Mind Map

```text
Java Basics
├──  History
│   ├── Green Project (1990, Sun Microsystems)
│   ├── Team: James Gosling, Mike Sheridan, Patrick Naughton
│   ├── Oak → Java (name change)
│   └── JDK 1.0 (23 Jan 1996)
│
├── ⚙ How It Works
│   ├── .java → javac → .class (bytecode) → JVM → execution
│   ├── WORA: Write Once, Run Anywhere
│   ├── JDK ⊃ JRE ⊃ JVM
│   └── Entry point: public static void main(String[] args)
│
├──  Data Types (8 Primitives)
│   ├── Integer
│   │   ├── byte   → 1 byte  → -128 to 127
│   │   ├── short  → 2 bytes → -32,768 to 32,767
│   │   ├── int    → 4 bytes → ~±2.1 billion
│   │   └── long   → 8 bytes → ~±9.2 quintillion
│   ├── Floating
│   │   ├── float  → 4 bytes → ~6-7 digits precision
│   │   └── double → 8 bytes → ~15-16 digits precision
│   ├── Character
│   │   └── char   → 2 bytes → 0 to 65,535 (UTF-16)
│   └── Boolean
│       └── boolean → true / false
│
├──  Number Literals
│   ├── Decimal  → 100
│   ├── Octal    → 0114 (prefix 0)
│   ├── Hex      → 0xAC (prefix 0x)
│   ├── Binary   → 0b1011 (prefix 0b)
│   └── Underscore → 1_000_000
│
├──  Type Casting
│   ├── Widening (Implicit):  byte → short → int → long → float → double
│   ├── Narrowing (Explicit): (type) value
│   └── Promotion: byte/short/char → int in arithmetic
│
├──  Operators
│   ├── Arithmetic     → + - * / %
│   ├── Relational     → < > <= >=
│   ├── Equality       → == !=
│   ├── Logical        → && || !
│   ├── Bitwise        → & | ^ ~
│   ├── Shift          → << >> >>>
│   ├── Assignment     → = += -= *= /= %=
│   ├── Ternary        → ? :
│   └── Unary          → + - ++ -- ! ~
│
├──  Characters
│   ├── 'A' = 65, 'a' = 97, '0' = 48
│   ├── char + char → int (numeric promotion)
│   └── Useful: Character.isDigit(), toUpperCase()
│
├──  Strings
│   ├── Reference type (class), immutable
│   ├── "+" → concatenation
│   ├── Use .equals() for comparison, NOT ==
│   └── String Pool concept
│
└──  printf()
    ├── %d (int), %f (float), %c (char), %s (String)
    ├── %.Nf (decimal precision)
    ├── %0Nd (zero padding)
    └── Escape: \n \t \\ \" \'
```

---

## Top Important Facts

| # | Fact |
|---|------|
| 1 | Decimal literals like `10.25` are **double** by default |
| 2 | Float literal ke liye `f`/`F` suffix chahiye: `10.25f` |
| 3 | `char` = 2 bytes, range 0–65535, UTF-16 |
| 4 | `char` literals → single quotes `'A'` |
| 5 | `String` literals → double quotes `"Hello"` |
| 6 | `char + char` = **int** (not char!) |
| 7 | `byte + byte` = **int** (numeric promotion) |
| 8 | `short + short` = **int** (numeric promotion) |
| 9 | Integer division by zero → `ArithmeticException` |
| 10 | Float division by zero → `Infinity`, `-Infinity`, or `NaN` |
| 11 | `<`, `>`, `<=`, `>=` boolean/String par work nahi karte |
| 12 | `==` primitives ke liye value compare, references ke liye reference compare |
| 13 | String content compare → `.equals()` use karo |
| 14 | `&&`, `\|\|` short-circuit karte hain |
| 15 | `++`/`--` boolean aur String par work nahi karte |
| 16 | `x += y` ≠ `x = x + y` (compound assignment has implicit cast) |
| 17 | Java identifiers case-sensitive hote hain |
| 18 | `x = x++` → x **nahi badlega** (tricky!) |
| 19 | `%b` boolean ke liye hai, binary ke liye nahi |
| 20 | `~n = -(n+1)` for bitwise complement |

---

## Operator Cheat Sheet

| Category | Operators |
|----------|-----------|
| Arithmetic | `+  -  *  /  %` |
| Unary | `+  -  ++  --  !  ~` |
| Relational | `<  >  <=  >=` |
| Equality | `==  !=` |
| Logical | `&&  \|\|  !` |
| Bitwise | `&  \|  ^  ~` |
| Shift | `<<  >>  >>>` |
| Assignment | `=` |
| Compound | `+=  -=  *=  /=  %=  &=  \|=  ^=  <<=  >>=  >>>=` |
| Ternary | `? :` |

---

## Type Casting Quick Reference

```text
WIDENING (Automatic):
byte → short → int → long → float → double
                ↑
              char

NARROWING (Manual):
double → float → long → int → short → byte
  Syntax: (targetType) value

ARITHMETIC PROMOTION:
byte + byte   →  int
short + short →  int
char + char   →  int
int + long    →  long
int + float   →  float
float + double → double
```

---

## Mental Models

```text
Smaller → Bigger = Widening (Implicit, Automatic)
Bigger → Smaller = Narrowing (Explicit, Manual cast)
```

```text
'A' = 65    (character → numeric code)
'a' = 97    ('a' - 'A' = 32, difference between cases)
'0' = 48    ('0' is a CHARACTER, not zero!)
```

```text
10.25 → double (by default)
10.25f → float (with suffix)
100L → long (with suffix)
```

```text
x++ → use THEN increment
++x → increment THEN use
```

```text
x << n = x × 2ⁿ    (left shift = multiply by power of 2)
x >> n = x / 2ⁿ    (right shift = divide by power of 2)
~n = -(n + 1)       (bitwise complement)
```

---

<p align="center">
  <b> Congratulations! You've covered all Java Basics!</b><br>
  <i>Keep revising, keep coding ☕</i>
</p>

---

[Back to Index](./README.md)
