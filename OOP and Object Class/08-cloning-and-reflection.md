# 🧬 Cloning & Reflection API — Deep Dive

> **Summary:** Object Cloning (Shallow vs Deep), `Cloneable` Marker Interface, `clone()` method details, aur Java Reflection API (`Class`, `Method`, `getDeclaredMethods()`).

---

## 1. Object Cloning Kya Hai?

**Cloning = Existing object ki exact duplicate copy (same content) alag memory address par create karna.**

```text
Normal Reference Copy:
A a1 = new A();
A a2 = a1;          // ❌ Ye clone nahi hai! Dono a1 aur a2 SAME object ko point kar rahe hain.

Actual Cloning:
A a1 = new A();
A a2 = (A) a1.clone(); // ✅ New separate object created in heap with same field values!
```

| Operation | Memory Allocation | Modification Effect |
|-----------|-------------------|---------------------|
| `a2 = a1` (Assignment) | Same heap memory | `a2` modify karoge toh `a1` bhi change hoga |
| `a2 = (A) a1.clone()` | Separate heap memory | `a2` primitive fields change karoge toh `a1` affect nahi hoga |

---

## 2. `Cloneable` Interface & `clone()` Method

### 🏷️ `Cloneable` ek Marker Interface hai
- **Package:** `java.lang.Cloneable`
- **Definition:** Iske paas **koi methods nahi** hote (empty interface).
- **Purpose:** JVM ko signal (mark) karta hai ki is class ke objects par field-by-field copy legal hai.

> ⚠️ **Crucial Rule:** Agar class `Cloneable` implement **nahi** karti aur aap `clone()` call karte ho, toh runtime par **`CloneNotSupportedException`** throw hoga!

### 🔍 `clone()` Method kahan define hai?
- `clone()` method **`Object` class** ke andar defined hai, `Cloneable` interface me nahi!

```java
// Object class me declaration:
protected native Object clone() throws CloneNotSupportedException;
```

**Key Characteristics:**
1. **`protected`** — Direct outside packages se access nahi ho sakta. Class ko override karke `public` banana padta hai.
2. **`native`** — JVM C/C++ level par bitwise memory copying karta hai (fast execution).
3. **Return Type `Object`** — Isliye hamesha explicit type casting `(MyClass)` ki zaroorat padti hai.
4. **Checked Exception** — `CloneNotSupportedException` handle karna zaroori hai (`throws` ya `try-catch`).

---

## 3. Basic Cloning Implementation Steps

1. Class me `implements Cloneable` likho
2. `public Object clone() throws CloneNotSupportedException` override karo
3. Method ke andar `return super.clone();` call karo
4. Caller side par typecast karo: `(MyClass) obj.clone()`

```java
class Student implements Cloneable {
    int roll;
    String name;

    Student(int roll, String name) {
        this.roll = roll;
        this.name = name;
    }

    void display() {
        System.out.println(roll + " " + name);
    }

    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone(); // JVM handles field copy
    }
}

public class Main {
    public static void main(String[] args) throws CloneNotSupportedException {
        Student s1 = new Student(101, "Rahul");
        Student s2 = (Student) s1.clone();

        s2.roll = 102; // Modifying clone

        s1.display(); // 101 Rahul (Original intact!)
        s2.display(); // 102 Rahul
        System.out.println(s1 == s2); // false (Different heap memory)
    }
}
```

---

## 4. Shallow Cloning vs Deep Cloning

Ye sabse popular interview question hai!

### 🌊 A. Shallow Cloning (Default Behavior)
`Object.clone()` by default **shallow copy** karta hai:
- **Primitives** (`int`, `float`, `boolean`, etc.) ki value direct copy hoti hai.
- **Reference variables** (Object references) ka sirf **address/pointer** copy hota hai! Yani original aur cloned object **dono same nested object** ko share karte hain!

```text
Shallow Copy Diagram:
Original Object (a1) ──┐
                       ├──> Shared Inner Object (b1) ⚠️ (Danger of side effects!)
Cloned Object (a2)   ──┘
```

#### Code Example (Shallow Copy Issue):
```java
class Address {
    String city;
    Address(String city) { this.city = city; }
}

class Person implements Cloneable {
    int id;
    Address address; // Reference field

    Person(int id, Address address) {
        this.id = id;
        this.address = address;
    }

    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone(); // Default shallow copy!
    }
}

public class Demo {
    public static void main(String[] args) throws CloneNotSupportedException {
        Address addr = new Address("Delhi");
        Person p1 = new Person(1, addr);
        Person p2 = (Person) p1.clone();

        p2.address.city = "Mumbai"; // Modifying address in clone

        System.out.println(p1.address.city); // ⚠️ Mumbai! (Original also changed!)
        System.out.println(p2.address.city); // Mumbai
    }
}
```

---

