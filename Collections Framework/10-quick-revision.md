# ⚡ Collections Framework — Master Quick Revision & Cheat Sheet

> **Summary:** Ek single master cheat sheet jisme Collection hierarchy, saare comparison tables, rapid-fire interview questions aur sorting rules consolidated hain.

---

## 1. 📊 Topic-Wise Summary Matrix

| # | Topic | Core Concept | Golden Rule |
|---|-------|--------------|-------------|
| **01** | **Framework Intro** | Array limitations → Collection as solution | `Collection` = Interface, `Collections` = Utility Class. Map is NOT part of Collection hierarchy. |
| **02** | **ArrayList** | Resizable array, RandomAccess | Default capacity 10, growth = `(old*3/2)+1`. NOT synchronized. |
| **03** | **Vector & Stack** | Synchronized resizable array + LIFO stack | Vector methods all synchronized, capacity doubles. Stack extends Vector. |
| **04** | **LinkedList** | Doubly Linked List, List + Deque | No RandomAccess. Best for frequent insert/delete at ends. O(n) index access. |
| **05** | **Synchronized Collections** | Thread-safe wrappers | `Collections.synchronizedList()` or `CopyOnWriteArrayList`. Manual sync needed for iteration! |
| **06** | **Cursors** | Enumeration → Iterator → ListIterator | Enumeration: legacy only. Iterator: universal + remove. ListIterator: bidirectional + add/set/remove. |
| **07** | **Set & HashSet** | No duplicates, HashMap-backed | Duplicate detection: `hashCode()` first, then `equals()`. Both must be overridden together! |
| **08** | **SortedSet & TreeSet** | Sorted Set, Red-Black Tree | No null allowed (NPE). Elements must be Comparable or provide Comparator. |
| **09** | **Comparable & Comparator** | Natural vs Custom sorting | `Comparable` = 1 natural order (`compareTo`). `Comparator` = unlimited custom orders (`compare`). |

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

| Feature | ArrayList | LinkedList | Vector |
|---------|-----------|------------|--------|
| **Internal DS** | Resizable Array | Doubly Linked List | Resizable Array |
| **RandomAccess** | ✅ O(1) | ❌ O(n) | ✅ O(1) |
| **Insert/Delete at Beginning** | O(n) shift | O(1) pointer update | O(n) shift |
| **Thread Safety** | ❌ No | ❌ No | ✅ Yes (synchronized) |
| **Capacity Growth** | `(old*3/2)+1` | N/A (no capacity) | `old * 2` |
| **Implements Deque?** | ❌ | ✅ | ❌ |

---

### B. HashSet vs LinkedHashSet vs TreeSet

| Feature | HashSet | LinkedHashSet | TreeSet |
|---------|---------|---------------|---------|
| **Order** | ❌ No order | ✅ Insertion order | ✅ Sorted order |
| **Internal DS** | HashMap | HashMap + Linked List | Red-Black Tree |
| **Null?** | ✅ 1 null | ✅ 1 null | ❌ NPE! |
| **Performance** | O(1) | O(1) | O(log n) |
| **Comparable Required?** | ❌ | ❌ | ✅ |
| **Duplicate Detection** | `hashCode()` + `equals()` | `hashCode()` + `equals()` | `compareTo()` / `compare()` returns 0 |

---

### C. Comparable vs Comparator

| Feature | `Comparable` | `Comparator` |
|---------|--------------|--------------|
| **Package** | `java.lang` | `java.util` |
| **Method** | `compareTo(T o)` | `compare(T o1, T o2)` |
| **Params** | 1 | 2 |
| **Sort Type** | Natural / Default | Custom / External |
| **Multiple Orders?** | ❌ 1 only | ✅ Unlimited |

---

### D. Enumeration vs Iterator vs ListIterator

| Feature | Enumeration | Iterator | ListIterator |
|---------|-------------|----------|--------------|
| **Direction** | Forward ➡️ | Forward ➡️ | Bidirectional ⬅️➡️ |
| **Works With** | Legacy (Vector) | Any Collection | List only |
| **Remove** | ❌ | ✅ | ✅ |
| **Add/Replace** | ❌ | ❌ | ✅ |
| **Methods** | 2 | 3 | 9 |

---

## 4. 🎯 Top 20 Rapid-Fire Interview Questions

1. **ArrayList ka default capacity?** → **10**
2. **HashSet ka default capacity?** → **16** (load factor 0.75)
3. **ArrayList capacity growth formula?** → `(old * 3/2) + 1`
4. **Vector capacity growth?** → Double (`old * 2`)
5. **TreeSet me null add kar sakte hain?** → ❌ **NullPointerException!**
6. **HashSet internally kya use karta hai?** → **HashMap** (element as key, dummy as value)
7. **LinkedList `RandomAccess` implement karta hai?** → ❌ **Nahi!** Index access O(n) hai.
8. **`Collection` aur `Collections` me farq?** → Interface vs Utility Class
9. **`Map` kya `Collection` ka child hai?** → ❌ **Nahi!** Alag hierarchy hai.
10. **`StringBuffer` ko TreeSet me seedha sort kar sakte hain?** → ❌ **ClassCastException!** (Not Comparable)
11. **`compare()` 0 return kare toh TreeSet kya karega?** → **Duplicate** maan kar reject karega!
12. **Iterator me `add()` method hota hai?** → ❌ **Nahi!** Sirf `ListIterator` me hai.
13. **Fail-Fast iterator kab exception throw karta hai?** → Jab **structural modification** ho iteration ke dauran.
14. **`CopyOnWriteArrayList` kab use karna chahiye?** → **Read-heavy, write-rare** scenarios me.
15. **`synchronizedList()` me iteration safe hai?** → ❌ Manual `synchronized` block chahiye!
16. **Stack extends kya karta hai?** → **`Vector`**
17. **`equals()` override kiya bina `hashCode()` ke — kya HashSet duplicates detect karega?** → ❌ **Nahi!**
18. **Natural sorting order set karne ke liye konsa interface implement karna padta hai?** → **`Comparable`** (`compareTo()` override karo)
19. **`headSet(30)` me 30 included hota hai?** → ❌ **Nahi!** Exclusive hai.
20. **`tailSet(30)` me 30 included hota hai?** → ✅ **Haan!** Inclusive hai.

---

[⬅️ Previous: Comparable & Comparator](./09-comparable-and-comparator.md) · [📖 Back to Collections Index](./README.md) · [🏠 Home / Root README](../README.md)
