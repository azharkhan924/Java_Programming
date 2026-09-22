# Local Inner Class

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


---

[Previous: Instance Inner Class](./02-instance-inner-class.md) · [Back to Index](./README.md) · [Next: Anonymous Inner Class](./04-anonymous-inner-class.md)
