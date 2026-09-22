# Operators in Java

---

## Operator Categories Overview

| Category | Operators | Result Type |
|----------|-----------|-------------|
| **Arithmetic** | `+  -  *  /  %` | Numeric |
| **Relational** | `<  >  <=  >=` | boolean |
| **Equality** | `==  !=` | boolean |
| **Logical** | `&&  \|\|  !` | boolean |
| **Assignment** | `=  +=  -=  *=  /=  %=` | Value |
| **Ternary** | `? :` | Value |
| **Unary** | `+  -  ++  --  !  ~` | Varies |

---

## Arithmetic Operators

```text
+   Addition
-   Subtraction
*   Multiplication
/   Division
%   Modulus (remainder)
```

### Examples

```java
int x = 10, y = 3;

System.out.println(x + y);   // 13
System.out.println(x - y);   // 7
System.out.println(x * y);   // 30
System.out.println(x / y);   // 3  (integer division — truncates!)
System.out.println(x % y);   // 1  (remainder)
```

### Note: Important — Integer Division

```java
10 / 3    // → 3   (NOT 3.33!)
10 / 3.0  // → 3.333...  (one operand is double, result is double)
```

> ** Tip:** Agar decimal result chahiye to at least ek operand ko `double`/`float` banao.

### Modulus `%` and Sign Rule

Java me `%` ka result **left operand (dividend) ka sign follow** karta hai:

```java
-10 % 3    // -1    (left operand is negative)
-10 % -3   // -1    (left operand is negative)
 10 % -3   //  1    (left operand is positive)
 10 % 3    //  1    (left operand is positive)
```

### Note: Division by Zero

| Type | Expression | Result |
|------|-----------|--------|
| Integer | `10 / 0` | `ArithmeticException`  (runtime error) |
| Float | `10.0 / 0.0` | `Infinity` |
| Float | `-10.0 / 0.0` | `-Infinity` |
| Float | `0.0 / 0.0` | `NaN` (Not a Number) |

```java
System.out.println(10.0 / 0.0);    // Infinity
System.out.println(-10.0 / 0.0);   // -Infinity
System.out.println(0.0 / 0.0);     // NaN
```

> Note: Arithmetic operators `boolean` par work **nahi** karte!

---

## Relational Operators

Comparison ke liye — result hamesha **boolean** hota hai.

```text
<    Less than
>    Greater than
<=   Less than or equal to
>=   Greater than or equal to
```

```java
int a = 10, b = 20;

System.out.println(a < b);     // true
System.out.println(a > b);     // false
System.out.println(a <= 10);   // true
System.out.println(a >= 20);   // false
```

### Cannot Use On

- `boolean` — `true < false` invalid hai
- `String` — `"abc" > "xyz"` invalid hai (use `compareTo()` instead)

### Note: Cannot Chain Comparisons

```java
10 < 20 < 30    // Error!
```

Kyunki `10 < 20` → `true`, phir `true < 30` — Java boolean ko integer se compare nahi kar sakta.

**Correct way:**
```java
10 < 20 && 20 < 30    // valid Use logical AND
```

---

## ⚖ Equality Operators

```text
==   Equal to
!=   Not equal to
```

### Primitives ke liye — Value Compare

```java
System.out.println(10 == 10);     // true
System.out.println(10 != 20);     // true
System.out.println('A' == 65);    // true  (char compared as int)
System.out.println('A' == 65.0);  // true  (widening to double)
```

### Note: Reference Types ke liye — Reference Compare (NOT content!)

```java
String a = new String("Java");
String b = new String("Java");

System.out.println(a == b);          // false  (different objects)
System.out.println(a.equals(b));     // true   (same content)
```

> ** Rule:** String content compare karne ke liye hamesha `.equals()` use karo, `==` nahi!

---

## Logical Operators

Multiple boolean conditions combine karne ke liye:

```text
&&   Logical AND   → dono true hone chahiye
||   Logical OR    → at least ek true hona chahiye
!    Logical NOT   → boolean reverse karta hai
```

### Truth Tables

| A | B | `A && B` | `A \|\| B` | `!A` |
|---|---|----------|------------|------|
| true | true | **true** | true | false |
| true | false | false | **true** | false |
| false | true | false | **true** | true |
| false | false | false | false | true |

### Example

```java
int age = 20;
System.out.println(age >= 18 && age <= 60);   // true
System.out.println(age < 18 || age > 60);     // false
System.out.println(!(age >= 18));              // false
```

### ⚡ Short-Circuit Evaluation

`&&` aur `||` **short-circuit** operators hain — agar result pehle condition se hi decide ho jaye to second condition evaluate nahi hoti.

```java
// AND: Agar first condition false hai → second check nahi hogi
false && someExpensiveMethod()   // someExpensiveMethod() NEVER called

// OR: Agar first condition true hai → second check nahi hogi
true || someExpensiveMethod()    // someExpensiveMethod() NEVER called
```

> ** Use Case:** Null check karte waqt short-circuiting useful hai:
> ```java
> if (str != null && str.length() > 0) { ... }
> ```
> Agar `str` null hai to `str.length()` call nahi hoga → NullPointerException avoid!

---

## Assignment Operators

### Simple Assignment

```java
int x = 10;
```

### Chain Assignment

```java
int a, b, c, d;
a = b = c = d = 10;   // Right-to-left evaluation
```

> Note: `int a = b = c = d = 10;` →  Error if `b`, `c`, `d` not already declared!

### Compound Assignment Operators

| Operator | Equivalent | Example |
|----------|-----------|---------|
| `+=` | `x = (type)(x + y)` | `x += 5;` |
| `-=` | `x = (type)(x - y)` | `x -= 5;` |
| `*=` | `x = (type)(x * y)` | `x *= 5;` |
| `/=` | `x = (type)(x / y)` | `x /= 5;` |
| `%=` | `x = (type)(x % y)` | `x %= 5;` |
| `&=` | `x = (type)(x & y)` | `x &= 5;` |
| `\|=` | `x = (type)(x \| y)` | `x \|= 5;` |
| `^=` | `x = (type)(x ^ y)` | `x ^= 5;` |
| `<<=` | `x = (type)(x << y)` | `x <<= 2;` |
| `>>=` | `x = (type)(x >> y)` | `x >>= 2;` |
| `>>>=` | `x = (type)(x >>> y)` | `x >>>= 2;` |

---

## Ternary Operator

```java
condition ? valueIfTrue : valueIfFalse
```

```java
int a = 10, b = 20;
int max = (a > b) ? a : b;    // max = 20

String result = (a > b) ? "A is bigger" : "B is bigger";
```

> ** Tip:** Ternary operator nested bhi kar sakte ho, lekin readability ke liye `if-else` better hai nested cases me.

---

## Operator Precedence (High → Low)

| Priority | Operators |
|----------|-----------|
| 1 (highest) | `()` `[]` `.` |
| 2 | `++` `--` `!` `~` (unary) |
| 3 | `*` `/` `%` |
| 4 | `+` `-` |
| 5 | `<<` `>>` `>>>` |
| 6 | `<` `>` `<=` `>=` `instanceof` |
| 7 | `==` `!=` |
| 8 | `&` |
| 9 | `^` |
| 10 | `\|` |
| 11 | `&&` |
| 12 | `\|\|` |
| 13 | `? :` |
| 14 (lowest) | `=` `+=` `-=` etc. |

> ** Tip:** Jab doubt ho — **parentheses `()` use karo!** Code readable bhi hoga aur galti bhi nahi hogi.

---

[Back to Index](./README.md) | [Next: Increment & Decrement](./10-increment-decrement.md)
