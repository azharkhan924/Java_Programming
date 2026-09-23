# Java Generics

## 1. Generics and Type Safety

Before Generics, if we use a normal `ArrayList`, the return type of the `get()` method is `Object`.

Therefore, at the time of retrieval, we have to perform **type casting**.

```java
ArrayList l = new ArrayList();

l.add("A");

String s = (String) l.get(0);
```

### Problem

- `add()` can accept different types of objects.
- `get()` returns `Object`.
- We have to perform type casting at the time of retrieval.
- Due to this, we are missing **type safety**.

---

## 2. Generic Version of ArrayList

From Java 1.5, a generic version of `ArrayList` is available.

Conceptually:

```java
class ArrayList<T> {

    public boolean add(T obj) {
        // ...
    }

    public T get(int index) {
        // ...
    }
}
```

Here, `T` is a **type parameter**.

Based on our runtime/compile-time requirement, `T` will be replaced with the provided type.

### Example: ArrayList of String

```java
ArrayList<String> l = new ArrayList<String>();
```

For this requirement, the compiler conceptually considers the `ArrayList` as:

```java
class ArrayList<String> {

    public boolean add(String obj) {
        // ...
    }

    public String get(int index) {
        // ...
    }
}
```

Therefore:

```java
l.add("A");       // Valid
l.add("B");       // Valid
l.add(10);        // Compile-time error
```

The argument to `add()` is `String`, hence we can add only `String` objects.

The return type of `get()` is `String`:

```java
String s = l.get(0);
```

No type casting is required.

### Hence

Through Generics, we get:

1. **Type safety**
2. **Resolution of type-casting problems**

---

# 3. Generic Classes

Such type-parameterized classes are called **Generic Classes** or **Template Classes**.

In Generics, we associate a **type parameter** with the class.

We can also define our own Generic Classes.

## Example

```java
class Account<T> {

    T obj;

    Account(T obj) {
        this.obj = obj;
    }

    public void display() {
        System.out.println(obj);
    }
}
```

Usage:

```java
Account<String> a1 = new Account<String>("Gold");
Account<Integer> a2 = new Account<Integer>(1000);
```

Here, based on our requirement, `T` can represent different types.

---

# 4. Bounded Types

We can bound the type parameter to a particular range using the `extends` keyword.

Such types are called **Bounded Types**.

## Unbounded Type

```java
class Test<T> {
}
```

As a type parameter, we can pass any type.

There are no restrictions.

Hence, it is called an **Unbounded Type**.

---

## Syntax for Bounded Type

```java
class Test<T extends X> {
}
```

Here, `X` can be either a **class** or an **interface**.

### If X is a Class

As a type parameter, we can pass:

- `X` type
- Child classes of `X`

### If X is an Interface

As a type parameter, we can pass:

- `X` type
- Classes that implement/extend `X`

---

# 5. Multiple Bounds

We can define bounded types even in combination.

The syntax uses `&`.

```java
class Test<T extends Number & Runnable> {
}
```

As a type parameter, we can take anything which:

- is a child class of `Number`
- and implements `Runnable`

### Valid Combinations

```java
<T extends Number & Runnable>
<T extends Comparable & Runnable>
<T extends Number & Comparable & Runnable>
```

### Important Rules

1. **First class, then interfaces.**
2. Only **one class** can be present in the bounds.
3. Multiple interfaces are allowed after the class.
4. If there is no class, interfaces can be written directly.

### Invalid

```java
<T extends Runnable & Number>
```

Error because `Runnable` is an interface and `Number` is a class.

The class must come first.

```java
<T extends Number & Thread>
```

Error because `Number` and `Thread` are both classes.

We cannot extend two classes simultaneously.

### Remember

```java
class A extends B implements C
```

is valid.

But:

```java
class A implements C extends B
```

is invalid.

---

# 6. `extends`, `implements` and `super` in Generic Bounds

We can define bounded types only by using the `extends` keyword.

We cannot use `implements` or `super` in the type-parameter bound syntax.

### Example

```java
class Test<T extends Number> {
}
```

Valid.

```java
class Test<T implements Runnable> {
}
```

Invalid.

For the purpose of implementing an interface, we use `extends`:

```java
class Test<T extends Runnable> {
}
```

Valid.

Similarly:

```java
class Test<T super String> {
}
```

Invalid.

`super` is used with **wildcards**, not with type-parameter declarations.

---

# 7. Type Parameter Naming Convention

As a type parameter, we can take any valid Java identifier.

However, by convention, we generally use:

```java
T
```

Common conventions:

```text
T → Type
E → Element
K → Key
V → Value
N → Number
```

---

# 8. Multiple Type Parameters

Based on our requirement, we can declare any number of type parameters.

All type parameters should be separated by commas.

### Example

```java
class HashMap<K, V> {
}
```

Here:

- `K` represents Key
- `V` represents Value

Example:

```java
HashMap<Integer, String> map = new HashMap<Integer, String>();
```

---

# 9. Generic Methods and Wildcard Character

