# toString() & hashCode()

---

## 1. Printing an Object — Default Behavior

```java
class A { }

A a1 = new A();
System.out.println(a1);
```

Output:

```text
A@5e2de80c
```

### What's Happening?

`System.out.println(a1)` internally calls `a1.toString()`.

Object class ki default `toString()` implementation:

```java
public String toString() {
    return getClass().getName() + "@" +
           Integer.toHexString(hashCode());
}
```

Format:

```text
ClassName@hexadecimalHashCode
```

```text
A          → class name
@          → separator
5e2de80c   → hashCode ka hexadecimal representation
```

---

## 2. Overriding toString()

Default output meaningful nahi hota — override karke useful representation de sakte hain:

```java
class Employee {
    int id;
    String name;

    Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public String toString() {
        return "Employee{id=" + id + ", name='" + name + "'}";
    }
}
```

Now:

```java
Employee e = new Employee(101, "Azhar");
System.out.println(e);
```

Output:

```text
Employee{id=101, name='Azhar'}
```

Instead of:

```text
Employee@6d03e736
```

> **Why override?** Meaningful object representation display karne ke liye — debugging aur logging ke liye essential.

---

## 3. hashCode() Method

```java
public native int hashCode()
```

`hashCode()` object ke liye ek **`int` value** return karta hai.

```java
A a1 = new A();
System.out.println(a1.hashCode());    // e.g. 1577213552
```

### Note: Common Misconception

> "JVM har object ko ek unique number assign karti hai aur wahi hashCode hai."

**Strictly correct nahi hai!**

### Correct Understanding

- `hashCode()` ek `int` value return karta hai
- **Different objects ka same hash code possible hai** (hash collision)
- Same object ka hash code, ek execution ke dauran, **consistent** rehna chahiye (jab tak equality-relevant info change na ho)
- Hash code ≠ guaranteed unique identity

---

## 4. Hexadecimal vs Decimal Relationship

```java
A a1 = new A();

System.out.println(a1);             // A@5e2de80c
System.out.println(a1.hashCode());  // 1580066828
```

```text
1580066828 (decimal) = 5e2de80c (hexadecimal)
```

Default `toString()` internally `Integer.toHexString(hashCode())` use karti hai.

So same value hai — bas representation alag hai (decimal vs hex).

---

## 5. toString(), hashCode() & equals() — The Trinity

```text
Object
 ├── toString()  → object ki string representation
 ├── hashCode()  → hash value (int)
 └── equals()    → logical equality check
```

### The Contract

```text
If two objects are equal according to equals(),
then their hashCode() values MUST be equal.
```

> Agar `equals()` override karo, to **`hashCode()` bhi override karo**.

---

## 6. Overriding hashCode() — Example

```java
class Employee {
    int id;
    String name;

    Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Employee)) return false;
        Employee other = (Employee) o;
        return this.id == other.id;
    }

    @Override
    public int hashCode() {
        return Integer.hashCode(id);
    }

    @Override
    public String toString() {
        return "Employee{id=" + id + ", name='" + name + "'}";
    }
}
```

> **Best Practice:** Always override `toString()`, `equals()`, and `hashCode()` together.

---

## Interview Quick Questions

| Question | Answer |
|----------|--------|
| Default `toString()` kya return karta hai? | `ClassName@hexHashCode` |
| `hashCode()` unique guarantee deta hai? | No — collisions possible |
| `equals()` override kiya to `hashCode()` bhi override karna chahiye? | Yes — contract |
| `toString()` kyu override karte hain? | Meaningful representation ke liye |
| `hashCode()` ka return type? | `int` |
| `hashCode()` `native` method hai? | Yes |
| `toString()` mein hex value kahan se aati hai? | `Integer.toHexString(hashCode())` |

---

[Previous: Object Class](./01-object-class.md) · [Back to OOP Index](./README.md) · [Next: equals() & ==](./03-equals-and-identity.md)
