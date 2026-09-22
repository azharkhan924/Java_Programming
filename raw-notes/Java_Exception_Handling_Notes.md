# Java Exception Handling

> **Complete Notes --- Exception Handling, Runtime Stack, Propagation,
> `throw`, `throws`, Multi-Catch & Checked Exceptions**

------------------------------------------------------------------------

## 1. What is an Exception?

An **exception** is an abnormal event that occurs during program
execution and disrupts the normal flow of the program.

### Example

Suppose a program contains 1000 statements and an exception occurs at
statement 15:

``` java
statement 1;
statement 2;
// ...
statement 15;   // Exception
statement 16;
statement 17;
```

Without exception handling:

-   Execution stops at the point where the exception occurs.
-   The remaining statements of that method are not executed normally.

With proper exception handling:

``` java
try {
    // risky code
} catch (Exception e) {
    // handling code
}
```

the exception can be handled and execution can continue after the
`catch` block.

> **Important:** Exceptions such as `ArithmeticException`,
> `NullPointerException`, etc. normally occur at runtime. However, Java
> also has **checked exceptions**, which are checked by the compiler at
> compile time. Therefore, saying "exceptions only occur at runtime" is
> an oversimplification.

------------------------------------------------------------------------

# 2. Exception Hierarchy

The basic hierarchy is:

``` text
java.lang.Object
       |
   Throwable
    /     \
 Error   Exception
           |
     ----------------
     |              |
 Checked        Unchecked
 Exceptions    Exceptions
                    |
              RuntimeException
```

### Important Classes

``` text
Object
  └── Throwable
       ├── Error
       │    ├── OutOfMemoryError
       │    └── StackOverflowError
       │
       └── Exception
            ├── IOException
            ├── SQLException
            └── RuntimeException
                 ├── ArithmeticException
                 ├── NullPointerException
                 ├── ArrayIndexOutOfBoundsException
                 └── ...
```

------------------------------------------------------------------------

# 3. Types of Exceptions

Java exceptions are broadly divided into:

1.  **Checked Exceptions**
2.  **Unchecked Exceptions**

## 3.1 Checked Exception

Checked exceptions are checked by the **compiler**.

The programmer must either:

-   handle them using `try-catch`, or
-   declare/propagate them using `throws`.

### Example

``` java
import java.io.*;

class Demo {
    public static void main(String[] args) {
        try {
            FileInputStream fis = new FileInputStream("abc.txt");
        } catch (IOException e) {
            System.out.println("File error");
        }
    }
}
```

If a checked exception is neither caught nor declared, compilation fails
with an error such as:

``` text
unreported exception IOException;
must be caught or declared to be thrown
```

------------------------------------------------------------------------

## 3.2 Unchecked Exception

Unchecked exceptions are subclasses of `RuntimeException`.

They are not required to be caught or declared.

Examples:

``` text
ArithmeticException
NullPointerException
ArrayIndexOutOfBoundsException
NumberFormatException
ClassCastException
```

Example:

``` java
int a = 10;
int b = 0;

System.out.println(a / b);
```

This produces:

``` text
ArithmeticException
```

------------------------------------------------------------------------

# 4. Exception Handling Keywords

The major exception-handling keywords are:

``` text
try
catch
finally
throw
throws
```

------------------------------------------------------------------------

# 5. `try` Block

The `try` block contains code where an exception may occur.

``` java
try {
    // risky code
}
```

A `try` block must be followed by at least one of:

-   `catch`
-   `finally`

or it can be used as part of **try-with-resources**.

### Invalid

``` java
try {
    int x = 10 / 0;
}

System.out.println("Hello"); // Invalid placement
```

There must be no normal statement between a `try` block and its
associated `catch`/`finally`.

------------------------------------------------------------------------

# 6. `catch` Block

The `catch` block handles an exception generated inside the
corresponding `try` block.

``` java
try {
    int x = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");
}
```

When the exception occurs, control transfers to the matching `catch`
block.

