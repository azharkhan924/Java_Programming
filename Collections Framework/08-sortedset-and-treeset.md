# 🌳 SortedSet, NavigableSet & TreeSet

> **Summary:** `SortedSet` interface and navigation methods (`first()`, `last()`, `headSet()`, `tailSet()`, `subSet()`), `NavigableSet` enhancements, `TreeSet` constructors, Natural Sorting Order, null handling rules, and complete code examples.

---

## 1. SortedSet Interface

> **SortedSet** is a child interface of `Set` where elements are automatically maintained in **sorted order** (either according to natural ordering or a custom `Comparator`).

```text
Collection (I)
    └── Set (I)
         └── SortedSet (I)           [Java 1.2]
              └── NavigableSet (I)   [Java 1.6]
                   └── TreeSet (C)   [Java 1.2]
```

### Key Properties:
```text
❌ Duplicates NOT allowed (enforced by Set contract)
✅ Elements stored in SORTED order (ascending/custom)
❌ null generally NOT allowed (TreeSet throws NullPointerException on comparison)
⚠️ Elements must be mutually Comparable, or a custom Comparator must be provided
```

---

## 2. SortedSet — Core Methods

Consider a `SortedSet` initialized with elements:

```java
SortedSet<Integer> ss = new TreeSet<>();
ss.add(50);
ss.add(10);
ss.add(30);
ss.add(20);
ss.add(40);
// Internally sorted: [10, 20, 30, 40, 50]
```

### Method Summary:

| Method | Description | Return Value for `[10, 20, 30, 40, 50]` |
|--------|-------------|----------------------------------------|
| `first()` | Returns the first (lowest) element | `10` |
| `last()` | Returns the last (highest) element | `50` |
| `headSet(toElement)` | Returns elements strictly less than `toElement` (exclusive) | `ss.headSet(30)` → `[10, 20]` |
| `tailSet(fromElement)` | Returns elements greater than or equal to `fromElement` (inclusive) | `ss.tailSet(30)` → `[30, 40, 50]` |
| `subSet(from, to)` | Range view: `from` (inclusive) to `to` (exclusive) | `ss.subSet(20, 40)` → `[20, 30]` |
| `comparator()` | Returns the `Comparator` used for ordering, or `null` for natural order | `null` |

### Visual Memory — Subsets:
```text
Elements: [10, 20, 30, 40, 50]

headSet(30):    [10, 20]           ← strictly less than 30 (30 excluded)
tailSet(30):    [30, 40, 50]       ← 30 and higher (30 included)
subSet(20, 40): [20, 30]           ← 20 (included) to 40 (excluded)
```

---

## 3. NavigableSet Interface (Java 1.6+)

`NavigableSet` extends `SortedSet` and provides rich navigation and retrieval methods:

| Method | Behavior | Example on `[10, 20, 30, 40, 50]` |
|--------|----------|-----------------------------------|
| `lower(e)` | Highest element strictly `< e` | `ns.lower(30)` → `20` |
| `floor(e)` | Highest element `<= e` | `ns.floor(30)` → `30` |
| `ceiling(e)` | Lowest element `>= e` | `ns.ceiling(30)` → `30` |
| `higher(e)` | Lowest element strictly `> e` | `ns.higher(30)` → `40` |
| `pollFirst()` | Retrieves and removes the lowest element | Returns `10` |
| `pollLast()` | Retrieves and removes the highest element | Returns `50` |
| `descendingSet()` | Returns a reverse-order view of the set | `[50, 40, 30, 20, 10]` |
| `descendingIterator()` | Iterator in descending order | Traverses high to low |

---

## 4. Natural Sorting Order

When no `Comparator` is provided, `TreeSet` relies on the **natural sorting order** defined by `Comparable`:

| Data Type | Natural Order Criteria | Example |
|-----------|------------------------|---------|
| `Integer` / numeric wrappers | Ascending numerical value | `1, 2, 10, 20, 50` |
| `String` | Lexicographical / Unicode order | `"A", "B", "Z", "a", "b"` |
| `Character` | Unicode integer value | `'A' (65), 'B' (66), 'a' (97)` |

> ⚠️ For natural sorting order to work, the elements' class **must implement `Comparable`**. Wrapper classes and `String` already implement it. For custom classes, you must implement `Comparable` or provide an explicit `Comparator`.

---

## 5. TreeSet Constructors

### 1. `TreeSet()`
Creates an empty `TreeSet` sorted according to natural ordering.
```java
TreeSet<Integer> ts = new TreeSet<>();
```

### 2. `TreeSet(Comparator<? super E> comparator)`
Creates an empty `TreeSet` sorted according to the provided `Comparator`.
```java
TreeSet<Integer> ts = new TreeSet<>(Comparator.reverseOrder());
ts.add(10); ts.add(30); ts.add(20);
System.out.println(ts); // [30, 20, 10] (descending order)
```

### 3. `TreeSet(SortedSet<E> s)`
Creates a `TreeSet` containing the elements and ordering of the given `SortedSet`.
```java
TreeSet<Integer> ts = new TreeSet<>(existingSortedSet);
```

### 4. `TreeSet(Collection<? extends E> c)`
Creates a `TreeSet` containing the elements of the given collection, sorted by natural order.
```java
List<Integer> list = List.of(40, 10, 30, 20);
TreeSet<Integer> ts = new TreeSet<>(list); // [10, 20, 30, 40]
```

---

## 6. TreeSet — Important Rules & Gotchas

