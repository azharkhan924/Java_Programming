# try, catch and finally Blocks

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


---

[Previous: Checked vs Unchecked Exceptions](./02-checked-vs-unchecked.md) · [Back to Index](./README.md) · [Next: throw and throws](./04-throw-and-throws.md)