------------------------------------------------------------------------

# 7. Multiple `catch` Blocks

A single `try` block can have multiple `catch` blocks.

``` java
try {
    // risky code
}
catch (ArithmeticException e) {
    // handle arithmetic exception
}
catch (NullPointerException e) {
    // handle null pointer exception
}
catch (Exception e) {
    // handle other exceptions
}
```

The JVM selects the **first matching catch block**.

### Important Rule

Specific exceptions must come before their parent exception.

### Correct

``` java
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

### Incorrect

``` java
try {
    // code
}
catch (Exception e) {
}
catch (ArithmeticException e) {
}
```

This causes a compilation error because:

``` text
ArithmeticException has already been caught
```

`Exception` can already catch `ArithmeticException`.

------------------------------------------------------------------------

# 8. One Exception at a Time

At a particular point of execution, one thrown exception object is
propagated at a time.

Once an exception is thrown and not handled at the current point, normal
execution of that block/method is interrupted.

For example:

``` java
try {
    statement1;
    statement2;
    statement3;  // exception occurs here
    statement4;
    statement5;
}
```

After the exception at `statement3`, `statement4` and `statement5` are
not executed normally from that `try` block.

------------------------------------------------------------------------

# 9. `finally` Block

The `finally` block contains code that should execute whether an
exception occurs or not.

``` java
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

### Example: Closing a Connection

``` java
try {
    // open connection
    // send data
}
catch (Exception e) {
    // handle exception
}
finally {
    // close connection
}
```

The `finally` block is commonly used for cleanup operations such as:

-   closing files
-   closing database connections
-   releasing resources

### Possible Structures

``` java
try {
}
finally {
}
```

or:

``` java
try {
}
catch (Exception e) {
}
finally {
}
```

or:

``` java
try {
}
catch (Exception e) {
}
catch (Exception e2) {
}
finally {
}
```

> **Note:** `finally` normally executes, but there are exceptional
> situations such as JVM termination (`System.exit()`), JVM crash, or
> forced process termination where it may not execute.

------------------------------------------------------------------------

# 10. `throw` Keyword

The `throw` keyword is used to **explicitly throw an exception object**.

It is useful when the programmer wants to generate an exception
according to a custom condition.

### Syntax

``` java
throw new ExceptionType("message");
```

### Example

``` java
int age = 15;

if (age < 18) {
    throw new ArithmeticException("Age is less than 18");
}
```

Here, the programmer manually throws the exception.

`throw` can be used with:

-   predefined exceptions
-   custom/user-defined exceptions

### Important

`throw` throws an **exception object**.

``` java
throw new ArithmeticException("Invalid age");
```

------------------------------------------------------------------------

# 11. User-Defined / Custom Exception

Java allows us to create our own exception classes.

A custom exception can extend:

-   `Exception` --- commonly used for a checked custom exception
-   `RuntimeException` --- commonly used for an unchecked custom
    exception

### Example: Custom Unchecked Exception

``` java
class InvalidAgeException extends RuntimeException {

    public InvalidAgeException() {
        super();
    }

    public InvalidAgeException(String message) {
        super(message);
    }
}
```

Using the custom exception:

``` java
class Demo {
    public static void main(String[] args) {

        int age = 15;

        if (age < 18) {
            throw new InvalidAgeException("Age must be 18 or above");
        }

        System.out.println("Eligible");
    }
}
```

### Why Two Constructors?

``` java
public InvalidAgeException() {
    super();
}
```

Creates the exception without a custom message.

``` java
public InvalidAgeException(String message) {
    super(message);
}
```

Allows us to pass a meaningful message.

------------------------------------------------------------------------

# 12. Checked Custom Exception

If we extend `Exception`, our custom exception becomes checked.

``` java
class InvalidAgeException extends Exception {

    public InvalidAgeException(String message) {
        super(message);
    }
}
```

Now it must be handled or declared.

``` java
static void checkAge(int age) throws InvalidAgeException {

    if (age < 18) {
        throw new InvalidAgeException("Invalid age");
    }
}
```

