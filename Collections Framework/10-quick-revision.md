# ⚡ Collections Framework — Master Quick Revision & Cheat Sheet

> **Summary:** Consolidated master cheat sheet featuring the complete Java Collection hierarchy, key comparison tables, essential formulas, and top 20 rapid-fire interview Q&As.

---

## 1. 📊 Topic-Wise Summary Matrix

| # | Topic | Core Concept | Golden Rule |
|---|-------|--------------|-------------|
| **01** | **Framework Intro** | Array limitations → Collection framework | `Collection` is an interface; `Collections` is a utility class. `Map` is outside `Collection`. |
| **02** | **ArrayList** | Resizable array, fast index access | Initial capacity 10, growth formula `(old * 3 / 2) + 1`. Not thread-safe. |
| **03** | **Vector & Stack** | Legacy synchronized array & LIFO stack | All methods synchronized; capacity doubles. `Stack` extends `Vector`. |
| **04** | **LinkedList** | Doubly linked list, implements `List` & `Deque` | No `RandomAccess`. Constant time `O(1)` insert/delete at ends. `O(n)` random lookup. |
| **05** | **Synchronized Collections** | Thread-safe wrappers & concurrent alternatives | `Collections.synchronizedList()` needs manual sync for iteration; use `CopyOnWriteArrayList` for read-heavy flows. |
| **06** | **Cursors** | `Enumeration` → `Iterator` → `ListIterator` | `Enumeration`: legacy read-only. `Iterator`: universal + remove. `ListIterator`: bidirectional + add/set/remove. |
| **07** | **Set & HashSet** | Unique elements, backed by `HashMap` | Duplicate detection relies on both `hashCode()` and `equals()`. Neither may be violated. |
| **08** | **SortedSet & TreeSet** | Ordered set backed by Red-Black Tree | No `null` allowed in Java 7+ (throws NPE). Elements must be mutually comparable. |
| **09** | **Comparable & Comparator** | Natural vs custom sorting strategies | `Comparable`: 1 natural order via `compareTo()`. `Comparator`: multiple custom orders via `compare()`. |

---

## 2. Collection Hierarchy — At-a-Glance

```text
Iterable (I)
  └── Collection (I)
       ├── List (I) ──── ArrayList, LinkedList, Vector → Stack
       ├── Set (I) ───── HashSet → LinkedHashSet
       │                 SortedSet (I) → NavigableSet (I) → TreeSet
       └── Queue (I) ── Deque (I) → ArrayDeque, LinkedList

Map (I) ── HashMap → LinkedHashMap
            Hashtable → Properties
            SortedMap (I) → NavigableMap (I) → TreeMap
```

---

## 3. ⚖️ Crucial Comparison Tables

### A. ArrayList vs LinkedList vs Vector

| Feature | `ArrayList` | `LinkedList` | `Vector` |
|---------|-------------|--------------|----------|
| **Internal Data Structure** | Resizable Array | Doubly Linked List | Resizable Array |
| **`RandomAccess` Support** | ✅ `O(1)` | ❌ `O(n)` | ✅ `O(1)` |
| **Insert / Delete at Head** | `O(n)` shift required | `O(1)` pointer update | `O(n)` shift required |
| **Thread Safety** | ❌ No (unsynchronized) | ❌ No (unsynchronized) | ✅ Yes (all methods synchronized) |
| **Growth Policy** | `(old * 3 / 2) + 1` | N/A (node-based) | `old * 2` (doubles) |
| **Implements `Deque`?** | ❌ No | ✅ Yes | ❌ No |

---

### B. HashSet vs LinkedHashSet vs TreeSet

| Feature | `HashSet` | `LinkedHashSet` | `TreeSet` |
|---------|-----------|-----------------|-----------|
| **Order Maintained** | ❌ None | ✅ Insertion order | ✅ Sorted order |
| **Underlying Structure** | `HashMap` | `HashMap` + Doubly Linked List | Red-Black Tree (`TreeMap`) |
| **Null Acceptance** | ✅ Up to 1 `null` | ✅ Up to 1 `null` | ❌ **NOT allowed** (throws NPE) |
| **Time Complexity** | `O(1)` average | `O(1)` average | `O(log n)` guaranteed |
| **Comparable Required?** | ❌ No | ❌ No | ✅ Yes (or pass `Comparator`) |
| **Duplicate Detection** | `hashCode()` then `equals()` | `hashCode()` then `equals()` | `compareTo()` / `compare() == 0` |

---

### C. Comparable vs Comparator

