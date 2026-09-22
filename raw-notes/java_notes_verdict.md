# Java Notes — Interfaces, Polymorphism, Switch, File Handling & Strings

> Clean, exam-friendly and technically corrected version of the handwritten notes.

## 1. Access Modifiers & Method Overriding

Access visibility cannot be reduced when overriding an inherited instance method.

```text
private < default < protected < public
```

| Superclass | Overriding method may use |
|---|---|
| `default` | `default`, `protected`, `public` |
| `protected` | `protected`, `public` |
| `public` | `public` |

`private` methods are not inherited and therefore are not overridden.

```java
class A {
    protected void show() { System.out.println("A"); }
}
class B extends A {
    public void show() { System.out.println("B"); } // OK
}
```

Reducing visibility gives an error such as `attempting to assign weaker access privileges`.

**Rule:** overriding can widen visibility, not reduce it.

---

## 2. Interface

An interface defines a contract that implementing classes fulfill.

### Interface methods by Java version

- Java 7 and earlier: ordinary interface methods are implicitly `public abstract`.
- Java 8: `default` and `static` methods can have implementations.
- Java 9: `private` interface methods are also allowed for code reuse inside interface methods.

If a concrete class does not implement required abstract methods, compilation fails, e.g.:

```text
A is not abstract and does not override abstract method ...
```

### Multiple inheritance through interfaces

```java
interface Inter1 { void show(); }
interface Inter2 { void display(); }

class A implements Inter1, Inter2 {
    public void show() { }
    public void display() { }
}
```

Java does not allow multiple class inheritance, but a class can implement multiple interfaces.

---

## 3. `extends` vs `implements`

| Relationship | Keyword |
|---|---|
| Class → Class | `extends` |
| Class → Interface | `implements` |
| Interface → Interface | `extends` |
| Interface → Class | Not allowed |

```java
class B extends A { }
class C implements Inter1 { }
interface Inter2 extends Inter1 { }
```

A class can extend only one class:

```java
class C extends A, B { } // ❌
```

But it can implement multiple interfaces:

```java
class C implements Inter1, Inter2 { } // ✅
```

---

## 4. Interface Variables

Interface fields are implicitly:

```java
public static final
```

Example:

```java
interface Demo {
    int x = 10;
}
```

is effectively a `public static final` constant.

---

## 5. Default Method — Java 8

Java 8 introduced interface `default` methods with a body:

```java
interface Demo {
    default void show() {
        System.out.println("Hello");
    }
}
```

This is not a preview feature; it became part of Java 8.

---

## 6. Abstract Class vs Interface

| Feature | Abstract Class | Interface |
|---|---|---|
| Abstract methods | Yes | Yes |
| Concrete methods | Yes | `default`/`static` since Java 8; `private` since Java 9 |
| Constructor | Yes | No |
| Instance variables | Yes | No instance fields |
| Static fields | Yes | Fields are `public static final` |
| Multiple class inheritance | No | Multiple interfaces possible |

Example:

```java
abstract class A {
    int x;
    static int y;

    A() { }

    abstract void show();

    void display() {
        System.out.println("Concrete method");
    }
}
```

---

## 7. Reference Variable & Object

```java
A a1 = new A();
```

- `A` → reference type
- `a1` → reference variable
- `new A()` → creates an object and produces its reference

A superclass reference can refer to a subclass object:

```java
class A { }
class B extends A { }

A a = new B();
```

This is **upcasting**.

---

## 8. Binding in Java

Binding means associating a method call with the method implementation that will execute.

### Static / compile-time binding

Common examples include `static`, `private`, and `final` methods. These are not dispatched through normal runtime overriding.

### Dynamic / runtime dispatch

Overridable instance methods are selected according to the actual object at runtime.

```java
class A {
    void show() { System.out.println("A"); }
}
class B extends A {
    @Override
    void show() { System.out.println("B"); }
}

A obj = new B();
obj.show(); // B
```

