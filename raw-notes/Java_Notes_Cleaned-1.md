# Java Notes — Arrays, OOP, Inheritance, Final, Abstract Class & Interface

> **Revision notes:** These notes are cleaned and corrected from the handwritten notes and explanations provided.  
> Focus: **Java 8 concepts + interview-oriented rules, examples, and common compiler errors.**

---

# 1. Arrays

## Definition

An array is an indexed collection of elements of the **same data type**.

```java
int[] x = new int[5];
```

This creates space for **5 integers**.

Indexes:

```text
0  1  2  3  4
```

Accessing an invalid index gives:

```text
ArrayIndexOutOfBoundsException
```

Example:

```java
int[] x = new int[5];
System.out.println(x[5]); // ❌ ArrayIndexOutOfBoundsException
```

---

## Default values in arrays

When an array is created, its elements receive Java's default values.

```java
int[] x = new int[5];
```

Conceptually:

```text
0  0  0  0  0
```

For primitive types:

| Type | Default value |
|---|---|
| byte | `0` |
| short | `0` |
| int | `0` |
| long | `0L` |
| float | `0.0f` |
| double | `0.0d` |
| char | `'\u0000'` |
| boolean | `false` |

For reference types:

```text
null
```

---

# 2. Array printing

Consider:

```java
int[] x = {1, 2, 3, 4};
System.out.println(x);
```

This does **not** print the elements.

It produces a representation similar to:

```text
[I@1b6d3586
```

The important part:

```text
[I
```

means an `int[]`.

Examples:

```text
[I@...   → int[]
[F@...   → float[]
[J@...   → long[]
[D@...   → double[]
```

To print array elements:

```java
System.out.println(Arrays.toString(x));
```

---

# 3. char[] special case

```java
char[] x = {50, 51, 52};
System.out.println(x);
```

Output:

```text
234
```

Because Unicode/ASCII values:

```text
50 → '2'
51 → '3'
52 → '4'
```

`System.out.println(char[])` prints the character contents rather than the normal object-style array representation.

---

# 4. 1D and 2D arrays

## 1D array

A collection of elements:

```java
int[] x = {10, 20, 30};
```

## 2D array

Conceptually, an array whose elements are themselves arrays:

```java
int[][] x = new int[3][5];
```

This represents 3 rows, each containing 5 integers.

---

# 5. Array declaration syntax

These are valid declarations:

```java
int[][] x;
int [][]x;
int x[][];
int[] x[];
```

The placement of `[]` can vary between the type and variable name.

---

# 6. Regular vs irregular (jagged) arrays

## Regular 2D array

```java
int[][] x = new int[3][5];
```

All 3 rows have length 5.

```text
[ ][ ][ ][ ][ ]
[ ][ ][ ][ ][ ]
[ ][ ][ ][ ][ ]
```

## Jagged / irregular array

```java
int[][] x = new int[3][];
```

This is **valid**.

Initially:

```text
x[0] → null
x[1] → null
x[2] → null
```

No `NullPointerException` occurs merely by creating it.

We can then allocate rows independently:

```java
x[0] = new int[2];
x[1] = new int[5];
x[2] = new int[3];
```

Now row sizes differ:

```text
[ ][ ]
[ ][ ][ ][ ][ ]
[ ][ ][ ]
```

This is a jagged/irregular array.

### When does NPE occur?

For example:

```java
int[][] x = new int[3][];
System.out.println(x[0][0]); // ❌ NullPointerException
```

because `x[0]` is still `null`.

---

# 7. Anonymous array

An array can be created without storing it in a named variable:

```java
new int[]{10, 20, 30}
```

Example:

```java
print(new int[]{10, 20, 30});
```

When using an array initializer with explicit values, do not specify the size:

```java
new int[3]{10, 20, 30}; // ❌ invalid
new int[]{10, 20, 30};  // ✅
```

---

# 8. Java 8 reserved words / keywords

In the commonly taught Java 8 classification:

```text
50 keywords
+ true
+ false
+ null
= 53 reserved words/tokens commonly listed
```

`true`, `false`, and `null` are literals, not keywords.

Two reserved words are not used as normal Java language keywords:

```text
goto
const
```

### Examples of Java keywords