------------------------------------------------------------------------

# 13. `throws` Keyword

The `throws` keyword is used in a **method signature** to declare that a
method may pass an exception to its caller.

### Syntax

``` java
returnType methodName() throws ExceptionType {
}
```

Example:

``` java
static void show() throws IOException {
    // code
}
```

`throws` does **not handle** the exception.

It delegates responsibility to the calling method.

------------------------------------------------------------------------

# 14. `throw` vs `throws`

  -----------------------------------------------------------------------
  `throw`                             `throws`
  ----------------------------------- -----------------------------------
  Used to explicitly throw an         Used to declare/propagate
  exception                           exceptions

  Used inside method/block            Used with method/constructor
                                      signature

  Throws an exception object          Specifies exception class(es)

  Example: `throw new IOException();` Example:
                                      `void show() throws IOException`

  Used to actually throw              Used to declare/forward
                                      responsibility

  One exception object is thrown at a Multiple exception types can be
  time                                declared
  -----------------------------------------------------------------------

### Example

``` java
static void show() throws IOException {
    throw new IOException("File error");
}
```

Here:

-   `throw` actually throws the exception.
-   `throws` declares that the method may throw it.

------------------------------------------------------------------------

# 15. `try-catch` vs `throws`

  -----------------------------------------------------------------------
  `try-catch`                         `throws`
  ----------------------------------- -----------------------------------
  Handles the exception               Does not handle the exception

  Exception handling happens in the   Responsibility is passed to caller
  current method                      

  Works with checked and unchecked    Mainly important for checked
  exceptions                          exceptions

  Uses `catch` block                  Used in method signature
  -----------------------------------------------------------------------

Example:

``` java
static void show() {
    try {
        // risky code
    }
    catch (Exception e) {
        // handled here
    }
}
```

Using `throws`:

``` java
static void show() throws IOException {
    // caller must handle or further declare it
}
```

------------------------------------------------------------------------

# 16. Exception Propagation

When an exception is not handled in the current method, the JVM looks
for a suitable handler in the calling method.

This process continues up the method-call chain.

``` text
Method C
   ↓
Method B
   ↓
Method A
   ↓
main()
```

If the exception is not handled in `C`:

``` text
C → B → A → main
```

This process is called **Exception Propagation**.

------------------------------------------------------------------------

# 17. Exception Propagation Using `throws`

Example:

``` java
class Demo {

    static void show2() throws ArithmeticException {
        int x = 10 / 0;
    }

    static void show1() throws ArithmeticException {
        show2();
    }

    public static void main(String[] args) {
        show1();
    }
}
```

Conceptually:

``` text
show2()
   ↓
show1()
   ↓
main()
```

If `show2()` does not handle the exception, responsibility can move to
`show1()` and then to `main()`.

> Unchecked exceptions can propagate automatically. For checked
> exceptions, the compiler requires them to be caught or declared with
> `throws`.

------------------------------------------------------------------------

# 18. Runtime Stack Mechanism

Every Java program starts with a **main thread** by default.

For each thread, JVM maintains a separate runtime stack.

### Runtime Stack

The runtime stack stores information related to method calls made by
that thread.

Suppose:

``` java
main()
   ↓
show1()
   ↓
show2()
```

The stack conceptually becomes:

``` text
| show2() |
| show1() |
| main()  |
```

When `show2()` completes:

``` text
| show1() |
| main()  |
```

When `show1()` completes:

``` text
| main() |
```

When `main()` completes:

``` text
Empty Stack
```

The thread then terminates, and its JVM-managed stack resources are
released.

------------------------------------------------------------------------

# 19. Runtime Stack During Exception

Suppose:

``` java
main()
   ↓
show1()
   ↓
show2()
```

and an exception occurs inside `show2()`.

The JVM looks for an appropriate exception handler.

### Step-by-step

