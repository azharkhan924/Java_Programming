# Java Notes — Applet, Interface, String, Object Class & Exception Handling

## 1. Applet

### What is an Applet?

An **Applet** is a legacy Java program designed to be embedded in an HTML page and executed using an applet-capable Java environment.

- **HTML → Static**
- **Java → Dynamic**
- The `<applet>` tag was historically used to embed the Java Applet inside an HTML page.
- **Important:** Java Applets and browser support for them are obsolete in modern web browsers. `appletviewer` was also a legacy testing tool.

### Steps to Create an Applet

#### Step 1 — Create `.java` file

```java
import java.applet.Applet;

public class AppDemo extends Applet {
    // Applet code
}
```

The class extends `Applet`.

#### Step 2 — Compile the Java file

Compile the `.java` file in the normal way:

```bash
javac AppDemo.java
```

This generates:

```text
AppDemo.class
```

#### Step 3 — Create an HTML file

Historically:

```html
<html>
<body>

<applet code="AppDemo.class" width="200" height="200">
</applet>

</body>
</html>
```

- `code` → Applet class file
- `width` → width of Applet area
- `height` → height of Applet area

#### Step 4 — Run/Test the Applet

An Applet does **not use `main()`** as its entry point.

Historically, it could be executed:
1. Through a Java-enabled browser/plugin.
2. Using the **Applet Viewer** for testing.

Example:

```bash
appletviewer ABC.html
```

> **Modern Java note:** Browser-based Java Applets are no longer supported by current browsers, so this is mainly a legacy/exam topic.

---

# 2. Life Cycle of Applet

The main lifecycle methods are:

```java
public void init()
public void start()
public void stop()
public void destroy()
```

### Applet Life Cycle Diagram

```text
                 ┌──────────────┐
                 │    init()    │
                 └──────┬───────┘
                        ↓
                 ┌──────────────┐
            ┌───→│   start()    │←──────────┐
            │    └──────┬───────┘           │
            │           ↓                   │
            │      ┌──────────┐             │
            │      │  paint() │             │
            │      └──────────┘             │
            │                               │
     Re-open│                               │
            │                               │
            └──────── stop() ← Minimize ────┘
                         │
                    Close/Unload
                         ↓
                 ┌──────────────┐
                 │  destroy()   │
                 └──────────────┘
```

### Sequence

1. `init()` is called **once** when the Applet is initialized.
2. `start()` is called when the Applet becomes active.
3. The Applet can receive calls to `paint(Graphics g)` when its display needs to be rendered.
4. When the Applet becomes inactive, `stop()` may be called.
5. When it becomes active again, `start()` may be called again.
6. Before the Applet is unloaded, `destroy()` is called.

### Important

`paint()` is a rendering method, not one of the four primary lifecycle methods. It may be called whenever the AWT system needs to redraw the Applet.

---

# 3. Interface — Default, Static & Private Methods

Suppose an interface contains **10 methods** and is implemented by **1000 classes**.

If we add one new abstract method to the interface:

```java
void newMethod();
```

then all implementing classes may be required to implement that method.

This can cause a large number of compilation errors.

### Solution — Default Method

Java introduced **default methods in Java 8**.

A default method provides a method body inside an interface:

```java
interface Demo {
    default void show() {
        System.out.println("Default method");
    }
}
```

Existing implementing classes can continue to work without being forced to override the new method.

### Java 8

Java 8 introduced:

- `default` methods in interfaces
- `static` methods in interfaces

Example:

```java
interface Demo {

    default void show() {
        System.out.println("Hello");
    }

    static void test() {
        System.out.println("Static method");
    }
}
```

### Java 9

Java 9 introduced **private methods in interfaces**.

```java
interface Demo {

    default void method1() {
        commonCode();
    }

    default void method2() {
        commonCode();
    }

    private void commonCode() {
        System.out.println("Common code");
    }
}
```

### Why private methods?

If multiple default methods have common code, that common code can be placed inside a private method and reused.

```text
default method 1 ──┐
                   ├──→ private common method
default method 2 ──┘
```

---

# 4. `transient` Keyword

The `transient` keyword is used with an **instance variable** to tell Java serialization to skip that field.

Example:

```java
class Student implements Serializable {

    String name;

    transient String password;
}
```

When the object is serialized, the `password` field is not serialized.

### Common uses

`transient` can be useful for:

- Passwords
- PINs
- Temporary/calculated data
- Sensitive information that should not be serialized
- Large objects that should not be stored as part of the serialized object

### Key Point

```text
transient variable → skipped during default serialization
```

---

# 5. String Class — Important Methods

`String` is an immutable class in Java.

Some commonly used methods:

