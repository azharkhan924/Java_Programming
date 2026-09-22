# Collection Framework — Introduction & Hierarchy

> **Summary:** Why collections are needed, Array limitations, Array vs Collection comparison, `Collection` interface vs `Collections` utility class, and the complete Collection Framework hierarchy.

---

## 1. Why Do We Need Collections?

If we have 10,000 values, creating 10,000 separate variables is a **bad / worst programming practice** because it makes the program difficult to manage and maintain.

### Solution: Arrays

```java
Student[] s = new Student[10000];
```

An array allows us to represent a **large number of values using a single variable**.

### Advantages of Arrays
- Represent a huge number of values using a single variable
- **Readability is improved**
- Elements can be accessed using an index

---

## 2. Array Limitations

### Limitation 1: Fixed Size

```java
int[] a = new int[5]; // Size = 5, once created it cannot be changed
```

The size cannot be directly increased or decreased after creation.

### Limitation 2: Homogeneous Data

Arrays normally store elements of the **same declared type**.

```java
int[] a = {10, 20, 30}; // All elements are of type int
```

> **Note:** An `Object[]` can hold objects of different classes because every class ultimately extends `Object`:
> ```java
> Object[] arr = { new Student(), new Customer(), "Azhar", 100 };
> ```

### Limitation 3: No Underlying Standard Data Structure

Arrays do not provide a rich set of predefined collection methods. For many requirements, we have to write the logic explicitly.

---

## 3. Array vs Collection

| Feature | Array | Collection |
|---------|-------|------------|
| **Size** | Fixed in size | Growable in nature |
| **Memory** | Generally less memory overhead | Generally more memory overhead |
| **Performance** | Generally better for simple indexed access | Generally more overhead than arrays |
| **Data Type** | Normally homogeneous | Can hold homogeneous and, where type permits, heterogeneous objects |
| **Underlying Structure** | No standard collection data structure | Implementations are based on data structures |
| **Ready-made Methods** | Very limited | Many predefined methods available |
| **Primitive Types** | Can hold primitives and objects | Hold objects, not primitives directly (autoboxing handles this) |
| **Flexibility** | Less flexible (fixed size) | More flexible (size can grow/shrink) |

> **Rule of Thumb:**
> - **Memory:** Arrays are generally preferred (lower overhead)
> - **Performance:** Arrays are generally preferred for direct indexed access
> - **Flexibility:** Collections are preferred (dynamic size)

---

## 4. What is a Collection?

> **Definition:** If we want to represent a **group of individual objects as a single entity**, then we should go for a collection.

```java
List<String> names = new ArrayList<>(); // Multiple String objects as one collection
```

---

## 5. What is the Collection Framework?

> **Definition:** The Collection Framework defines a **set of interfaces and classes** that provide a standard architecture for representing and manipulating groups of objects.

It provides ready-made data structures and methods for:
- Adding, removing, searching, sorting elements
- Iterating through elements
- Checking size

| Java | C++ |
|------|-----|
| Collection Framework | STL (Standard Template Library) |

---

## 6. Note: Collection vs Collections (Important Distinction!)

| Feature | `Collection` (Interface) | `Collections` (Utility Class) |
|---------|--------------------------|-------------------------------|
| **Type** | `interface` | `class` (with all `static` methods) |
| **Package** | `java.util.Collection` | `java.util.Collections` |
| **Purpose** | Root interface — represents a group of objects | Helper utility methods for collection operations |
| **Example** | `Collection<String> c = new ArrayList<>();` | `Collections.sort(list);` `Collections.reverse(list);` |

```text
Collection  → Interface
Collections → Utility class
```

---

## 7. Complete Collection Framework Hierarchy

```text
                            Iterable (I)
                               │
                          Collection (I)
                         /     |        \
                       /       |          \
                   List (I)   Set (I)    Queue (I)
                   /  |  \      |   \         |
                  /   |   \     |    \      Deque (I)
                 /    |    \    |     \       |
          ArrayList  LL  Vector HashSet  SortedSet (I)
                      |      |       |         |
                      |    Stack  LHS    NavigableSet (I)
                      |                        |
                      |                     TreeSet
                      └──── implements Deque too

  ┌─────────────── MAP (I) ── (NOT a child of Collection!) ────┐
  │   Map (I)                                                   │
  │    ├── HashMap → LinkedHashMap                              │
  │    ├── Hashtable → Properties                               │
  │    ├── SortedMap (I) → NavigableMap (I) → TreeMap           │
  │    └── ConcurrentHashMap                                    │
  └─────────────────────────────────────────────────────────────┘
```

> **(I)** = Interface | **LL** = LinkedList | **LHS** = LinkedHashSet
> **Important:** `Map` does **not** extend `Collection`.

---

## 8. Core Collection Interfaces at a Glance

| Interface | Duplicates? | Ordered? | Sorted? | Key Feature |
|-----------|-------------|----------|---------|-------------|
| **List** | Yes |  Insertion order preserved | No | Index-based access |
| **Set** | No | Not guaranteed (HashSet) | No | Uniqueness enforced |
| **SortedSet** | No |  Sorted order | Yes | Natural/custom sorting |
| **Queue** | Yes |  FIFO order | No | First-In-First-Out |
| **Deque** | Yes |  Both ends | No | Double-ended operations |
| **Map** | Keys:  / Values:  | Depends on impl | No | Key-Value pairs |

---

## 9. List Interface — Quick Overview

> **List** = Duplicates allowed + Insertion order preserved + Index-based access.

```java
List<String> list = new ArrayList<>();
list.add("A");
list.add("B");
list.add("A"); // valid Duplicate allowed!
System.out.println(list); // [A, B, A] → Insertion order maintained!
```

### Common List Implementations:
```text
List (I)
 ├── ArrayList      → Resizable array, fast random access, not synchronized
 ├── LinkedList     → Doubly-linked list, fast insert/delete, implements Deque too
 └── Vector         → Synchronized (thread-safe) resizable array (legacy)
      └── Stack     → LIFO structure, extends Vector
```

---

## Interview Quick Traps

| Trap | Answer |
|------|--------|
| Is `Map` a child of `Collection`? | No. Map is a separate interface, not part of the Collection hierarchy. |
| Difference between `Collection` and `Collections`? | `Collection` = Interface, `Collections` = Utility class with static methods. |
| Can arrays store primitives directly? Can collections? | Arrays: Yes. Collections: No — autoboxing converts to wrapper objects (`Integer`, `Double`). |
| What is the root interface of the Collection hierarchy? | `Iterable` is the root; `Collection` extends `Iterable`. |
| Are duplicates allowed in `List`? | Yes. In `Set`, no. |

---

[Back to Collections Index](./README.md) · [Next: ArrayList](./02-arraylist.md)