```text
data types:
byte, short, int, long, float, double, char, boolean

control:
if, else, switch, case, default

loops:
for, while, do

OOP:
class, object-related constructs, extends, implements,
this, super, new

exceptions:
try, catch, finally, throw, throws

other:
assert, enum, native, volatile, transient,
strictfp, synchronized, instanceof
```

---

# 9. Local vs instance variables

## Local variable

A local variable must be definitely initialized before use.

```java
void test() {
    int x;
    System.out.println(x); // ❌ compile-time error
}
```

## Instance variable

Instance fields receive default values automatically.

```java
class A {
    int x;
}
```

`x` gets:

```text
0
```

Instance fields can also be initialized at declaration:

```java
class A {
    int x = 10;
}
```

---

# 10. Static variables

A static variable belongs to the class rather than to an individual object.

```java
class A {
    static int x;
}
```

Its default value is:

```text
0
```

A static variable cannot be declared as a local variable:

```java
void test() {
    static int x; // ❌
}
```

---

# 11. Static method and static context

A static method can directly access static members:

```java
class A {
    static int x = 10;

    static void show() {
        System.out.println(x); // ✅
    }
}
```

A static method cannot directly access an instance field:

```java
class A {
    int x = 10;

    static void show() {
        System.out.println(x); // ❌
    }
}
```

Typical error:

```text
non-static variable x cannot be referenced from a static context
```

However, an object reference can be used:

```java
static void show() {
    A obj = new A();
    System.out.println(obj.x); // ✅
}
```

### Key rule

> A static context cannot directly access instance members because there is no implicit current object (`this`) available.

---

# 12. Static method and `this` / `super`

`this` and `super` require an object context.

Therefore:

```java
static void test() {
    this.x;   // ❌
    super.x;  // ❌
}
```

---

# 13. Static import ambiguity

Static imports can create ambiguity.

For example, if two imported classes expose members with the same name:

```java
import static java.lang.Byte.*;
import static java.lang.Short.*;
```

and then:

```java
System.out.println(MIN_VALUE);
```

the compiler may report an ambiguity because both classes provide `MIN_VALUE`.

Using the class name removes the ambiguity:

```java
System.out.println(Byte.MIN_VALUE);
System.out.println(Short.MIN_VALUE);
```

---

# 14. Inheritance types

## Supported with Java classes

```text
1. Single inheritance
2. Multilevel inheritance
3. Hierarchical inheritance
```

## Not supported with classes

```text
Multiple inheritance
Hybrid inheritance
```

Example of unsupported multiple class inheritance:

```java
class C extends A, B { } // ❌
```

### Why?

Java avoids the ambiguity associated with inheriting the same members from multiple classes.

However, Java supports implementing multiple interfaces:

```java
interface A {}
interface B {}

class C implements A, B {}
```

---

# 15. Constructors

If a class contains **no constructor at all**, the compiler provides a default no-argument constructor.

```java
class A {
}
```

Conceptually:

```java
class A {
    A() {
        super();
    }
}
```

But if we explicitly write any constructor:

```java
class A {
    A(int x) {
    }
}
```

the compiler does **not** provide a no-argument default constructor.

Therefore:

```java
new A(); // ❌
```

---

# 16. Constructor chaining

Constructor chaining means one constructor causes another constructor to execute.

There are two important constructor calls:

```text
this()  → current class constructor
super() → parent class constructor
```

Example:

```java
class A {
    A() {
        System.out.println("A");
    }
}

class B extends A {
    B() {
        System.out.println("B");
    }
}
```

The compiler implicitly inserts:

```java
super();
```

at the beginning of `B()` if we have not explicitly written `this()` or `super()`.

Execution:

```text
A constructor
↓
B constructor
```

---

# 17. Important constructor rules

### Rule 1

`this()` or `super()` must be the **first statement** in a constructor.

### Rule 2

You cannot use both as constructor invocations in the same constructor:

```java
A() {
    this();
    super(); // ❌
}
```

### Rule 3

Only one explicit constructor invocation can be the first statement.

### Rule 4

`this()` calls a constructor of the current class.

### Rule 5

`super()` calls a constructor of the immediate parent class.

---

# 18. Parameterized parent constructor trap

Consider:

```java
class A {
    A(int x) {
    }
}

class B extends A {
    B() {
    }
}
```

The compiler attempts to insert:

```java
super();
```