| # | Method | Purpose |
|---|---|---|
| 1 | `length()` | Returns number of characters |
| 2 | `charAt(int)` | Returns character at given index |
| 3 | `concat(String)` | Joins two strings |
| 4 | `toUpperCase()` | Converts to uppercase |
| 5 | `toLowerCase()` | Converts to lowercase |
| 6 | `equals(String)` | Compares string contents |
| 7 | `equalsIgnoreCase(String)` | Compares ignoring case |
| 8 | `compareTo(String)` | Lexicographically compares strings |
| 9 | `compareToIgnoreCase(String)` | Lexicographically compares ignoring case |
| 10 | `indexOf(String)` | Finds first occurrence |
| 11 | `lastIndexOf(String)` | Finds last occurrence |
| 12 | `substring(int)` | Extracts part of string |
| 13 | `trim()` | Removes leading/trailing whitespace |
| 14 | `replace()` | Replaces characters/sequences |
| 15 | `startsWith(String)` | Checks starting sequence |
| 16 | `endsWith(String)` | Checks ending sequence |
| 17 | `contains(CharSequence)` | Checks whether sequence exists |
| 18 | `split(String)` | Splits string into an array |

### Example

```java
String s = "Hello Java";

System.out.println(s.length());
System.out.println(s.charAt(0));
System.out.println(s.toUpperCase());
System.out.println(s.contains("Java"));
```

---

# 6. Object Class

`Object` is the **root/superclass of the Java class hierarchy**.

Every Java class directly or indirectly inherits from `java.lang.Object`.

Example:

```java
class Student {
}
```

is conceptually:

```java
class Student extends Object {
}
```

### Important Object Methods

Commonly studied methods include:

1. `getClass()`
2. `hashCode()`
3. `equals(Object)`
4. `clone()`
5. `toString()`
6. `notify()`
7. `notifyAll()`
8. `wait()`
9. `wait(long)`
10. `wait(long, int)`
11. `finalize()` *(deprecated for removal in modern Java)*

> **Note:** `wait()` has overloaded versions, so some classroom notes count the methods differently. The commonly listed inherited methods are 11 when the overloaded `wait` methods are counted separately. `Object` also has a constructor, but a constructor is not a method.

---

# 7. Exception Handling

## What is an Exception?

An **exception** is an unwanted or unexpected event that occurs during the execution of a program and disrupts the normal flow of the program.

Example:

```java
int a = 10;
int b = 0;

System.out.println(a / b);
```

This causes:

```text
ArithmeticException
```

### Important Point

Exceptions occur **during runtime**.

However, Java's compiler can detect certain exceptions, called **checked exceptions**, and force the programmer to handle or declare them.

> A compilation error such as a syntax error is not an exception.

---

# 8. Why Exception Handling?

Suppose a program contains 1000 statements and an exception occurs around the 50th statement.

Without proper handling:

```text
Statement 1
Statement 2
...
Statement 50 → Exception
Statement 51
Statement 52
...
Statement 1000
```

The normal flow is interrupted, so the remaining statements may not execute.

With exception handling, the program can handle the problem and continue where appropriate.

### Benefits

- Prevents abnormal termination where possible
- Maintains normal program flow
- Separates error-handling code from normal code
- Makes programs more robust

---

# 9. Exception Hierarchy

```text
                 Throwable
                /         \
             Error       Exception
              |             |
      OutOfMemoryError   Checked Exceptions
      StackOverflowError       |
                         RuntimeException
                              |
                    Unchecked Exceptions
```

### Error

Errors generally represent serious problems that applications normally should not try to recover from.

Examples:

- `OutOfMemoryError`
- `StackOverflowError`

### Exception

Exceptions represent conditions that application code may handle.

---

# 10. Checked vs Unchecked Exception

## Checked Exception

Checked exceptions are checked by the compiler.

The programmer must either:

1. Handle them using `try-catch`, or
2. Declare them using `throws`.

Examples:

- `IOException`
- `SQLException`
- `ClassNotFoundException`
- `InterruptedException`

```java
void readFile() throws IOException {
    // code
}
```

## Unchecked Exception

Unchecked exceptions are subclasses of `RuntimeException`.

They are not required to be explicitly caught or declared.

Examples:

- `ArithmeticException`
- `NullPointerException`
- `ArrayIndexOutOfBoundsException`
- `NumberFormatException`

```java
int x = 10 / 0;   // ArithmeticException
```

### Quick Difference

```text
Checked Exception
      ↓
Compiler checks handling/declaring

Unchecked Exception
      ↓
RuntimeException hierarchy
Compiler does not force handling
```

---

# 11. `try-catch`

The `try` block contains code that may generate an exception.

The `catch` block handles the exception.

### Syntax

```java
try {
    // risky code
}
catch (Exception e) {
    // exception handling
}
```

### Example

```java
try {
    int a = 10 / 0;
}
catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");
}
```

### Flow

```text
try block
   ↓
Exception occurs?
   ↓
  YES
   ↓
Matching catch block
   ↓
Continue after catch
```

---

# 12. Important Rules of `try-catch`

### Rule 1

At a time, normally only **one exception is thrown from a particular execution path** in a try block.

Once an exception occurs, normal execution of the try block stops and control moves to a matching catch block.

### Rule 2

