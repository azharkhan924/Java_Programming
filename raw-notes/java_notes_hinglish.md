# Java Notes --- Data Types, Type Casting & Operators

> **Language style:** Hinglish + English, exactly the way we normally
> explain things in WhatsApp/chat language.

------------------------------------------------------------------------

## 1. Type Casting

**Type Casting** ka matlab hai ek data type ki value ko doosre data type
me convert karna.

Java me broadly 2 types ki type conversion hoti hai:

1.  **Widening Type Casting (Implicit)**
2.  **Narrowing Type Casting (Explicit)**

### 1.1 Widening / Implicit Type Casting

Smaller data type ki value ko larger/broader data type me convert karna
**widening** kehlata hai.

``` text
byte → short → int → long → float → double
                  ↑
                char
```

Typical primitive widening conversions:

``` text
byte → short → int → long → float → double
byte → char   (directly nahi)
short → char  (directly nahi)
char → int → long → float → double
```

### Main points

-   JVM/compiler automatically conversion kar deta hai.
-   Normally information loss nahi hota.
-   Source aur destination types compatible hone chahiye.
-   Destination type ko source type se broader hona chahiye.

Example:

``` java
int x = 10;
double y = x;     // implicit widening
System.out.println(y);   // 10.0
```

### Important Note

Widening ka matlab sirf **bytes ki simple ranking** nahi hai. `float` 4
bytes ka hota hai, phir bhi `long` se `float` widening allowed hai
because Java language rules ke according `long → float` valid widening
conversion hai. Is conversion me very large integer values ke exact
digits lose ho sakte hain, so "no information loss" ko **exact numerical
representation** ke context me blindly apply nahi karna chahiye.

------------------------------------------------------------------------

## 2. Narrowing / Explicit Type Casting

Bigger/broader data type ki value ko smaller/narrower data type me
convert karna **narrowing** kehlata hai.

Example:

``` java
double d = 10.75;
int x = (int) d;

System.out.println(x);   // 10
```

Yahan decimal part discard ho gaya.

### Main points

-   Explicit cast likhna padta hai.
-   Syntax:

``` java
(targetType) value
```

Example:

``` java
double d = 10.5;
float f = (float) d;
```

### Common narrowing direction

``` text
double
  ↓
float
  ↓
long
  ↓
int
  ↓
short
  ↓
byte
```

Aur:

``` text
char → byte/short/int/... 
```

specific conversions ke liye explicit cast ki zarurat ho sakti hai.

------------------------------------------------------------------------

## 3. Primitive Data Type Sizes

  Data Type              Size Important Range / Precision
  ----------- --------------- -------------------------------------
  `byte`               1 byte -128 to 127
  `short`             2 bytes -32,768 to 32,767
  `int`               4 bytes -2³¹ to 2³¹-1
  `long`              8 bytes -2⁶³ to 2⁶³-1
  `float`             4 bytes \~6--7 significant decimal digits
  `double`            8 bytes \~15--16 significant decimal digits
  `char`              2 bytes 0 to 65,535
  `boolean`     JVM-dependent `true` / `false`

### Float vs Double

Java me decimal literal by default **double** hota hai.

``` java
double x = 10.25;
```

Agar float banana hai:

``` java
float x = 10.25f;
```

ya:

``` java
float x = (float) 10.25;
```

### Float range --- correction

`float` ka approximate maximum positive value:

``` text
3.4028235 × 10^38
```

Smallest positive non-zero value approximately:

``` text
1.4 × 10^-45
```

### Double range --- correction

`double` ka approximate maximum positive value:

``` text
1.7976931348623157 × 10^308
```

Smallest positive non-zero value approximately:

``` text
4.9 × 10^-324
```

------------------------------------------------------------------------

# 4. Character Data Type --- `char`

`char` ek **single UTF-16 code unit** store karta hai.

``` java
char ch = 'A';
```

Character literal ko **single quotes** me likhte hain.

``` java
'A'
'1'
'#'
'\n'
```

