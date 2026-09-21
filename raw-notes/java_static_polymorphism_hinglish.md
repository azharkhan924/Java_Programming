# Java Notes — Static, Polymorphism & `main()`

> **Style:** Hinglish + English, WhatsApp-style explanation.  
> Notes ko exam + interview dono ke point of view se structured rakha gaya hai.

---

# 1. Static Block

A **static block** `static { }` ke andar likha jata hai aur class initialization ke time execute hota hai.

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

- Static block **class initialization ke time** execute hota hai.
- Ek class me **multiple static blocks** ho sakte hain.
- Multiple static blocks **top-to-bottom / textual order** me execute hote hain.
- Normal application startup me static initialization `main()` execute hone se pehle hoti hai.

---

## 1.1 Static Block Without `main()`

Old Java versions me ek famous behavior tha:

- **Java 6 and earlier:** static initializer ke through program ko `main()` ke bina run karna possible tha.
- **Java 7 onwards:** Java launcher valid `main()` method expect karta hai. Sirf static block hone par program normally start nahi hota aur `main method not found` type error milta hai.

So modern Java application ke liye standard entry point:

```java
public static void main(String[] args)
```

---

# 2. Static Variable

Static variable ko `static` keyword ke saath declare karte hain.

```java
class Student {
    static String college = "AITR";
}
```

Static variable **class-level member** hota hai.

### Main Points

- Static member class se belong karta hai.
- Same class ke objects static variable ko share karte hain.
- Static member ko class name se access karna preferred hai.

```java
System.out.println(Student.college);
```

Object ke through bhi technically access possible hai:

```java
Student s = new Student();
System.out.println(s.college);
```

But class-level member ke liye `Student.college` clearer hai.

### Static variable local variable nahi hota

Ye Java me invalid hai:

```java
void show() {
    static int x = 10;
}
```

Java local variables ko `static` declare nahi kar sakte.

---

# 3. Static Method

Static method ko `static` keyword ke saath declare karte hain.

```java
class Demo {

    static void show() {
        System.out.println("Hello");
    }

    public static void main(String[] args) {
        Demo.show();
    }
}
```

Static method ko object create kiye bina class name se call kar sakte hain.

```java
Demo.show();
```

---

## 3.1 Static Method — What Can It Access?

Static method directly:

- static variables ko access kar sakta hai
- static methods ko call kar sakta hai

But instance members ko directly access nahi kar sakta.

```java
class Demo {

    int x = 10;
    static int y = 20;

    static void show() {
        System.out.println(y);  // valid
        // System.out.println(x);  // error
    }
}
```

Instance variable ko object ke through access kar sakte hain:

```java
static void show() {
    Demo d = new Demo();
    System.out.println(d.x);
}
```

### Easy reason

Static method ko object ke bina call kiya ja sakta hai, while instance variable kisi particular object ka member hota hai.

---

# 4. Instance Method

Jo method `static` nahi hota, wo **instance method** hota hai.

```java
class Demo {

    int x = 10;
    static int y = 20;

    void show() {
        System.out.println(x);
        System.out.println(y);
    }
}
```

Instance method object context me execute hota hai, so wo:

- instance data access kar sakta hai
- static data access kar sakta hai

Call:

```java
Demo d = new Demo();
d.show();
```

---

# 5. Static vs Instance

| Point | Static | Instance |
|---|---|---|
| Belongs to | Class | Object |
| Object required for normal access? | No | Yes |
| Direct static data | Yes | Yes |
| Direct instance data | No | Yes |
| `this` | No | Yes |
| `super` | No | Yes |

---

# 6. `this` in Static Method

`this` current object ko refer karta hai.

Static method ke paas current object context nahi hota.

So:

```java
static void show() {
    // System.out.println(this.x);  // error
}
```

Static method me `this` use nahi kar sakte.

---

# 7. `super` in Static Context

`super` current object ke parent-class part ko refer karta hai.

Static context me current object context available nahi hota.

So:

```java
static void show() {
    // super.show();  // error
}
```

Static method me `super` use nahi kar sakte.

---

# 8. Object ke Through Static Member

Object ke through static member access karna technically possible hai:

```java
Demo d = new Demo();

d.staticMethod();
System.out.println(d.staticVariable);
```

But recommended:

```java
Demo.staticMethod();
System.out.println(Demo.staticVariable);
```

Because static member class se belong karta hai.

---

# 9. Static Import

Normally static member ko class name ke through access karte hain:

```java
System.out.println("Hello");
```

Agar `System` ka static member directly use karna hai without `System.`:

```java
import static java.lang.System.out;
```

Then:

```java
out.println("Hello");
```

### Wildcard Static Import

```java
import static java.lang.System.*;
```

Then:

```java
out.println("Hello");
```

### General Syntax

```java
import static package.ClassName.member;
```

or:

```java
import static package.ClassName.*;
```

Example:

```java
import static java.lang.Math.*;

class Demo {
    public static void main(String[] args) {
        System.out.println(sqrt(25));
        System.out.println(pow(2, 3));
    }
}
```

---

# 10. Polymorphism

**Polymorphism** ka meaning hai:

> One name, multiple forms.

Java me commonly:

```text
Polymorphism
     |
     +-----------------------+
     |                       |
Compile-Time             Run-Time
Static Binding           Dynamic Binding
Early Binding            Late Binding
     |                       |
Overloading              Overriding
Static Hiding
```

> Static methods override nahi hoti; same signature ki static methods parent-child classes me **hide** hoti hain.

---

# 11. Compile-Time Polymorphism

Compile-time polymorphism me method selection compiler decide karta hai.

Isko:

- Static Binding
- Early Binding

bhi kehte hain.

Main examples:

```text
Method Overloading
Static Method Hiding
```

---

# 12. Method Overloading

**Same class + same method name + different parameter list** = Method Overloading.

Example:

```java
class A {

    void show(int x, int y) {
        System.out.println(x + y);
    }

    void show(int x, int y, int z) {
        System.out.println(x + y + z);
    }
}
```

Call:

```java
A obj = new A();

obj.show(10, 20);
```

2 parameters hain → first method.

```java
obj.show(10, 20, 30);
```

3 parameters hain → second method.

---

## 12.1 Parameter List Me Kya Change Ho Sakta Hai?

### Number of parameters

```java
show(int x)
show(int x, int y)
```

### Type of parameters

```java
show(int x, double y)
show(double x, double y)
```

### Order of parameter types

```java
show(int x, double y)
show(double x, int y)
```

Ye bhi different parameter lists hain.

---

## 12.2 Return Type Change Karke Overloading Nahi Hoti

Invalid overloading:

```java
int show(int x) {
    return x;
}

double show(int x) {
    return x;
}
```

Sirf return type different hai, parameter list same hai.

---

# 13. Method Overloading — Given Examples

Consider:

```java
class A {

    void show(int x, double y) {
        System.out.println("int-double");
    }

    void show(int x, int y) {
        System.out.println("int-int");
    }

    void show(double x, int y) {
        System.out.println("double-int");
    }

    void show(double x, double y) {
        System.out.println("double-double");
    }
}
```

## Case 1

```java
obj.show(10.8, 20);
```

Types:

```text
double, int
```

Exact match:

```java
show(double, int)
```

Output:

```text
double-int
```

## Case 2

```java
obj.show(10, 20.7);
```

Types:

```text
int, double
```

Exact match:

```java
show(int, double)
```

Output:

```text
int-double
```

## Case 3

```java
obj.show(10.8, 20.7);
```

Types:

```text
double, double
```

Exact match:

```java
show(double, double)
```

Output:

```text
double-double
```

## Case 4

```java
obj.show(10, 20);
```

Types:

```text
int, int
```

Exact match:

```java
show(int, int)
```

Output:

```text
int-int
```

---

# 14. Important Overloading Ambiguity

Suppose methods:

