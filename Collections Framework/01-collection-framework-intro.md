# 🏗️ Collection Framework — Introduction & Hierarchy

> **Summary:** Why collections are needed, Array limitations, Array vs Collection comparison, `Collection` interface vs `Collections` utility class, aur complete Collection Framework hierarchy.

---

## 1. Why Do We Need Collections?

Agar humein 10,000 students ka data store karna ho:

```java
// ❌ Bad Practice — 10,000 alag variables!
int s1 = 101, s2 = 102, s3 = 103; // ... aur 9997 aur!
```

### Solution 1: Arrays
```java
Student[] students = new Student[10000]; // ✅ Single variable se 10k values!
```

**But arrays ke bhi serious limitations hain...**

---

## 2. Array ki Limitations

### ❌ Limitation 1: Fixed Size
```java
int[] arr = new int[5]; // Size = 5, ek baar declare kiya toh change nahi hoga!
// Agar 6th element add karna ho toh? → Naya bada array banana padega + purana copy karna padega 🤦
```

### ❌ Limitation 2: Normally Homogeneous Data
```java
int[] arr = {10, 20, 30}; // Sirf integers hi dal sakte ho
// arr[0] = "Hello"; → ❌ Compile Error!
```

> **Exception:** `Object[]` array me mixed types rakh sakte ho kyunki sab classes Object ki child hain:
> ```java
> Object[] arr = {new Student(), "Azhar", 100, 3.14};
> ```
> Lekin ye **type-safety kho deta hai** aur casting ki zaroorat padti hai.

### ❌ Limitation 3: No Built-in Data Structure Methods
Arrays ke paas sorting, searching, insertion, deletion ke liye **koi ready-made collection methods nahi** hote. Sab manually likhna padta hai.

---

## 3. Array vs Collection — The Big Picture

| Feature | Array | Collection |
|---------|-------|------------|
| **Size** | Fixed (create karte waqt decide hota hai) | Dynamic / Growable (runtime par badh/ghat sakta hai) |
| **Data Type** | Normally homogeneous | Heterogeneous objects bhi rakh sakte hain |
| **Primitive Support** | ✅ Directly store kar sakte hain (`int[]`, `double[]`) | ❌ Sirf objects store hote hain (primitives Autoboxing se `Integer`, `Double` ban jaate hain) |
| **Memory Overhead** | Kam (contiguous block) | Zyada (internal nodes, pointers, metadata) |
| **Raw Performance** | Fast direct indexed access | Thoda overhead (depends on implementation) |
| **Ready-Made Methods** | Bahut limited (`Arrays.sort()`, `Arrays.binarySearch()`) | Rich API — `add()`, `remove()`, `contains()`, `sort()`, `stream()`, etc. |
| **Flexibility** | Less flexible | Highly flexible |
| **Underlying Data Structure** | Simple contiguous memory block | List, Set, Queue, Map — har ek optimized data structure par based |

> **Rule of Thumb:** Agar size fixed hai aur primitives hain → Array. Baaki sab cases → Collection!

---

## 4. What is a Collection?

> **Definition:** Collection ek **group of individual objects ko ek single entity ke roop me represent** karna hai.

```java
List<String> names = new ArrayList<>();
names.add("Azhar");
names.add("Rahul");
names.add("Sara");
// 3 alag String objects → 1 single List object me!
```

---

## 5. What is Collection Framework?

> **Definition:** Collection Framework ek **standard architecture** hai jo interfaces aur classes ka set provide karta hai taaki objects ke groups ko efficiently **store, retrieve, manipulate aur iterate** kiya ja sake.

Ready-made operations milte hain:
- Adding & Removing elements
- Searching & Sorting
- Iterating (for-each, Iterator, Stream)
- Size checking, Filtering, Transforming

| Java | C++ Equivalent |
|------|----------------|
| Collection Framework | STL (Standard Template Library) |

---

## 6. ⚠️ Collection vs Collections (Interview Trap!)

Ye do alag cheezein hain — interviewers hamesha confuse karne ki koshish karte hain:

