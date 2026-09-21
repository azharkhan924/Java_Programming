# Java Notes --- Varargs, Control Statements, Loops & I/O

> **Quick Revision Notes**\
> Topics covered from the handwritten pages: Variable Arguments, Method
> Overloading, Control Statements, Switch, Access Modifiers, Loops, I/O,
> BufferedReader, StringTokenizer, Scanner, Escape Characters, For-each
> Loop and AWT hierarchy.

------------------------------------------------------------------------

## 1. Variable Arguments (Varargs)

### Introduction

The **variable arguments (varargs)** concept was introduced in **Java 5
(JDK 1.5)**.

It is useful when the number of arguments to be passed to a method is
**not fixed**.

### Why was Varargs introduced?

Consider a method for addition.

Without varargs, if we want to support:

-   2 numbers
-   3 numbers
-   4 numbers
-   5 numbers
-   ...
-   n numbers

we would need multiple overloaded methods:

``` java
void sum(int a, int b) { }
void sum(int a, int b, int c) { }
void sum(int a, int b, int c, int d) { }
```

This becomes lengthy and difficult to maintain.

With varargs, a single method can accept a variable number of arguments.

### Main idea

> **When we don't know how many arguments will be passed to a method, or
> when we want to allow a variable number of arguments, we can use
> varargs.**

A varargs parameter allows us to pass **zero or more values** of the
specified type.

### Syntax

``` java
returnType methodName(dataType... variableName)
```

Example:

``` java
void sum(int... x)
```

### Important points

1.  Varargs was introduced in **Java 5**.
2.  A method can have **only one varargs parameter**.
3.  The varargs parameter **must be the last parameter**.
4.  We can pass **zero, one, two, or any number of values**.
5.  An array reference can also be passed to a varargs parameter.
6.  Varargs has the **lowest precedence** during method overloading.
7.  These are valid:

``` java
void sum(int... a)
void sum(int ...a)
void sum(int...a)
```

There must be **three dots (`...`)**. Spaces around them are allowed.

------------------------------------------------------------------------

## 2. Varargs vs Array Parameter

### Varargs

``` java
void sum(int... a)
```

We can call it in multiple ways:

``` java
sum();
sum(10);
sum(10, 20);
sum(10, 20, 30);
```

We can also pass an array:

``` java
int[] x = {10, 20, 30};
sum(x);
```

### Array parameter

``` java
void sum(int[] a)
```

Here, an array is required.

``` java
int[] x = {10, 20, 30};
sum(x);
```

We cannot do:

``` java
sum();
sum(10);
sum(10, 20);
```

### Key difference

  -----------------------------------------------------------------------
  Varargs                             Array
  ----------------------------------- -----------------------------------
  `int... a`                          `int[] a`

  Zero or more arguments can be       Array reference is required
  passed                              

  Direct values can be passed         Direct multiple values cannot be
                                      passed

  Array reference can also be passed  Array reference can be passed
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 3. Rules of Varargs

### Rule 1 --- Varargs must be the last parameter

Valid:

``` java
void display(int x, int... y)
```

Invalid:

``` java
void display(int... x, int y)
```

### Rule 2 --- Only one varargs parameter

Valid:

``` java
void display(int x, int... y)
```

Invalid:

``` java
void display(int... x, int... y)
```

### Rule 3 --- Varargs and array cannot be overloaded separately

These two methods have the same effective signature:

``` java
void sum(int... a)
void sum(int[] a)
```

Therefore, they **cannot coexist as overloaded methods**.

------------------------------------------------------------------------

## 4. Example of Varargs

``` java
class A {

    void sum(int... a) {
        int s = 0;

        for (int i : a) {
            s = s + i;
        }

        System.out.println(s);
    }

    public static void main(String[] args) {
        A obj = new A();

        obj.sum();
        obj.sum(10);
        obj.sum(10, 20);
        obj.sum(100, 200, 300);

        int[] x = {10, 20, 30, 40};
        obj.sum(x);
    }
}
```

### Output

``` text
0
10
30
600
100
```

------------------------------------------------------------------------

# 5. Method Overloading

### Definition

**Method overloading** means defining multiple methods with:

-   Same method name
-   Different parameter list

inside the **same class**.

Example:

``` java
class Calculator {

    void sum(int a, int b) {
        System.out.println(a + b);
    }

    void sum(int a, int b, int c) {
        System.out.println(a + b + c);
    }
}
```

