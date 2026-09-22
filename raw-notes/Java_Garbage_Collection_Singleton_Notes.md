# Java Notes — Garbage Collection, `equals()`, Finalization & Singleton

> **Topic:** Garbage Object / Garbage Collection, `==` vs `equals()`, `finalize()`, Reflection and Singleton Pattern

---

## 1. Garbage Object

### Definition

An object which is **unreferenced / unreachable** from any active reference in the application is called a **garbage object**.

Such an object becomes **eligible for Garbage Collection (GC)**.

> **Important:** Eligible for GC does **not** mean that the object is destroyed immediately. GC is controlled by the JVM.

---

## 2. Five Ways to Make an Object Eligible for GC

### 1. Null Assignment

When the reference variable is assigned `null`, the object previously referred to by that variable may become unreachable.

```java
class Demo {
    public static void main(String[] args) {

        Demo d1 = new Demo();

        d1 = null;       // object becomes eligible for GC

        // d1.show();    // NullPointerException
    }

    void show() {
        System.out.println("Hello");
    }
}
```

### Working

```text
Before:
d1 ───────► [Object]

After:
d1 ───────► null

             [Object]
          (no active reference)
                 ↓
             GC eligible
```

If we try:

```java
d1.show();
```

the reference is `null`, so Java throws:

```text
NullPointerException
```

---

### 2. Reassigning the Reference Variable

If one reference variable is reassigned to another object, the previously referenced object may become unreachable.

```java
class Demo {
    public static void main(String[] args) {

        Demo d1 = new Demo();
        Demo d2 = new Demo();

        d1 = d2;
    }
}
```

Initially:

```text
d1 ───────► [Object 1]

d2 ───────► [Object 2]
```

After:

```java
d1 = d2;
```

```text
d1 ───────► [Object 2] ◄────── d2

[Object 1]
    ↓
GC eligible
```

### Important Point

> **More than one reference variable can point to a single object.**

---

### 3. Local Object

An object created inside a method can become eligible for GC when the method finishes, **provided no reference to that object escapes the method**.

```java
class Demo {

    void show() {
        Demo d = new Demo();   // local object
        System.out.println("Inside method");
    }

    public static void main(String[] args) {
        Demo obj = new Demo();
        obj.show();
    }
}
```

During method execution:

```text
show()
  |
  └── d ─────► [Object]
```

After `show()` completes:

```text
d  →  local reference disappears
          ↓
     [Object]
          ↓
      GC eligible
```

---

### 4. Anonymous Object

An object created without storing its reference in a variable is called an **anonymous object**.

```java
new Demo();
```

It is commonly used when the object is needed only once.

Example:

```java
class Demo {

    void show() {
        System.out.println("Hello");
    }

    public static void main(String[] args) {

        new Demo().show();
    }
}
```

Here:

```java
new Demo()
```

creates an object, but no reference variable stores it.

After the expression has been evaluated and if no other reference exists, the object becomes **eligible for GC**.

> Anonymous object ≠ necessarily "destroyed immediately". It simply has no stored reference after the expression unless the object escapes through some other mechanism.

---

### 5. Island of Isolation

An **island of isolation** is a group of objects that **reference one another**, but the group is **not reachable from any active reference/root**.

Such objects can still become eligible for GC.

### Example

```java
class A {

    A i;

    public static void main(String[] args) {

        A a1 = new A();
        A a2 = new A();

        a1.i = a2;
        a2.i = a1;

        a1 = null;
        a2 = null;
    }
}
```

### Diagram

Before `null`:

```text
        a1
         |
         v
      +-------+
      | Obj 1 |
      | i  ──────────┐
      +-------+      |
                     v
                  +-------+
                  | Obj 2 |
                  | i  ──────────┐
                  +-------+      |
                     ^           |
                     └───────────┘
```

After:

```java
a1 = null;
a2 = null;
```

```text
a1 → null

a2 → null

      [Obj 1] ─────► [Obj 2]
         ▲              │
         └──────────────┘

No active reference reaches either object.
        ↓
Both objects are GC eligible.
```

### Key Point

> Objects can reference each other and still be garbage if the entire group is unreachable from active references.

---

# 3. `==` Operator vs `equals()`

## `==` with Objects