```java
void show(int x, double y) { }

void show(double x, int y) { }
```

Call:

```java
show(10, 20);
```

Both methods are applicable:

```text
10 → int       exact
20 → double    widening
```

and:

```text
10 → double    widening
20 → int       exact
```

Dono equally applicable ho sakti hain, so compiler **ambiguous method call** error de sakta hai.

---

# 15. Method Overloading Resolution

Compiler generally more specific/suitable method ko prefer karta hai.

Basic order ko yaad rakh sakte ho:

```text
Exact match
   ↓
Widening primitive conversion
   ↓
Boxing / unboxing
   ↓
Varargs
```

Example:

```java
void show(int x) { }
void show(double x) { }

show(10);
```

`int` exact match hai, so `show(int)` call hogi.

---

# 16. Method Overriding

Method overriding inheritance se related hai.

Main conditions:

- Parent-child relationship hona chahiye.
- Same method name.
- Same parameter list.
- Instance methods honi chahiye.
- Return type same ya compatible covariant type ho sakta hai.
- Access level ko unnecessarily reduce nahi kar sakte.
- `static` methods override nahi hoti; they are hidden.

Example:

```java
class A {

    void show() {
        System.out.println("Class A");
    }
}

class B extends A {

    @Override
    void show() {
        System.out.println("Class B");
    }
}
```

Call:

```java
A obj = new B();
obj.show();
```

Output:

```text
Class B
```

---

# 17. Why Method Overriding?

Parent class me already method hai:

```java
class A {
    void show() {
        System.out.println("Class A");
    }
}
```

Child class same method ko apne requirement ke according modify kar sakti hai:

```java
class B extends A {

    @Override
    void show() {
        System.out.println("Class B");
    }
}
```

So:

```text
Inheritance
→ existing class ka behavior reuse

Overriding
→ inherited method ka implementation customize
```

---

# 18. Runtime Polymorphism

Method overriding **runtime polymorphism** ka main example hai.

Isko:

- Dynamic Binding
- Late Binding

bhi kehte hain.

Example:

```java
A obj = new B();

obj.show();
```

Yahan:

```text
Reference type = A
Actual object  = B
```

`show()` instance method hone ki wajah se runtime par actual object ke according implementation select hogi.

Output:

```text
Class B
```

---

# 19. Reference Variable vs Object

Example:

```java
A obj = new B();
```

Yahan:

```text
A → reference type
B → actual object type
```

### Compile-time

Compiler mainly reference type `A` ko dekhta hai to check karta hai ki `show()` accessible/available hai ya nahi.

### Runtime

Overridden instance method ke liye actual object `B` decide karta hai ki kaunsi implementation execute hogi.

---

# 20. Binding

**Binding** ka matlab method call ko actual method implementation ke saath connect karna.

### Compile-Time Binding

Method selection compile time par decide hota hai.

Examples:

```text
Method Overloading
Static Method Hiding
```

### Runtime Binding

Method selection runtime par actual object ke basis par hota hai.

Example:

```text
Method Overriding
```

---

# 21. Java Instance Methods and Virtual Dispatch

Java me normal instance methods dynamically dispatched hoti hain.

Example:

```java
A obj = new B();
obj.show();
```

Agar `show()` B me override hai, to B ka `show()` execute hoga.

C++ me virtual dispatch ke liye `virtual` keyword important hai.

Java me normal instance methods ke liye separate `virtual` keyword use nahi karte; JVM method dispatch rules apply karta hai.

---

# 22. Method Hiding

Static methods **override nahi hoti**.

Agar parent aur child class me same signature ki static method ho, to ise **method hiding** kehte hain.

Example:

```java
class A {

    static void show() {
        System.out.println("Class A");
    }
}

class B extends A {

    static void show() {
        System.out.println("Class B");
    }
}
```

Call:

```java
class Demo {

    public static void main(String[] args) {

        A obj = new B();

        obj.show();
    }
}
```

Output:

```text
Class A
```

