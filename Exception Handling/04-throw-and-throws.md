# throw and throws Keywords

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

- predefined exceptions
- custom/user-defined exceptions

### Important

`throw` throws an **exception object**.

``` java
throw new ArithmeticException("Invalid age");
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
 `throw` `throws`
 ----------------------------------- -----------------------------------
 Used to explicitly throw an Used to declare/propagate
 exception exceptions

 Used inside method/block Used with method/constructor
 signature

 Throws an exception object Specifies exception class(es)

 Example: `throw new IOException();` Example:
 `void show() throws IOException`

 Used to actually throw Used to declare/forward
 responsibility

 One exception object is thrown at a Multiple exception types can be
 time declared
 -----------------------------------------------------------------------

### Example

``` java
static void show() throws IOException {
 throw new IOException("File error");
}
```

Here:

- `throw` actually throws the exception.
- `throws` declares that the method may throw it.

------------------------------------------------------------------------

# 15. `try-catch` vs `throws`

 -----------------------------------------------------------------------
 `try-catch` `throws`
 ----------------------------------- -----------------------------------
 Handles the exception Does not handle the exception

 Exception handling happens in the Responsibility is passed to caller
 current method 

 Works with checked and unchecked Mainly important for checked
 exceptions exceptions

 Uses `catch` block Used in method signature
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


---

[Previous: try, catch and finally](./03-try-catch-finally.md) · [Back to Index](./README.md) · [Next: Custom Exceptions](./05-custom-exceptions.md)
