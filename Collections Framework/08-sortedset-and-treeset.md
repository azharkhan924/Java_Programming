# 🌳 SortedSet, NavigableSet & TreeSet

> **Summary:** SortedSet interface aur methods (`first()`, `last()`, `headSet()`, `tailSet()`, `subSet()`), NavigableSet extra methods, TreeSet constructors, Natural Sorting Order, null handling, aur TreeSet examples.

---

## 1. SortedSet Interface

> **SortedSet** = Ek `Set` jisme elements **sorted order** me store hote hain (either natural ordering ya custom Comparator ke through).

```text
SortedSet (I)  ← extends Set
    └── NavigableSet (I)  ← extends SortedSet (Java 6+)
         └── TreeSet (C)  ← Primary implementation
```

### Key Properties:
```text
❌ Duplicates NOT allowed (ye toh Set ka rule hai)
✅ Elements SORTED order me stored hote hain
❌ null generally NOT allowed (TreeSet me NullPointerException aata hai comparison ke dauran)
⚠️ Elements ko Comparable hona chahiye, ya fir Comparator provide karna padta hai
```

---

## 2. SortedSet — Important Methods

```java
SortedSet<Integer> ss = new TreeSet<>();
ss.add(50); ss.add(10); ss.add(30); ss.add(20); ss.add(40);
// Internal order: [10, 20, 30, 40, 50] (sorted!)

// 1. first() — Sabse chhota (lowest) element
ss.first();    // 10

// 2. last() — Sabse bada (highest) element
ss.last();     // 50

// 3. headSet(toElement) — toElement se PEHLE wale elements (exclusive!)
ss.headSet(30); // [10, 20] (30 included nahi hoga!)

// 4. tailSet(fromElement) — fromElement SE LEKAR aage wale (inclusive!)
ss.tailSet(30); // [30, 40, 50] (30 included hai!)

// 5. subSet(from, to) — Range: from (inclusive) to to (exclusive)
ss.subSet(20, 40); // [20, 30] (20 included, 40 NOT included!)

// 6. comparator() — Natural ordering ke liye null, custom comparator ke liye Comparator object
ss.comparator(); // null (natural ordering chal rahi hai)
```

### headSet, tailSet, subSet — Visual Memory:
```text
Elements: [10, 20, 30, 40, 50]

headSet(30):  [10, 20]           ← 30 se pehle sab (30 excluded)
tailSet(30):  [30, 40, 50]       ← 30 se sab (30 included!)
subSet(20,40):[20, 30]           ← 20 (included) to 40 (excluded)
```

---

## 3. Natural Sorting Order

Jab TreeSet me koi Comparator nahi diya jaata, toh elements **Natural Sorting Order** me store hote hain:

| Data Type | Natural Order |
|-----------|---------------|
| `Integer` / `int` | Ascending numeric: 1, 2, 3, 10, 100 |
| `String` | Alphabetical (lexicographic): "A", "B", "C", "a", "b" |
| `Double` | Ascending numeric: 1.1, 2.5, 3.0 |
| `Character` | Unicode value order: 'A' (65), 'B' (66), 'a' (97) |

> ⚠️ **Natural ordering ke liye class ko `Comparable` interface implement karna zaroori hai!** (`Integer`, `String`, `Double` already implement karte hain). Custom class me `compareTo()` override karo.

---

## 4. TreeSet Constructors

### Constructor 1: Default — `TreeSet()`
```java
TreeSet<Integer> ts = new TreeSet<>();
// Natural sorting order (ascending for numbers, alphabetical for strings)
```

### Constructor 2: Custom Comparator — `TreeSet(Comparator c)`
```java
TreeSet<Integer> ts = new TreeSet<>(Comparator.reverseOrder());
ts.add(10); ts.add(30); ts.add(20);
System.out.println(ts); // [30, 20, 10] ← Descending order!
```

### Constructor 3: From SortedSet — `TreeSet(SortedSet s)`
```java
TreeSet<Integer> ts2 = new TreeSet<>(existingSortedSet);
// Same ordering as source SortedSet
```