Because static method selection reference type ke basis par hota hai.

---

# 23. Method Hiding Internal Working

```java
A obj = new B();

obj.show();
```

Compiler dekhega:

```text
Reference type = A
```

`A` me static `show()` available hai.

Static method ka dispatch compile-time/reference type ke basis par hota hai.

So:

```text
A.show()
```

selected hoga.

Actual object `B` hone se static method dispatch change nahi hota.

---

# 24. Method Overriding vs Method Hiding

| Feature | Method Overriding | Method Hiding |
|---|---|---|
| Methods | Instance | Static |
| Inheritance | Required | Required |
| Name | Same | Same |
| Parameter list | Same | Same |
| Binding | Runtime | Compile-time |
| Depends on | Actual object | Reference type |
| Polymorphism | Runtime | Static/compile-time dispatch |
| `@Override` | Valid | Static methods override nahi hoti |

### Easy Trick

```text
Instance
   ↓
Object
   ↓
Runtime
   ↓
Overriding
```

```text
Static
   ↓
Reference
   ↓
Compile-time
   ↓
Hiding
```

---

# 25. `public static void main(String[] args)`

Standard Java application ka entry point:

```java
public static void main(String[] args)
```

### `public`

JVM/application launcher ko method ko access karna hota hai, isliye standard main method public hoti hai.

### `static`

JVM ko main call karne ke liye class ka object create nahi karna padta.

### `void`

`main()` JVM ko koi return value nahi deta.

### `main`

Ye standard entry-point method name/signature ka part hai.

### `String[] args`

Command-line arguments receive karne ke liye.

---

# 26. Command-Line Arguments

Example:

```java
class Demo {

    public static void main(String[] args) {

        System.out.println(args[0]);
        System.out.println(args[1]);
    }
}
```

Run:

```text
java Demo Java Developer
```

Output:

```text
Java
Developer
```

Because:

```text
args[0] = "Java"
args[1] = "Developer"
```

Command-line arguments strings hote hain.

Number chahiye to:

```java
int x = Integer.parseInt(args[0]);
```

---

# 27. Why `main()` Is Static?

Agar `main()` instance method hota:

```java
public void main(String[] args)
```

to application start karne ke liye JVM ko pehle object create karna padta.

`static` hone ki wajah se standard Java launcher class-level method ko object create kiye bina invoke kar sakta hai.

---

# 28. `System.out.println()` Internal Understanding

Hum normally likhte hain:

```java
System.out.println("Hello");
```

Isko pieces me samjho:

```text
System
  ↓
Class

out
  ↓
static field
  ↓
PrintStream object/reference

println()
  ↓
PrintStream ki instance method
```

So:

```text
System → out → println()
Class    field/object   method
```

---

# 29. `System` Class

`System` class:

```text
java.lang.System
```

package me hoti hai.

`java.lang` automatically imported hota hai.

Isliye:

```java
System.out.println();
```

ke liye normally explicit import nahi likhna padta.

---

# 30. `out` Field

`System.out` me:

```text
System → class
out    → static field
```

`out` ka type:

```java
PrintStream
```

hai.

Conceptually:

```java
public static final PrintStream out
```

So `out` ek class-level reference hai jo `PrintStream` object ko refer karta hai.

---

# 31. `PrintStream`

`PrintStream` class:

```text
java.io.PrintStream
```

package me hoti hai.

`println()` `PrintStream` ki instance method hai.

So:

```java
System.out.println("Hello");
```

means:

```text
System
  ↓
static out reference
  ↓
PrintStream object
  ↓
println() instance method
```

---

# 32. Static Import + `System.out`

Normally:

```java
System.out.println("Hello");
```

Static import:

```java
import static java.lang.System.out;

class Demo {

    public static void main(String[] args) {
        out.println("Hello");
    }
}
```

Wildcard:

```java
import static java.lang.System.*;
```

Then:

```java
out.println("Hello");
```

---

# 33. Static Class

