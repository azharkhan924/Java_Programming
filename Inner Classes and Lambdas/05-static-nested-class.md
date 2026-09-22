# Static Nested Class

# 15. Static Inner Class

A class declared inside another class is called an **inner/nested
class**.

A class declared using the `static` keyword is called a **static nested
class**.

Example:

``` java
class A {

 static class B {

 void show() {
 System.out.println("Show");
 }
 }
}
```

Here:

``` text
A
└── static class B
 └── show()
```

------------------------------------------------------------------------

# 16. Creating Object of Static Inner Class

A static nested class can be accessed using the outer class name.

``` java
class A {

 static class B {

 void show() {
 System.out.println("Show");
 }
 }
}
```

Another class:

``` java
class D {

 public static void main(String[] args) {

 A.B b = new A.B();

 b.show();
 }
}
```

### Important syntax

``` java
A.B b = new A.B();
```

Here:

- `A.B` → type of reference variable
- `b` → reference variable
- `new A.B()` → creates object of static nested class `B`

No object of outer class `A` is required.

------------------------------------------------------------------------

# 17. Why Static Inner Class Can Be Created Without Outer Object

For a normal instance inner class, an outer object is required:

``` java
A a = new A();
A.B b = a.new B();
```

But for a static nested class:

``` java
A.B b = new A.B();
```

is sufficient.

Reason:

> A static nested class belongs to the outer class itself, not to a
> particular object of the outer class.

------------------------------------------------------------------------

# 18. Static Members in Static Nested Class

A static nested class can contain:

- static variables
- static methods
- non-static variables
- non-static methods
- `main()` method

Example:

``` java
class A {

 static class B {

 static int x = 100;

 int y = 200;

 static void staticShow() {
 System.out.println("Static method");
 }

 void instanceShow() {
 System.out.println("Instance method");
 }

 public static void main(String[] args) {
 System.out.println("Main method inside static nested class");
 }
 }
}
```

------------------------------------------------------------------------

# 19. Instance Inner Class

A non-static class declared inside another class is an **instance inner
class**.

Example:

``` java
class A {

 class B {

 void show() {
 System.out.println("Show");
 }
 }
}
```

To create its object:

``` java
A a = new A();

A.B b = a.new B();

b.show();
```

An instance inner class is associated with an object of the outer class.

------------------------------------------------------------------------

# 20. Static Members in Instance Inner Class

An instance inner class cannot generally declare static members.

For example, this is not allowed in the traditional Java rule:

``` java
class A {

 class B {

 static void show() {
 System.out.println("Show");
 }
 }
}
```

Similarly, a static variable is not allowed in an ordinary inner class.

### Exception

A `static final` constant is allowed:

``` java
class A {

 class B {

 static final int X = 100;
 }
}
```

### Modern Java note

Recent Java versions have relaxed the old restriction for static members
in inner classes in some cases. For basic/core-Java notes and
traditional syllabus questions, remember the conventional rule above
unless your course specifically covers the newer Java-version behavior.

------------------------------------------------------------------------

# 21. Main Method in Static Nested Class

A `main()` method is static.

Therefore, a static nested class can contain:

``` java
class A {

 static class B {

 public static void main(String[] args) {
 System.out.println("Hello");
 }
 }
}
```

This is valid.

An ordinary instance inner class cannot be used in the same simple way
to declare a traditional static `main()` method under the old
inner-class restriction.

------------------------------------------------------------------------


---

[Previous: Anonymous Inner Class](./04-anonymous-inner-class.md) · [Back to Index](./README.md) · [Next: Adapter Classes](./06-adapter-classes.md)