### Constructor 4: From Collection — `TreeSet(Collection c)`
```java
TreeSet<Integer> ts3 = new TreeSet<>(existingArrayList);
// Elements sorted in natural order
```

---

## 5. TreeSet — Important Rules & Gotchas

### Rule 1: Null Handling
```java
TreeSet<String> ts = new TreeSet<>();
ts.add("A");
ts.add(null); // ❌ NullPointerException! TreeSet tries to compare null → crash!
```

> **Exception:** Agar TreeSet empty hai aur first element null add karo (Java 6 me allowed tha), lekin **Java 7+ me first element bhi null add karne par NPE aata hai**.

### Rule 2: Homogeneous & Comparable Elements
```java
TreeSet ts = new TreeSet(); // Raw type (no generics)
ts.add("Hello");
ts.add(10);  // ❌ ClassCastException! String aur Integer compare nahi ho sakte!
```

> TreeSet internally `compareTo()` ya Comparator ka `compare()` call karta hai. Agar types incompatible hain toh `ClassCastException` throw hota hai.

### Rule 3: Custom Objects must be Comparable
```java
class Student {
    int roll;
    Student(int roll) { this.roll = roll; }
}

TreeSet<Student> ts = new TreeSet<>();
ts.add(new Student(3));
ts.add(new Student(1)); // ❌ ClassCastException! Student is not Comparable!
```

**Fix:** Ya toh `Student implements Comparable<Student>` karo, ya constructor me `Comparator` do.

---

## 6. TreeSet Complete Example

```java
TreeSet<Integer> ts = new TreeSet<>();
ts.add(50);
ts.add(10);
ts.add(30);
ts.add(20);
ts.add(40);
ts.add(10); // Duplicate — ignored!

System.out.println(ts);          // [10, 20, 30, 40, 50]
System.out.println(ts.first());  // 10
System.out.println(ts.last());   // 50
System.out.println(ts.headSet(30)); // [10, 20]
System.out.println(ts.tailSet(30)); // [30, 40, 50]
System.out.println(ts.subSet(20, 40)); // [20, 30]
```

---

## 7. ⚖️ HashSet vs LinkedHashSet vs TreeSet

| Feature | HashSet | LinkedHashSet | TreeSet |
|---------|---------|---------------|---------|
| **Order** | ❌ No order | ✅ Insertion order | ✅ Sorted order |
| **Internal DS** | HashMap | HashMap + Linked List | Red-Black Tree (Self-Balancing BST) |
| **Duplicates** | ❌ No | ❌ No | ❌ No |
| **Null?** | ✅ 1 null allowed | ✅ 1 null allowed | ❌ **NOT allowed** (NPE!) |
| **Performance (add/remove/contains)** | O(1) average | O(1) average | O(log n) |
| **Comparable Required?** | ❌ No | ❌ No | ✅ Yes (ya Comparator do) |

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| TreeSet me null dal sakte hain? | ❌ **NullPointerException** aayega (Java 7+). |
| `headSet(30)` me 30 included hota hai? | ❌ **Nahi!** headSet exclusive hai. `headSet(30, true)` se inclusive banao. |
| `tailSet(30)` me 30 included hota hai? | ✅ **Haan!** tailSet by default inclusive hai. |
| TreeSet ka internal data structure kya hai? | **Red-Black Tree** (self-balancing Binary Search Tree). |
| Heterogeneous elements TreeSet me store ho sakte hain? | ❌ Generally nahi. `ClassCastException` aayega agar types comparable nahi hain. |
| TreeSet me duplicate detection `hashCode()` se hoti hai? | ❌ **Nahi!** TreeSet `compareTo()` ya `compare()` ka result `0` hone se duplicate detect karta hai. |

---

[⬅️ Previous: Set & HashSet](./07-set-and-hashset.md) · [📖 Back to Collections Index](./README.md) · [Next → Comparable & Comparator ➡️](./09-comparable-and-comparator.md)