For reference types, `==` checks **reference identity** — whether two references refer to the **same object**.

```java
String s1 = new String("aaa");
String s2 = new String("aaa");

System.out.println(s1 == s2);
```

Output:

```text
false
```

Because:

```text
s1 ─────► [String "aaa"]  ← different object
s2 ─────► [String "aaa"]  ← different object
```

Even though the contents are the same, the objects are different.

---

## Why `String` and `StringBuffer` with `==` Can Give Compile-Time Error

```java
String s1 = new String("aaa");
StringBuffer s2 = new StringBuffer("aaa");

System.out.println(s1 == s2);
```

This is a **compile-time error** because `String` and `StringBuffer` are unrelated final classes, so the compiler knows the references cannot refer to the same object.

### Correction to Remember

`==` does **not** mean "only parent-child relationship".

For reference types, Java permits `==` when the reference types are compatible by the relevant casting-conversion rules.

Example:

```java
Object o1 = new String("aaa");
Object o2 = new String("aaa");

System.out.println(o1 == o2);   // false
```

---

# 4. Internal Working of `Object.equals()`

The `equals()` method is defined in the `Object` class.

Conceptually, its implementation is:

```java
public boolean equals(Object obj) {
    return this == obj;
}
```

Therefore, the default `Object.equals()` checks whether:

```text
this
  |
  └────► same object ◄──── obj
```

So:

```java
Demo d1 = new Demo();
Demo d2 = new Demo();

System.out.println(d1.equals(d2));  // false
System.out.println(d1.equals(d1));  // true
```

### Important

Classes such as `String` **override** `equals()` to compare their contents.

```java
String s1 = new String("aaa");
String s2 = new String("aaa");

System.out.println(s1 == s2);       // false
System.out.println(s1.equals(s2));  // true
```

---

# 5. `equals()` Method — Custom Example

```java
class A {

    int x;

    A(int x) {
        this.x = x;
    }

    @Override
    public boolean equals(Object obj) {

        if (this == obj)
            return true;

        if (!(obj instanceof A))
            return false;

        A a = (A) obj;

        return this.x == a.x;
    }
}
```

Here, `equals()` is overridden to compare object data instead of only reference identity.

> Whenever `equals()` is overridden, `hashCode()` should also be overridden consistently.

---

# 6. Garbage Collection Request

Java automatically performs garbage collection when the JVM determines that it is useful.

We can request GC using:

```java
System.gc();
```

or:

```java
Runtime.getRuntime().gc();
```

### Important

These calls are only **requests / suggestions** to the JVM.

They do **not guarantee** that garbage collection will happen immediately.

---

# 7. `finalize()` — Historical GC Concept

Historically, Java provided:

```java
protected void finalize() throws Throwable
```

A class could override `finalize()` for cleanup before an object was reclaimed.

Example from the traditional concept:

```java
class Demo {

    @Override
    protected void finalize() throws Throwable {
        System.out.println("finalize() called");
    }
}
```

### Traditional Flow

```text
Object becomes unreachable
          ↓
JVM may arrange finalization
          ↓
finalize() may run
          ↓
Object may later be reclaimed
```

### Very Important Modern Java Note

`finalize()` is **deprecated for removal** in modern Java. It should **not** be used for new resource-management code.

Prefer:

- `try-with-resources`
- `AutoCloseable`
- explicit `close()`
- `Cleaner` where appropriate

---

# 8. Reflection — Counting Methods

The notes also use Reflection to inspect methods of a class.

```java
import java.lang.reflect.*;

class Demo {

    public static void main(String[] args)
            throws ClassNotFoundException {

        Class c = Class.forName("java.lang.Object");

        Method m[] = c.getDeclaredMethods();

        for (Method m1 : m) {
            System.out.println(m1);
        }
    }
}
```

### Count the methods

```java
import java.lang.reflect.*;

class Demo {

    public static void main(String[] args) {

        Object o = new String();

        Class c = o.getClass();

        Method m[] = c.getDeclaredMethods();

        int count = 0;

        for (Method m1 : m) {
            count++;
        }

        System.out.println(count);
    }
}
```

### Important Reflection Methods

```java
o.getClass();
```

Returns the runtime class of the object.

```java
c.getDeclaredMethods();
```