Java methods are not literally all virtual: `static`, `private`, and `final` methods do not participate in normal overriding-based dynamic dispatch.

---

## 9. Method Overriding

Subclass and superclass have compatible instance methods with the same signature, and the subclass provides a new implementation.

```java
class A {
    void show() { System.out.println("A"); }
}
class B extends A {
    @Override
    void show() { System.out.println("B"); }
}
```

This is a key example of runtime polymorphism.

```java
A obj = new B();
obj.show(); // B
```

The actual object determines the overridden method at runtime.

---

## 10. Method Hiding

`static` methods are not overridden. If a subclass declares a same-signature static method, it is called **method hiding**.

```java
class A {
    static void show() { System.out.println("A"); }
}
class B extends A {
    static void show() { System.out.println("B"); }
}

A obj = new B();
obj.show(); // A
```

The static method is selected from the reference/class type rather than the runtime object.

| Overriding | Hiding |
|---|---|
| Instance method | `static` method |
| Runtime dispatch | Static/compile-time binding |
| Object matters | Reference/class type matters |

> Calling method hiding “compile-time polymorphism” is common in classroom notes, but **static/compile-time binding** is the more precise term.

---

## 11. Switch Arrow Labels

The modern switch arrow syntax was introduced as a preview feature in Java 12 and was later finalized.

Traditional form:

```java
switch (x) {
    case 1:
        System.out.println("A");
        break;
    case 2:
        System.out.println("B");
        break;
    default:
        System.out.println("C");
}
```

Arrow form:

```java
switch (x) {
    case 1 -> System.out.println("A");
    case 2 -> System.out.println("B");
    default -> System.out.println("C");
}
```

Arrow rules do **not fall through**, so a separate `break` is not needed.

---

## 12. Multiple Case Labels

Modern switch supports comma-separated labels:

```java
switch (x) {
    case 1, 2 -> System.out.println("S1");
    case 3, 4 -> System.out.println("S2");
    default -> System.out.println("Invalid");
}
```

### Odd / Even example

```java
switch (x) {
    case 1, 3, 5, 7, 9 -> System.out.println("Odd");
    case 2, 4, 6, 8, 10 -> System.out.println("Even");
    default -> System.out.println("Invalid");
}
```

Do not mix the old `case:` statement-group style with arrow rules as if they were interchangeable forms.

### Multiple statements after arrow

Use a block:

```java
case 1 -> {
    System.out.println("A");
    System.out.println("B");
}
```

### Duplicate labels

```java
case 1, 2 -> System.out.println("A");
case 2, 3 -> System.out.println("B"); // ❌ duplicate case label
```

---

## 13. File Handling — FileOutputStream

```java
FileOutputStream fi = new FileOutputStream("abc.txt");
fi.write('a');
fi.close();
```

### Append mode

```java
FileOutputStream fi = new FileOutputStream("abc.txt", true);
```

`true` means **append mode**: existing content is retained and new bytes are added at the end.

`write()` writes bytes to the output stream.

For input streams:

```java
int x = fi.read();
```

`read()` returns an `int`; `-1` means end of stream. It is better not to call this simply an “ASCII value” because it returns a byte value (or `-1`), while character encoding is a separate concept.

---

## 14. `==` vs `equals()` for String

`==` compares references:

```java
String s1 = new String("abc");
String s2 = new String("abc");

System.out.println(s1 == s2);      // false
System.out.println(s1.equals(s2)); // true
```

**Rule:**

```text
==       → reference comparison
 equals() → content comparison
```

---

## 15. String Pool / String Constant Pool

String literals are maintained in the JVM's **String Pool / String Constant Pool**.

```java
String s3 = "abc";
String s4 = "abc";
```

The same pooled literal can be reused, so in this example:

```java
s3 == s4          // true
s3.equals(s4)     // true
```

With `new`:

```java
String s1 = new String("abc");
String s2 = new String("abc");
```

