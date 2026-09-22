# Cursors in Java

If we want to retrieve objects one by one from a collection, we should go for a **Cursor**.

Java provides three types of cursors:

1. **Enumeration**
2. **Iterator**
3. **ListIterator**

---

## 1. Enumeration

### Introduction

- **Enumeration** was introduced in **Java 1.0**.
- It can be used to retrieve objects one by one from **legacy collection classes**.
- We can create an `Enumeration` object by using the `elements()` method of the `Vector` class.

### Method Signature

```java
public Enumeration elements();
```

Here, `v` represents a `Vector` object.

### Creating Enumeration Object

```java
Enumeration e = v.elements();
```

### Important Methods

Enumeration provides two important methods:

```java
public boolean hasMoreElements();
public Object nextElement();
```

### Example

```java
import java.util.*;

class Demo {
    public static void main(String[] args) {
        Vector<String> v = new Vector<>();

        v.add("abc");
        v.add("xyz");
        v.add("qwerty");

        Enumeration e = v.elements();

        while (e.hasMoreElements()) {
            System.out.println(e.nextElement());
        }
    }
}
```

### Limitations of Enumeration

1. Enumeration is applicable only to **legacy collection classes**, so it is **not a universal cursor**.
2. Using Enumeration, we can perform only **read/traversal operations**.
3. We cannot perform a **remove operation** using Enumeration.

> To overcome these limitations, we should go for **Iterator**.

---

# 2. Iterator

### Introduction

- `Iterator` was introduced in **Java 1.2**.
- We can apply the Iterator concept to **any Collection object**, so it is called a **universal cursor**.
- Using Iterator, we can perform both **read** and **remove** operations.

### Creating Iterator Object

We can create an Iterator object by using the `iterator()` method of the `Collection` interface.

### Method Signature

```java
public Iterator iterator();
```

Here, `c` represents any Collection object.

```java
Iterator itr = c.iterator();
```

### Important Methods

Iterator provides three main methods:

```java
public boolean hasNext();
public Object next();
public void remove();
```

### Example

```java
import java.util.*;

class Demo {
    public static void main(String[] args) {
        ArrayList<String> al = new ArrayList<>();

        al.add("abc");
        al.add("xyz");
        al.add("qwerty");

        Iterator itr = al.iterator();

        while (itr.hasNext()) {
            String s = (String) itr.next();

            if (s.equals("abc")) {
                itr.remove();
            }
        }

        System.out.println(al);
    }
}
```

### Limitations of Iterator

1. Using Iterator, we can move only in the **forward direction**.
2. We cannot move in the **backward direction**.
3. Hence, Iterator is a **single-directional cursor**.
4. Iterator supports read and remove operations, but it does not provide operations for **replacing an existing object** or **adding a new object at the cursor position**.

> To overcome these limitations, we should go for **ListIterator**.

---

# 3. ListIterator

### Introduction

- `ListIterator` was introduced in **Java 1.2**.
- It is applicable only to **List objects**.
- Using ListIterator, we can move in both **forward and backward directions**.
- Hence, ListIterator is a **bidirectional cursor**.
- It supports read, remove, replace and add operations.

### Creating ListIterator Object

We can create a ListIterator object by using the `listIterator()` method of the `List` interface.

### Method Signature

```java
public ListIterator listIterator();
```

Here, `l` represents a List object.

```java
ListIterator itr = l.listIterator();
```

### Relationship with Iterator

`ListIterator` is a **child interface of Iterator**.

Therefore, all methods of Iterator are available in ListIterator by default.

### Nine Methods of ListIterator

ListIterator defines **9 methods**.

#### Forward Direction

```java
public boolean hasNext();
public Object next();
public int nextIndex();
```

#### Backward Direction

```java
public boolean hasPrevious();
public Object previous();
public int previousIndex();
```

#### Other Operations

```java
public void remove();
public void set(Object obj);
public void add(Object obj);
```

> In `set()` and `add()`, a new object is passed as a parameter.

### Example

```java
import java.util.*;

class Demo {
    public static void main(String[] args) {
        LinkedList<String> l = new LinkedList<>();

        l.add("abc");
        l.add("xyz");
        l.add("qwerty");

        ListIterator<String> itr = l.listIterator();

        while (itr.hasNext()) {
            String s = itr.next();

            if (s.equals("abc")) {
                itr.remove();
            }
            else if (s.equals("xyz")) {
                itr.set("ABC");
            }
            else if (s.equals("qwerty")) {
                itr.add("123");
            }
        }

        System.out.println(l);
    }
}
```

### Limitations of ListIterator

ListIterator is the **most powerful cursor**, but it also has a limitation:

- It is applicable only to **List-implemented classes**.
- Hence, it is **not a universal cursor**.

---

# Difference Between Enumeration, Iterator and ListIterator

| Property | Enumeration | Iterator | ListIterator |
|---|---|---|---|
| **Applicable for** | Only legacy classes | Any Collection object | Only List objects |
| **Movement** | Forward only | Forward only | Forward + Backward |
| **Type of cursor** | Single-directional | Single-directional | Bidirectional |
| **Accessibility / Operations** | Read only | Read + Remove | Read + Remove + Replace + Add |
| **How to get it?** | `elements()` method of `Vector` | `iterator()` method of `Collection` | `listIterator()` method of `List` |
| **Number of methods** | 2 | 3 | 9 |
| **Introduced in** | Java 1.0 | Java 1.2 | Java 1.2 |
| **Legacy** | Yes | No | No |

### Quick Revision

```text
Enumeration
    ↓
Legacy classes
    ↓
Forward movement
    ↓
Read only
    ↓
2 methods

Iterator
    ↓
Any Collection object
    ↓
Forward movement
    ↓
Read + Remove
    ↓
3 methods

ListIterator
    ↓
Only List objects
    ↓
Forward + Backward movement
    ↓
Read + Remove + Replace + Add
    ↓
9 methods
```

---

# Common Example: Getting the Runtime Class Name

`getClass().getName()` can be used to get the **runtime class name** of an object.

```java
import java.util.*;

class Demo {
    public static void main(String[] args) {

        Vector<String> v = new Vector<>();
        v.add("abc");
        v.add("xyz");
        v.add("qwerty");

        Enumeration e = v.elements();
        Iterator itr = v.iterator();
        ListIterator litr = v.listIterator();

        System.out.println(e.getClass().getName());
        System.out.println(itr.getClass().getName());
        System.out.println(litr.getClass().getName());
    }
}
```

### Important Point

Although `Enumeration`, `Iterator`, and `ListIterator` are **interfaces**, the objects returned by `elements()`, `iterator()`, and `listIterator()` are instances of concrete implementation classes at runtime.

Therefore, `getClass().getName()` displays the **actual runtime implementation class name**, not simply the interface name.

---

## Final Comparison

```text
                    Enumeration      Iterator       ListIterator
                    -----------      --------       -----------
Applicable for      Legacy           Collection     List
Movement            Forward          Forward        Forward + Backward
Read                ✓                ✓              ✓
Remove              ✗                ✓              ✓
Replace             ✗                ✗              ✓
Add                 ✗                ✗              ✓
Methods             2                3              9
Version             1.0              1.2            1.2
```
