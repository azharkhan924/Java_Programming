# Java --- Short Notes

> **Style:** Ye notes quick revision ke liye hain. Language
> intentionally **Hinglish + English** rakhi gayi hai, taaki concepts
> short aur easy-to-revise rahen.

------------------------------------------------------------------------

## 1. Java --- Uses

Java ka use mainly:

-   Software Development
-   Web Applications
-   Websites
-   Enterprise Applications
-   Backend Development

------------------------------------------------------------------------

# 2. Java History

## Green Project

-   **December 1990:** Sun Microsystems me ek project start hua jiska
    goal tha aisi technology/language banana jo electronic devices ke
    liye useful ho.
-   Project ka naam **Green Project** tha.
-   **1991:** Team bani:
    -   **Mike Sheridan** → Business Development
    -   **Patrick Naughton** → Graphics/System side
    -   **James Gosling** → Programming language / technical development

### Initial Language: C / C++

Sabse pehle C/C++ ko consider kiya gaya.

Problem:

-   C/C++ **platform/system dependent** hain.
-   Ek platform par compile ki gayi native object/executable file
    directly doosre platform par generally run nahi hoti.

Example:

``` text
Windows
   ↓
first.c
   ↓
Compiler
   ↓
first.obj
   ↓
Windows → Run ho sakti hai
Linux   → Same object file directly run nahi hogi
```

Is problem ki wajah se platform-independent approach ki need hui.

## Oak → Java

-   James Gosling ne new language develop ki.
-   Initially iska naam **Oak** tha.
-   Baad me Oak naam already registered hone ki wajah se naam change
    kiya gaya.
-   Final name **Java** rakha gaya.

> Java naming ke popular history explanation me coffee/Java Island
> connection ka mention milta hai.

## JDK 1.0

-   **23 January 1996:** JDK 1.0 officially release hua.
-   Java ka development Sun Microsystems ke under hua.

------------------------------------------------------------------------

# 3. C++ vs Java --- Basic Differences

  -----------------------------------------------------------------------
  C++                                 Java
  ----------------------------------- -----------------------------------
  Platform dependent native code      Platform independent through JVM

  Not purely object-oriented          Mostly/strongly object-oriented

  Supports pointers                   No direct pointer arithmetic

  Manual memory management possible   JVM + Garbage Collector handles
                                      memory management

  `public`, `private`, `protected`    `public`, `private`, `protected` +
                                      package-private/default access

  `goto` statement exists             `goto` is reserved but not
                                      implemented as a statement
  -----------------------------------------------------------------------

### Platform Independence

Java ka basic idea:

``` text
Java Source Code
       ↓
     javac
       ↓
   Bytecode (.class)
       ↓
      JVM
       ↓
Platform-specific execution
```

Same `.class` bytecode ko different OS ke JVM par run kiya ja sakta hai.

------------------------------------------------------------------------

# 4. Java Features

## 1. Platform Independent

Java source code directly OS-specific machine code me compile nahi hota.

``` text
.java
 ↓
javac
 ↓
.class (Bytecode)
 ↓
JVM
 ↓
Machine/OS
```

**Write Once, Run Anywhere (WORA)** concept isi idea se related hai.

## 2. Mostly Object-Oriented

Java strongly object-oriented language hai, but technically **purely
object-oriented nahi** hai because primitive types (`int`, `char`,
`boolean`, etc.) exist karte hain.

## 3. Simple

Java ka syntax C/C++ se familiar hai, but many complex features
remove/avoid kiye gaye hain.

## 4. No Direct Pointers

Java me C/C++ jaise explicit pointers aur pointer arithmetic nahi hoti.

## 5. Robust

Java ko robust banane wale factors:

-   Strong type checking
-   Exception handling
-   Automatic memory management
-   Garbage Collection
-   No direct pointer arithmetic

## 6. Secure

Java me direct memory access/pointer arithmetic na hone se memory-safety
improve hoti hai.

Security sirf pointers ki absence ki wajah se nahi hai; Java ka broader
runtime/security architecture bhi important hai.

------------------------------------------------------------------------

# 5. `.java` → `.class` → JVM

Example:

``` text
Demo.java
   ↓
javac Demo.java
   ↓
Demo.class
   ↓
JVM
   ↓
main()
```

### Internal Basic Flow

1.  `.java` file me source code hota hai.
2.  `javac` compiler source code ko **bytecode** me convert karta hai.
3.  Bytecode `.class` file me store hota hai.
4.  JVM class ko load karti hai.
5.  JVM bytecode ko execute karti hai.
6.  Java application ka entry point normally:

``` java
public static void main(String[] args)
```

hota hai.

------------------------------------------------------------------------

# 6. Variables

### Variable kya hai?

Variable ek **named storage location/container** hai jo value hold karta
hai.

Example:

``` java
int age = 21;
```

