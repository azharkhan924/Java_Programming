# Multi-Catch and Exception Handling Rules

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


---

[Previous: Exception Propagation and Runtime Stack](./06-propagation-and-runtime-stack.md) · [Back to Index](./README.md) · [Next: Quick Revision](./08-quick-revision.md)
