# 🔍 Cursors — Enumeration, Iterator & ListIterator

> **Summary:** 3 cursor types for traversing Java collections, their methods, forward vs bidirectional traversal, fail-fast behavior, and when to use which cursor.

---

## 1. What are Cursors?

Cursors are used to **traverse (access one-by-one) the elements** of a collection. Java provides 3 types:

```text
Cursors in Java:
1. Enumeration  → Oldest (Java 1.0), only for Vector/legacy classes
2. Iterator     → Universal (Java 1.2), works with any Collection
3. ListIterator → Most Powerful (Java 1.2), only for List implementations
```

---

## 2. Enumeration (Legacy Cursor)

**Enumeration** is the oldest cursor (Java 1.0). It works only with **legacy classes (Vector, Stack, Hashtable)**.

### How to Get Enumeration:
```java
Vector<String> v = new Vector<>();
v.add("A"); v.add("B"); v.add("C");
Enumeration<String> e = v.elements(); // Vector's special method
```

### Methods (Only 2):
```java
while (e.hasMoreElements()) {       // Is there a next element?
    String val = e.nextElement();   // Return next element
    System.out.println(val);
}
```

### Limitations:
- ❌ **Forward direction** only
- ❌ Cannot **remove elements** during traversal
- ❌ Works only with **legacy classes** (not with ArrayList, HashSet, etc.)

---

## 3. Iterator (Universal Cursor)

**Iterator** was introduced in Java 1.2. It works with **any Collection type** (ArrayList, HashSet, LinkedList, TreeSet, etc.).

### How to Get Iterator:
```java
ArrayList<String> list = new ArrayList<>();
list.add("X"); list.add("Y"); list.add("Z");
Iterator<String> it = list.iterator(); // Collection interface method
```

### Methods (3 Methods):
```java
while (it.hasNext()) {          // Is there a next element?
    String val = it.next();     // Return next element

    if (val.equals("Y")) {
        it.remove();            // ✅ Safe removal during iteration!
    }
}
```

### Key Advantages over Enumeration:
- ✅ **Universal** — works with any Collection
- ✅ **Safe `remove()`** — can safely delete elements during iteration

### Limitations:
- ❌ **Forward direction** only
- ❌ Cannot **add or replace** elements during traversal (only remove)

---

## 4. ListIterator (Most Powerful Cursor)

**ListIterator** works only with **List** implementations (ArrayList, LinkedList, Vector). It supports **bidirectional traversal + modification**.

### How to Get ListIterator:
```java
ArrayList<String> list = new ArrayList<>(List.of("A", "B", "C", "D"));
ListIterator<String> lit = list.listIterator();       // Start from index 0
ListIterator<String> lit2 = list.listIterator(2);     // Start from index 2
```

### Methods (9 Methods — Most Rich Cursor):

#### Forward Traversal:
```java
while (lit.hasNext()) {
    int index = lit.nextIndex();    // Index of next element
    String val = lit.next();        // Return next element + move forward
}
```

#### Backward Traversal:
```java
while (lit.hasPrevious()) {
    int index = lit.previousIndex(); // Index of previous element
    String val = lit.previous();     // Return previous element + move backward
}
```

#### Modification During Iteration:
```java
ListIterator<String> lit = list.listIterator();
while (lit.hasNext()) {
    String val = lit.next();

    if (val.equals("B")) {
        lit.remove();          // ✅ Remove current element
    }
    if (val.equals("C")) {
        lit.set("C-MODIFIED"); // ✅ Replace current element!
    }
    if (val.equals("D")) {
        lit.add("NEW");        // ✅ Insert new element at current position!
    }
}
```

---

## 5. ⚖️ The Ultimate Comparison: Enumeration vs Iterator vs ListIterator

| Feature | Enumeration | Iterator | ListIterator |
|---------|-------------|----------|--------------|
| **Introduced** | Java 1.0 | Java 1.2 | Java 1.2 |
| **Works With** | Legacy only (Vector, Stack, Hashtable) | **Any Collection** (Universal) | **List only** (ArrayList, LinkedList, Vector) |
| **Direction** | Forward only ➡️ | Forward only ➡️ | **Bidirectional** ⬅️➡️ |
| **Read** | ✅ `nextElement()` | ✅ `next()` | ✅ `next()` + `previous()` |
| **Remove** | ❌ Not possible | ✅ `remove()` | ✅ `remove()` |
| **Add** | ❌ | ❌ | ✅ `add()` |
| **Replace** | ❌ | ❌ | ✅ `set()` |
| **Method Count** | 2 | 3 | 9 |
| **How to Get** | `vector.elements()` | `collection.iterator()` | `list.listIterator()` |

---

## 6. Fail-Fast vs Fail-Safe Iterators

### Fail-Fast (Default Behavior):
If the iterator detects that the collection was **structurally modified** during iteration (by another thread or via direct `list.add()`), it **immediately throws `ConcurrentModificationException`**.

```java
ArrayList<String> list = new ArrayList<>(List.of("A", "B", "C"));
Iterator<String> it = list.iterator();

while (it.hasNext()) {
    String s = it.next();
    list.remove(s);  // ❌ Direct modification → ConcurrentModificationException!
    // it.remove();  // ✅ Use iterator's remove() — this is safe
}
```

### Fail-Safe:
Iterators of `CopyOnWriteArrayList` and `ConcurrentHashMap` work on a **snapshot**, so modifications don't cause exceptions:

```java
CopyOnWriteArrayList<String> cowList = new CopyOnWriteArrayList<>(List.of("A", "B"));
for (String s : cowList) {
    cowList.add("NEW"); // ✅ No exception! Iterator traverses a snapshot
}
```

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| Is Enumeration universal? | ❌ No! It only works with legacy classes (Vector, Stack, Hashtable). |
| Does Iterator have an `add()` method? | ❌ No! Only `hasNext()`, `next()`, `remove()`. Use ListIterator for `add()`. |
| Will ListIterator work with HashSet? | ❌ No! ListIterator works only with `List` implementations. |
| Is direct `list.remove()` safe during iteration? | ❌ Can throw `ConcurrentModificationException`. Use **`iterator.remove()`** instead. |
| Who throws `ConcurrentModificationException`? | **Fail-Fast** iterators (those of ArrayList, HashSet, HashMap, etc.). |

---

[⬅️ Previous: Synchronized Collections](./05-synchronized-collections.md) · [📖 Back to Collections Index](./README.md) · [Next → Set & HashSet ➡️](./07-set-and-hashset.md)