String ko double quotes me likhte hain:

``` java
"Hello"
```

### Size

``` text
char = 2 bytes
```

### Range

``` text
0 to 65535
```

Java ka `char` unsigned hota hai.

------------------------------------------------------------------------

## 5. Unicode

Unicode ek universal character encoding standard hai jisme different
languages aur symbols ke characters ko code points diye jaate hain.

Java ka `char` UTF-16 representation ka 16-bit code unit hai.

Common ASCII/Unicode values:

``` text
'A' → 65
'Z' → 90

'a' → 97
'z' → 122

'0' → 48
'9' → 57

'\n' → 10
'\r' → 13
'*'  → 42
```

Example:

``` java
int x = '1';
System.out.println(x);
```

Output:

``` text
49
```

Because `'1'` ka numeric character code 49 hai.

------------------------------------------------------------------------

## 6. `char` and Integer Conversion

Example:

``` java
char x = 50;
System.out.println(x);
```

Output:

``` text
2
```

Because Unicode value `50` character `'2'` ko represent karti hai.

Example:

``` java
int x = 65;
char ch = (char) x;

System.out.println(ch);
```

Output:

``` text
A
```

Yahan explicit casting use hui hai.

### Important: Constant vs Variable

Ye valid hai:

``` java
char ch = 65;
```

because `65` ek compile-time constant hai aur `char` range ke andar hai.

Lekin:

``` java
int x = 65;
char ch = x;       // compile-time error
```

Yahan explicit cast chahiye:

``` java
char ch = (char) x;
```

------------------------------------------------------------------------

# 7. Character Arithmetic

Characters ke saath arithmetic karne par Java numeric promotion apply
karta hai.

``` java
char a = 'A';
char b = 'B';

System.out.println(a + b);
```

Output:

``` text
131
```

because:

``` text
65 + 66 = 131
```

### Important

`char + char` ka result `int` hota hai, `char` nahi.

Example:

``` java
char y = '1' + '0';
System.out.println(y);
```

`'1' + '0'`:

``` text
49 + 48 = 97
```

97 ka character `'a'` hai.

So output:

``` text
a
```

------------------------------------------------------------------------

# 8. String

`String` Java me ek **class/reference type** hai.

String literal double quotes me hota hai:

``` java
String name = "Azhar";
```

### `+` with String

String ke saath `+` use karne par concatenation hoti hai.

``` java
String x = "Hello";
String y = "World";

System.out.println(x + y);
```

Output:

``` text
HelloWorld
```

Example:

``` java
System.out.println("Age: " + 20);
```

Output:

``` text
Age: 20
```

### Important

``` java
'A' + 'B'
```

numeric addition karega:

``` text
65 + 66 = 131
```

But:

``` java
"A" + "B"
```

String concatenation karega:

``` text
AB
```

------------------------------------------------------------------------

# 9. Variable Naming Rules

Java identifiers ke basic rules:

-   Name letter, `_` (underscore), ya `$` se start ho sakta hai.
-   Digit se start nahi kar sakta.
-   Spaces allowed nahi hain.
-   Special characters generally allowed nahi hain, except `_` and `$`.
-   Java keyword ko identifier ke naam ke roop me use nahi kar sakte.
-   Java identifiers case-sensitive hote hain.

Valid:

``` java
int age;
int _count;
int $value;
int studentAge;
```

Invalid:

``` java
int 2age;
int student age;
int class;
```

### Naming Convention

Single word:

``` java
int marks;
```

Multiple words ke liye **camelCase**:

``` java
int studentMarks;
String firstName;
```

------------------------------------------------------------------------

# 10. Assignment Operators

Assignment ka basic operator:

``` java
=
```

Example:

``` java
int x = 10;
```

## Types of Assignment

### 10.1 Simple Assignment

``` java
int x = 10;
```

### 10.2 Chain Assignment

``` java
int a, b, c, d;

a = b = c = d = 10;
```

Right-to-left evaluation hoti hai.

### 10.3 Compound Assignment

