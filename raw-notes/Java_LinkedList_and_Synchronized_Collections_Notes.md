# Java Collections — LinkedList & Synchronized Collections

> **Topic:** LinkedList → Constructors, Properties, Methods, ArrayList vs LinkedList  
> **Also:** ArrayList synchronization and `Collections` synchronized wrappers

---

## 1. ArrayList vs Vector — Thread Safety

### ArrayList

Every method present in `ArrayList` is **non-synchronized**.

Therefore:

- Multiple threads can operate on the same `ArrayList` concurrently.
- `ArrayList` is **not thread-safe by default**.
- There is no intrinsic synchronization on its normal methods.
- This avoids the synchronization overhead present in `Vector`.

### Vector

Most commonly used legacy `Vector` methods are synchronized.

Therefore:

- Competing threads may have to wait for the Vector object's monitor.
- Vector provides synchronization for its individual synchronized operations.
- It has synchronization overhead compared with a normal `ArrayList`.

```text
ArrayList → Non-synchronized → Not thread-safe by default

Vector    → Synchronized     → Thread-safe for individual operations
```

---

## 2. How to Get a Synchronized Version of ArrayList?

By default:

```java
ArrayList<Integer> list = new ArrayList<>();
```

is non-synchronized.

We can get a synchronized view using the `Collections` utility class.

### Method Signature

```java
public static <T> List<T> synchronizedList(List<T> list)
```

### Example

```java
ArrayList<Integer> list = new ArrayList<>();

List<Integer> syncList =
        Collections.synchronizedList(list);
```

### Important

```text
ArrayList
   ↓
Collections.synchronizedList()
   ↓
Synchronized List View
```

> When using an iterator on the synchronized wrapper, external synchronization is required during iteration as specified by the Java API.

---

## 3. Synchronized Set and Map

### `synchronizedSet()`

```java
public static <T> Set<T> synchronizedSet(Set<T> s)
```

Example:

```java
Set<Integer> set =
        Collections.synchronizedSet(new HashSet<>());
```

### `synchronizedMap()`

```java
public static <K,V> Map<K,V> synchronizedMap(Map<K,V> m)
```

Example:

```java
Map<Integer,String> map =
        Collections.synchronizedMap(new HashMap<>());
```

### Remember

```text
Collections.synchronizedList()
Collections.synchronizedSet()
Collections.synchronizedMap()
```

---

# 4. LinkedList

The underlying data structure of `LinkedList` is a:

**Doubly Linked List**

```text
null ← [A] ⇄ [B] ⇄ [C] → null
```

Each node maintains links to the previous and next nodes.

---

## 5. Important Properties of LinkedList

- Underlying data structure → **Doubly Linked List**
- Duplicates are allowed.
- Insertion order is preserved.
- `null` insertion is possible.
- Heterogeneous objects are possible when generics are not restricted.
- `LinkedList` implements `Serializable`.
- `LinkedList` implements `Cloneable`.
- `LinkedList` implements `List`.
- `LinkedList` implements `Deque`.
- `LinkedList` does **not** implement `RandomAccess`.

### Remember

```text
LinkedList
   |
   +-- Serializable
   +-- Cloneable
   +-- List
   +-- Deque
   +-- NOT RandomAccess
```

---

## 6. LinkedList — Best and Worst Choice

### Best choice

If our frequent operation is **insertion or deletion**, especially around the middle/end when the required node/position has already been located, `LinkedList` can be a suitable choice.

Example:

```text
Before:

[A] ⇄ [B] ⇄ [C] ⇄ [D]

Insert X between B and C:

[A] ⇄ [B] ⇄ [X] ⇄ [C] ⇄ [D]
```

Only the relevant links need to be changed.

### Worst choice

If our frequent operation is **retrieval by index**, `LinkedList` is generally a poor choice because it does not support `RandomAccess`.

```java
list.get(index);
```

Finding an arbitrary index may require traversal through the linked nodes.

> **Important:** Saying "LinkedList is best for middle insertion/deletion" is an exam-friendly rule. In real performance analysis, locating the node can itself take `O(n)`. Once the node/position is known, link updates are `O(1)`.

---

# 7. LinkedList — Constructors

### 1. No-argument constructor