Returns methods declared directly by that class.

---

# 9. Singleton Class

## Definition

A **Singleton** is a design pattern used when a class should allow **only one object/instance** to be created and provide a common way to access that instance.

### Main Purpose

> Restrict a class to a single instance.

---

## How to Create a Singleton

### Step 1 — Make the constructor private

```java
private A() {
}
```

This prevents outside classes from directly creating objects using:

```java
new A();
```

Example:

```java
class A {

    private A() {
    }
}
```

Trying:

```java
A a = new A();
```

causes an access error because the constructor is private.

---

## Step 2 — Create a static reference of the same class

```java
private static A obj;
```

---

## Step 3 — Create a static factory method

```java
public static A getA() {

    if (obj == null) {
        obj = new A();
    }

    return obj;
}
```

The method returns the same object every time.

---

# 10. Complete Singleton Example

```java
class A {

    private static A obj;

    private A() {
    }

    public static A getA() {

        if (obj == null) {
            obj = new A();
        }

        return obj;
    }
}

class Demo {

    public static void main(String[] args) {

        A a1 = A.getA();
        A a2 = A.getA();

        System.out.println(a1 == a2);
    }
}
```

Output:

```text
true
```

### Working

First call:

```java
A a1 = A.getA();
```

Since `obj == null`:

```text
obj ─────► [A Object]
```

Second call:

```java
A a2 = A.getA();
```

The existing object is returned:

```text
          ┌──────────────► [A Object]
a1 ───────┤
a2 ───────┘
```

Therefore:

```java
a1 == a2
```

is:

```text
true
```

---

# 11. Factory Method

A **factory method** is a method used to create/provide objects instead of allowing the caller to directly invoke the constructor.

In the Singleton example:

```java
public static A getA()
```

acts as the factory/access method because it controls creation and returns the instance.

### Singleton's factory method

```java
public static A getA() {

    if (obj == null)
        obj = new A();

    return obj;
}
```

### Key Point

Every call:

```java
A.getA();
```

returns the **same Singleton instance** in this implementation.

---

# 12. Quick Revision

| Topic | Key Point |
|---|---|
| Garbage Object | Unreachable/unreferenced object |
| `null` assignment | Reference is removed from object |
| Reassignment | Old object may become unreachable |
| Local object | May become unreachable after method ends |
| Anonymous object | No stored reference after expression, if it doesn't escape |
| Island of Isolation | Mutually referencing but unreachable objects |
| `==` | Reference identity for objects |
| `Object.equals()` | Default behavior is effectively `this == obj` |
| `String.equals()` | Compares String contents |
| `System.gc()` | Request/suggestion, not a guarantee |
| `finalize()` | Legacy mechanism; deprecated for removal |
| Reflection | Runtime inspection of classes/methods |
| Singleton | Restricts class to one instance |
| Private constructor | Prevents direct object creation outside the class |
| Static factory method | Controls/provides object creation/access |

---

## Exam-Friendly Definitions

### Garbage Object
> An object that is no longer reachable from any active reference/root and therefore becomes eligible for garbage collection.

### Island of Isolation
> A group of objects that reference each other but are not reachable from any active reference/root.

### Singleton
> A design pattern that restricts a class to a single instance and provides a controlled way to access that instance.

### Anonymous Object
> An object created without storing its reference in a named reference variable.

### Factory Method
> A method that provides/creates objects while hiding or controlling the object-creation process.

### `==`
> For object references, `==` checks whether two references refer to the same object.

### `equals()`
> A method used to compare objects for logical equality; the default implementation in `Object` compares reference identity.

---

## Memory Diagram Shortcut

```text
REFERENCE
    |
    v
+---------+
| OBJECT  |
+---------+

Reference removed
       ↓
+---------+
| OBJECT  |
+---------+
    ↓
GC eligible
```

### Island of Isolation

```text
[Obj A] ─────► [Obj B]
   ▲               |
   └───────────────┘

No active reference
        ↓
   GC eligible
```

### Singleton

```text
a1 ───────┐
          ├──────► [ ONE A OBJECT ]
a2 ───────┘
```


---

# 3. When Does Garbage Collection Actually Happen?

Making an object **unreachable / unreferenced** only makes it **eligible for GC**. It does **not** mean that the object is immediately destroyed.