## 9.1 `ArrayList<String>`

```java
void m1(ArrayList<String> l) {
    // ...
}
```

We can call this method by passing an `ArrayList` of **only String type**.

Inside the method, we can add only `String` objects.

```java
void m1(ArrayList<String> l) {
    l.add("A");       // Valid
    l.add("B");       // Valid
    // l.add(10);     // Compile-time error
}
```

---

# 10. `ArrayList<?>` — Unbounded Wildcard

```java
void m1(ArrayList<?> l) {
    // ...
}
```

We can call this method by passing an `ArrayList` of **any type**.

```java
ArrayList<String> l1 = new ArrayList<String>();
ArrayList<Integer> l2 = new ArrayList<Integer>();

m1(l1);
m1(l2);
```

But inside the method, we cannot add anything to the list except `null`.

```java
void m1(ArrayList<?> l) {

    // l.add(10);       // Error
    // l.add(10.5);     // Error
    // l.add("A");      // Error
    l.add(null);        // Valid
}
```

### Why?

Because we don't know the exact type of the list.

`null` is valid for any reference type.

### Conclusion

Methods using `ArrayList<?>` are best suitable for **read-only operations**.

---

# 11. `? extends X` — Upper Bounded Wildcard

Syntax:

```java
void m1(ArrayList<? extends X> l) {
}
```

Here, `X` can be either a class or an interface.

If `X` is a class, we can pass:

- `ArrayList<X>`
- `ArrayList` of child classes of `X`

If `X` is an interface, we can pass:

- `ArrayList<X>`
- `ArrayList` of classes implementing `X`

### Example

```java
void m1(ArrayList<? extends Number> l) {
}
```

Valid:

```java
ArrayList<Integer> l1 = new ArrayList<Integer>();
ArrayList<Double> l2 = new ArrayList<Double>();
ArrayList<Number> l3 = new ArrayList<Number>();

m1(l1);
m1(l2);
m1(l3);
```

Invalid:

```java
ArrayList<String> l = new ArrayList<String>();

m1(l);       // Compile-time error
```

### Adding Elements

Inside:

```java
ArrayList<? extends Number>
```

we cannot add a specific `Number` value because the exact subtype is unknown.

```java
l.add(null);     // Valid
// l.add(10);    // Error
```

Hence, `? extends X` is also best suitable for **read-only operations**.

---

# 12. `? super X` — Lower Bounded Wildcard

Syntax:

```java
void m1(ArrayList<? super X> l) {
}
```

Here, `X` can be either a class or an interface.

If `X` is a class, we can pass:

- `ArrayList<X>`
- `ArrayList` of its superclasses

Example:

```java
ArrayList<? super String> l = new ArrayList<Object>();
```

This works because `Object` is a superclass of `String`.

Inside the method, we can add:

- `X` type objects
- `null`

Example:

```java
void m1(ArrayList<? super String> l) {

    l.add("A");       // Valid
    l.add("B");       // Valid
    l.add(null);      // Valid
}
```

---

# 13. Wildcard Examples

### Example 1

```java
ArrayList<String> l = new ArrayList<String>();
```

Valid.

---

### Example 2

```java
ArrayList<?> l = new ArrayList<String>();
```

Valid.

---

### Example 3

```java
ArrayList<?> l = new ArrayList<Integer>();
```

Valid.

---

### Example 4

```java
ArrayList<? super String> l = new ArrayList<Object>();
```

Valid.

---

### Example 5

```java
ArrayList<?> l = new ArrayList<?>();
```

Compile-time error.

We cannot create an object using a wildcard as the type argument.

Use:

```java
ArrayList<?> l = new ArrayList<>();
```

or:

```java
ArrayList<?> l = new ArrayList<String>();
```

---

### Example 6

```java
ArrayList<? extends Number> l = new ArrayList<Integer>();
```

Valid.

`Integer` is a child class of `Number`.

---

### Example 7

```java
ArrayList<? extends Number> l = new ArrayList<String>();
```

Compile-time error.

`String` is not a child class of `Number`.

---

# 14. Generic Methods

We can declare type parameters either at:

1. **Class level**
2. **Method level**

---

## 14.1 Declaring Type Parameter at Class Level

```java
class Test<T> {

    // We can use T within this class
    // based on our requirement.
}
```

Here, `T` is available throughout the class.

---

## 14.2 Declaring Type Parameter at Method Level

We have to declare the type parameter **just before the method return type**.

```java
class Test {

    public <T> void m1(T obj) {

        // We can use T anywhere within this method
        // based on our requirement.
    }
}
```

Here, `T` belongs to the method.

---

# 15. Bounded Types at Method Level

We can define bounded types even at method level.

### Unbounded

```java
public <T> void m1(T obj) {
}
```

### Bounded by Class

```java
public <T extends Number> void m1(T obj) {
}
```

### Bounded by Interface

```java
public <T extends Runnable> void m1(T obj) {
}
```

### Class + Interface

