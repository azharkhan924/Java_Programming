# Anonymous Inner Class

# 18. Anonymous Inner Class

An **Anonymous Inner Class** is an inner class that has **no explicit class name**.

Common forms:

1. Extending a class
2. Implementing an interface
3. Passing an anonymous implementation directly as a method argument

---

# 19. Anonymous Inner Class — Extending a Class

```java
class A {

 void show() {
 System.out.println("A");
 }
}

class Demo {

 public static void main(String[] args) {

 A a = new A() {

 void show() {
 System.out.println("Anonymous Inner Class");
 }
 };

 a.show();
 }
}
```

### What happens?

```java
A a = new A() {
 ...
};
```

1. `A` is the superclass.
2. `new A() { ... }` creates an anonymous subclass of `A`.
3. The anonymous subclass has no explicit name.
4. Its object is created.
5. Reference variable `a` of type `A` holds that object.

```text
Superclass A reference
 ↓
Anonymous subclass object
```

---

# 20. Anonymous Inner Class — Implementing an Interface

```java
interface A {

 void show();
}

class Demo {

 public static void main(String[] args) {

 A a = new A() {

 public void show() {
 System.out.println("Anonymous implementation");
 }
 };

 a.show();
 }
}
```

Here the anonymous class implements interface `A`.

---

# 21. Anonymous Inner Class as Method Argument

```java
interface A {
 void show();
}

class Demo {

 static void display(A a) {
 a.show();
 }

 public static void main(String[] args) {

 display(new A() {

 public void show() {
 System.out.println("Anonymous Class");
 }
 });
 }
}
```

This is especially useful in **event listeners** and GUI programming.

---

# 22. Normal Subclass — Superclass Reference Holding Subclass Object

```java
class A {

 void show() {
 System.out.println("A");
 }
}

class B extends A {

 void show() {
 System.out.println("B");
 }
}

class Demo {

 public static void main(String[] args) {

 A a = new B();

 a.show();
 }
}
```

### Step-by-step

**Step 1:** Class `A` is created.

**Step 2:** Class `B` is created:

```java
class B extends A
```

So `B` is a subclass of `A`.

**Step 3:** A `B` object is created:

```java
new B()
```

**Step 4:** The `B` object is held by a superclass reference:

```java
A a = new B();
```

So:

```text
Superclass A reference
 ↓
 Subclass B object
```

**Step 5:** `a.show()` is called.

Since `show()` is overridden, runtime polymorphism executes `B`'s `show()`.

### Output

```text
B
```

---

# 23. Abstract Class Reference

An abstract class cannot be instantiated directly:

```java
abstract class A {
}
```

This is invalid:

```java
A a = new A(); // Error
```

But a reference variable can be created:

```java
A a;
```

And it can hold an object of a concrete subclass:

```java
abstract class A {

 void show() {
 System.out.println("A");
 }
}

class B extends A {
}

class Demo {

 public static void main(String[] args) {

 A a = new B();

 a.show();
 }
}
```

### Output

```text
A
```

### Important

```text
Abstract class object directly → 
Abstract class reference → 
Reference holding subclass → 
```

---

# 24. Anonymous Class vs Named Subclass

### Named subclass

```java
class B extends A {
}
```

### Anonymous subclass

```java
A a = new A() {
 // body
};
```

| Named Subclass | Anonymous Inner Class |
|---|---|
| Has a class name | No explicit class name |
| Can be reused | Usually one-time use |
| Declared separately | Declared at object creation |
| `class B extends A` | `new A() { ... }` |

---


# 2. Reference Variable vs Object vs Constructor

This is an important concept.

Consider:

``` java
A a1 = new B();
```

If `B` extends `A`:

``` java
class A {
}

class B extends A {
}
```

then:

``` java
A a1 = new B();
```

has three different things to understand:

### 1. `A`

`A` is the **reference type**.

So `a1` can directly access members available through the `A` reference.

### 2. `a1`

`a1` is a **reference variable**.

It stores a reference to the object.

### 3. `new B()`

`new B()` creates an **object of class `B`**.

The `B` constructor is called.

Conceptually:

``` text
Reference variable
 |
 v
 a1
 |
 v
 +---------+
 | B object |
 +---------+
```

The object is a `B` object, not an `A` object.

`A` is only the reference type.

### Memory concept

When:

``` java
A a1 = new B();
```

executes:

1. `new B()` requests memory for a new `B` object.
2. The `B` constructor is executed.
3. The resulting object exists in heap memory.
4. `a1` stores a reference to that object.
5. Because the reference type is `A`, only members accessible through
 `A` can be directly accessed using `a1`.

Example:

``` java
class A {
 void showA() {
 System.out.println("A");
 }
}

class B extends A {
 void showB() {
 System.out.println("B");
 }
}
```

``` java
A a1 = new B();

a1.showA(); // valid
// a1.showB(); // compile-time error
```

The object is still a `B` object.

This is commonly called **upcasting**.

------------------------------------------------------------------------

# 3. Anonymous Inner Class Implementing an Interface

An interface cannot normally be instantiated directly:

``` java
// Inter1 i = new Inter1(); // invalid
```

But an anonymous class can implement the interface:

``` java
interface Inter1 {
 void show();
}

class Demo {
 public static void main(String[] args) {

 Inter1 in = new Inter1() {
 @Override
 public void show() {
 System.out.println("ABC");
 }
 };

 in.show();
 }
}
```

### What is happening here?

``` java
Inter1 in = new Inter1() {
 public void show() {
 System.out.println("ABC");
 }
};
```

The important point is:

- `Inter1` → reference type
- `in` → reference variable
- `new Inter1() { ... }` → creates an **anonymous class object**
- The anonymous class **implements `Inter1`**
- The object is stored/referenced through the `Inter1` reference

Conceptually:

``` text
Inter1 in
 |
 v
+---------------------------+
| Anonymous class object |
| |
| show() { |
| System.out.println(...); |
| } |
+---------------------------+
```

### Does an object of the interface get created?

**No.**

An interface itself is not instantiated.

The object belongs to the **anonymous class**.

The interface reference simply refers to that object.

This is similar to:

``` java
A a = new B();
```

Here:

``` java
Inter1 in = new Inter1() { ... };
```

means:

> Create an object of an anonymous class that implements `Inter1`, and
> store its reference in an `Inter1` reference variable.

------------------------------------------------------------------------

# 4. Anonymous Inner Class Inside Method Argument

Sometimes we don't even need a separate reference variable.

For example:

``` java
addWindowListener(new MyAdapter() {
 @Override
 public void windowClosing(WindowEvent e) {
 System.exit(0);
 }
});
```

Here the anonymous object is directly passed as an argument.

------------------------------------------------------------------------


---

[Previous: Local Inner Class](./03-local-inner-class.md) · [Back to Index](./README.md) · [Next: Static Nested Class](./05-static-nested-class.md)