The JVM decides when garbage collection should actually run.

### Important Flow

```text
Object becomes unreachable
        ↓
Object becomes eligible for GC
        ↓
JVM may run Garbage Collector
        ↓
GC reclaims the object's memory
```

There is **no guarantee about the exact time** at which GC will run.

Also, calling `System.gc()` or `Runtime.getRuntime().gc()` is only a **request/suggestion** to the JVM. The JVM is not required to perform a collection because of that call. Oracle's documentation explicitly states that there is no guarantee that any particular objects will be reclaimed or that collection will complete at a particular time. 

### Requesting GC

There are two commonly used forms:

```java
System.gc();
```

and

```java
Runtime.getRuntime().gc();
```

`System.gc()` is the conventional and convenient way to request GC. It is effectively equivalent to:

```java
Runtime.getRuntime().gc();
```

So both ultimately request the JVM to perform garbage collection. 

> **Correction to the classroom note:** Do not rely on a statement such as "JVM accepts the request 90% of the time." There is no such guaranteed percentage in the Java specification/API.

---

# 4. Finalization

### Meaning

**Finalization** was a mechanism in which the JVM could invoke an object's `finalize()` method after the object became unreachable and before the object was reclaimed.

Historically, it was used for cleanup activities associated with an object.

However, `Object.finalize()` is **deprecated for removal** in modern Java. It is not recommended for new code. Safer alternatives include `AutoCloseable` + try-with-resources, `Cleaner`, or explicit cleanup methods. 

### `Object.finalize()` Signature

Historically, `Object` defines:

```java
protected void finalize() throws Throwable
```

The body of `Object.finalize()` performs no special action; it simply returns normally.

Modern Java marks this method:

```text
@Deprecated(since = "9", forRemoval = true)
```

Oracle recommends avoiding finalization. 

---

## 4.1 Overriding `finalize()`

Example:

```java
class Demo {

    @Override
    protected void finalize() throws Throwable {
        System.out.println("Finalize method called");
    }

    public static void main(String[] args) {

        Demo d = new Demo();

        d = null;

        System.gc();
    }
}
```

### Important

`System.gc()` does **not** guarantee that `finalize()` will immediately execute.

If finalization is enabled in the JVM, the JVM may invoke the overridden `finalize()` method after determining that the object is unreachable. The timing is not guaranteed. In JVMs where finalization has been disabled or removed, it will not be called. 

---

## 4.2 Which `finalize()` Method Is Called?

Suppose a class overrides `finalize()`:

```java
class A {

    @Override
    protected void finalize() throws Throwable {
        System.out.println("A finalize()");
    }
}
```

When an eligible object of class `A` is finalized, the overridden method is the relevant implementation.

If a class does **not** override `finalize()`, historically the inherited `Object.finalize()` would be used, whose body performs no special action.

> Modern Java note: finalization is deprecated for removal, so this mechanism should not be used in new programs.

---

## 4.3 `finalize()` Is Not the Same as Normal Explicit Cleanup

### If `finalize()` is called manually

```java
Demo d = new Demo();

d.finalize();
```

This is simply an ordinary method call.

It does **not** mean that the object is destroyed.

It can be called more than once manually, just like an ordinary method, subject to normal access rules.

If the method throws an exception during a manual call, normal Java exception handling rules apply.

### If the JVM invokes finalization

The JVM's finalization mechanism has special semantics. Historically, an uncaught exception thrown by `finalize()` was ignored and finalization for that invocation terminated. The JVM did not propagate that exception as an ordinary uncaught exception from the finalizer thread. 

### Quick Comparison

| Manual `finalize()` call | JVM finalization |
|---|---|
| Ordinary method invocation | JVM-managed finalization mechanism |
| Does not destroy the object | Associated with an object becoming unreachable |
| Can be invoked repeatedly by code | Historically invoked at most once automatically for an object |
| Normal exception rules apply | Uncaught exception from finalizer is ignored |
| Not a GC operation | Historically part of object reclamation process |

> **Modern recommendation:** Do not use `finalize()` for resource cleanup.

---

# 5. `Runtime` Class and Garbage Collection

The `Runtime` class represents the runtime environment associated with the current Java application.

To obtain the runtime object:

