# Nested Classes Overview and Hierarchy

## 1. Nested Class

A **class declared inside another class** is called a **Nested Class**.

```java
class Outer {
 class Inner {
 // inner class
 }
}
```

### Types

```text
 Nested Class
 / \
 Non-static Static
 |
 ┌────────┼────────┐
 │ │ │
 Instance Local Anonymous
 Inner Inner Inner
 Class Class Class
```

### Non-static Nested Classes
1. Instance Inner Class
2. Local Inner Class
3. Anonymous Inner Class

### Static Nested Class
A nested class declared using `static`.

---


# 7. Access Modifiers

## Top-level Outer Class

Common modifiers:

```text
default
public
final
abstract
strictfp
```

`private` and `protected` are not allowed for a top-level class.

## Inner Class

An inner/member class can additionally use:

```text
private
protected
static
```

Example:

```java
class A {

 private class B {
 }

 protected class C {
 }

 static class D {
 }
}
```

---


# 17. Class File Naming

The compiler generates separate `.class` files for nested/local/anonymous classes.

For example:

```java
class A {

 void show() {

 class B {
 }
 }
}
```

A local class may get a compiler-generated name such as:

```text
A$1B.class
```

Anonymous classes commonly get names such as:

```text
A$1.class
A$2.class
```

These are compiler-generated names and should not be treated as source-level names.

---


---

[Back to Inner Classes Index](./README.md) · [Next: Instance Inner Class](./02-instance-inner-class.md)