1.  Exception occurs in `show2()`.
2.  JVM looks for a matching handler in `show2()`.
3.  If found, it handles the exception.
4.  If not found, `show2()` terminates abruptly.
5.  Its stack frame is removed.
6.  JVM checks the caller `show1()`.
7.  If `show1()` has a suitable handler, it handles the exception.
8.  Otherwise, `show1()` also terminates abruptly.
9.  JVM continues toward `main()`.
10. If `main()` also does not handle it, the default exception handler
    handles the uncaught exception.
11. The program terminates.

------------------------------------------------------------------------

# 20. Default Exception Handler

If an exception reaches the top of the call stack without being handled,
Java's **default uncaught exception handler** prints information about
the uncaught exception and its stack trace.

Typical output:

``` text
Exception in thread "main" java.lang.ArithmeticException: / by zero
    at Demo.show2(Demo.java:5)
    at Demo.show1(Demo.java:9)
    at Demo.main(Demo.java:13)
```

This contains:

1.  Exception type/name
2.  Exception message/description
3.  Stack trace showing where the exception propagated through the
    program

------------------------------------------------------------------------

# 21. Stack Trace

A stack trace tells us where the exception occurred and how execution
reached that point.

Example:

``` text
java.lang.ArithmeticException: / by zero
    at Demo.show2(Demo.java:5)
    at Demo.show1(Demo.java:9)
    at Demo.main(Demo.java:13)
```

Read it from the top:

``` text
show2()
  ↓
show1()
  ↓
main()
```

------------------------------------------------------------------------

# 22. Methods to Print Exception Information

Java provides commonly used methods for displaying exception
information.

## 22.1 `toString()`

Returns exception class name and message.

``` java
System.out.println(e.toString());
```

Example:

``` text
java.lang.ArithmeticException: / by zero
```

------------------------------------------------------------------------

## 22.2 `getMessage()`

Returns only the exception message.

``` java
System.out.println(e.getMessage());
```

Example:

``` text
/ by zero
```

------------------------------------------------------------------------

## 22.3 `printStackTrace()`

Prints the exception and its stack trace.

``` java
e.printStackTrace();
```

Example:

``` text
java.lang.ArithmeticException: / by zero
    at Demo.main(Demo.java:5)
```

### Quick Comparison

  Method                Output
  --------------------- -----------------------------------
  `toString()`          Exception name + message
  `getMessage()`        Message only
  `printStackTrace()`   Exception + message + stack trace

------------------------------------------------------------------------

# 23. Multi-Catch

Java allows multiple exception types to be handled by a single `catch`
block using the pipe (`|`) operator.

### Syntax

``` java
try {
    // code
}
catch (ArithmeticException | NullPointerException e) {
    System.out.println("Exception occurred");
}
```

This avoids writing separate catch blocks when the handling logic is the
same.

------------------------------------------------------------------------

# 24. Rule for Multi-Catch

Exception types in a multi-catch statement **must not have a
parent-child relationship**.

### Invalid

``` java
catch (ArithmeticException | Exception e) {
}
```

Why?

``` text
Exception
   |
ArithmeticException
```

`ArithmeticException` is already covered by `Exception`.

Therefore, the compiler rejects this combination.

### Correct

``` java
catch (ArithmeticException | NullPointerException e) {
}
```

These two exceptions do not have a parent-child relationship with each
other.

------------------------------------------------------------------------

# 25. Parent Catch Block

A superclass exception can catch exceptions of its subclasses.

Example:

``` java
try {
    int x = 10 / 0;
}
catch (Exception e) {
    System.out.println("Exception handled");
}
```

`Exception` can handle:

``` text
ArithmeticException
NullPointerException
NumberFormatException
...
```

because these are subclasses of `RuntimeException`, which itself is a
subclass of `Exception`.

------------------------------------------------------------------------

# 26. Fully Checked Exception

A checked exception is called **fully checked** when all of its
subclasses are also checked exceptions.

### Example

``` text
IOException
```

Its relevant subclasses are checked exceptions.

