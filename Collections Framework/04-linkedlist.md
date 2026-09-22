# LinkedList — Deep Dive

> **Summary:** LinkedList internal structure (Doubly Linked List), constructors, List + Deque/Queue operations, special first/last methods, and ArrayList vs LinkedList comparison.

---

## 1. What is LinkedList?

**LinkedList** is a **Doubly Linked List** data structure where each element (node) maintains a **reference to both its previous and next node**.

```text
LinkedList Internal Structure:
null ← [prev | A | next] ⟷ [prev | B | next] ⟷ [prev | C | next] → null
          ↑ head                                         ↑ tail
```

### Key Properties:
```text
- Maintains insertion order
- Allows duplicate elements
- Allows null values (multiple nulls too)
- Implements List + Deque interfaces simultaneously
- Does NOT implement RandomAccess (index access is O(n), not O(1)!)
- NOT synchronized (Not thread-safe)
```

### Class Declaration:
```java
public class LinkedList<E> extends AbstractSequentialList<E>
        implements List<E>, Deque<E>, Cloneable, Serializable
```

---

## 2. LinkedList Constructors

### Constructor 1: Default — `LinkedList()`
```java
LinkedList<String> list = new LinkedList<>();
```
- No initial capacity concept (unlike ArrayList's default 10)
- Nodes are created dynamically when elements are added

### Constructor 2: From Collection — `LinkedList(Collection c)`
```java
LinkedList<String> list = new LinkedList<>(existingArrayList);
```

---

## 3. Why No `RandomAccess`?

In ArrayList, elements are stored in contiguous memory → `get(5)` directly accesses the 5th slot → **O(1)**.

In LinkedList, there is no contiguous memory — elements are in scattered nodes. `get(5)` requires traversal from the head to the 5th node → **O(n)**.

```text
ArrayList:  [A][B][C][D][E] → get(3) = Direct jump to D → O(1)
LinkedList: A → B → C → D → E → get(3) = Start from A, 3 hops → O(n)
```

This is why LinkedList does **not** implement the `RandomAccess` marker interface.

---

## 4. LinkedList — Best & Worst Use Cases

### Best Choice (Use LinkedList When):
- **Frequent insertion/deletion at the beginning or middle** — no shifting, only pointer (prev/next) updates → O(1) for first/last
- **Stack or Queue behavior** is needed — LinkedList implements `Deque`
- **Elements are frequently reordered** (insert, remove, reorder)

### Worst Choice (Avoid LinkedList When):
- **Frequent random access by index** (`get(i)`) — O(n) traversal required
- **Memory-sensitive application** — each node has extra overhead for 2 pointers (prev + next)

---

## 5. LinkedList — Special First/Last Methods

LinkedList has special methods from the `Deque` interface that are not available in ArrayList:

```java
LinkedList<String> list = new LinkedList<>();
list.add("A"); list.add("B"); list.add("C");

// ─── Adding at ends ───
list.addFirst("Z");    // [Z, A, B, C] → Add at beginning
list.addLast("D");     // [Z, A, B, C, D] → Add at end
list.offerFirst("Y");  // Same as addFirst but returns boolean
list.offerLast("E");   // Same as addLast but returns boolean

// ─── Accessing (without removing) ───
list.getFirst();       // Throws NoSuchElementException if empty
list.getLast();        // Throws NoSuchElementException if empty
list.peekFirst();      // Returns null if empty (safer!)
list.peekLast();       // Returns null if empty (safer!)

// ─── Removing from ends ───
list.removeFirst();    // Throws if empty
list.removeLast();     // Throws if empty
list.pollFirst();      // Returns null if empty (safer!)
list.pollLast();       // Returns null if empty (safer!)
```

---

## 6. LinkedList as Queue (FIFO) & Stack (LIFO)

```java
// ─── As Queue (FIFO: First In First Out) ───
LinkedList<String> queue = new LinkedList<>();
queue.offer("Task1");    // Add at rear
queue.offer("Task2");
queue.poll();            // Remove from front → "Task1"

// ─── As Stack (LIFO: Last In First Out) ───
LinkedList<String> stack = new LinkedList<>();
stack.push("A");         // Internally addFirst()
stack.push("B");
stack.pop();             // Internally removeFirst() → "B"
```

---

## 7. ⚖ ArrayList vs LinkedList (The Big Interview Table!)

| Feature | ArrayList | LinkedList |
|---------|-----------|------------|
| **Internal Structure** | Resizable Array (`Object[]`) | Doubly Linked List (Nodes with prev/next) |
| **`RandomAccess`** |  Implements |  Does not implement |
| **`get(index)` Time** | **O(1)**  Direct jump | **O(n)** Node traversal |
| **`add(end)` Time** | O(1) amortized | O(1) |
| **`add(0, elem)` — Insert at beginning** | **O(n)**  Shift all elements | **O(1)**  Update head pointers |
| **`remove(0)` — Remove from beginning** | **O(n)**  Shift all elements | **O(1)**  Update head pointers |
| **Memory Overhead per Element** | Low (just the element reference) | High (element + prev pointer + next pointer) |
| **Implements Deque?** | No | Yes (Stack + Queue operations) |
| **Thread Safety** | Not synchronized | Not synchronized |
| **Best For** | Random access, bulk reads | Frequent insert/delete at ends, Queue/Stack behavior |

---

## 8. Important LinkedList Methods — Quick Table

| Method | Description | Time |
|--------|-------------|------|
| `add(E e)` | Add element at the end | O(1) |
| `add(int i, E e)` | Insert at index `i` | O(n) |
| `get(int i)` | Return element at index `i` | O(n) |
| `remove(int i)` | Remove element at index `i` | O(n) |
| `addFirst(E e)` | Add at beginning | O(1) |
| `addLast(E e)` | Add at end | O(1) |
| `removeFirst()` | Remove first element | O(1) |
| `removeLast()` | Remove last element | O(1) |
| `peekFirst()` / `peekLast()` | View first/last (null if empty) | O(1) |
| `pollFirst()` / `pollLast()` | Remove first/last (null if empty) | O(1) |
| `push(E e)` / `pop()` | Stack operations (LIFO) | O(1) |
| `offer(E e)` / `poll()` | Queue operations (FIFO) | O(1) |

---

## Interview Quick Traps

| Trap | Answer |
|------|--------|
| Does LinkedList implement `RandomAccess`? | No! Index-based access is O(n). |
| Is LinkedList a singly linked list? |  It is a **Doubly Linked List** — both prev and next pointers exist. |
| ArrayList vs LinkedList for insertion — which is better? | **Frequent insertion at beginning/end** → LinkedList. **End-only add + frequent reads** → ArrayList. |
| Does LinkedList have an initial capacity concept? | No! Nodes are created on-demand dynamically. |
| What major interface does LinkedList implement besides `List`? | **`Deque`** (Double-Ended Queue). |

---

[Previous: Vector & Stack](./03-vector-and-stack.md) · [Back to Collections Index](./README.md) · [Next: Synchronized Collections](./05-synchronized-collections.md)