Yahan:

-   `int` → data type
-   `age` → variable
-   `21` → value

Every variable ko use karne se pehle suitable data type ke saath declare
karna hota hai.

------------------------------------------------------------------------

# 7. Integer Data Types

Java ke integer types:

  Type           Size Range
  --------- --------- -------------------
  `byte`       1 byte -128 to 127
  `short`     2 bytes -32,768 to 32,767
  `int`       4 bytes -2³¹ to 2³¹ - 1
  `long`      8 bytes -2⁶³ to 2⁶³ - 1

### General signed n-bit range

Two's complement representation me:

``` text
-2^(n-1)  to  2^(n-1) - 1
```

And:

``` text
1 byte = 8 bits
```

------------------------------------------------------------------------

# 8. Number Literals

Java me different bases ke literals:

### Octal

Prefix: `0`

``` java
int x = 0114;
```

`0114` octal hai.

``` text
0114 (octal) = 76 (decimal)
```

### Hexadecimal

Prefix: `0x` / `0X`

``` java
int x = 0xAC;
```

``` text
0xAC = 172
```

### Binary

Prefix: `0b` / `0B`

``` java
int x = 0b1011;
```

``` text
0b1011 = 11
```

### Invalid Octal

``` java
int x = 058;
```

Error because octal me digits sirf `0–7` hote hain.

------------------------------------------------------------------------

# 9. `printf()` Format Specifiers

  Specifier     Use
  ------------- --------------------------------------------
  `%d`          Decimal integer
  `%o`          Octal integer
  `%x` / `%X`   Hexadecimal integer
  `%b` / `%B`   Boolean representation
  `%c`          Character
  `%f`          Floating-point
  `%h` / `%H`   Hash-code based hexadecimal representation

> `%h` hexadecimal integer print karne ke liye nahi hai. Hexadecimal
> integer ke liye `%x` / `%X` use karo.

### Binary print karna ho

`%b` binary number ke liye nahi hai.

``` java
Integer.toBinaryString(11)
```

Output:

``` text
1011
```

------------------------------------------------------------------------

# 10. Escape Sequences

  Escape   Meaning
  -------- ---------------------------------------------------
  `\n`     New line
  `\r`     Carriage return --- current line ki beginning par
  `\b`     Backspace
  `\"`     Double quote
  `\'`     Single quote
  `\\`     Backslash

Example:

``` java
System.out.println("\"Hello\"");
```

Output:

``` text
"Hello"
```

------------------------------------------------------------------------

# 11. Modulus `%` and Sign

Java me `%` ka result generally **left operand (dividend) ka sign follow
karta hai**.

``` java
-10 % 3   // -1
-10 % -3  // -1
 10 % -3  //  1
 10 % 3   //  1
```

------------------------------------------------------------------------

# 12. Swapping Two Numbers

Variables swap karne ke common ways:

### 1. Third Variable

``` java
int temp = a;
a = b;
b = temp;
```

### 2. Addition/Subtraction

``` java
a = a + b;
b = a - b;
a = a - b;
```

> Overflow ka risk ho sakta hai.

### 3. Multiplication/Division

``` java
a = a * b;
b = a / b;
a = a / b;
```

> `0` values aur overflow ki wajah se practical use me risky.

### 4. XOR

``` java
a = a ^ b;
b = a ^ b;
a = a ^ b;
```

------------------------------------------------------------------------

# 13. Floating-Point Data Types

Java me two floating-point types:

-   `float`
-   `double`

  Type            Size       Approx. Precision
  ---------- --------- -----------------------
  `float`      4 bytes     6--7 decimal digits
  `double`     8 bytes   15--16 decimal digits

### Approximate Range

-   `float`: `~1.4E-45` to `~3.4E38`
-   `double`: `~4.9E-324` to `~1.8E308`

### Default Type

Decimal floating-point literal by default **`double`** hota hai.

``` java
double x = 10.25;
```

For `float`:

``` java
float x = 10.25f;
```

or explicit cast:

``` java
float x = (float) 10.25;
```

------------------------------------------------------------------------

# 14. Numeric Literals with `_`

Readable numbers ke liye underscores use kar sakte hain:

``` java
int x = 10_20;
```

Value:

``` text
1020
```

Large numbers:

``` java
int x = 1_000_000;
```

------------------------------------------------------------------------

# 15. Variable / Identifier Naming Rules

Java identifiers:

-   Letter, `_`, ya `$` se start ho sakte hain.
-   Digit se start nahi kar sakte.
-   Spaces allowed nahi hain.
-   Most special characters allowed nahi hain.
-   Java keyword/reserved word ko identifier ke naam ke roop me use nahi
    kar sakte.
-   Java identifiers case-sensitive hote hain.

Valid:

``` java
age
studentName
_marks
$total
```

Invalid:

``` java
2name
student name
class
```

### Naming Convention

Single word:

``` java
age
```

Two/multiple words → **camelCase**:

``` java
studentName
totalMarks
firstName
```

------------------------------------------------------------------------

# 16. Assignment Operators

Basic assignment:

``` java
x = 10;
```

Compound assignment operators:

``` java
x += 5;   // x = x + 5
x -= 5;   // x = x - 5
x *= 5;   // x = x * 5
x /= 5;   // x = x / 5
x %= 5;   // x = x % 5
```

Java arithmetic operation + assignment ko combine karne ke liye compound
assignment operators provide karta hai.

------------------------------------------------------------------------

# 17. `char` Data Type

`char` ek **single UTF-16 code unit** store karta hai.

``` java
char ch = 'A';
```

### Size

``` text
2 bytes
```

### Range

``` text
0 to 65535
```

Java `char` UTF-16 based hai.

> Note: Har keyboard key ko technically `char` nahi kaha ja sakta.
> `char` text ke UTF-16 code unit ko represent karta hai.

------------------------------------------------------------------------

# 18. Unicode

Unicode ek universal character encoding standard hai jo different
languages/scripts ke characters ko represent karne ke liye use hota hai.

Basic ASCII values:

  Character     Decimal value
  ----------- ---------------
  `A–Z`                65--90
  `a–z`               97--122
  `0–9`                48--57

Example:

``` java
int x = '1';
System.out.println(x);
```

Output:

``` text
49
```

Because `'1'` ka Unicode code point/value 49 hai.

------------------------------------------------------------------------

# 19. `char` and Type Casting

### Integer → char

``` java
int x = 65;
char ch = (char) x;

System.out.println(ch);
```

Output:

``` text
A
```

### char → int

``` java
char ch = 'A';
int x = ch;

System.out.println(x);
```

Output:

``` text
65
```

### Important

``` java
int x = 250;
char c = x;       // compile-time error
```

Because `int` → `char` narrowing conversion hai.

``` java
char c = (char) x;
```

explicit cast ke saath allowed hai.

------------------------------------------------------------------------

# 20. Character Arithmetic

`char` arithmetic me `int` me promote ho sakta hai.

``` java
char a = 'A';
char b = 'B';

int x = a + b;
```

Calculation:

``` text
65 + 66 = 131
```

Similarly:

``` java
char c = '1' + '0';   // compile-time error
```

because `'1' + '0'` ka result `int` hai.

Correct:

``` java
char c = (char) ('1' + '0');
```

Result:

``` text
a
```

Because:

``` text
'1' = 49
'0' = 48
49 + 48 = 97
97 = 'a'
```

------------------------------------------------------------------------

# 21. Useful Character Values

``` text
'\n' = 10
'\r' = 13
'*'  = 42
```

------------------------------------------------------------------------

# 22. `printf()` with Characters

Character print karne ke liye:

``` java
char ch = 'A';

System.out.printf("%c", ch);
```

Output:

``` text
A
```

`%c` character formatting ke liye hai.

------------------------------------------------------------------------

# 23. Float with `printf()`

Example:

``` java
float x = 10 / 3.0f;
System.out.printf("%f", x);
```

Default `%f` approximately **6 digits after decimal** show karta hai.

For 3 digits after decimal:

``` java
System.out.printf("%.3f", x);
```

Example output:

``` text
3.333
```

------------------------------------------------------------------------

# 24. `printf()` Width and Zero Padding

### `%4d`

``` java
System.out.printf("%4d", 10);
```

Minimum field width = 4.

``` text
  10
```

### `%04d`

``` java
System.out.printf("%04d", 10);
```

Width = 4, aur empty positions me `0` fill honge.

``` text
0010
```

### `%05d`

``` java
System.out.printf("%05d", 25);
```

Output:

``` text
00025
```

------------------------------------------------------------------------

# 25. String

`String` Java ki ek class hai.

String literal **double quotes** me likhte hain:

``` java
String name = "Azhar";
```

### `+` with String

`+` String ke saath use karne par **concatenation** karta hai.

``` java
String a = "Hello";
String b = "Java";

System.out.println(a + b);
```

Output:

``` text
HelloJava
```

### char + char

``` java
'A' + 'B'
```

Ye String concatenation nahi karega.

Instead:

``` text
65 + 66 = 131
```

because both operands `char` hain aur arithmetic context me `int` me
promote hote hain.

------------------------------------------------------------------------

# 26. Increment Operators

## Pre-increment

``` java
++x
```

Concept:

``` text
x ko pehle increment karo
↓
updated value expression me use karo
```

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

## Post-increment

``` java
x++
```

Concept:

``` text
current value expression me use karo
↓
x ko increment karo
```

Example:

``` java
int x = 5;
int y = x++;
```

Result:

``` text
y = 5
x = 6
```