```java
Runtime r = Runtime.getRuntime();
```

`getRuntime()` is a **static factory-style method** that returns the runtime object associated with the current application.

Then:

```java
r.gc();
```

requests garbage collection.

Therefore:

```java
Runtime.getRuntime().gc();
```

is valid.

---

## 5.1 Why `new Runtime()` Does Not Work

You cannot normally create a `Runtime` object directly:

```java
Runtime r = new Runtime();   // compilation error
```

The constructor is not publicly accessible.

Instead, Java provides:

```java
Runtime.getRuntime();
```

This gives access to the runtime object associated with the current application.

### Singleton-style Understanding

For learning purposes, `Runtime` is commonly described as a **singleton-style class** because the application accesses its associated runtime object through `getRuntime()` rather than creating a new `Runtime` with `new`.

---

# 6. Relationship Between `System.gc()` and `Runtime.getRuntime().gc()`

Conceptually:

```java
System.gc();
```

is effectively equivalent to:

```java
Runtime.getRuntime().gc();
```

The Java API documentation explicitly specifies this equivalence. 

So:

```java
System.gc();
```

is the simpler and conventional form.

```java
Runtime.getRuntime().gc();
```

directly accesses the `Runtime` object's `gc()` method.

---

# 7. Large Object Creation and `OutOfMemoryError`

If a program continuously allocates objects/arrays and the JVM cannot provide enough memory, it may throw:

```text
java.lang.OutOfMemoryError
```

Example:

```java
class Demo {

    public static void main(String[] args) {

        int[][] arr = new int[100000][100000];
    }
}
```

Depending on the environment, this can result in:

```text
java.lang.OutOfMemoryError: Java heap space
```

### Important Correction

`OutOfMemoryError` and `StackOverflowError` are subclasses of `Error`, not `Exception`.

They **can technically be caught** because `Error` is a subclass of `Throwable`:

```java
try {
    // code
} catch (OutOfMemoryError e) {
    // technically possible
}
```

However, catching such serious VM resource errors does **not** mean the application is necessarily safe or recoverable.

---

## 7.1 StackOverflowError

A common cause is infinite or excessively deep recursion:

```java
class Demo {

    static void test() {
        test();
    }

    public static void main(String[] args) {
        test();
    }
}
```

Eventually:

```text
java.lang.StackOverflowError
```

---

# 8. Reflection API

Java Reflection allows a program to inspect classes, methods, fields, constructors, etc. at runtime.

Package:

```java
java.lang.reflect
```

---

## 8.1 Printing All Declared Methods Using `Class`

```java
import java.lang.reflect.*;

class Demo {

    public void m1() {
    }

    private void m2() {
    }

    protected void m3() {
    }

    public static void main(String[] args) throws Exception {

        Class<?> c = Class.forName("java.lang.Object");

        Method[] m = c.getDeclaredMethods();

        for (Method m1 : m) {
            System.out.println(m1);
        }
    }
}
```

### Important Method

```java
c.getDeclaredMethods();
```

returns the methods declared by that class.

---

## 8.2 Using an Object's `getClass()`

The same idea can be performed through an object:

```java
import java.lang.reflect.*;

class Demo {

    public static void main(String[] args) {

        Object o = new String();

        Class<?> c = o.getClass();

        Method[] m = c.getDeclaredMethods();

        for (Method m1 : m) {
            System.out.println(m1);
        }
    }
}
```

Here:

```java
o.getClass()
```

returns the runtime class of the object.

Then:

```java
c.getDeclaredMethods()
```

returns the methods declared by that class.

### Two Approaches

```text
Class object directly
        ↓
Class.forName(...)
        ↓
getDeclaredMethods()
```

or

```text
Object
  ↓
getClass()
  ↓
getDeclaredMethods()
```

---

# 9. Cloning

### Definition

**Cloning** means creating another object with the contents of an existing object while keeping the clone as a separate object.

Simple idea:

```text
Original Object                 Clone Object

+-----------+                   +-----------+
| x = 10    |    cloning →      | x = 10    |
| y = 20    |                   | y = 20    |
+-----------+                   +-----------+
      ↑                               ↑
  separate memory                 separate memory
```

So:

> **Same field contents, but a different object/reference.**

---