`new String()` explicitly creates distinct String objects, so:

```java
s1 == s2          // false
s1.equals(s2)     // true
```

> The String Pool is associated with heap memory in modern JVMs. Not every String object is automatically pooled; string literals and explicitly interned strings are the key cases.

### Complete example

```java
String s1 = new String("abc");
String s2 = new String("abc");
String s3 = "abc";
String s4 = "abc";

System.out.println(s1 == s2);      // false
System.out.println(s1.equals(s2)); // true
System.out.println(s1 == s3);      // false
System.out.println(s1.equals(s3)); // true
System.out.println(s3 == s4);      // true
System.out.println(s3.equals(s4)); // true
```

---

## 16. Immutable String

`String` objects are immutable: an existing String object cannot be modified.

```java
String s = "Hello";
s.concat(" World");
System.out.println(s); // Hello
```

`concat()` returns a new String. To store it:

```java
s = s.concat(" World");
```

---

## 17. Mutable Strings

For mutable string operations:

```java
StringBuilder
StringBuffer
```

are commonly used.

- `StringBuilder` → mutable, generally preferred for single-threaded string building.
- `StringBuffer` → mutable and synchronized.

---

## 18. Static vs Dynamic Website

### Static Website

Content is mostly predefined and the server can serve prepared HTML/CSS/JS files directly.

Examples: simple portfolio, basic documentation, simple static blog.

### Dynamic Website

Content can be generated or changed according to requests, users, database data, authentication, APIs, or server-side logic.

Examples: Gmail, Instagram, e-commerce and banking portals.

Typical flow:

```text
User Request
     ↓
Server-side Logic
     ↓
Database / API
     ↓
Generated Response
     ↓
User
```

---

# Quick Revision Sheet

### Access

```text
private → default → protected → public
```

Visibility cannot be reduced during overriding.

### Interface

```text
class → implements → interface
interface → extends → interface
```

### Interface fields

```text
public static final
```

### Interface methods

```text
Java 7 and earlier → abstract methods
Java 8             → default + static methods
Java 9             → private methods
```

### Abstract class

```text
constructor          ✅
instance variables   ✅
static variables     ✅
abstract methods     ✅
concrete methods     ✅
multiple classes     ❌
```

### Binding

```text
static/private/final → static binding
overridable instance → dynamic dispatch
```

### Overriding

```text
instance method → runtime dispatch → object matters
```

### Hiding

```text
static method → static binding → reference/class type matters
```

### Switch

```java
case 1 -> ...
case 2, 3 -> ...
```

Arrow rules do not fall through.

### String

```text
==       → reference comparison
equals() → content comparison
```

### String Pool

```text
"abc"                 → pooled literal
new String("abc")     → new String object
```

### Mutable strings

```text
StringBuilder
StringBuffer
```

### File

```java
new FileOutputStream("abc.txt", true)
```

`true` → append mode.

---

# Interview One-Liners

- **Can overriding reduce access?** → No.
- **Can overriding increase access?** → Yes.
- **Can a class extend multiple classes?** → No.
- **Can a class implement multiple interfaces?** → Yes.
- **Can an interface extend multiple interfaces?** → Yes.
- **Can an interface extend a class?** → No.
- **Can an interface have variables?** → Yes; fields are implicitly `public static final`.
- **Can an interface have implemented methods?** → Yes, via `default`/`static`; private methods are also allowed since Java 9.
- **Can an abstract class have a constructor?** → Yes.
- **Can an interface have a constructor?** → No.
- **What does `==` compare for objects?** → References.
- **What does String `equals()` compare?** → Content.
- **Why can `s3 == s4` be true for literals?** → The same pooled String can be reused.
- **Why can `new String("abc")` objects be different?** → `new` explicitly creates String objects.
- **Why does arrow switch not need `break`?** → Arrow rules do not fall through.
- **What does `InputStream.read()` return?** → An `int`; `-1` indicates end of stream.