If multiple catch blocks are used, the catch block should be ordered from **more specific to more general**.

Correct:

```java
try {
    // code
}
catch (ArithmeticException e) {
    // specific
}
catch (Exception e) {
    // general
}
```

Incorrect:

```java
try {
    // code
}
catch (Exception e) {
}
catch (ArithmeticException e) {
}
```

because `Exception` would already catch the `ArithmeticException`.

### Multiple catch blocks

```java
try {
    // risky code
}
catch (ArithmeticException e) {
}
catch (NullPointerException e) {
}
catch (Exception e) {
}
```

---

# 13. `finally`

`finally` is a block that is generally executed whether an exception occurs or not.

### Syntax

```java
try {
    // risky code
}
catch (Exception e) {
    // handling
}
finally {
    // cleanup code
}
```

### Example

```java
try {
    System.out.println("Try block");
}
catch (Exception e) {
    System.out.println("Catch block");
}
finally {
    System.out.println("Finally block");
}
```

If no exception occurs:

```text
try → finally
```

If an exception occurs and is handled:

```text
try → catch → finally
```

### Common use

`finally` is commonly used for cleanup operations such as closing resources.

Modern Java often prefers **try-with-resources** for automatically closing `AutoCloseable` resources.

---

# 14. `throw` Keyword

The `throw` keyword is used to **explicitly throw an exception**.

### Syntax

```java
throw new ExceptionType("message");
```

Example:

```java
if (age < 18) {
    ArithmeticException ae =
        new ArithmeticException("Invalid age");
    throw ae;
}
```

Shorter form:

```java
if (age < 18) {
    throw new ArithmeticException("Invalid age");
}
```

### Example Program

```java
import java.util.Scanner;

class Demo {
    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);
        int age = sc.nextInt();

        if (age < 18) {
            ArithmeticException ae =
                new ArithmeticException("Invalid age: " + age);
            throw ae;
        }
        else {
            System.out.println("Welcome");
        }

        System.out.println("SWT");
    }
}
```

If the user enters `12`:

```text
Exception in thread "main"
java.lang.ArithmeticException: Invalid age: 12
```

### Key Point

`throw` is used to **manually/explicitly generate an exception**.

---

# 15. `throws` Keyword

`throws` is used in a method declaration to indicate that the method may pass one or more exceptions to its caller.

### Syntax

```java
returnType methodName() throws ExceptionType {
    // code
}
```

Example:

```java
void readFile() throws IOException {
    // file handling code
}
```

### Why use `throws`?

If a method does not handle a checked exception itself, it can declare that exception using `throws`.

The caller then becomes responsible for handling or further declaring it.

---

# 16. `throw` vs `throws`

| `throw` | `throws` |
|---|---|
| Used to explicitly throw an exception | Used to declare possible exceptions |
| Used inside method/block | Used in method declaration |
| Throws one exception object at a time | Can declare multiple exception types |
| Example: `throw new IOException();` | Example: `void test() throws IOException` |

Example:

```java
void test() throws IOException {
    throw new IOException("File error");
}
```

Here:

- `throw` → actually throws the exception
- `throws` → declares that the method can throw it

---

# 17. Custom / User-Defined Exception

Java allows us to create our own exception classes.

Usually, a custom checked exception extends `Exception`.

Example:

```java
class InvalidAgeException extends Exception {

    InvalidAgeException() {
    }

    InvalidAgeException(String msg) {
        super(msg);
    }
}
```

### Using the Custom Exception

```java
class Demo {

    public static void main(String[] args) {

        int age = 12;

        try {
            if (age < 18) {
                throw new InvalidAgeException(
                    "Age must be 18 or above"
                );
            }

            System.out.println("Welcome");
        }
        catch (InvalidAgeException e) {
            System.out.println(e.getMessage());
        }
    }
}
```

### Flow

```text
age < 18
   ↓
throw InvalidAgeException
   ↓
catch (InvalidAgeException e)
   ↓
handle exception
```

---

# 18. Quick Revision

```text
Applet
  ↓
Legacy Java program embedded in HTML

Applet lifecycle
  ↓
init() → start() → paint()
             ↕
           stop()
             ↓
          destroy()

Interface
  ↓
Java 8 → default + static methods
Java 9 → private interface methods

transient
  ↓
Skip field during default serialization

String
  ↓
Immutable class
18 important methods listed above

Object
  ↓
Root class of Java class hierarchy

Exception
  ↓
Runtime event that disrupts normal flow

Exception hierarchy
  ↓
Throwable
 ├── Error
 └── Exception
      └── RuntimeException

Checked Exception
  ↓
Compiler requires handling/declaring

Unchecked Exception
  ↓
RuntimeException and subclasses

try
  ↓
Risky code

catch
  ↓
Handle exception

finally
  ↓
Cleanup code; generally executes whether exception occurs or not

throw
  ↓
Explicitly throw an exception

throws
  ↓
Declare exception in method signature

Custom Exception
  ↓
Create your own exception class