Arithmetic/bitwise operation + assignment ko combine karte hain.

``` java
x += 5;
x -= 5;
x *= 5;
x /= 5;
x %= 5;
```

Equivalent:

``` java
x += 5;
```

roughly:

``` java
x = (type of x)(x + 5);
```

Compound assignment me implicit narrowing conversion jaisa behavior
allowed hota hai.

Example:

``` java
byte x = 10;
x += 20;       // valid
```

Whereas:

``` java
byte x = 10;
x = x + 20;    // error
```

because `x + 20` ka result `int` hota hai.

------------------------------------------------------------------------

# 11. Numeric Promotion

Arithmetic operations me Java operands ko promote karta hai.

Basic rule:

``` text
byte / short / char → int
```

Agar operands me `int`, `long`, `float`, `double` aaye to appropriate
wider type use hota hai.

A commonly useful rule:

``` text
result type ≈ max(int, type of x, type of y)
```

Example:

``` java
byte a = 10;
byte b = 20;

int c = a + b;
```

`a + b` ka result `int` hai.

Similarly:

``` java
char a = 'A';
char b = 'B';

int x = a + b;
```

------------------------------------------------------------------------

# 12. Arithmetic Operators

Arithmetic operators:

``` text
+   -   *   /   %
```

Normally numeric primitive types par use hote hain:

``` text
byte
short
int
long
char
float
double
```

`boolean` par arithmetic operators work nahi karte.

`String` ke saath `+` concatenation ke liye use hota hai.

Example:

``` java
int x = 10;
int y = 3;

System.out.println(x + y);  // 13
System.out.println(x - y);  // 7
System.out.println(x * y);  // 30
System.out.println(x / y);  // 3
System.out.println(x % y);  // 1
```

------------------------------------------------------------------------

# 13. Division by Zero

### Integer division

``` java
10 / 0
```

compile/runtime level par `ArithmeticException` cause karega.

### Floating-point division

``` java
10.0 / 0.0
```

Output:

``` text
Infinity
```

``` java
-10.0 / 0.0
```

Output:

``` text
-Infinity
```

``` java
0.0 / 0.0
```

Output:

``` text
NaN
```

------------------------------------------------------------------------

# 14. Relational Operators

Relational operators comparison ke liye use hote hain aur result
**boolean** hota hai.

Operators:

``` text
<    less than
>    greater than
<=   less than or equal to
>=   greater than or equal to
```

Example:

``` java
int a = 10;
int b = 20;

System.out.println(a < b);    // true
System.out.println(a > b);    // false
```

Ye operators generally numeric/character types par work karte hain, but
`boolean` aur `String` par `<`, `>`, `<=`, `>=` use nahi kar sakte.

------------------------------------------------------------------------

# 15. Equality Operators

Equality operators:

``` text
==    equal to
!=    not equal to
```

Example:

``` java
System.out.println(10 == 10);     // true
System.out.println(10 != 20);     // true
```

`==` / `!=` primitives ke liye value comparison karte hain.

Reference types ke case me `==` / `!=` references compare karte hain,
content nahi.

Example:

``` java
String a = new String("Java");
String b = new String("Java");

System.out.println(a == b);       // false
System.out.println(a.equals(b));  // true
```

### String

String content compare karne ke liye normally:

``` java
.equals()
```

use karo.

------------------------------------------------------------------------

# 16. Relational Operator with `char`

`char` ko numeric value ke context me compare kiya ja sakta hai.

``` java
System.out.println('A' == 65);      // true
System.out.println('A' == 65.0);    // true
System.out.println('A' < 65);       // false
```

Because:

``` text
'A' = 65
```

------------------------------------------------------------------------

# 17. Can We Compare 3 Numbers Directly?

Java me aisa:

``` java
10 < 20 < 30
```

valid nahi hai.

Reason:

``` text
10 < 20
```

ka result:

``` text
true
```

Ab Java `true < 30` nahi kar sakta.

Agar multiple conditions combine karni hain:

