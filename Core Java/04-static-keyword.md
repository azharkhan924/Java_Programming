# Static Keyword in Java

---

## 1. Static Variable

Static variable `static` keyword ke saath declare hoti hai — ye **class-level member** hoti hai.

```java
class Student {
 static String college = "AITR";
}
```

### Key Points

- Static member **class se belong** karta hai, objects se nahi
- Same class ke sabhi objects static variable ko **share** karte hain
- **Class name** se access karna preferred hai

```java
System.out.println(Student.college); // preferred

Student s = new Student();
System.out.println(s.college); // works but not recommended
```

### Note: Static Variable Local Nahi Hoti

```java
void show() {
 static int x = 10; // invalid in Java
}
```

> Java me local variables ko `static` declare nahi kar sakte.

---

## 2. Static Method

Static method ko **object create kiye bina** class name se call kar sakte hain.

```java
class Demo {
 static void show() {
 System.out.println("Hello");
 }
}

Demo.show(); // no object needed
```

### What Can Static Method Access?

| Access | Allowed? |
|--------|----------|
| Static variables | Yes — directly |
| Static methods | Yes — directly |
| Instance variables | No — directly nahi |
| Instance methods | No — directly nahi |
| Instance via object | Yes — object reference ke through |

```java
class Demo {
 int x = 10;
 static int y = 20;

 static void show() {
 System.out.println(y); // static data
 // System.out.println(x); // instance data

 Demo d = new Demo();
 System.out.println(d.x); // via object
 }
}
```

### `this` and `super` in Static Context

```java
static void test() {
 // this.x; // no current object
 // super.x; // no current object
}
```

> **Reason:** Static method ko object ke bina call kiya ja sakta hai, so `this`/`super` (jo object context chahte hain) available nahi hote.

---

## 3. Instance Method

Instance method `static` ke bina declare hota hai — ye **dono** (static + instance) members access kar sakta hai.

```java
class Demo {
 int x = 10;
 static int y = 20;

 void show() {
 System.out.println(x); // instance data
 System.out.println(y); // static data
 }
}

Demo d = new Demo();
d.show(); // object required
```

---

## 4. Static vs Instance — Quick Comparison

| Point | Static | Instance |
|-------|--------|----------|
| Belongs to | Class | Object |
| Object required? | No | Yes |
| Direct static data | Yes | Yes |
| Direct instance data | No | Yes |
| `this` available | No | Yes |
| `super` available | No | Yes |

### When to Use Static?

```text
Object-specific data → Instance variable
 Example: student ka age (alag-alag)

Common/class-level data → Static variable
 Example: college name (sabke liye same)
```

---

## 5. Static Block

Static block `static { }` ke andar likha jata hai — ye **class initialization ke time** execute hota hai.

```java
class Demo {
 static {
 System.out.println("Static Block");
 }

 public static void main(String[] args) {
 System.out.println("Main");
 }
}
```

Output:

```text
Static Block
Main
```

### Important Points

- Static block **class initialization ke time** execute hota hai
- Ek class me **multiple static blocks** ho sakte hain
- Multiple blocks **top-to-bottom / textual order** me execute hote hain
- Normal application me `main()` se pehle hoti hai static initialization

### Java 6 vs Java 7+

| Version | Behavior |
|---------|----------|
| Java 6 & earlier | `main()` ke bina program run ho sakta tha static block se |
| Java 7+ | `main method not found` error — valid `main()` required |

---

## 6. Static Import

Normally static member ko class name ke through access karte hain. Static import se **class name hatakar directly** use kar sakte hain.

```java
// Normal
System.out.println(Math.sqrt(25));

// Static import
import static java.lang.Math.*;

System.out.println(sqrt(25)); // no Math prefix
System.out.println(pow(2, 3));
```

### Syntax

```java
import static package.ClassName.member; // specific member
import static package.ClassName.*; // all static members
```

### `System.out` ke saath

```java
import static java.lang.System.out;

out.println("Hello"); // no System prefix
```

### Note: Static Import Ambiguity

```java
import static java.lang.Byte.*;
import static java.lang.Short.*;

System.out.println(MIN_VALUE); // ambiguous — both have MIN_VALUE
```

Fix:

```java
System.out.println(Byte.MIN_VALUE); // explicit class name
```

---

## 7. Static Nested Class

Java me `static` class sirf **nested class** ke context me possible hai.

```java
class Outer {
 static class Inner {
 void show() {
 System.out.println("Static Inner");
 }
 }
}

Outer.Inner obj = new Outer.Inner();
obj.show();
```

### Static Nested vs Non-Static Inner

| Feature | Static Nested Class | Non-Static Inner Class |
|---------|-------------------|----------------------|
| Keyword | `static class Inner` | `class Inner` |
| Outer object needed? | No | Yes |
| Creation | `new Outer.Inner()` | `outer.new Inner()` |

```java
// Non-static inner class — outer object required
Outer outer = new Outer();
Outer.Inner inner = outer.new Inner();
```

### Note: Top-Level Class Static Nahi Ho Sakti

```java
static class Demo { } // invalid — top-level class
```

---

## Important Static Rules — Revision

```text
static variable
 → class-level/shared member

static method
 → object ke bina call possible
 → directly static members access kar sakta hai
 → instance members directly 

static block
 → class initialization ke time execute
 → multiple blocks possible (top-to-bottom order)

static import
 → static member ko class name ke bina use karna

static nested class
 → nested class only — top-level nahi
 → outer object ki need nahi
```

---

[Previous: Constructors & Instance Blocks](./03-constructors-and-instance-blocks.md) · [Back to Core Java Index](./README.md) · [Next: Inheritance & Final](./05-inheritance-and-final.md)