```java
public <T extends Number & Runnable> void m1(T obj) {
}
```

### Multiple Interfaces

```java
public <T extends Comparable & Runnable> void m1(T obj) {
}
```

### Class + Multiple Interfaces

```java
public <T extends Number & Comparable & Runnable> void m1(T obj) {
}
```

### Invalid: Interface before Class

```java
public <T extends Runnable & Number> void m1(T obj) {
}
```

Error.

**First class, then interfaces.**

### Invalid: Two Classes

```java
public <T extends Number & Thread> void m1(T obj) {
}
```

Error.

Two classes cannot be extended simultaneously.

---

# 16. Communication with Non-Generic Code

If we send a **generic object to a non-generic area**, it starts behaving like a non-generic object.

Similarly, if we send a **non-generic object to a generic area**, its behavior is determined by the type information available at that location.

## Example

```java
class Demo {

    public static void main(String[] args) {

        ArrayList<String> l = new ArrayList<String>();

        l.add("A");
        l.add("B");

        // l.add(10);       // Compile-time error

        m1(l);

        System.out.println(l);
    }

    public static void m1(ArrayList l) {

        l.add(10);
        l.add(10.5);
        l.add(true);
    }
}
```

Here:

```java
ArrayList<String> l
```

is a **generic area**.

Therefore:

```java
l.add(10);
```

gives a compile-time error.

But:

```java
public static void m1(ArrayList l)
```

is a **non-generic/raw area**.

Therefore, inside this method:

```java
l.add(10);
l.add(10.5);
l.add(true);
```

are allowed.

When the generic object is passed to the non-generic area, its generic type-safety property is not enforced by that raw reference.

---

# 17. Conclusion of Generics

The main purpose of Generics is:

1. **To provide type safety**
2. **To resolve type-casting problems**

Both type checking and type-casting requirements are handled at **compile time**.

Hence:

> The Generic concept is mainly applicable at compile time, not at runtime.

---

# 18. Type Erasure

At compile time, the compiler uses the generic information for type checking.

As a final step, the **generic syntax is removed**.

This process is called **Type Erasure**.

The JVM does not work with the generic syntax in the same way the compiler does.

Conceptually:

```java
ArrayList<String>
ArrayList<Integer>
ArrayList<Double>
```

are represented as:

```java
ArrayList
```

after type erasure.

### Important

This does **not** mean that these declarations are identical at compile time.

At compile time, the compiler uses:

```java
ArrayList<String>
ArrayList<Integer>
ArrayList<Double>
```

as different parameterized types for type checking.

After type erasure, the generic type arguments are removed from the runtime representation.

---

# 19. Why the Following Declarations Become Equivalent After Erasure

```java
ArrayList<String> l = new ArrayList<String>();

ArrayList<Integer> l = new ArrayList<Integer>();

ArrayList<Double> l = new ArrayList<Double>();

ArrayList l = new ArrayList();
```

At compile time, they are not the same because the generic type information is used for type safety.

After type erasure, the type argument is removed:

```java
ArrayList<String>   → ArrayList
ArrayList<Integer>  → ArrayList
ArrayList<Double>   → ArrayList
ArrayList            → ArrayList
```

Therefore, for the JVM/runtime representation, they all use the raw `ArrayList` type.

---

# 20. Compilation Process of Generics

At compile time, conceptually:

```text
Source Code
    ↓
Compile code normally by considering generic syntax
    ↓
Perform type checking
    ↓
Remove generic syntax (Type Erasure)
    ↓
Compile the resultant code
    ↓
Bytecode
```

The important point is that **Generics provide compile-time type safety, while generic type information is erased before runtime.**

---

# Quick Revision

| Concept | Key Point |
|---|---|
| Generics | Provides type safety |
| Main problem solved | Type casting + type safety |
| Generic class | `class Test<T>` |
| Generic method | `public <T> void m1(T obj)` |
| Unbounded type | `T` |
| Bounded type | `T extends X` |
| Multiple bounds | `T extends Class & Interface & Interface` |
| Class position | Always first |
| `?` | Unbounded wildcard |
| `? extends X` | Upper bounded wildcard |
| `? super X` | Lower bounded wildcard |
| `?` add operation | Only `null` can be added |
| `? extends X` add operation | Only `null` can safely be added |
| `? super X` add operation | `X` and `null` can be added |
| Generic type checking | Compile time |
| Type erasure | Generic type information removed before runtime |
| Runtime representation | Generic arguments are not retained in the same form |

---

## Most Important Rules to Remember

```text
1. Generics → Type Safety + No Type Casting
2. Generic class → class Test<T>
3. Generic method → public <T> void m1(T obj)
4. Bounded type → T extends X
5. No implements/super in type-parameter bounds
6. Multiple bounds → Class first, Interfaces next
7. Only one class can be present in bounds
8. ? → Can accept any type, but add only null
9. ? extends X → Read/produce values, add only null
10. ? super X → Can add X and null
11. Generics work mainly at compile time
12. Type erasure removes generic type arguments before runtime
```
