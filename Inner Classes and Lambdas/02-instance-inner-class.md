# Instance Inner Class

# 2. Instance Inner Class

A class declared inside another class **without `static`** is called an **Instance Inner Class**.

```java
class Outer {

 class Inner {

 void show() {
 System.out.println("Inner Class");
 }
 }
}
```

An instance inner class object is associated with an object of the outer class.

> **Instance Inner Class → Outer Class Object Required**

---

## 3. Instance Variable + Instance Method + Instance Inner Class

```java
class Demo3 {

 int x = 100; // Instance variable

 void showC() { // Instance method
 System.out.println("A");
 }

 class A { // Instance inner class

 void show() {
 System.out.println("Class A");
 }
 }

 public static void main(String[] args) {

 Demo3 d = new Demo3();

 System.out.println(d.x);
 d.showC();

 Demo3.A a = d.new A();

 a.show();
 }
}
```

### Output

```text
100
A
Class A
```

### Syntax

```java
Outer.Inner obj = outerObject.new Inner();
```

Example:

```java
Demo3 d = new Demo3();
Demo3.A a = d.new A();
```

### Remember

```text
Outer Class Object
 ↓
 d
 ↓
 d.new A()
 ↓
Inner Class Object
```

---

# 4. Why Is the Outer Object Required?

Because an instance inner class is non-static and is associated with a particular outer-class object.

```java
Demo3 d1 = new Demo3();
Demo3 d2 = new Demo3();

Demo3.A a1 = d1.new A();
Demo3.A a2 = d2.new A();
```

Here:

```text
a1 → associated with d1
a2 → associated with d2
```

---

# 5. Inner Class Can Access Outer Class Members

An inner class can access members of its outer class, including **private members**.

```java
class Outer {

 private int x = 100;

 class Inner {

 void show() {
 System.out.println(x);
 }
 }
}
```

---

# 6. Private Inner Class

An inner class can be declared `private`.

```java
class A {

 private class B {

 void show() {
 System.out.println("B");
 }
 }

 void show2() {

 B b = new B();
 b.show();
 }

 public static void main(String[] args) {

 A a = new A();
 a.show2();
 }
}
```

Since `B` is private, it cannot be directly accessed outside class `A`.

```java
A.B b; // Error outside A
```

But inside `A`:

```java
B b = new B();
```

is valid.

---


# 8. Static Block in Inner Class

Traditional Java syllabus notes often state that a non-static inner class cannot contain a static block.

```java
class Demo {

 class A {

 static {
 System.out.println("A");
 }
 }
}
```

> **Exam Note:** If following the older syllabus rule, write: **Static block cannot be declared inside a non-static inner class.**

> **Modern Java Note:** Java's rules for static members in inner classes changed in newer Java versions, so this is not an absolute rule for every modern Java version.

---

# 9. Outer Class Can Be Abstract

```java
abstract class A {

 class B {

 void show() {
 System.out.println("B");
 }
 }
}

class C extends A {

 public static void main(String[] args) {

 C c = new C();

 A.B b = c.new B();

 b.show();
 }
}
```

### Output

```text
B
```

---

# 10. Same Name as Outer and Inner Class

This is an error:

```java
class A {

 class A {
 }
}
```

An inner/member class cannot have the same simple name as its enclosing class.

---

# 11. Abstract Inner Class

An inner class can itself be `abstract`.

```java
class A {

 abstract class B {

 abstract void show();
 }

 class C extends B {

 void show() {
 System.out.println("Class C");
 }
 }

 public static void main(String[] args) {

 A a = new A();

 A.C c = a.new C();

 c.show();
 }
}
```

### Output

```text
Class C
```

Abstract inner class cannot be instantiated directly:

```java
A.B b = a.new B(); // Error
```

But its concrete child can be instantiated:

```java
A.C c = a.new C();
```

---


---

[Previous: Nested Classes Overview](./01-nested-classes-overview.md) · [Back to Index](./README.md) · [Next: Local Inner Class](./03-local-inner-class.md)