Java me `static` class sirf **nested class** ke context me possible hai.

Example:

```java
class Outer {

    static class Inner {

        void show() {
            System.out.println("Inner");
        }
    }
}
```

Access:

```java
Outer.Inner obj = new Outer.Inner();
obj.show();
```

### Top-Level Class ko `static` nahi bana sakte

Invalid:

```java
static class Demo {
}
```

Reason:

Top-level class kisi outer class ki member nahi hoti; wo package level par hoti hai.

### Static Nested Class

Static nested class ko outer class ke object ki need nahi hoti.

```java
Outer.Inner obj = new Outer.Inner();
```

---

# 34. Static Nested Class vs Inner Class

### Static Nested Class

```java
class Outer {
    static class Inner {
    }
}
```

Outer object ki need nahi.

### Non-Static Inner Class

```java
class Outer {
    class Inner {
    }
}
```

Outer class ka object required hota hai:

```java
Outer outer = new Outer();
Outer.Inner inner = outer.new Inner();
```

---

# 35. Important Static Rules

```text
static variable
    ↓
class-level/shared member

static method
    ↓
object ke bina call possible

static block
    ↓
class initialization ke time execute

static import
    ↓
static member ko class name ke bina use karne ki facility

static nested class
    ↓
nested class only
```

---

# 36. `this`, `super`, Static — Quick Revision

```text
this
 ↓
current object

super
 ↓
parent-class part of current object

static method
 ↓
no current-object context
 ↓
this/super directly unavailable
```

---

# 37. Overloading vs Overriding

| Point | Overloading | Overriding |
|---|---|---|
| Classes | Usually same class | Parent + child |
| Inheritance | Not required | Required |
| Method name | Same | Same |
| Parameters | Must differ | Same |
| Binding | Compile-time | Runtime |
| Polymorphism | Compile-time | Runtime |
| Return type alone enough? | No | No |
| Main purpose | Same method name with different inputs | Child-specific implementation |

---

# 38. Most Important Interview Difference

```text
Overloading
→ Same class
→ Same name
→ Different parameter list
→ Compile-time

Overriding
→ Parent + Child
→ Same signature
→ Instance method
→ Runtime
→ Object based

Hiding
→ Parent + Child
→ Same static method
→ Compile-time
→ Reference based
```

---

# 39. Final Quick Revision

### Static

- Static member class se belong karta hai.
- Static variable class-level/shared member hota hai.
- Static method directly static members ko access kar sakta hai.
- Instance method static + instance dono members ko directly access kar sakta hai.
- `this` and `super` static context me directly use nahi kar sakte.
- Static block class initialization ke time execute hota hai.
- Multiple static blocks possible hain.
- Static import se static members ko class name ke bina access kar sakte hain.
- Static nested class possible hai.
- Top-level class ko `static` nahi bana sakte.

### Polymorphism

```text
Compile-Time
→ Overloading
→ Static method hiding

Run-Time
→ Overriding
```

### Overloading

```text
Same class
+
Same method name
+
Different parameter list
```

### Overriding

```text
Inheritance
+
Same method signature
+
Instance methods
+
Runtime dispatch
```

### Hiding

```text
Inheritance
+
Same static method signature
+
Compile-time dispatch
+
Reference type based
```

### Main

```java
public static void main(String[] args)
```

```text
public  → accessible entry point
static  → object create kiye bina invoke
void    → no return value
main    → standard entry-point name
String[] args → command-line arguments
```

---

# 40. Super Short Memory Trick

```text
STATIC
→ Class
→ No object required
→ this ❌
→ super ❌
→ Static data
→ Static block at class initialization

OVERLOADING
→ Same class
→ Same name
→ Different parameters
→ Compile-time

OVERRIDING
→ Parent + Child
→ Same signature
→ Instance method
→ Runtime
→ Object based

HIDING
→ Parent + Child
→ Same static method
→ Compile-time
→ Reference based

MAIN
→ public + static + void
→ JVM entry point
→ String[] args
```