| Feature | `Collection` (Interface) | `Collections` (Utility Class) |
|---------|--------------------------|-------------------------------|
| **Type** | `interface` | `class` (with all `static` methods) |
| **Package** | `java.util.Collection` | `java.util.Collections` |
| **Purpose** | Root interface — represents a group of objects | Helper utility methods for collection operations |
| **Example** | `Collection<String> c = new ArrayList<>();` | `Collections.sort(list);` `Collections.reverse(list);` |

```java
// Collection — Interface (ye hai KYA store karna hai)
Collection<Integer> nums = new ArrayList<>();

// Collections — Utility Class (ye hai KAISE operate karna hai)
Collections.sort(nums);
Collections.shuffle(nums);
Collections.max(nums);
Collections.unmodifiableList(nums);
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
          ArrayList  LL  Vector HashSet  SortedSet (I)    ← "LL" = LinkedList
                      |      |       |         |
                      |    Stack  LHS    NavigableSet (I)
                      |                        |
                      |                     TreeSet
                      └──── implements Deque too

  ┌─────────────── MAP (I) ── (Separate hierarchy, NOT a child of Collection!) ────┐
  │                                                                                 │
  │   Map (I)                                                                       │
  │    ├── HashMap                                                                  │
  │    │    └── LinkedHashMap                                                       │
  │    ├── Hashtable                                                                │
  │    │    └── Properties                                                          │
  │    ├── SortedMap (I)                                                            │
  │    │    └── NavigableMap (I)                                                    │
  │    │         └── TreeMap                                                        │
  │    └── ConcurrentHashMap                                                        │
  └─────────────────────────────────────────────────────────────────────────────────┘
```

> **Key Interfaces:** `(I)` = Interface | Classes bina bracket ke hain  
> **LHS** = LinkedHashSet | **LL** = LinkedList

---

## 8. Core Collection Interfaces at a Glance

| Interface | Duplicates? | Ordered? | Sorted? | Key Feature |
|-----------|-------------|----------|---------|-------------|
| **List** | ✅ Yes | ✅ Insertion order preserved | ❌ No | Index-based access |
| **Set** | ❌ No | ❌ Not guaranteed (HashSet) | ❌ No | Uniqueness enforced |
| **SortedSet** | ❌ No | ✅ Sorted order | ✅ Yes | Natural/custom sorting |
| **Queue** | ✅ Yes | ✅ FIFO order | ❌ No | First-In-First-Out |
| **Deque** | ✅ Yes | ✅ Both ends | ❌ No | Double-ended operations |
| **Map** | Keys: ❌ / Values: ✅ | Depends on implementation | ❌ No | Key-Value pairs |

---

## 9. List Interface — Quick Overview

> **List** = Duplicates allowed + Insertion order preserved + Index-based access.

```java
List<String> list = new ArrayList<>();
list.add("A");
list.add("B");
list.add("A"); // ✅ Duplicate allowed!
System.out.println(list); // [A, B, A] → Insertion order maintained!
System.out.println(list.get(0)); // A → Index-based retrieval!
```

### List Implementations:
```text
List (I)
 ├── ArrayList      → Resizable array, fast random access, not synchronized
 ├── LinkedList     → Doubly-linked list, fast insert/delete, implements Deque too
 └── Vector         → Synchronized (thread-safe) resizable array (legacy)
      └── Stack     → LIFO structure, extends Vector
```

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| `Map` kya `Collection` ka child hai? | ❌ **Nahi!** Map alag interface hai, Collection hierarchy se bahar hai. |
| `Collection` aur `Collections` me farq? | `Collection` = Interface, `Collections` = Utility class with static methods. |
| Array me directly primitive store ho sakta hai, Collection me? | Collection me **nahi** — Autoboxing se wrapper objects (`Integer`, `Double`) store hote hain. |
| Collection Framework ka root interface konsa hai? | `Iterable` technically root hai, `Collection` uska child hai. |
| `List` me duplicate allowed hai? | ✅ Yes! `Set` me nahi allowed. |

---

[📖 Back to Collections Index](./README.md) · [Next → ArrayList ➡️](./02-arraylist.md)