``` java
10 < 20 && 20 < 30
```

------------------------------------------------------------------------

# 18. Logical Operators

Logical operators multiple boolean conditions ko combine karne ke liye
use hote hain.

``` text
&&   Logical AND
||   Logical OR
!    Logical NOT
```

### AND `&&`

Dono conditions true honi chahiye.

``` java
true && true    → true
true && false   → false
false && true   → false
false && false  → false
```

Example:

``` java
int age = 20;

System.out.println(age >= 18 && age <= 60);
```

### OR `||`

At least ek condition true honi chahiye.

``` java
true || false   → true
false || true   → true
false || false  → false
```

### NOT `!`

Boolean result ko reverse karta hai.

``` java
!true  → false
!false → true
```

------------------------------------------------------------------------

# 19. Short-Circuiting

`&&` aur `||` short-circuit operators hain.

Example:

``` java
false && something
```

Agar first condition hi false hai, second condition evaluate karne ki
need nahi hoti.

Similarly:

``` java
true || something
```

me second condition evaluate nahi hoti.

------------------------------------------------------------------------

# 20. Increment and Decrement Operators

``` text
++   Increment
--   Decrement
```

Ye variable ki value ko 1 se increase/decrease karte hain.

### Pre-Increment

``` java
++x
```

Pehle value increment hoti hai, phir expression me use hoti hai.

Example:

``` java
int x = 5;
int y = ++x;
```

Result:

``` text
x = 6
y = 6
```

### Post-Increment

``` java
x++
```

Pehle current value expression me use hoti hai, phir variable increment
hota hai.

``` java
int x = 5;
int y = x++;
```

Result:

``` text
x = 6
y = 5
```

Same concept decrement `--` par apply hota hai.

### Important

`++` / `--` `boolean` aur `String` par work nahi karte.

------------------------------------------------------------------------

# 21. Weird `++++` Expressions

Java tokenization ki wajah se increment operators ke saath spacing
important ho sakti hai.

Clear code likho:

``` java
x++ + ++x
```

Ye valid expression ho sakta hai.

Lekin:

``` java
x+++++x
```

jaisi expressions confusing/invalid ho sakti hain because Java lexer
`++` ko tokens me parse karta hai aur postfix `++` ke operand rules
apply hote hain.

**Best practice:** increment operators ko clearly separate karke likho.

------------------------------------------------------------------------

# 22. Bitwise Operators

Bitwise operators bits par operation perform karte hain.

Main operators:

``` text
&    Bitwise AND
|    Bitwise OR
^    Bitwise XOR
~    Bitwise Complement
<<   Left Shift
>>   Signed Right Shift
>>>  Unsigned Right Shift
```

------------------------------------------------------------------------

## 22.1 Bitwise AND `&`

Rule:

``` text
1 & 1 = 1
baaki sab = 0
```

Example:

``` text
18 & 21
```

binary:

``` text
18 = 10010
21 = 10101
     -----
     10000 = 16
```

Output:

``` text
16
```

------------------------------------------------------------------------

## 22.2 Bitwise OR `|`

Rule:

``` text
0 | 0 = 0
baaki sab = 1
```

Example:

``` text
18 | 21
```

------------------------------------------------------------------------

## 22.3 Bitwise XOR `^`

Rule:

``` text
same bits → 0
different bits → 1
```

``` text
1 ^ 1 = 0
1 ^ 0 = 1
0 ^ 1 = 1
0 ^ 0 = 0
```

------------------------------------------------------------------------

## 22.4 Bitwise Complement `~`

Bits ko invert karta hai:

``` text
0 → 1
1 → 0
```

Example:

``` java
System.out.println(~15);
```

Output:

``` text
-16
```

Because signed integer representation me:

``` text
~n = -(n + 1)
```

------------------------------------------------------------------------

# 23. Left Shift `<<`

Bits ko left side shift karta hai.

``` java
20 << 3
```

20:

``` text
10100
```

3 positions left:

``` text
10100000
```

Result:

``` text
160
```

For normal positive values, approximately:

``` text
x << n = x × 2^n
```

subject to overflow and Java's shift rules.

------------------------------------------------------------------------

# 24. Signed Right Shift `>>`

Right shift me sign bit preserve hoti hai.

Example:

``` java
20 >> 3
```

approximately:

``` text
20 / 2³ = 2
```

Result:

``` text
2
```

Negative numbers me sign extension hoti hai.

------------------------------------------------------------------------

# 25. Unsigned Right Shift `>>>`

`>>>` right shift karta hai aur left side par zero fill karta hai.

``` java
x >>> n
```

Negative numbers ke saath `>>` aur `>>>` ka difference important ho jata
hai.

------------------------------------------------------------------------

# 26. Bitwise Operators and Boolean

`&`, `|`, `^` boolean operands par bhi work kar sakte hain:

``` java
true & false    // false
true | false    // true
true ^ false    // true
```

But:

``` text
<<
>>
>>>
~
```

boolean par work nahi karte.

------------------------------------------------------------------------

# 27. Operator Categories --- Quick Revision

### Arithmetic

``` text
+  -  *  /  %
```

### Unary

``` text
+  -  ++  --  !
~
```

### Relational

``` text
<  >  <=  >=
```

### Equality

``` text
==  !=
```

### Logical

``` text
&&  ||  !
```

### Bitwise

``` text
&  |  ^  ~
```

### Shift

``` text
<<  >>  >>>
```

### Assignment

``` text
=  +=  -=  *=  /=  %=
&=  |=  ^=  <<=  >>=  >>>=
```

### Ternary

``` text
condition ? value1 : value2
```

Example:

``` java
int max = (a > b) ? a : b;
```

------------------------------------------------------------------------

# 28. Compound Assignment Operators

Common compound operators:

``` text
+=
-=
*=
/=
%=
&=
|=
^=
<<=
>>=
>>>=
```

Example:

``` java
int x = 10;

x += 5;   // x = 15
x -= 3;   // x = 12
x *= 2;   // x = 24
x /= 4;   // x = 6
x %= 4;   // x = 2
```

### Automatic casting behavior

``` java
byte x = 10;

x += 20;       // valid
```

Conceptually similar to:

``` java
x = (byte)(x + 20);
```

But:

``` java
x = x + 20;    // error
```

because `x + 20` gives an `int`.

------------------------------------------------------------------------

# 29. Assignment Compatibility

Simple assignment me RHS ki type LHS ke saath compatible honi chahiye.

``` java
int x = 10;
double y = x;       // valid widening
```

But:

``` java
double x = 10.5;
int y = x;          // error
```

Use:

``` java
int y = (int) x;
```

------------------------------------------------------------------------

# 30. Multiple Assignment

Ye invalid hai:

``` java
int a = b = c = d = 10;
```

agar `b`, `c`, `d` declare nahi kiye gaye hain.

Valid:

``` java
int a, b, c, d;

a = b = c = d = 10;
```

------------------------------------------------------------------------

# 31. `printf()` Formatting

Java me:

``` java
System.out.printf();
```

formatted output ke liye use hota hai.

### `%d`

Integer ke liye:

``` java
System.out.printf("%d", 10);
```

### `%f`

Floating-point ke liye:

``` java
System.out.printf("%f", 10.25);
```

Default `%f` normally 6 digits after decimal print karta hai.

``` text
10.250000
```

### `%.3f`

Decimal ke baad 3 digits:

``` java
System.out.printf("%.3f", 10.25678);
```

Output:

``` text
10.257
```

### `%c`

Character print karne ke liye:

``` java
char ch = 'A';
System.out.printf("%c", ch);
```

Output:

``` text
A
```

### `%s`

String ke liye:

``` java
System.out.printf("%s", "Java");
```

------------------------------------------------------------------------

# 32. Width and Zero Padding

### `%4d`

Minimum width 4:

``` java
System.out.printf("%4d", 25);
```

Output conceptually:

``` text
  25
```

2 spaces + 25.

### `%04d`

Width 4, empty space ki jagah `0`:

``` java
System.out.printf("%04d", 25);
```

Output:

``` text
0025
```

### `%15d`

Minimum width 15:

``` java
System.out.printf("%15d", 25);
```

Yahan total field width 15 characters hogi.

------------------------------------------------------------------------

# 33. Character Printing --- Important Correction

Java `printf` me character print karne ke liye:

``` java
System.out.printf("%c", 'A');
```

use kar sakte ho.

`%d` character ka numeric code print karne ke liye useful hai:

``` java
System.out.printf("%d", (int)'A');
```

Output:

``` text
65
```

------------------------------------------------------------------------

# 34. Common Character Codes

``` text
'A' = 65
'B' = 66
...
'Z' = 90

'a' = 97
'b' = 98
...
'z' = 122

'0' = 48
'1' = 49
...
'9' = 57

'\n' = 10
'\r' = 13
' '  = 32
'*'  = 42
```

------------------------------------------------------------------------

# 35. Useful Type-Casting Examples

### int → double

``` java
int x = 10;
double y = x;
```

### double → int

``` java
double x = 10.99;
int y = (int) x;
```

Output:

``` text
10
```

### int → char

``` java
int x = 65;
char ch = (char) x;
```

Output:

``` text
A
```

### char → int

``` java
char ch = 'A';
int x = ch;
```

Output:

``` text
65
```

------------------------------------------------------------------------

# 36. Important Java Facts --- Quick Revision

-   Decimal literals like `10.25` are **double by default**.
-   Float literal ke liye `f` / `F` use karo.
-   `char` = 2 bytes, range `0–65535`.
-   `char` literals single quotes me hote hain.
-   `String` literals double quotes me hote hain.
-   `char + char` ka result `int` hota hai.
-   `byte + byte` ka result `int` hota hai.
-   `short + short` ka result `int` hota hai.
-   `byte`, `short`, `char` arithmetic me generally `int` me promote
    hote hain.
-   Integer division by zero → `ArithmeticException`.
-   Floating-point division by zero → `Infinity`, `-Infinity`, ya `NaN`.
-   `<`, `>`, `<=`, `>=` boolean/String par work nahi karte.
-   `==` and `!=` primitives ke liye equality compare karte hain.
-   String content compare karne ke liye `.equals()` use karo.
-   `&&`, `||`, `!` logical operators hain.
-   `&`, `|`, `^` bitwise operators hain aur boolean par bhi applicable
    ho sakte hain.
-   `<<`, `>>`, `>>>`, `~` integral types par work karte hain.
-   `++` / `--` boolean aur String par work nahi karte.
-   Compound assignment me implicit conversion allowed hoti hai.
-   `x += y` aur `x = x + y` always exactly same compile-time behavior
    nahi dete.
-   Java identifiers case-sensitive hote hain.

------------------------------------------------------------------------

# 37. One-Page Operator Cheat Sheet

  Category              Operators
  --------------------- -----------------------------------------
  Arithmetic            `+ - * / %`
  Unary                 `+ - ++ -- ! ~`
  Relational            `< > <= >=`
  Equality              `== !=`
  Logical               `&& || !`
  Bitwise               `& \| ^ ~`
  Shift                 `<< >> >>>`
  Assignment            `=`
  Compound Assignment   `+= -= *= /= %= &= \|= ^= <<= >>= >>>=`
  Ternary               `?:`

------------------------------------------------------------------------

## Final Mental Model

``` text
Smaller type → Bigger compatible type
             ↓
        Widening / Implicit

Bigger type → Smaller type
             ↓
        Narrowing / Explicit
```

``` text
byte + byte
     ↓
    int
```

``` text
char 'A'
   ↓
Unicode/character value
   ↓
65
```

``` text
10.25
   ↓
double by default

10.25f
   ↓
float
```

``` text
x++
→ use old value
→ then increment

++x
→ increment first
→ then use new value
```