But `A` has no no-argument constructor.

Therefore `B()` gives a compile-time error.

Fix:

```java
class B extends A {
    B() {
        super(10);
    }
}
```

---

# 19. `final` keyword

`final` has three major uses.

## Final variable

Cannot be reassigned:

```java
final int x = 10;

x = 20; // ❌
```

Error is conceptually:

```text
cannot assign a value to final variable x
```

## Final method

Cannot be overridden:

```java
class A {
    final void show() {
    }
}

class B extends A {
    void show() { } // ❌
}
```

## Final class

Cannot be inherited:

```java
final class A {
}

class B extends A { } // ❌
```

---

# 20. Cyclic inheritance

A class cannot inherit from itself:

```java
class A extends A {
}
```

This results in an error similar to:

```text
cyclic inheritance involving A
```

---

# 21. Why use inheritance?

Inheritance allows a subclass to reuse and extend the behavior/properties of an existing superclass.

Conceptually:

```text
Existing class
      ↓
   inherit
      ↓
New class with reused + additional behavior
```

A `final` class intentionally prevents further class inheritance.

For example:

```java
String
```

is a final class, so it cannot be subclassed.

---

# 22. Method overriding

Method overriding allows a subclass to provide its own implementation of an inherited superclass method.

```java
class A {
    void show() {
        System.out.println("A");
    }
}

class B extends A {
    @Override
    void show() {
        System.out.println("B");
    }
}
```

If the parent implementation is not suitable for the child, the child can override it.

### Final method

A final method cannot be overridden.

### Private method

A private method is not inherited, so it cannot be overridden.

---

# 23. Blank final variable

A very important distinction:

```java
final int x;
```

does **not** receive the normal instance-field default value `0`.

It is called a **blank final variable**.

It must be definitely assigned exactly once before the constructor can complete.

Valid:

```java
class A {
    final int x;

    A() {
        x = 10;
    }
}
```

Invalid:

```java
class A {
    final int x;

    A() {
        // x never assigned
    }
}
```

---

# 24. Blank final and multiple constructors

If an instance blank-final field exists, every possible constructor path must definitely assign it.

```java
class A {
    final int x;

    A() {
        x = 10;
    }

    A(int value) {
        x = value;
    }
}
```

Both constructors assign `x`.

---

# 25. Static blank final

A static blank final variable can be initialized using a static initializer block:

```java
class A {
    static final int X;

    static {
        X = 100;
    }
}
```

---

# 26. Instance blank final

An instance blank final can be initialized in an instance initializer block:

```java
class A {
    final int x;

    {
        x = 100;
    }
}
```

An instance initializer runs as part of object construction.

---

# 27. Private methods and overriding

Private methods are not inherited by subclasses.

Therefore they cannot be overridden.

```java
class A {
    private void show() {
    }
}

class B extends A {
    private void show() {
    }
}
```

The `show()` methods are separate methods, not an overriding relationship.

---

# 28. Abstract class

An abstract class is declared using:

```java
abstract class A {
}
```

You cannot directly create an object of an abstract class:

```java
A obj = new A(); // ❌
```

Typical error:

```text
A is abstract; cannot be instantiated
```

---

# 29. Abstract class does NOT require an abstract method

This is an important interview trap.

This is completely valid:

```java
abstract class Vehicle {
    void start() {
        System.out.println("Start");
    }
}
```

There is no abstract method, but the class is abstract.

### Correct rule

> If a class contains an abstract method, the class must be declared abstract.

But an abstract class **may contain zero abstract methods**.

---

# 30. Abstract method

An abstract method has no body:

```java
abstract void show();
```

It must be declared using `abstract`.

Example:

```java
abstract class A {
    abstract void show();
}
```

A concrete subclass must implement all inherited abstract methods:

```java
class B extends A {
    @Override
    void show() {
        System.out.println("Hello");
    }
}
```

If it does not, `B` itself must be abstract:

```java
abstract class B extends A {
}
```

---

# 31. Interface

An interface is declared using:

```java
interface A {
}
```

A normal interface cannot be instantiated directly:

```java
A obj = new A(); // ❌
```

A class implements an interface:

```java
class B implements A {
}
```

---

# 32. Interface methods

In Java 8, a normal abstract interface method is implicitly:

```java
public abstract
```

So:

```java
interface A {
    void show();
}
```

is conceptually equivalent to:

```java
interface A {
    public abstract void show();
}
```

### Java 8 exception to the simple rule

Interfaces can also contain:

```java
default
static
```

methods with implementations.

Therefore the clean rule is:

> Normal interface methods are implicitly `public abstract`; default and static interface methods are different cases.

---

# 33. Implementing interface methods

Because an interface method is public, the implementation cannot reduce its visibility.

Correct:

```java
interface A {
    void show();
}

class B implements A {
    public void show() {
    }
}
```

Incorrect:

```java
class B implements A {
    void show() {
    }
}
```

This causes an error similar to:

```text
attempting to assign weaker access privileges
```

Reason:

```text
interface method → public
implementation   → cannot be less accessible than public
```

---

# 34. Access modifier rule for overriding

When overriding a method, the child method cannot have weaker access.

General rule:

```text
private   → not overridden
default   → default / protected / public
protected → protected / public
public    → public
```

A child can maintain or increase accessibility, but cannot reduce it.

---

# 35. Quick interview traps

## Trap 1

```java
int x;
System.out.println(x);
```

Inside a method → ❌ local variable not initialized.

But:

```java
class A {
    int x;
}
```

→ `x == 0`.

---

## Trap 2

```java
final int x;
```

A blank final instance field is **not automatically assigned 0**.

---

## Trap 3

```java
int[][] x = new int[3][];
```

Valid.

It does not immediately throw NPE.

NPE can occur when accessing an uninitialized row:

```java
x[0][0];
```

---

## Trap 4

```java
abstract class A {
}
```

Valid.

An abstract class does not need to contain an abstract method.

---

## Trap 5

Hierarchical inheritance **is supported** in Java.

Multiple inheritance of classes is not.

---

## Trap 6

```java
static void test() {
    System.out.println(instanceVariable);
}
```

Direct access to an instance field from static context → ❌.

But:

```java
A obj = new A();
System.out.println(obj.instanceVariable);
```

→ ✅.

---

# 36. Ultra-short revision sheet

```text
ARRAY
→ indexed collection of same type
→ invalid index = ArrayIndexOutOfBoundsException
→ arrays receive default element values
→ int[] println gives representation like [I@...
→ char[] println prints characters
→ new int[3][] is valid
→ jagged array = rows can have different lengths

LOCAL VARIABLE
→ must be initialized before use

INSTANCE VARIABLE
→ gets default value

STATIC
→ belongs to class
→ cannot be local
→ static context cannot directly access instance members
→ this/super cannot be used in static context

INHERITANCE
→ single, multilevel, hierarchical supported
→ multiple class inheritance not supported
→ multiple interfaces can be implemented

CONSTRUCTOR
→ if no constructor is declared, compiler supplies default no-arg constructor
→ if any constructor is declared, compiler does not supply the default one
→ this()/super() must be first statement
→ this() = current class constructor
→ super() = parent constructor

FINAL
→ variable: cannot reassign
→ method: cannot override
→ class: cannot extend
→ blank final must be definitely assigned

ABSTRACT CLASS
→ cannot instantiate
→ may contain zero or more abstract methods
→ class containing abstract method must be abstract

INTERFACE
→ cannot instantiate directly
→ class implements interface
→ normal abstract interface methods are public abstract
→ implementation cannot reduce access

OVERRIDING
→ child changes inherited method implementation
→ private methods are not overridden
→ final methods cannot be overridden
→ access cannot be reduced
```

---

# Final correction checklist from the original notes

Before using the handwritten notes for interview revision, remember these corrections:

1. `char` default value = `'\u0000'`, not space.
2. `new int[3][]` is valid and does not itself throw NPE.
3. NPE occurs when an uninitialized row is dereferenced.
4. Hierarchical inheritance is supported.
5. Multiple inheritance of classes is not supported.
6. A final blank instance variable does not automatically become `0`.
7. An abstract class can have **no abstract methods**.
8. An abstract class cannot be instantiated directly.
9. Static methods can access instance members through an object reference.
10. Interface methods are not all simply `public abstract` in Java 8 because `default` and `static` methods exist.
11. `String` is a reference type; its instance-field default value is `null`.
12. `super()` is implicitly inserted only when no explicit `this()` or `super()` constructor invocation is present.