Therefore, `IOException` is commonly described as a fully checked
exception.

------------------------------------------------------------------------

# 27. Partially Checked Exception

An exception class is called **partially checked** when it has both
checked and unchecked subclasses.

Examples:

``` text
Throwable
Exception
```

For example:

``` text
Exception
├── IOException        → Checked
├── SQLException       → Checked
└── RuntimeException   → Unchecked
```

Therefore, `Exception` is partially checked.

Similarly, `Throwable` contains both:

``` text
Error
Exception
```

and the hierarchy ultimately contains both checked and unchecked
categories.

------------------------------------------------------------------------

# 28. Important Exception Handling Flow

``` text
              Exception Occurs
                     |
                     v
              Current Method
                     |
             Handler Present?
               /           \
             Yes            No
              |              |
           Handle       Method terminates
                             |
                             v
                       Calling Method
                             |
                     Handler Present?
                       /         \
                     Yes          No
                      |            |
                   Handle     Continue propagation
                                   |
                                   v
                                 main()
                                   |
                         Handler Present?
                           /         \
                         Yes          No
                          |            |
                       Handle     Default Handler
                                      |
                                      v
                              Stack Trace + Termination
```

------------------------------------------------------------------------

# 29. Important Rules to Remember

1.  `try` contains risky code.
2.  `catch` handles exceptions.
3.  `finally` is mainly used for cleanup.
4.  `throw` explicitly throws an exception object.
5.  `throws` declares/propagates exception responsibility to the caller.
6.  Checked exceptions are checked by the compiler.
7.  Unchecked exceptions are subclasses of `RuntimeException`.
8.  Checked exceptions must be caught or declared.
9.  `try` must be followed by `catch`, `finally`, or used with
    try-with-resources.
10. Multiple `catch` blocks are allowed.
11. Specific `catch` blocks must come before general `catch` blocks.
12. Multi-catch uses `|`.
13. Multi-catch alternatives cannot have a parent-child relationship.
14. `getMessage()` returns the exception message.
15. `toString()` returns exception type and message.
16. `printStackTrace()` prints the stack trace.
17. Unhandled exceptions propagate through the calling methods.
18. If an exception reaches the top without a handler, the default
    uncaught exception handling mechanism reports it.
19. A custom exception can extend `Exception` or `RuntimeException`.
20. `throw` can be used for both checked and unchecked exceptions;
    checked exceptions must additionally satisfy Java's catch-or-declare
    rule.

------------------------------------------------------------------------

# 30. Quick Revision Table

  -----------------------------------------------------------------------
  Concept                             Meaning
  ----------------------------------- -----------------------------------
  Exception                           Abnormal event that disrupts normal
                                      execution

  Checked Exception                   Checked by compiler

  Unchecked Exception                 `RuntimeException` and its
                                      subclasses

  `try`                               Contains risky code

  `catch`                             Handles exception

  `finally`                           Cleanup code

  `throw`                             Explicitly throws an exception
                                      object

  `throws`                            Declares exception in method
                                      signature

  Exception Propagation               Passing an unhandled exception
                                      toward callers

  Runtime Stack                       Stores method-call stack frames for
                                      a thread

  `toString()`                        Exception name + message

  `getMessage()`                      Exception message

  `printStackTrace()`                 Full stack trace

  Multi-Catch                         Handle multiple exception types in
                                      one catch

  Custom Exception                    Programmer-defined exception

  Fully Checked                       All subclasses are checked

  Partially Checked                   Contains both checked and unchecked
                                      subclasses
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 31. One-Line Memory Tricks

``` text
try     → Try risky code
catch   → Catch the exception
finally → Finally do cleanup
throw   → Throw an exception
throws  → Tell caller about the exception
```

``` text
throw  = actually throw
throws = declare / pass responsibility
```

``` text
getMessage()   → Message
toString()     → Name + Message
printStackTrace() → Full path
```

``` text
Checked   → Compiler checks
Unchecked → RuntimeException hierarchy
```