### Drawbacks of Method Overloading

If we want to support an unlimited number of arguments, we would need
many overloaded methods.

For example:

``` java
sum(a, b)
sum(a, b, c)
sum(a, b, c, d)
sum(a, b, c, d, e)
```

For every different number of arguments, another method may be required.

**Varargs solves this problem.**

------------------------------------------------------------------------

# 6. `printf()` and Varargs

`printf()` also accepts a variable number of arguments.

Example:

``` java
System.out.printf("%d", x);

System.out.printf("%d %d %d", x, y, z);
```

The method can accept a variable number of arguments because of varargs.

Conceptually, its declaration is based on a varargs parameter.

------------------------------------------------------------------------

# 7. Program Control Statements

Main control statements in Java:

1.  `if`
2.  `if-else`
3.  Nested `if`
4.  `else-if ladder`
5.  `switch-case`
6.  Ternary operator
7.  Loops

------------------------------------------------------------------------

## 8. `if` Condition in Java

Java requires a **boolean expression** as the condition of `if`.

Valid:

``` java
if (true) {
    System.out.println("Hello");
}
```

``` java
boolean x = true;

if (x) {
    System.out.println("Hello");
}
```

Invalid:

``` java
if (0) {
}
```

Error:

``` text
incompatible types: int cannot be converted to boolean
```

Java does **not** treat `0` as false and `1` as true like C/C++.

This is also invalid:

``` java
boolean x = 1;
```

because `1` is an `int`, not a `boolean`.

------------------------------------------------------------------------

# 9. Switch Statement

Basic syntax:

``` java
switch (x) {

    case 1:
        // code
        break;

    case 2:
        // code
        break;

    default:
        // code
}
```

### If matching case is found

That case is executed.

### If no case matches and `default` exists

The `default` block is executed.

### If no case matches and no default exists

No switch case body is executed.

------------------------------------------------------------------------

## 10. `break` in Switch

`break` terminates the switch.

Example:

``` java
switch (x) {

    case 1:
        System.out.println("One");
        break;

    case 2:
        System.out.println("Two");
        break;

    default:
        System.out.println("Other");
}
```

### Duplicate `break`

If an extra `break` is placed after an unconditional `break`, the later
statement may become unreachable.

Example:

``` java
case 1:
    System.out.println("One");
    break;
    break;   // unreachable
```

This results in an **unreachable statement** compile-time error.

------------------------------------------------------------------------

# 11. Duplicate Case Label

The same case value cannot be repeated.

Invalid:

``` java
switch (x) {

    case 1:
        System.out.println("A");
        break;

    case 1:
        System.out.println("B");
        break;
}
```

Error:

``` text
duplicate case label
```

Similarly, `default` can appear only once.

Invalid:

``` java
default:
    System.out.println("A");

default:
    System.out.println("B");
```

Error:

``` text
duplicate default label
```

------------------------------------------------------------------------

## 12. `case default` is Invalid

`default` is a separate label.

Correct:

``` java
default:
    System.out.println("Default");
```

Incorrect:

``` java
case default:
    System.out.println("Default");
```

This produces a syntax-related compile-time error such as:

``` text
illegal start of expression
```

------------------------------------------------------------------------

# 13. Data Types Allowed in Switch

Traditional Java `switch` supports:

-   `byte`
-   `short`
-   `char`
-   `int`
-   `String`
-   corresponding wrapper types in applicable Java versions
-   `enum`

The handwritten rule to remember for basic Java learning is:

``` text
byte, short, int, char, String  -> allowed
long, float, double, boolean    -> not allowed
```

### String in switch

`String` became supported in `switch` from **Java 7**.

Before Java 7, `String` could not be used directly in a switch
statement.

------------------------------------------------------------------------

# 14. Switch with Byte

If the switch expression is a `byte`, case constants must be
representable within the `byte` range.

`byte` range:

``` text
-128 to 127
```

Example:

``` java
byte x = 10;

switch (x) {

    case 10:
        System.out.println("Ten");
        break;

    case 20:
        System.out.println("Twenty");
        break;
}
```

A case label outside the valid range cannot be used for a `byte` switch
expression, even if that case would never execute.

------------------------------------------------------------------------

# 15. Access Modifiers

There are four main access modifiers:

1.  `private`
2.  default (no modifier)
3.  `protected`
4.  `public`

  -----------------------------------------------------------------------
  Modifier                            Access
  ----------------------------------- -----------------------------------
  `private`                           Same class only

  default                             Same package

  `protected`                         Same package + subclasses outside
                                      the package

  `public`                            Accessible wherever the containing
                                      type/member is accessible
  -----------------------------------------------------------------------

### 1. private

``` java
private int x;
```

Accessible only inside the same class.

### 2. default

``` java
int x;
```

No modifier is written.

Accessible within the same package.

### 3. protected

``` java
protected int x;
```

Accessible within the same package and through inheritance in
subclasses, including subclasses in another package subject to Java's
protected-access rules.

### 4. public

``` java
public int x;
```

Generally accessible from anywhere, subject to the accessibility of the
containing class/type.

------------------------------------------------------------------------

# 16. Loops in Java

Main loops:

1.  `while`
2.  `do-while`
3.  `for`
4.  `for-each`

------------------------------------------------------------------------

# 17. `while` Loop

Syntax:

``` java
while (condition) {
    // body
}
```

The condition must be boolean.

Invalid:

``` java
while (0) {
}
```

Error:

``` text
incompatible types: int cannot be converted to boolean
```

Java does not treat `0` as `false`.

------------------------------------------------------------------------

# 18. Constant Expression vs Variable in Loop

Java performs compile-time analysis for certain **constant
expressions**.

If the compiler can prove that a loop is infinite and a statement after
it can **never be reached**, an unreachable-statement error can occur.

Example:

``` java
while (true) {
}

System.out.println("Hello");
```

The compiler knows that `while(true)` cannot terminate normally.

### Variable case

``` java
boolean x = true;

while (x) {
}

System.out.println("Hello");
```

Here `x` is a variable, so the compiler does not treat the condition
exactly like the constant expression `true` for unreachable-statement
analysis.

The actual runtime value can matter.

> **Exam point:** Constant conditions can allow the compiler to prove
> unreachable code; variable conditions are generally evaluated at
> runtime.

------------------------------------------------------------------------

# 19. `for` Loop

Syntax:

``` java
for (initialization; condition; update) {
    // body
}
```

Example:

``` java
for (int i = 0; i < 5; i++) {
    System.out.println(i);
}
```

A `for` statement has **exactly two semicolons** in its header.

Valid:

``` java
for (;;) {
    // infinite loop
}
```

Valid:

``` java
for (int i = 0; i < 5; i++) {
}
```

Invalid:

``` java
for (int i = 0; i < 5; i++;) {
}
```

The extra semicolon changes the grammar and causes a compile-time error.

------------------------------------------------------------------------

# 20. `break` and `continue`

### break

`break` transfers control out of the **nearest enclosing loop or
switch**.

Example:

``` java
for (int i = 1; i <= 5; i++) {

    if (i == 3)
        break;

    System.out.println(i);
}
```

Output:

``` text
1
2
```

### continue

`continue` skips the remaining body of the current loop iteration and
moves to the next iteration.

Example:

``` java
for (int i = 1; i <= 5; i++) {

    if (i == 3)
        continue;

    System.out.println(i);
}
```

Output:

``` text
1
2
4
5
```

### Important

`continue` must be associated with a loop.

A labeled `continue` must also target an enclosing loop.

A `break` can target a loop or switch; a **labeled break** can target an
enclosing labeled statement.

------------------------------------------------------------------------

# 21. `System.out.println()` Return Type

The return type of:

``` java
System.out.println()
```

is:

``` java
void
```

It prints data and does not return a value.

------------------------------------------------------------------------

# 22. `%n` in `printf`

`%n` is a platform-independent line separator used with formatted
output.

Example:

``` java
System.out.printf("Hello%nWorld");
```

It produces:

``` text
Hello
World
```

------------------------------------------------------------------------

# 23. I/O Concepts

I/O = **Input / Output**

### Standard Output

``` java
System.out
```

It represents the standard output stream, normally the
**console/screen**.

### Standard Input

``` java
System.in
```

It represents the standard input stream, normally the **keyboard**.

------------------------------------------------------------------------

# 24. InputStreamReader (ISR)

For keyboard input:

``` java
System.in
```

is a byte-oriented input stream.

`InputStreamReader` acts as a bridge between:

``` text
Byte Stream → Character Stream
```

So:

