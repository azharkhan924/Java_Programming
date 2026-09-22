# Exception Hierarchy and Basics

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
statement 15; // Exception
statement 16;
statement 17;
```

Without exception handling:

- Execution stops at the point where the exception occurs.
- The remaining statements of that method are not executed normally.

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
 / \
 Error Exception
 |
 ----------------
 | |
 Checked Unchecked
 Exceptions Exceptions
 |
 RuntimeException
```

### Important Classes

``` text
Object
 └── Throwable
 ├── Error
 │ ├── OutOfMemoryError
 │ └── StackOverflowError
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


---

[Back to Exception Handling Index](./README.md) · [Next: Checked vs Unchecked Exceptions](./02-checked-vs-unchecked.md)