### 🏔️ B. Deep Cloning
**Deep Cloning me outer object ke saath-saath uske saare referenced/nested objects ki bhi completely fresh copies create ki jaati hain.**

```text
Deep Copy Diagram:
Original Object (a1) ────> Original Inner Object (b1)
Cloned Object (a2)   ────> Cloned Inner Object (b2) ✅ (Complete 100% independence!)
```

#### Code Example (Deep Copy Solution):
```java
class Address implements Cloneable {
    String city;
    Address(String city) { this.city = city; }

    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}

class Person implements Cloneable {
    int id;
    Address address;

    Person(int id, Address address) {
        this.id = id;
        this.address = address;
    }

    @Override
    public Object clone() throws CloneNotSupportedException {
        // Step 1: Shallow copy of outer object
        Person cloned = (Person) super.clone();
        // Step 2: Explicitly clone the nested reference object!
        cloned.address = (Address) this.address.clone();
        return cloned;
    }
}

public class DemoDeep {
    public static void main(String[] args) throws CloneNotSupportedException {
        Address addr = new Address("Delhi");
        Person p1 = new Person(1, addr);
        Person p2 = (Person) p1.clone();

        p2.address.city = "Mumbai"; // Modifying clone

        System.out.println(p1.address.city); // ✅ Delhi (Original remains safe!)
        System.out.println(p2.address.city); // Mumbai
    }
}
```

### ⚖️ Comparison Table: Shallow vs Deep Cloning

| Feature | Shallow Cloning | Deep Cloning |
|---------|-----------------|--------------|
| **Default behavior** | `Object.clone()` default yahi hai | Manual implementation karni padti hai |
| **Primitives** | Copied by value | Copied by value |
| **References** | Shared reference (same pointer) | Fresh independent objects created |
| **Performance** | Fast (JVM native copy) | Slower (multiple objects instantiate hote hain) |
| **Side Effects** | Risk of accidental data mutation | Safe, no side effects |

---

## 5. Reflection API

### 🔍 Reflection Kya Hai?
**Reflection API** Java ka ek powerful mechanism hai jisse hum **Runtime par class ka metadata inspect aur manipulate** kar sakte hain (methods, private fields, constructors, annotations, etc.).

- **Package:** `java.lang.reflect`

### 3 Ways to Get `Class` Object

```java
// Way 1: Using .class syntax (Known at compile time)
Class<?> c1 = String.class;

// Way 2: Using getClass() method on an instance
String str = "Hello";
Class<?> c2 = str.getClass();

// Way 3: Using Class.forName() (Dynamic loading at runtime)
Class<?> c3 = Class.forName("java.lang.String");
```

---

### 🛠️ Example: Inspecting All Declared Methods

```java
import java.lang.reflect.Method;

class Calculator {
    public int add(int a, int b) { return a + b; }
    private int multiply(int a, int b) { return a * b; }
}

public class ReflectionDemo {
    public static void main(String[] args) {
        Class<?> c = Calculator.class;

        // getDeclaredMethods() returns ALL methods (including private!)
        Method[] methods = c.getDeclaredMethods();

        System.out.println("Methods in " + c.getName() + ":");
        for (Method m : methods) {
            System.out.println("  " + m.getName() + " -> Return type: " + m.getReturnType());
        }
    }
}
```

### 🔑 `getMethods()` vs `getDeclaredMethods()`

| Method | Kya Return Karta Hai? |
|--------|-----------------------|
| `getMethods()` | Sirf **`public`** methods (including inherited parent/Object methods) |
| `getDeclaredMethods()` | Usi class ke **saare** methods (`public`, `private`, `protected`, default) lekin inherited nahi |

### ⚠️ Reflection ke Risks
1. **Breaks Encapsulation:** Private fields/constructors ko `setAccessible(true)` se access/modify kiya ja sakta hai.
2. **Performance Overhead:** Dynamic method lookup runtime par slow hoti hai.
3. **Security Constraints:** SecurityManager under restricted environments me reflection block kar sakta hai.

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| `Cloneable` interface me kaunsa method hota hai? | ❌ Koi nahi! Ye Marker Interface hai. |
| `clone()` method kahan hota hai? | `java.lang.Object` class me! |
| Agar `Cloneable` implement na karein toh kya hoga? | `CloneNotSupportedException` aayega. |
| `Object.clone()` deep copy karta hai ya shallow? | Shallow copy karta hai. |
| Private methods ko runtime pe inspect kiya ja sakta hai? | ✅ Yes, using `Class.getDeclaredMethods()`. |
| Clone method ka default access modifier kya hai? | `protected`. Overriding me `public` banana padta hai. |

---

[⬅️ Previous: Garbage Collection](./07-garbage-collection.md) · [📖 Back to OOP Index](./README.md) · [Next → Singleton Pattern ➡️](./09-singleton-pattern.md)