``` text
Keyboard
   ↓
System.in
   ↓
InputStreamReader
   ↓
Character data
```

### Import

``` java
import java.io.InputStreamReader;
```

### Example

``` java
InputStreamReader isr =
        new InputStreamReader(System.in);
```

`InputStreamReader` reads characters by decoding bytes using a character
encoding.

------------------------------------------------------------------------

# 25. BufferedReader (BR)

`BufferedReader` is a wrapper around a `Reader`.

It uses a buffer to make character input more efficient and convenient.

Typical structure:

``` text
System.in
   ↓
InputStreamReader
   ↓
BufferedReader
```

Example:

``` java
InputStreamReader isr =
        new InputStreamReader(System.in);

BufferedReader br =
        new BufferedReader(isr);
```

Imports:

``` java
import java.io.InputStreamReader;
import java.io.BufferedReader;
import java.io.IOException;
```

------------------------------------------------------------------------

# 26. BufferedReader `read()`

`read()` reads a single character and returns its integer value.

Method:

``` java
int read()
```

Example:

``` java
int x = br.read();
```

The returned `int` represents the character value (Unicode code unit).
It returns `-1` when the end of the stream is reached.

------------------------------------------------------------------------

# 27. BufferedReader `readLine()`

`readLine()` reads an entire line and returns a `String`.

Method:

``` java
String readLine()
```

Example:

``` java
String s = br.readLine();
```

If the input is:

``` text
10 20 30
```

then `readLine()` reads the whole line as:

``` java
"10 20 30"
```

------------------------------------------------------------------------

# 28. IOException

I/O operations can throw `IOException`.

Example:

``` java
import java.io.*;

class Demo {

    public static void main(String[] args)
            throws IOException {

        BufferedReader br =
            new BufferedReader(
                new InputStreamReader(System.in)
            );

        String s = br.readLine();

        System.out.println(s);
    }
}
```

`java.lang` is automatically imported by Java.

Classes such as `InputStreamReader`, `BufferedReader`, and `IOException`
are from `java.io`.

------------------------------------------------------------------------

# 29. StringTokenizer

`StringTokenizer` is used to divide a string into smaller parts called
**tokens**.

Package:

``` java
java.util
```

Import:

``` java
import java.util.StringTokenizer;
```

Example:

``` java
String s = "10 20";

StringTokenizer st =
        new StringTokenizer(s);
```

By default, whitespace characters are used as delimiters.

The string:

``` text
10 20
```

is divided into:

``` text
10
20
```

------------------------------------------------------------------------

# 30. StringTokenizer with Custom Delimiters

Syntax:

``` java
new StringTokenizer(string, delimiters)
```

Example:

``` java
String s = "10,20 30";

StringTokenizer st =
        new StringTokenizer(s, ", ");
```

Here both:

``` text
,
```

and

``` text
space
```

are delimiters.

------------------------------------------------------------------------

# 31. Important StringTokenizer Methods

### `nextToken()`

Returns the next token and moves the tokenizer position forward.

``` java
String x = st.nextToken();
```

### `countTokens()`

Returns the number of remaining tokens.

``` java
int n = st.countTokens();
```

### `hasMoreTokens()`

Checks whether more tokens are available.

``` java
while (st.hasMoreTokens()) {
    System.out.println(st.nextToken());
}
```

### NoSuchElementException

If `nextToken()` is called when no token is available, a:

``` text
NoSuchElementException
```

can occur.

------------------------------------------------------------------------

# 32. Scanner Class

`Scanner` belongs to:

``` java
java.util
```

Import:

``` java
import java.util.Scanner;
```

Example:

``` java
Scanner sc = new Scanner(System.in);
```

### `next()`

Reads the next token, normally separated by whitespace.

Example input:

``` text
Hello World
```

``` java
String s = sc.next();
```

Result:

``` text
Hello
```

It stops at whitespace.

### `nextInt()`

Reads an integer.

``` java
int x = sc.nextInt();
```

If the next input token is not a valid integer, `nextInt()` throws:

``` text
InputMismatchException
```

------------------------------------------------------------------------

# 33. Enter Key --- `\r` and `\n`

The Enter key is associated with a line separator.

Commonly, a Windows-style line ending is:

``` text
\r\n
```

where:

``` text
\r = carriage return
\n = line feed
```

So if input is:

``` text
AB
```

followed by Enter, the input stream can contain:

``` text
A B \r \n
```