```java
LinkedList l = new LinkedList();
```

Creates an empty `LinkedList` object.

---

### 2. Collection constructor

```java
LinkedList l =
        new LinkedList(Collection c);
```

Creates an equivalent `LinkedList` containing the elements of the given collection.

Example:

```java
ArrayList<Integer> list = new ArrayList<>();

list.add(10);
list.add(20);
list.add(30);

LinkedList<Integer> l1 =
        new LinkedList<>(list);
```

### Meaning

```text
Given Collection
      ↓
new LinkedList(collection)
      ↓
Equivalent LinkedList
```

---

# 8. LinkedList — Basic Example

```java
import java.util.*;

class Demo {

    public static void main(String[] args) {

        LinkedList l = new LinkedList();

        l.add("abc");
        l.add(30);
        l.add("xyz");
        l.add("abc");

        System.out.println(l);

        l.set(0, "software");
        l.add(0, "very");
        l.removeLast();
        l.addFirst("ccc");

        System.out.println(l);
    }
}
```

### Important observations

Because raw `LinkedList` is used:

- Different object types can be inserted.
- Duplicate objects can be inserted.
- `null` can also be inserted.

With generics, we normally restrict the element type:

```java
LinkedList<String> names = new LinkedList<>();
```

---

# 9. LinkedList — Adding Elements

### `add(Object o)`

Inherited from `Collection` / `List`.

```java
l.add("A");
```

Adds an element to the end of the list.

### `add(int index, Object o)`

Inherited from `List`.

```java
l.add(1, "B");
```

Adds an element at the specified index.

### `addFirst(Object o)`

LinkedList/Deque method.

```java
l.addFirst("A");
```

Adds an element at the beginning.

### `addLast(Object o)`

LinkedList/Deque method.

```java
l.addLast("Z");
```

Adds an element at the end.

---

# 10. LinkedList — Removing Elements

### `remove(Object o)`

Removes the first matching object.

```java
l.remove("A");
```

### `remove(int index)`

Removes the element at the specified index.

```java
l.remove(2);
```

### `removeFirst()`

Removes and returns the first element.

```java
l.removeFirst();
```

### `removeLast()`

Removes and returns the last element.

```java
l.removeLast();
```

### `clear()`

Removes all elements.

```java
l.clear();
```

### `removeFirstOccurrence(Object o)`

Removes the first occurrence of the specified element.

```java
l.removeFirstOccurrence("A");
```

### `removeLastOccurrence(Object o)`

Removes the last occurrence of the specified element.

```java
l.removeLastOccurrence("A");
```

---

# 11. LinkedList — Accessing Elements

### `get(int index)`

Returns the element at the specified index.

```java
Object x = l.get(2);
```

### `getFirst()`

Returns the first element without removing it.

```java
Object x = l.getFirst();
```

### `getLast()`

Returns the last element without removing it.

```java
Object x = l.getLast();
```

### `element()`

Returns the first element.

```java
Object x = l.element();
```

### `peek()`

Returns the first element without removing it; returns `null` if the list is empty.

```java
Object x = l.peek();
```

### `peekFirst()`

Returns the first element without removing it; returns `null` if empty.

### `peekLast()`

Returns the last element without removing it; returns `null` if empty.

---

# 12. LinkedList — Queue/Deque Operations

Because `LinkedList` implements `Deque`, it can also be used as a Queue or Deque.

### Queue-style methods

```java
offer(Object o);
poll();
peek();
```

### Deque-style methods

```java
offerFirst(Object o);
offerLast(Object o);

pollFirst();
pollLast();

peekFirst();
peekLast();
```

---

# 13. Important LinkedList Methods — Quick Table

| Method | Purpose |
|---|---|
| `add(E e)` | Adds element at end |
| `add(int index, E e)` | Adds at specified index |
| `addFirst(E e)` | Adds at beginning |
| `addLast(E e)` | Adds at end |
| `remove(Object o)` | Removes first matching object |
| `remove(int index)` | Removes element at index |
| `removeFirst()` | Removes first element |
| `removeLast()` | Removes last element |
| `get(int index)` | Retrieves element at index |
| `getFirst()` | Retrieves first element |
| `getLast()` | Retrieves last element |
| `peek()` | Retrieves first element, returns `null` if empty |
| `poll()` | Removes and returns first element, returns `null` if empty |
| `clear()` | Removes all elements |
| `size()` | Returns number of elements |