| Feature | `Comparable` | `Comparator` |
|---------|--------------|--------------|
| **Package** | `java.lang` | `java.util` |
| **Method** | `compareTo(T o)` | `compare(T o1, T o2)` |
| **Parameter Count** | 1 (`this` vs parameter) | 2 explicit arguments |
| **Sorting Intent** | Natural / Default sequence | Custom / Alternative sequences |
| **Code Location** | Defined inside domain class | Separate classes, lambdas, or factories |
| **Flexibility** | Exactly 1 sorting strategy | Unlimited sorting strategies |

---

### D. Enumeration vs Iterator vs ListIterator

| Feature | `Enumeration` | `Iterator` | `ListIterator` |
|---------|---------------|------------|----------------|
| **Direction** | Forward only ➡️ | Forward only ➡️ | Bidirectional ⬅️➡️ |
| **Scope** | Legacy classes (`Vector`, `Hashtable`) | Universal across all `Collection`s | `List` implementations only |
| **Element Removal** | ❌ No | ✅ Yes (`remove()`) | ✅ Yes (`remove()`) |
| **Element Modification** | ❌ No | ❌ No | ✅ Yes (`set()`, `add()`) |
| **Total Methods** | 2 | 3 (4 with `forEachRemaining`) | 9 |

---

## 4. 🎯 Top 20 Rapid-Fire Interview Questions

1. **What is the default initial capacity of an `ArrayList`?**  
   → **10** (allocated upon the first element insertion).

2. **What is the default initial capacity and load factor of a `HashSet`?**  
   → Initial capacity is **16**, and load factor is **0.75**.

3. **What is the capacity growth formula of `ArrayList`?**  
   → `newCapacity = (currentCapacity * 3 / 2) + 1` (or `currentCapacity + (currentCapacity >> 1)` in modern JDKs).

4. **How does `Vector` grow when its capacity is exceeded?**  
   → It **doubles** its capacity (`currentCapacity * 2`).

5. **Can we insert `null` into a `TreeSet`?**  
   → ❌ **No.** In Java 7+, inserting `null` into a `TreeSet` using natural ordering throws `NullPointerException`.

6. **What is the underlying data structure of `HashSet`?**  
   → Internally backed by a **`HashMap`**, where elements are keys and a dummy `PRESENT` constant is the value.

7. **Does `LinkedList` implement `RandomAccess`?**  
   → ❌ **No.** Traversing by index requires sequential `O(n)` pointer dereferencing.

8. **What is the difference between `Collection` and `Collections`?**  
   → `Collection` is a root interface; `Collections` is a utility class containing static helper algorithms (`sort`, `binarySearch`, `reverse`).

9. **Is `Map` a child interface of `Collection`?**  
   → ❌ **No.** `Map` defines key-value mappings and belongs to a completely separate hierarchy.

10. **Can `StringBuffer` be sorted directly in a default `TreeSet`?**  
    → ❌ **No.** Throws `ClassCastException` at runtime because `StringBuffer` does not implement `Comparable`.

11. **What happens if a custom `Comparator` always returns `0` in a `TreeSet`?**  
    → Only the **first added element** is preserved; all subsequent elements are discarded as duplicates.

12. **Does `Iterator` support an `add()` method?**  
    → ❌ **No.** Only `ListIterator` supports adding new elements during traversal.

13. **When does a fail-fast iterator throw `ConcurrentModificationException`?**  
    → When the collection is structurally modified during iteration through any mechanism other than the iterator's own `remove()` method.

14. **When should `CopyOnWriteArrayList` be preferred over `Collections.synchronizedList()`?**  
    → In **read-heavy, write-rare** multi-threaded scenarios where locks on read operations would severely bottleneck performance.

15. **Is iterating over `Collections.synchronizedList()` intrinsically thread-safe?**  
    → ❌ **No.** You must explicitly enclose the iteration loop within a `synchronized (list)` block to prevent concurrent structural modifications.

16. **Which class does `Stack` extend in the JDK?**  
    → It extends **`Vector`** (a legacy inheritance choice; modern applications prefer `Deque` / `ArrayDeque`).

17. **If you override `equals()` without overriding `hashCode()`, will `HashSet` work properly?**  
    → ❌ **No.** Objects that are equal will produce different hash codes and land in separate buckets, breaking duplicate prevention.

18. **Which interface must be implemented to establish default natural ordering?**  
    → **`java.lang.Comparable`** by overriding `compareTo()`.

19. **Does `headSet(30)` in `SortedSet` include the element `30`?**  
    → ❌ **No.** `headSet(toElement)` is strictly exclusive of the bound.

20. **Does `tailSet(30)` in `SortedSet` include the element `30`?**  
    → ✅ **Yes.** `tailSet(fromElement)` is inclusive of the specified element.

---

[⬅️ Previous: Comparable & Comparator](./09-comparable-and-comparator.md) · [📖 Back to Collections Index](./README.md) · [🏠 Home / Root README](../README.md)
