# Object Class & Its Methods

---

## 1. What is the Object Class?

`Object` Java ki **root / top-most superclass** hai.

- **Package:** `java.lang`
- Har Java class directly ya indirectly `Object` class ko inherit karti hai
- Isliye `Object` ke methods **har Java object** ko available hote hain

```java
class A {
}
```

Conceptually:

```text
Object
  ↑
  A
```

> Agar hum explicitly `extends` nahi likhte, tab bhi class indirectly `Object` ko inherit karti hai.

---

## 2. Object Class ke 12 Methods

| # | Method | Modifier | Overridable? |
|---|--------|----------|-------------|
| 1 | `getClass()` | `public final native` | No |
| 2 | `hashCode()` | `public native` | Yes |
| 3 | `equals(Object obj)` | `public` | Yes |
| 4 | `clone()` | `protected native` | Yes |
| 5 | `toString()` | `public` | Yes |
| 6 | `notify()` | `public final native` | No |
| 7 | `notifyAll()` | `public final native` | No |
| 8 | `wait(long timeout)` | `public final` | No |
| 9 | `wait(long timeout, int nanos)` | `public final` | No |
| 10 | `wait()` | `public final` | No |
| 11 | `finalize()` | `protected` |  (deprecated) |
| 12 | `registerNatives()` | `private static native` | No |

### Modifier Summary

| Category | Count |
|----------|-------|
| `public` methods | 9 |
| `protected` methods | 2 |
| `private` methods | 1 |
| `final` methods | 6 |
| Overridable methods | 5 |

### The 5 Overridable Methods

```text
hashCode()    equals()    clone()    toString()    finalize()
```

> Note: `finalize()` is **deprecated for removal** since Java 9. Do not use in new code.

---

## 3. `getClass()` Method

```java
public final native Class<?> getClass()
```

Runtime par object ki **actual class** ka `Class` object return karta hai.

```java
class A { }

A a1 = new A();
System.out.println(a1.getClass());          // class A
System.out.println(a1.getClass().getName()); // A
```

### Method Chaining

```text
a1 → getClass() → Class object → getName() → "A"
```

> `getClass()` **final** hai — override nahi kar sakte.

---

## 4. Every Class Extends Object

### Rule 1 — Universal Inheritance

```text
Every class directly/indirectly extends Object.
```

### Rule 2 — Object Reference Holds Any Object

```java
Object o = new Employee();    // valid
Object o = new String("Hi");  // valid
Object o = new int[]{1,2};    // valid
```

### Rule 3 — Reference Type Controls Compile-Time

```java
Object o = new Employee();
// o.id;          // Object class me id nahi hai
// o.getName();   // Object class me getName() nahi hai
```

> Compiler reference type (`Object`) dekhta hai, actual object type nahi.

---

## Interview Quick Questions

| Question | Answer |
|----------|--------|
| Object class kaunse package me hai? | `java.lang` |
| Object class ke kitne methods hain? | 12 commonly discussed |
| Kitne methods override ho sakte hain? | 5 |
| `getClass()` override ho sakta hai? | No — `final` hai |
| `wait()` methods kitne hain? | 3 overloaded versions |
| `finalize()` use karna chahiye? | No — deprecated for removal |
| Har class Object ko extend karti hai? | Yes — directly or indirectly |

---

[Back to OOP Index](./README.md) · [Next: toString() & hashCode()](./02-tostring-and-hashcode.md)
