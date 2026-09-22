# Exception Propagation and Runtime Stack

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
| main() |
```

When `show2()` completes:

``` text
| show1() |
| main() |
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

1. Exception occurs in `show2()`.
2. JVM looks for a matching handler in `show2()`.
3. If found, it handles the exception.
4. If not found, `show2()` terminates abruptly.
5. Its stack frame is removed.
6. JVM checks the caller `show1()`.
7. If `show1()` has a suitable handler, it handles the exception.
8. Otherwise, `show1()` also terminates abruptly.
9. JVM continues toward `main()`.
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

1. Exception type/name
2. Exception message/description
3. Stack trace showing where the exception propagated through the
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

 Method Output
 --------------------- -----------------------------------
 `toString()` Exception name + message
 `getMessage()` Message only
 `printStackTrace()` Exception + message + stack trace

------------------------------------------------------------------------


---

[Previous: Custom Exceptions](./05-custom-exceptions.md) · [Back to Index](./README.md) · [Next: Multi-Catch and Rules](./07-multi-catch-and-rules.md)
