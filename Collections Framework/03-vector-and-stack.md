# Vector & Stack

> **Summary:** Vector constructors, capacity vs size, Vector-specific methods, ArrayList vs Vector comparison, Stack class (LIFO), push/pop/peek methods, and Stack usage example.

---

## 1. What is Vector?

**Vector** is a resizable array similar to ArrayList, but with one major difference:

>  **All of Vector's methods are `synchronized` — it is Thread-Safe by default!**

```text
Vector Key Properties:
- Maintains insertion order
- Allows duplicate elements
- Allows null values
- Implements RandomAccess → Fast index-based access O(1)
- Synchronized (Thread-safe) — Every method is locked
 Legacy class (exists since Java 1.0, rarely used directly now)
```

---

## 2. Vector Constructors

### Constructor 1: Default — `Vector()`
```java
Vector<String> v = new Vector<>();
```
- **Initial Capacity:** 10
- **Capacity Increment:** Previous capacity is doubled (x2)

### Constructor 2: Custom Capacity — `Vector(int initialCapacity)`
```java
Vector<String> v = new Vector<>(50);
```
- Custom starting capacity, increment still 2x

### Constructor 3: Custom Capacity + Custom Increment — `Vector(int capacity, int incrementBy)`
```java
Vector<String> v = new Vector<>(50, 10);
```
- Starting capacity 50, each resize adds only 10 extra slots (instead of doubling)

### Constructor 4: From Collection — `Vector(Collection c)`
```java
Vector<String> v = new Vector<>(existingList);
```

---

## 3. Capacity Growth: ArrayList vs Vector

| Feature | ArrayList | Vector |
|---------|-----------|--------|
| **Growth Formula** | `(old * 3/2) + 1` | `old * 2` (default), or custom increment |
| **Default Initial Capacity** | 10 | 10 |
| **Custom Increment?** | No | Yes (3rd constructor) |

---

## 4. Capacity vs Size — Critical Difference

```java
Vector<Integer> v = new Vector<>(); // capacity = 10
v.add(1); v.add(2); v.add(3);

System.out.println(v.capacity()); // 10 → Total allocated space
System.out.println(v.size());     //  3 → Actual elements stored
```

```text
Capacity: [1] [2] [3] [ ] [ ] [ ] [ ] [ ] [ ] [ ]
                        ↑___ Empty slots (capacity - size = 7)
Size = 3 elements stored
Capacity = 10 total slots available
```

> Note: **Note:** ArrayList does **not** have a `capacity()` method. This is a Vector-only feature.

---

## 5. ⚖ ArrayList vs Vector (Top Interview Question!)

| Feature | ArrayList | Vector |
|---------|-----------|--------|
| **Thread Safety** |  NOT synchronized |  Synchronized (lock on every method) |
| **Performance** | Faster (no locking overhead) | Slower (synchronization cost) |
| **Capacity Growth** | `(old * 3/2) + 1` | `old * 2` (or custom increment) |
| **`capacity()` Method** | Not available |  Available |
| **Legacy?** | No (Java 1.2+) | Yes (Java 1.0, re-engineered in 1.2) |
| **Iteration** | `Iterator` and `ListIterator` | `Enumeration` also supported (plus Iterator/ListIterator) |
| **When to use?** | Single-threaded apps, high-performance reads | Multi-threaded apps (but modern code prefers `CopyOnWriteArrayList`) |

---

## 6. Vector-Specific Methods

Vector has some extra methods not found in ArrayList:

```java
Vector<String> v = new Vector<>();
v.add("A"); v.add("B"); v.add("C");

// ─── Adding ───
v.addElement("D");       // Legacy add method (same as add())

// ─── Removing ───
v.removeElement("B");    // Removes first occurrence of "B"
v.removeElementAt(0);    // Removes element at index 0
v.removeAllElements();   // Clears entire vector

// ─── Accessing ───
v.elementAt(0);          // Legacy version of get(0)
v.firstElement();        // First element (throws exception if empty)
v.lastElement();         // Last element (throws exception if empty)

// ─── Capacity ───
v.capacity();            // Current internal array capacity
v.trimToSize();          // Shrink capacity to current size
v.ensureCapacity(100);   // Ensure minimum capacity of 100
```

---

## 7. Stack — LIFO Data Structure

**Stack** class **extends Vector** and provides **LIFO (Last-In-First-Out)** behavior.

```text
Stack extends Vector

    ┌─────┐
    │  C  │ ← Top (Last added, first removed)
    ├─────┤
    │  B  │
    ├─────┤
    │  A  │ ← Bottom (First added, last removed)
    └─────┘
```

### Stack Constructor:
```java
Stack<String> stack = new Stack<>(); // Only one constructor — no-arg
```

---

## 8. Stack Methods (5 Core Operations)

```java
Stack<Integer> stack = new Stack<>();

// 1. push() — Add element to top
stack.push(10);  // [10]
stack.push(20);  // [10, 20]
stack.push(30);  // [10, 20, 30]

// 2. pop() — Remove + return top element
int top = stack.pop(); // Returns 30, Stack: [10, 20]

// 3. peek() — View top element WITHOUT removing
int current = stack.peek(); // Returns 20, Stack: [10, 20] (unchanged!)

// 4. empty() — Check if stack is empty
boolean isEmpty = stack.empty(); // false

// 5. search(element) — Returns 1-based position from top
int pos = stack.search(10); // Returns 2 (10 is 2nd from top)
// Returns -1 if element is not found
```

### Stack LIFO Example:
```java
Stack<String> stack = new Stack<>();
stack.push("A"); stack.push("B"); stack.push("C");

while (!stack.empty()) {
    System.out.println(stack.pop());
}
// Output: C, B, A → Reverse order (LIFO!)
```

---

## 9. Note: Modern Alternative to Stack

> Stack class is **legacy** (Java 1.0). Modern code should use the **`Deque`** interface:

```java
// valid Modern approach
Deque<Integer> stack = new ArrayDeque<>();
stack.push(10);
stack.push(20);
stack.pop(); // 20
```

**Why ArrayDeque over Stack?**
- `Stack` inherits `Vector`'s synchronization overhead (even when not needed)
- `ArrayDeque` is faster and has no unnecessary synchronization

---

## Interview Quick Traps

| Trap | Answer |
|------|--------|
| Vector is thread-safe but ArrayList is not, so always use Vector? | No! Synchronization slows performance. Always use ArrayList for single-threaded apps. |
| What class does Stack extend? | `java.util.Vector` |
| Difference between `peek()` and `pop()`? | `peek()` only views (stack unchanged). `pop()` removes + returns. |
| What happens when `pop()` is called on an empty stack? | `EmptyStackException` is thrown! |
| What is Vector's default capacity growth? | It **doubles** (2x). |
| Does `stack.search()` return a 0-based index? |  It returns a **1-based** position from the top! |

---

[Previous: ArrayList](./02-arraylist.md) · [Back to Collections Index](./README.md) · [Next: LinkedList](./04-linkedlist.md)
