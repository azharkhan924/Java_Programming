# Java — Inner Classes / Nested Classes

> **Topic:** Nested Classes → Non-static & Static → Instance Inner Class, Local Inner Class, Anonymous Inner Class  
> **Applications:** Graphics, Lambda Expressions, Event Listeners

---

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
                   /            \
             Non-static         Static
                 |
        ┌────────┼────────┐
        │        │        │
    Instance   Local   Anonymous
     Inner     Inner     Inner
     Class     Class     Class
```

### Non-static Nested Classes
1. Instance Inner Class
2. Local Inner Class
3. Anonymous Inner Class

### Static Nested Class
A nested class declared using `static`.

---

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

    int x = 100;              // Instance variable

    void showC() {            // Instance method
        System.out.println("A");
    }

    class A {                 // Instance inner class

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
A.B b;       // Error outside A
```

But inside `A`:

```java
B b = new B();
```

is valid.

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
A.B b = a.new B();       // Error
```

But its concrete child can be instantiated:

```java
A.C c = a.new C();
```

---

# 12. Local Inner Class

A class declared inside a **method, constructor or local block** is called a **Local Inner Class**.

```java
class A {

    void show() {

        class B {

            void show2() {
                System.out.println("B");
            }
        }

        B b = new B();
        b.show2();
    }

    public static void main(String[] args) {

        A a = new A();
        a.show();
    }
}
```

### Output

```text
B
```

---

# 13. Scope of Local Inner Class

A local inner class has scope limited to the method/block in which it is declared.

```java
class A {

    void show() {

        class B {
            void display() {
                System.out.println("B");
            }
        }

        B b = new B();
        b.display();
    }
}
```

`B` cannot be directly accessed outside `show()`.

```java
void test() {

    B b = new B();       // Error
}
```

---

# 14. Class Can Be Declared in Different Local Scopes

A local class can be declared inside:

- Method
- Constructor
- Instance block
- Static block
- Control statement/block
- Loop
- Static method
- Instance method

Example inside a constructor:

```java
class A {

    A() {

        class B {

            void show() {
                System.out.println("B");
            }
        }

        B b = new B();
        b.show();
    }
}
```

---

# 15. Local Inner Class — Local Variable Access

A local inner class can access a local variable from its enclosing method only when that variable is **final or effectively final**.

```java
class A {

    void show() {

        int y = 21;

        class B {

            void show2() {
                System.out.println(y);
            }
        }

        B b = new B();
        b.show2();

        System.out.println("A");
    }

    public static void main(String[] args) {
        new A().show();
    }
}
```

### Output

```text
21
A
```

---

# 16. Final or Effectively Final

A local variable referenced from an inner class must be:

- `final`, or
- **effectively final**

### Explicit final

```java
final int y = 21;
```

### Effectively final

```java
int y = 21;
```

If `y` is never updated after initialization, it is **effectively final**.

If we do:

```java
int y = 21;
y = 30;
```

then `y` is no longer effectively final.

### Version Concept

```text
Java 7 and earlier → explicitly final
Java 8+            → final OR effectively final
```

> **Effectively final = variable is not declared final, but its value is never changed after initialization.**

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
A a = new A();       // Error
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
Abstract class object directly → ❌
Abstract class reference       → ✅
Reference holding subclass     → ✅
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

# 25. Instance Inner vs Local Inner vs Anonymous Inner

| Feature | Instance Inner | Local Inner | Anonymous Inner |
|---|---|---|---|
| Has name? | Yes | Yes | No |
| Declared inside | Class | Method/block/constructor | Expression/object creation |
| Scope | Outer class | Declaring local scope | Usually one use |
| Common use | Encapsulation | Temporary local logic | Listeners/callbacks |

---

# 26. Graphics, Lambda Expressions and Listeners

Inner/anonymous classes are commonly encountered in Java GUI and event-driven programming.

Example using an event listener:

```java
button.addActionListener(new ActionListener() {

    public void actionPerformed(ActionEvent e) {
        System.out.println("Button clicked");
    }
});
```

This is an anonymous class implementing `ActionListener`.

With modern Java, a functional interface can often be written using a lambda:

```java
button.addActionListener(e -> {
    System.out.println("Button clicked");
});
```

Thus anonymous inner classes are closely related to **listeners, callbacks and lambda expressions**.

---

# 27. Quick Revision Map

```text
                    NESTED CLASS
                         │
             ┌───────────┴───────────┐
             │                       │
        NON-STATIC                 STATIC
             │                    NESTED CLASS
      ┌──────┼──────┐
      │      │      │
   Instance Local  Anonymous
    Inner  Inner     Inner
    Class  Class     Class
```

### Instance Inner Class

```java
Outer o = new Outer();
Outer.Inner i = o.new Inner();
```

### Local Inner Class

```java
void show() {

    class B {
    }
}
```

### Anonymous Inner Class

```java
A a = new A() {
    // body
};
```

### Abstract Inner Class

```java
abstract class B {
}
```

### Private Inner Class

```java
private class B {
}
```

---

# 28. Exam-Oriented One-Liners

- **Nested class:** A class declared inside another class.
- **Instance inner class:** A non-static class declared inside another class.
- **Instance inner class object:** Created using the outer class object.
- **Syntax:** `Outer.Inner i = outer.new Inner();`
- **Private inner class:** Can be accessed only within its enclosing class.
- **Local inner class:** A class declared inside a method, constructor or local block.
- **Anonymous inner class:** A class without an explicit name.
- **Anonymous class can:** Extend a class or implement an interface.
- **Local variable accessed by inner class:** Must be `final` or effectively final.
- **Effectively final:** A variable whose value is not modified after initialization.
- **Superclass reference:** Can hold a subclass object, e.g. `A a = new B();`
- **Abstract class:** Cannot be instantiated directly but can have reference variables.
- **Abstract reference:** `A a = new B();` is valid when `B extends A`.
- **Inner class:** Can access members of its outer class, including private members.
