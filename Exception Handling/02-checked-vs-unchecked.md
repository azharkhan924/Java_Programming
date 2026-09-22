# Checked vs Unchecked Exceptions

# 3. Types of Exceptions

Java exceptions are broadly divided into:

1. **Checked Exceptions**
2. **Unchecked Exceptions**

## 3.1 Checked Exception

Checked exceptions are checked by the **compiler**.

The programmer must either:

- handle them using `try-catch`, or
- declare/propagate them using `throws`.

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
├── IOException → Checked
├── SQLException → Checked
└── RuntimeException → Unchecked
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


---

[Previous: Exception Hierarchy and Basics](./01-exception-hierarchy-and-basics.md) · [Back to Index](./README.md) · [Next: try, catch and finally](./03-try-catch-finally.md)
