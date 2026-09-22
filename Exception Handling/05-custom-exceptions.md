# User-Defined and Custom Exceptions

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


---

[Previous: throw and throws](./04-throw-and-throws.md) · [Back to Index](./README.md) · [Next: Exception Propagation and Runtime Stack](./06-propagation-and-runtime-stack.md)
