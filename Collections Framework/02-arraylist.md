# 📋 ArrayList — Deep Dive

> **Summary:** ArrayList constructors, initial capacity & growth formula, `RandomAccess` / `Serializable` / `Cloneable` marker interfaces, `toString()` behavior, and best/worst use case scenarios.

---

## 1. What is ArrayList?

**ArrayList** is a **resizable array** (dynamic array) that internally uses an ordinary array, but the size automatically grows when elements are added.

```text
ArrayList Key Properties:
✅ Maintains insertion order
✅ Allows duplicate elements
✅ Allows null values (multiple nulls too)
✅ Implements RandomAccess → Fast index-based retrieval O(1)
❌ NOT synchronized (Not thread-safe by default)
```

### Class Declaration:
```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, Serializable
```

---

## 2. ArrayList Constructors

### Constructor 1: Default — `ArrayList()`
```java
ArrayList<String> list = new ArrayList<>();
```
- **Initial Capacity:** 10 (internally a size 10 array is created)
- When 10 elements are filled, a new larger array is automatically created

### Constructor 2: Custom Capacity — `ArrayList(int initialCapacity)`
```java
ArrayList<String> list = new ArrayList<>(100);
```
- Use when you roughly know how many elements will be added
- **Performance Benefit:** Avoids repeated resize/copy operations

### Constructor 3: From Existing Collection — `ArrayList(Collection c)`
```java
List<String> original = List.of("A", "B", "C");
ArrayList<String> copy = new ArrayList<>(original);
```
- Copies data from any existing Collection into a new ArrayList

---

## 3. Capacity Growth Formula

When the internal array is full and another element needs to be added:

```text
New Capacity = (Old Capacity * 3 / 2) + 1

Example:
Initial Capacity = 10
After 1st growth  = (10 * 3/2) + 1 = 16
After 2nd growth  = (16 * 3/2) + 1 = 25
After 3rd growth  = (25 * 3/2) + 1 = 38
```

### What Happens Internally?
```text
1. A new larger array is created (with the new capacity)
2. All elements from the old array are copied to the new array (System.arraycopy)
3. The old array becomes GC eligible
4. ArrayList's internal reference points to the new array
```

> ⚠️ **Performance Warning:** If the starting capacity is too small and many elements are added, repeated resizing + copying degrades performance. Specify initial capacity if the approximate size is known!

---

## 4. Marker Interfaces Implemented by ArrayList

ArrayList implements 3 important marker interfaces:

| Marker Interface | Purpose |
|------------------|---------|
| **`RandomAccess`** | Signals to algorithms that this data structure supports **O(1) index-based access** (`get(i)` is fast) |
| **`Serializable`** | Object can be **converted to a byte stream** and sent over network or saved to file |
| **`Cloneable`** | **Shallow copy** can be created via `clone()` without `CloneNotSupportedException` |

### `instanceof` Check Example:
```java
ArrayList<String> list = new ArrayList<>();

System.out.println(list instanceof RandomAccess);  // true
System.out.println(list instanceof Serializable);  // true
System.out.println(list instanceof Cloneable);      // true
System.out.println(list instanceof List);           // true
System.out.println(list instanceof Collection);     // true
```

---

## 5. `toString()` Behavior in Collections

ArrayList's `toString()` method is already overridden (in `AbstractCollection`):

```java
ArrayList<Integer> nums = new ArrayList<>();
nums.add(10); nums.add(20); nums.add(30);

System.out.println(nums);           // [10, 20, 30] ← Clean readable output!
System.out.println(nums.toString()); // [10, 20, 30] ← Same result
```

> **Comparison:** A plain array prints ugly output like `[I@1a2b3c`. Collections have `toString()` already overridden for a human-readable `[elem1, elem2, ...]` format.

---

## 6. ArrayList — Best & Worst Use Cases

### ✅ Best Choice (Use ArrayList When):
- **Frequent retrieval / read operations** (index-based access is O(1))
- Data is mostly **sequential reads** (e.g., display a list of products, render table rows)
- Elements are inserted/removed mostly **at the end** (`add()` is amortized O(1))

### ❌ Worst Choice (Avoid ArrayList When):
- **Frequent insertion/deletion in the middle** — elements must be shifted → O(n)!
- **Thread-safety** is required — ArrayList is not synchronized, race conditions can occur
- Elements are **frequently added/removed at the beginning** — LinkedList is better for this

```text
Operation Performance (ArrayList):
┌────────────────────────┬──────────┐
│ Operation              │ Time     │
├────────────────────────┼──────────┤
│ get(index)             │ O(1) ✅  │
│ add(element) at end    │ O(1)* ✅ │  (* amortized, resize excluded)
│ add(index, element)    │ O(n) ❌  │  (shift elements right)
│ remove(index)          │ O(n) ❌  │  (shift elements left)
│ contains(element)      │ O(n)     │  (linear search)
│ size()                 │ O(1)     │
└────────────────────────┴──────────┘
```

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| What is ArrayList's default initial capacity? | **10** |
| What is the capacity growth formula? | `(OldCapacity * 3/2) + 1` |
| How many methods does `RandomAccess` interface have? | **0** (It is a Marker Interface!) |
| Can `null` be stored in ArrayList? | ✅ Yes, multiple nulls are allowed. |
| Is ArrayList thread-safe? | ❌ No. Use `Collections.synchronizedList()` or `CopyOnWriteArrayList` for thread-safety. |
| What data structure does ArrayList use internally? | An ordinary **resizable array** (`Object[]`) |

---

[⬅️ Previous: Framework Intro](./01-collection-framework-intro.md) · [📖 Back to Collections Index](./README.md) · [Next → Vector & Stack ➡️](./03-vector-and-stack.md)