------------------------------------------------------------------------

# 27. `+++` / Increment Tokenization

Java me `+` characters ko lexer valid tokens me break karta hai.

Isliye multiple `+` ko dekh kar automatically ye assume nahi karna
chahiye ki woh ek single operator hai.

Example expressions involving:

``` java
x++
++x
```

ko actual tokens ke according parse kiya jata hai.

> Revision ke time `x+++ ++x` jaise expressions ko carefully tokenize
> karo; spacing/token boundaries aur operand placement matter karte
> hain.

------------------------------------------------------------------------

# 28. `StringBuilder`

`StringBuilder` ek **mutable character sequence** hai.

Matlab: iska content change kar sakte hain without creating a new `String`
object every time.

### Why `StringBuilder`?

`String` immutable hai, isliye repeated modifications/concatenations me
multiple String objects create ho sakte hain.

`StringBuilder` mutable hone ki wajah se frequent modifications ke liye
useful hai.

### Important Points

- Methods **synchronized nahi hote**.
- Isliye ye `StringBuffer` se generally faster hota hai.
- Multiple threads same `StringBuilder` object ko concurrently access kar
  sakte hain, but required synchronization khud handle karni padegi.
- **Thread safety required nahi ho** to `StringBuilder` prefer kiya jata hai.
- `StringBuilder` **Java 5** me introduce hua.

Example:

```java
StringBuilder sb = new StringBuilder("Hello");
sb.append(" Java");

System.out.println(sb);
```

Output:

```text
Hello Java
```

### Common Methods

```java
sb.append("Java");
sb.insert(0, "Hello ");
sb.delete(0, 6);
sb.reverse();
```

------------------------------------------------------------------------

# 29. `StringBuffer`

`StringBuffer` bhi **mutable character sequence** hai.

Main difference: iske methods synchronized hote hain.

### Important Points

- Most public methods **synchronized** hain.
- Same object par ek time me synchronization ke through ek thread
  synchronized method execute karta hai.
- Thread-safety provide karne ki wajah se `StringBuilder` ke comparison me
  generally slower ho sakta hai.
- Multiple threads ke shared mutable text data ke case me useful ho sakta hai.
- `StringBuffer` **Java 1.0** se available hai.

Example:

```java
StringBuffer sb = new StringBuffer("Hello");
sb.append(" Java");

System.out.println(sb);
```

Output:

```text
Hello Java
```

> **Note:** `StringBuffer` synchronized hai, but agar multiple method calls
> milkar ek single logical operation banate hain, to complete operation ke
> liye additional synchronization ki need ho sakti hai.

------------------------------------------------------------------------

# 30. `String` vs `StringBuffer` vs `StringBuilder`

| Feature | `String` | `StringBuffer` | `StringBuilder` |
|---|---|---|---|
| Content | Immutable | Mutable | Mutable |
| Methods synchronized? | Not applicable | Yes, most public methods | No |
| Thread safety | Immutable objects can be safely shared | Designed for synchronized access | Not thread-safe by itself |
| Modification | New String/object may be created | Same object can be modified | Same object can be modified |
| Performance | Repeated modification ke liye inefficient ho sakta hai | Synchronization overhead ki wajah se generally slower | Generally faster than StringBuffer |
| Introduced | Java 1.0 | Java 1.0 | Java 5 |

### Easy Rule

```text
String
  → Content change nahi karna / immutable data

StringBuffer
  → Mutable + synchronized access required

StringBuilder
  → Mutable + synchronization required nahi
  → Frequent modifications
```

### One-Line Revision

> **String = Immutable**  
> **StringBuffer = Mutable + Synchronized**  
> **StringBuilder = Mutable + Not Synchronized**

------------------------------------------------------------------------

# Quick Revision --- One Page

```text
Java
├── Uses
│   ├── Software Development
│   ├── Web Applications
│   └── Backend / Enterprise
│
├── History
│   ├── Green Project
│   ├── Oak
│   └── Java
│
├── Platform Independence
│   └── .java → javac → .class → JVM
│
├── Integer
│   ├── byte  → 1 byte
│   ├── short → 2 bytes
│   ├── int   → 4 bytes
│   └── long  → 8 bytes
│
├── Floating
│   ├── float  → 4 bytes
│   └── double → 8 bytes
│
├── Character
│   └── char → 2 bytes → UTF-16 code unit
│
├── Number Literals
│   ├── 0   → Octal
│   ├── 0x  → Hex
│   └── 0b  → Binary
│
├── Operators
│   ├── Assignment
│   ├── Compound Assignment
│   ├── Modulus
│   ├── Increment
│   └── Swap techniques
│
├── String
│   └── Immutable
│
├── StringBuffer
│   └── Mutable + Synchronized
│
└── StringBuilder
    └── Mutable + Not Synchronized
```