---

# 14. ArrayList vs LinkedList

| Basis | ArrayList | LinkedList |
|---|---|---|
| Underlying data structure | Resizable/Growable Array | Doubly Linked List |
| Retrieval | Generally efficient | Generally slower for arbitrary index |
| Frequent retrieval | Good choice | Poor choice |
| Frequent insertion/deletion | Less suitable in middle | Suitable when node/position is available |
| RandomAccess | Yes | No |
| Duplicates | Allowed | Allowed |
| Insertion order | Preserved | Preserved |
| `null` | Allowed | Allowed |
| Serializable | Yes | Yes |
| Cloneable | Yes | Yes |
| Deque | No | Yes |

---

## 15. ArrayList vs LinkedList — Exam View

### ArrayList

**Best choice:** frequent retrieval.

**Worst choice:** frequent insertion/deletion in the middle.

Reason:

```text
Insertion/deletion
       ↓
Elements may need shifting
```

### LinkedList

**Best choice:** frequent insertion/deletion, particularly when the location/node is already known.

**Worst choice:** frequent retrieval by index.

Reason:

```text
get(index)
    ↓
Node traversal may be required
```

---

# 16. RandomAccess — Important Point

`RandomAccess` is a marker interface.

```java
java.util.RandomAccess
```

### Implementations

```text
ArrayList → RandomAccess
Vector    → RandomAccess
LinkedList → NOT RandomAccess
```

### Meaning

`RandomAccess` indicates that indexed access is expected to be efficient.

It does **not** mean that every operation on the collection is fast.

---

# 17. LinkedList vs ArrayList — Visual Understanding

### ArrayList

```text
Index:    0    1    2    3
          ↓    ↓    ↓    ↓
Array:   [A]  [B]  [C]  [D]

get(2) → direct indexed access
```

### LinkedList

```text
[A] ⇄ [B] ⇄ [C] ⇄ [D]
             ↑
          get(2)

Traversal may be required to reach the node.
```

---

# 18. Quick Revision Map

```text
                         List
                           |
             ┌─────────────┼─────────────┐
             |             |             |
         ArrayList      LinkedList      Vector
             |             |             |
      Growable Array   Doubly LL    Growable Array
      RandomAccess     No RandomAccess
      Non-sync         Deque        Synchronized
      Java 1.2                       Java 1.0
      Non-legacy                     Legacy
                                        |
                                      Stack
                                        |
                                       LIFO
```

---

# 19. Exam-Oriented One-Liners

- **ArrayList underlying DS:** Resizable/Growable Array.
- **LinkedList underlying DS:** Doubly Linked List.
- **ArrayList:** Implements `RandomAccess`.
- **LinkedList:** Does not implement `RandomAccess`.
- **ArrayList:** Generally preferred for frequent indexed retrieval.
- **LinkedList:** Useful for frequent insertion/deletion when the relevant position/node is available.
- **LinkedList:** Implements `List` and `Deque`.
- **LinkedList:** Implements `Serializable` and `Cloneable`.
- **Duplicates in LinkedList:** Allowed.
- **Insertion order in LinkedList:** Preserved.
- **Null in LinkedList:** Allowed.
- **`addFirst()`** → adds at beginning.
- **`addLast()`** → adds at end.
- **`getFirst()`** → returns first element.
- **`getLast()`** → returns last element.
- **`removeFirst()`** → removes and returns first element.
- **`removeLast()`** → removes and returns last element.
- **`get(index)`** → retrieves element at index.
- **`Collections.synchronizedList(list)`** → returns a synchronized List view.
- **`Collections.synchronizedSet(set)`** → returns a synchronized Set view.
- **`Collections.synchronizedMap(map)`** → returns a synchronized Map view.

---

# 20. Final Memory Trick

```text
ArrayList
→ Array
→ RandomAccess
→ Retrieval

LinkedList
→ Linked nodes
→ No RandomAccess
→ Insertion/Deletion

Vector
→ Array
→ Synchronized
→ Legacy

Stack
→ Vector child
→ LIFO
```
