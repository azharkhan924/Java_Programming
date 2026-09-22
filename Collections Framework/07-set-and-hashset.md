# Set Interface — HashSet & LinkedHashSet

> **Summary:** Set interface properties, HashSet internal working (HashMap-backed), `equals()` + `hashCode()` contract for duplicate detection, HashSet constructors and Load Factor, LinkedHashSet (insertion order preserved), and critical examples.

---

## 1. What is the Set Interface?

> **Set** = A collection that contains **no duplicate elements** and generally **no guaranteed insertion order** (implementation-dependent).

```text
Set Properties:
 Duplicates NOT allowed (if you try to add a duplicate, it is silently ignored — no error)
 Insertion order NOT guaranteed (in HashSet)
 At most ONE null element allowed (in HashSet)
 Based on the mathematical "Set" concept
```

### Set Hierarchy:
```text
Set (I)
 ├── HashSet (C) → Hashing-based, fastest, no order guarantee
 │ └── LinkedHashSet (C) → Hashing-based + Insertion order preserved
 ├── SortedSet (I)
 │ └── NavigableSet (I)
 │ └── TreeSet (C) → Sorted order (natural/custom), no nulls
 └── EnumSet (C) → Optimized for enum types
```

---

## 2. HashSet — How It Works Internally (MOST IMPORTANT!)

> **Secret:** HashSet internally uses a **`HashMap`**!

```java
// HashSet source code (simplified):
public class HashSet<E> {
 private HashMap<E, Object> map;
 private static final Object PRESENT = new Object(); // Dummy value

 public boolean add(E e) {
 return map.put(e, PRESENT) == null; // Element as KEY, dummy as VALUE!
 }
}
```

### Duplicate Detection — 2-Step Process:

```text
Step 1: hashCode() is called → Bucket number is determined
Step 2: If an element already exists in that bucket →
 equals() is called → If true → DUPLICATE! Element is NOT added.
```

```text
hashSet.add("Hello")
 ↓
hashCode("Hello") → Bucket #5
 ↓
Is Bucket #5 empty? → YES → Element stored! 

hashSet.add("Hello") // Duplicate attempt
 ↓
hashCode("Hello") → Bucket #5
 ↓
Bucket #5 already has "Hello" → equals("Hello", "Hello") → true → REJECTED! 
```

---

## 3. `equals()` and `hashCode()` Contract (THE Golden Rule!)

### The Contract:
1. **If `o1.equals(o2)` is true → then `o1.hashCode() == o2.hashCode()` MUST be true!**
2. If `hashCode()` is the same → `equals()` can be true or false (Hash Collision).
3. If `equals()` is false → `hashCode()` can be same or different.

### What Happens If You Break This Contract?

| Scenario | `equals()` | `hashCode()` | HashSet Behavior |
|----------|------------|---------------|------------------|
| Both overridden correctly | Content match → true | Same for equal objects | Duplicates correctly detected! |
| Only `equals()` overridden | Content match → true | Different (default Object hashCode) | **Duplicate stored!** Different bucket → `equals()` never called! |
| Only `hashCode()` overridden | Reference check (default) | Same value | Same bucket, but `equals()` returns false → **Duplicate stored!** |
| Neither overridden | Reference check | Random memory address | Logical duplicates both stored! |

---

## 4. Critical Examples

### Example 1: String Duplicates (Works Perfectly)
```java
HashSet<String> set = new HashSet<>();
set.add("Java");
set.add("Python");
set.add("Java"); // Duplicate — silently ignored!

System.out.println(set.size()); // 2
System.out.println(set); // [Java, Python] (order not guaranteed)
```
> **Why it works:** `String` class already overrides both `equals()` and `hashCode()` for content-based comparison.

### Example 2: Custom Object WITHOUT Override (Bug!)
```java
class Student {
 int id;
 Student(int id) { this.id = id; }
}

HashSet<Student> set = new HashSet<>();
set.add(new Student(101));
set.add(new Student(101)); // Same logical data but DIFFERENT objects!

System.out.println(set.size()); // 2 ← WRONG! Both added because default equals() checks reference!
```

### Example 3: Custom Object WITH Correct Override (Fixed!)
```java
class Student {
 int id;
 Student(int id) { this.id = id; }

 @Override
 public boolean equals(Object obj) {
 if (this == obj) return true;
 if (obj == null || getClass() != obj.getClass()) return false;
 Student other = (Student) obj;
 return this.id == other.id;
 }

 @Override
 public int hashCode() {
 return Integer.hashCode(id); // Same id → Same hashCode → Same bucket → equals() called!
 }
}

HashSet<Student> set = new HashSet<>();
set.add(new Student(101));
set.add(new Student(101)); // Duplicate correctly detected!

System.out.println(set.size()); // 1 ← CORRECT!
```

---

## 5. HashSet Constructors

| Constructor | Default Capacity | Load Factor |
|-------------|------------------|-------------|
| `HashSet()` | 16 | 0.75 |
| `HashSet(int initialCapacity)` | Custom | 0.75 |
| `HashSet(int initialCapacity, float loadFactor)` | Custom | Custom |
| `HashSet(Collection c)` | Based on collection size | 0.75 |

### Load Factor / Fill Ratio

```text
Load Factor = Threshold ratio at which rehashing (resize + rehash) is triggered

Default Load Factor = 0.75 (75%)
Meaning: When the HashSet is 75% full → the internal HashMap doubles its capacity
 and rehashes all existing elements into the new buckets.

Example:
Default Capacity = 16 buckets
Threshold = 16 × 0.75 = 12 elements
When the 13th element is added → Capacity doubles to 32 + rehashing occurs!
```

> Note: **High Load Factor** (e.g., 0.9) → Saves memory, but more hash collisions → Slower search 
> Note: **Low Load Factor** (e.g., 0.5) → Faster operations, but more memory waste

---

## 6. LinkedHashSet — Insertion Order Preserved!

**LinkedHashSet** = HashSet + **Doubly Linked List** that maintains insertion order.

```java
LinkedHashSet<String> lhs = new LinkedHashSet<>();
lhs.add("C"); lhs.add("A"); lhs.add("B");
lhs.add("A"); // Duplicate — ignored!

System.out.println(lhs); // [C, A, B] ← Insertion order preserved! 
```

### HashSet vs LinkedHashSet:

| Feature | HashSet | LinkedHashSet |
|---------|---------|---------------|
| **Order** | No guaranteed order | Insertion order preserved |
| **Internal Structure** | HashMap | HashMap + Doubly Linked List |
| **Performance** | Slightly faster (no linked list overhead) | Slightly slower (extra pointers to maintain) |
| **Memory** | Less | More (extra prev/next pointers per entry) |
| **Null Allowed?** | One null | One null |

---

## Interview Quick Traps

| Trap | Answer |
|------|--------|
| What data structure does HashSet use internally? | **HashMap** (elements as keys, dummy object as value). |
| What is the order of duplicate detection in HashSet? | **First `hashCode()`** (find the bucket), **then `equals()`** (match the content). |
| If you override only `equals()` without `hashCode()` — will duplicates be detected? | **No!** Different hashCode → different bucket → `equals()` is never called! |
| How many null elements are allowed in HashSet? | Maximum **1 null** element. |
| Does LinkedHashSet preserve insertion order? | **Yes!** |
| What is HashSet's default initial capacity? | **16** (not 10 like ArrayList!) |

---

[Previous: Cursors](./06-cursors.md) · [Back to Collections Index](./README.md) · [Next: SortedSet & TreeSet](./08-sortedset-and-treeset.md)