depending on the platform/input mechanism.

> **Important:** The exact line-ending sequence is platform-dependent.
> Java provides APIs such as `System.lineSeparator()` when the
> platform's line separator is needed.

------------------------------------------------------------------------

# 34. For-each Loop

The **for-each loop** is mainly used to traverse arrays and iterable
collections.

Syntax:

``` java
for (dataType variable : arrayOrCollection) {
    // body
}
```

Example:

``` java
int[] x = {10, 20, 30, 40};

for (int i : x) {
    System.out.println(i);
}
```

Output:

``` text
10
20
30
40
```

### Important points

-   It automatically traverses the elements.
-   We do not manually manage the index.
-   It is useful for arrays and collections implementing `Iterable`.
-   It starts from the first element and continues until the last
    available element.

------------------------------------------------------------------------

# 35. AWT Hierarchy

Basic AWT hierarchy from the handwritten notes:

``` text
Object
   ↓
Component
   ↓
Container
   ↓
Window
   ↓
Frame
```

### Meaning

-   `Object` --- root class of Java class hierarchy.
-   `Component` --- base class for AWT components.
-   `Container` --- a component that can contain other components.
-   `Window` --- top-level container without normal frame decorations.
-   `Frame` --- a top-level window with frame decorations.

### Diagram

``` text
Object
  │
  ▼
Component
  │
  ▼
Container
  │
  ▼
Window
  │
  ▼
Frame
```

------------------------------------------------------------------------

# 36. Quick Revision --- One-Liners

### Varargs

``` text
Introduced in Java 5.
```

``` java
void sum(int... a)
```

-   Zero or more arguments
-   Only one varargs parameter
-   Must be the last parameter
-   Array reference can be passed
-   Varargs has lower/least priority in overload resolution

### Method Overloading

``` text
Same method name + different parameter list
```

### `if`

``` text
Condition must be boolean.
```

### Switch

``` text
byte, short, char, int, String, enum → supported
long, float, double, boolean → not supported
```

`String` switch support started in **Java 7**.

### Access Modifiers

``` text
private   → same class
default   → same package
protected → same package + subclass access
public    → broadest access
```

### Loops

``` text
while
do-while
for
for-each
```

### `break`

``` text
Exits the nearest enclosing loop or switch.
```

### `continue`

``` text
Skips the current loop iteration and continues with the next iteration.
```

### I/O

``` text
System.in  → standard input
System.out → standard output
```

### Input hierarchy

``` text
System.in → InputStreamReader → BufferedReader
```

### BufferedReader

``` java
int read()
String readLine()
```

### StringTokenizer

``` text
java.util.StringTokenizer
```

Important methods:

``` java
nextToken()
countTokens()
hasMoreTokens()
```

### Scanner

``` text
java.util.Scanner
```

``` java
next()
nextInt()
```

`nextInt()` can throw `InputMismatchException`.

### For-each

``` java
for (int x : array) {
}
```

Used mainly for traversal.

------------------------------------------------------------------------

# 37. Important Exam/Interview Traps

1.  **Java does not convert `0`/`1` to `false`/`true`.**
2.  `void sum(int... a)` can accept **zero arguments**.
3.  `void sum(int[] a)` cannot be called with zero arguments.
4.  `int... a` and `int[] a` cannot form two separate overloaded
    methods.
5.  Varargs must be the **last parameter**.
6.  A method can have only **one varargs parameter**.
7.  `String` became available in switch from **Java 7**.
8.  `long`, `float`, `double`, and `boolean` cannot be used as switch
    expression types.
9.  `BufferedReader.read()` returns `int`, not `char`.
10. `BufferedReader.readLine()` returns `String`.
11. `StringTokenizer.nextToken()` moves to the next token after
    returning the current token.
12. Calling `nextToken()` when no token remains can throw
    `NoSuchElementException`.
13. `Scanner.next()` reads one token, not the complete line.
14. `Scanner.nextInt()` expects an integer token.
15. `%n` produces a platform-appropriate line separator in formatted
    output.
16. A `for` loop header contains exactly **two semicolons**.
17. `break` exits a loop/switch; `continue` moves to the next loop
    iteration.
18. A labeled `continue` must target a loop.
19. Java performs compile-time unreachable-code analysis for conditions
    it can prove constant.
20. `System.in` is the standard input stream and `System.out` is the
    standard output stream.