# 10. `Cloneable` Interface

The class whose object is to be cloned generally needs to implement:

```java
Cloneable
```

Example:

```java
class A implements Cloneable {

    int x;
    int y;
}
```

`Cloneable` is a **marker interface**.

### Marker Interface

An interface that contains **no methods** and is used to indicate/mark a capability or property is called a marker interface.

`Cloneable` is present in:

```java
java.lang
```

---

## 10.1 What Happens Without `Cloneable`?

If `Object.clone()` is invoked on an object whose class does not implement `Cloneable`, Java throws:

```text
CloneNotSupportedException
```

Oracle's API specifies this behavior. 

---

# 11. `clone()` Method

The `clone()` method is defined in:

```java
java.lang.Object
```

Historically its signature is:

```java
protected native Object clone() throws CloneNotSupportedException;
```

Important points:

- `clone()` belongs to `Object`.
- It is `protected`.
- Its return type in `Object` is `Object`.
- It can throw `CloneNotSupportedException`.
- `Cloneable` itself does **not** contain a `clone()` method.

A class commonly overrides `clone()` and exposes it as `public`:

```java
class A implements Cloneable {

    int x;
    int y;

    @Override
    public A clone() throws CloneNotSupportedException {
        return (A) super.clone();
    }
}
```

Usage:

```java
class Demo {

    public static void main(String[] args)
            throws CloneNotSupportedException {

        A a1 = new A();

        a1.x = 10;
        a1.y = 20;

        A a2 = a1.clone();

        System.out.println(a1.x + " " + a1.y);
        System.out.println(a2.x + " " + a2.y);
    }
}
```

### Result

```text
a1 ─────► [x=10, y=20]     // Object 1

a2 ─────► [x=10, y=20]     // Object 2
```

The objects contain the same field values, but they are separate objects.

---

## 11.1 Important: `==` After Cloning

```java
System.out.println(a1 == a2);
```

Output:

```text
false
```

because `a1` and `a2` refer to different objects.

But:

```java
System.out.println(a1.x == a2.x);
```

can be:

```text
true
```

because their field values are the same.

---

## 11.2 Shallow Copy

`Object.clone()` performs a **shallow copy**.

For primitive fields, the values are copied.

For reference fields, the references are copied, so both objects can refer to the same referenced object.

```text
Original                 Clone

A1                       A2
 |                        |
 | x=10                   | x=10
 |                        |
 └──────► B ◄─────────────┘
```

The `A` objects are different, but their reference fields may point to the same `B` object.

Oracle's documentation describes `Object.clone()` as a shallow, field-by-field copy. 

---

# 12. Quick Revision

```text
Unreachable Object
       ↓
GC Eligible
       ↓
No guarantee of immediate collection
       ↓
JVM may run GC automatically
       ↓
System.gc() / Runtime.getRuntime().gc()
       ↓
Only a request/suggestion
```

### Finalization

```text
finalize()
    ↓
Old JVM cleanup mechanism
    ↓
Deprecated for removal
    ↓
Do not use for new code
```

### Runtime

```text
Runtime.getRuntime()
        ↓
Runtime object
        ↓
gc()
```

### Reflection

```text
Object
  ↓
getClass()
  ↓
Class
  ↓
getDeclaredMethods()
```

### Cloning

```text
Original Object
      ↓ clone()
New Object
      ↓
Separate object
      ↓
Shallow copy by Object.clone()
```

---

## ⚠️ Exam/Interview Corrections to Remember

1. **GC eligibility ≠ immediate destruction.**
2. `System.gc()` is a **request/suggestion**, not a command.
3. There is **no guaranteed percentage** that the JVM will accept a GC request.
4. `System.gc()` is effectively equivalent to `Runtime.getRuntime().gc()`.
5. `finalize()` is **deprecated for removal** in modern Java.
6. Manually calling `finalize()` is just a normal method call; it does not destroy the object.
7. `OutOfMemoryError` and `StackOverflowError` are `Error`s, not `Exception`s.
8. `Cloneable` is a marker interface and does **not** declare `clone()`.
9. `Object.clone()` performs a **shallow copy**.
10. `CloneNotSupportedException` occurs when `Object.clone()` is used for an object whose class does not implement `Cloneable`.