### Rule 1: Null Handling (Strict Prohibition in Java 7+)
```java
TreeSet<String> ts = new TreeSet<>();
ts.add("Apple");
ts.add(null); // ❌ Throws NullPointerException!
```
- In Java 6 and earlier, an empty `TreeSet` allowed a single `null` insertion.
- In **Java 7+**, inserting `null` into a `TreeSet` using natural ordering **always throws `NullPointerException`**, even if the set is empty, because `null` cannot be compared to any object.

### Rule 2: Mutually Comparable / Homogeneous Elements
```java
TreeSet ts = new TreeSet(); // Raw type without generics
ts.add("Hello");
ts.add(10); // ❌ Throws ClassCastException! String cannot be compared to Integer!
```
- `TreeSet` invokes `compareTo()` or `compare()`. If elements cannot be mutually compared, a runtime `ClassCastException` occurs.

### Rule 3: Custom Classes Require `Comparable` or `Comparator`
```java
class Student {
    int rollNo;
    Student(int rollNo) { this.rollNo = rollNo; }
}

TreeSet<Student> ts = new TreeSet<>();
ts.add(new Student(101));
ts.add(new Student(102)); // ❌ ClassCastException: Student cannot be cast to Comparable!
```
**Solution:**
1. Either implement `Comparable<Student>` in `Student`:
   ```java
   class Student implements Comparable<Student> {
       int rollNo;
       Student(int rollNo) { this.rollNo = rollNo; }
       @Override public int compareTo(Student o) { return Integer.compare(this.rollNo, o.rollNo); }
   }
   ```
2. Or pass a `Comparator` into the `TreeSet` constructor:
   ```java
   TreeSet<Student> ts = new TreeSet<>(Comparator.comparingInt(s -> s.rollNo));
   ```

### Rule 4: `StringBuffer` & `StringBuilder` are Not Comparable
Neither `StringBuffer` nor `StringBuilder` implements `Comparable`. Adding them to `new TreeSet<>()` without an explicit `Comparator` causes a `ClassCastException`.

---

## 7. Complete Code Example

```java
import java.util.*;

public class TreeSetDemo {
    public static void main(String[] args) {
        SortedSet<Integer> set = new TreeSet<>();
        set.add(50);
        set.add(10);
        set.add(30);
        set.add(20);
        set.add(40);
        set.add(10); // Duplicate ignored

        System.out.println("Set: " + set);             // [10, 20, 30, 40, 50]
        System.out.println("First: " + set.first());    // 10
        System.out.println("Last: " + set.last());      // 50
        System.out.println("HeadSet(30): " + set.headSet(30)); // [10, 20]
        System.out.println("TailSet(30): " + set.tailSet(30)); // [30, 40, 50]
        System.out.println("SubSet(20, 40): " + set.subSet(20, 40)); // [20, 30]

        // Custom comparator for descending order
        TreeSet<String> descNames = new TreeSet<>(Comparator.reverseOrder());
        descNames.add("Alice");
        descNames.add("Charlie");
        descNames.add("Bob");
        System.out.println("Descending: " + descNames); // [Charlie, Bob, Alice]
    }
}
```

---

## 8. ⚖️ HashSet vs LinkedHashSet vs TreeSet

| Feature | `HashSet` | `LinkedHashSet` | `TreeSet` |
|---------|-----------|-----------------|-----------|
| **Ordering** | ❌ None (random hash order) | ✅ Insertion order | ✅ Sorted order (Natural/Comparator) |
| **Data Structure** | Hash Table (`HashMap`) | Hash Table + Doubly Linked List | Red-Black Tree (`TreeMap`) |
| **Duplicates** | ❌ Not allowed | ❌ Not allowed | ❌ Not allowed |
| **Null Acceptance** | ✅ Up to 1 `null` allowed | ✅ Up to 1 `null` allowed | ❌ **NOT allowed** (throws NPE) |
| **Time Complexity** | `O(1)` average | `O(1)` average | `O(log n)` guaranteed |
| **Comparable Required?** | ❌ No | ❌ No | ✅ Yes (or explicit `Comparator`) |
| **Introduced In** | Java 1.2 | Java 1.4 | Java 1.2 |

---

## 🧠 Interview Quick Traps

| Question / Trap | Correct Answer |
|-----------------|----------------|
| Can we insert `null` into a `TreeSet`? | ❌ **No.** In Java 7+, inserting `null` into a `TreeSet` with natural ordering throws `NullPointerException`. |
| Does `headSet(30)` include `30`? | ❌ **No.** `headSet(toElement)` is strictly exclusive. In `NavigableSet`, use `headSet(30, true)` for inclusive. |
| Does `tailSet(30)` include `30`? | ✅ **Yes.** `tailSet(fromElement)` is inclusive by default. |
| What is the underlying data structure of `TreeSet`? | A **Red-Black Tree** (self-balancing binary search tree), backed by `TreeMap`. |
| Can heterogeneous objects be added to a `TreeSet`? | ❌ **No.** Runtime `ClassCastException` occurs because elements must be mutually comparable. |
| How does `TreeSet` detect duplicate elements? | By comparing via `compareTo()` or `compare()`. If it returns `0`, the element is treated as a duplicate (does NOT use `equals()` or `hashCode()`). |

---

[⬅️ Previous: Set & HashSet](./07-set-and-hashset.md) · [📖 Back to Collections Index](./README.md) · [Next → Comparable & Comparator ➡️](./09-comparable-and-comparator.md)
