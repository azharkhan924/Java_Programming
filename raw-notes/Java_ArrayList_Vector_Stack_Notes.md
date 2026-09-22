# Java Collections — ArrayList, Vector & Stack

> **Topic:** List Implementations → ArrayList, Vector, Stack  
> **Focus:** Properties, constructors, methods, capacity, synchronization, RandomAccess

---

## 1. ArrayList

The underlying data structure of `ArrayList` is a:

**Resizable / Growable Array**

### Important Properties

- Duplicates are allowed.
- Insertion order is preserved.
- `null` insertion is possible.
- Heterogeneous objects are possible when generics are not restricted.
- `ArrayList` implements `Serializable`.
- `ArrayList` implements `Cloneable`.
- `ArrayList` implements `RandomAccess`.
- `ArrayList` is **not synchronized** by default.
- `ArrayList` was introduced in **Java 1.2**.
- It is a **non-legacy** collection class.

---

## 2. ArrayList — Constructors

### 1. No-argument constructor

```java
ArrayList l = new ArrayList();
```

Creates an empty `ArrayList`.

> **Modern Java note:** The commonly taught default capacity is 10, but modern OpenJDK implementations may defer allocation until the first element is added. The exact internal capacity behavior is an implementation detail.

### 2. Initial-capacity constructor

```java
ArrayList l = new ArrayList(int initialCapacity);
```

Creates an empty `ArrayList` with the specified initial capacity.

Example:

```java
ArrayList l = new ArrayList(20);
```

### 3. Collection constructor

```java
ArrayList l = new ArrayList(Collection c);
```

Creates an `ArrayList` containing the elements of the given collection.

Example:

```java
ArrayList l1 = new ArrayList();

l1.add(10);
l1.add(20);
l1.add(30);

ArrayList l2 = new ArrayList(l1);
```

---

## 3. ArrayList — Best and Worst Choice

### Best choice

If our frequent operation is **retrieval**, `ArrayList` is generally a good choice.

```java
list.get(index);
```

Because `ArrayList` supports efficient random/index-based access.

### Worst choice

If our frequent operation is **insertion or deletion in the middle**, `ArrayList` can be relatively inefficient because elements may need to be shifted.

Example:

```text
Before:
[A, B, C, D, E]

Insert X at index 2:

[A, B, X, C, D, E]
       ↑
   elements shifted
```

---

## 4. ArrayList — Thread Safety

Every method present in `ArrayList` is **non-synchronized**.

Therefore:

- Multiple threads are allowed to operate on an `ArrayList` object at the same time.
- `ArrayList` is **not thread-safe by default**.
- Threads do not have to wait for the `ArrayList` object's intrinsic lock.
- Therefore, in a simple single-threaded or externally synchronized situation, it generally has lower synchronization overhead than `Vector`.

```text
ArrayList
   ↓
Non-synchronized
   ↓
Not thread-safe by default
```

---

## 5. How to Get a Synchronized Version of ArrayList?

By default, an `ArrayList` object is non-synchronized.

We can get a synchronized view by using the `synchronizedList()` method of the `Collections` utility class.

### Method Signature

```java
public static <T> List<T> synchronizedList(List<T> list)
```

Example:

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

> The wrapper synchronizes individual operations. When iterating, external synchronization on the returned list is still required as documented by the Java API.

---

## 6. Other Synchronization Utility Methods

### synchronizedSet()

```java
public static <T> Set<T> synchronizedSet(Set<T> s)
```

Returns a synchronized view of the specified `Set`.

Example:

```java
Set<Integer> set =
        Collections.synchronizedSet(new HashSet<>());
```

### synchronizedMap()

```java
public static <K,V> Map<K,V> synchronizedMap(Map<K,V> m)
```

Returns a synchronized view of the specified `Map`.

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

## 7. Vector

The underlying data structure of `Vector` is a:

**Resizable / Growable Array**

### Important Properties

- Duplicates are allowed.
- Insertion order is preserved.
- `null` insertion is possible.
- Heterogeneous objects are possible when generics are not restricted.
- `Vector` implements `Serializable`.
- `Vector` implements `Cloneable`.
- `Vector` implements `RandomAccess`.
- Most of its commonly used methods are synchronized.
- `Vector` is thread-safe for its individual synchronized operations.
- `Vector` was introduced in **Java 1.0**.
- It is a **legacy collection class**.

### Thread Safety

At a time, only one thread can enter a synchronized `Vector` method for the same Vector object's monitor.

Therefore, competing threads may have to wait.

```text
Vector
   ↓
Synchronized methods
   ↓
Thread-safe for individual operations
   ↓
Synchronization overhead
```

> **Exam perspective:** `Vector` is traditionally described as a synchronized alternative to `ArrayList`. In modern application design, the choice should depend on the actual concurrency requirement.

---

## 8. ArrayList vs Vector

| Feature | ArrayList | Vector |
|---|---|---|
| Introduced | Java 1.2 | Java 1.0 |
| Type | Non-legacy | Legacy |
| Underlying DS | Resizable/Growable Array | Resizable/Growable Array |
| Synchronized | No | Yes, commonly used methods |
| Thread-safe by default | No | Yes for individual synchronized operations |
| RandomAccess | Yes | Yes |
| Serializable | Yes | Yes |
| Cloneable | Yes | Yes |
| Retrieval | Efficient | Efficient |
| Synchronization overhead | Lower by default | Higher due to synchronization |

---

## 9. Vector — Constructors

### 1. Default constructor

```java
Vector v = new Vector();
```

Creates an empty Vector with a default initial capacity of **10**.

If the Vector needs to grow, its capacity is expanded automatically.

For the traditional Vector growth rule:

```text
new capacity = 2 × old capacity
```

when `capacityIncrement` is zero.

> The growth policy is an implementation/API behavior associated with Vector and is different from ArrayList's implementation-specific growth policy.

---

### 2. Initial-capacity constructor

```java
Vector v = new Vector(int initialCapacity);
```

Creates an empty Vector with the specified initial capacity.

Example:

```java
Vector v = new Vector(20);
```

---

### 3. Initial-capacity + incremental-capacity constructor

```java
Vector v =
        new Vector(int initialCapacity,
                   int capacityIncrement);
```

- `initialCapacity` → initial capacity of Vector.
- `capacityIncrement` → amount by which capacity increases when the Vector needs to grow.

Example:

```java
Vector v = new Vector(10, 5);
```

If the Vector becomes full, its capacity increases by the specified increment.

If `capacityIncrement` is zero, Vector traditionally doubles its capacity when growth is required.

---

### 4. Collection constructor

```java
Vector v = new Vector(Collection c);
```

Creates an equivalent Vector containing the elements of the given collection.

Example:

```java
ArrayList<Integer> list = new ArrayList<>();

list.add(10);
list.add(20);
list.add(30);

Vector v = new Vector(list);
```

---

## 10. Vector — Important Methods

### Adding elements

#### `add(Object o)`

Inherited from `List` / `Collection`.

```java
v.add(10);
```

Adds an element to the Vector.

#### `add(int index, Object o)`

Inherited from `List`.

```java
v.add(1, 20);
```

Adds an element at the specified index.

#### `addElement(Object o)`

Vector-specific legacy method.

```java
v.addElement(10);
```

Adds an element to the Vector.

---

## 11. Vector — Removing Elements

### `remove(Object o)`

Removes the first matching occurrence.

```java
v.remove(10);
```

### `removeElement(Object o)`

Vector-specific method.

```java
v.removeElement(10);
```

### `remove(int index)`

Removes the element at the specified index.

```java
v.remove(2);
```

### `removeElementAt(int index)`

Vector-specific method.

```java
v.removeElementAt(2);
```

### `clear()`

Removes all elements.

```java
v.clear();
```

### `removeAllElements()`

Vector-specific method that removes all elements.

```java
v.removeAllElements();
```

---

## 12. Vector — Accessing Elements

### `get(int index)`

List method.

```java
v.get(2);
```

### `elementAt(int index)`

Vector-specific method.

```java
v.elementAt(2);
```

### `firstElement()`

Returns the first element.

```java
v.firstElement();
```

### `lastElement()`

Returns the last element.

```java
v.lastElement();
```

---

## 13. Vector — Other Important Methods

### `size()`

Returns the current number of elements.

```java
int size = v.size();
```

```text
size() → current number of stored elements
```

### `capacity()`

Returns the current capacity of the Vector.

```java
int c = v.capacity();
```

```text
capacity() → how many elements can be stored before growth is required
```

### `elements()`

Returns an `Enumeration` for traversing Vector elements.

```java
Enumeration e = v.elements();
```

Example:

```java
Enumeration e = v.elements();

while (e.hasMoreElements()) {
    System.out.println(e.nextElement());
}
```

---

## 14. Vector — Capacity vs Size

```text
Vector capacity = 10

Elements:
[10, 20, 30]

size()     = 3
capacity() = 10
```

### Remember

- `size()` → number of elements currently present.
- `capacity()` → current storage capacity.

---

## 15. Stack

`Stack` is a **child class of `Vector`**.

```text
Vector
   |
 Stack
```

It is specially designed for **LIFO** operations.

```text
LIFO = Last In, First Out
```

### Constructor

```java
Stack s = new Stack();
```

Creates an empty Stack.

---

## 16. Stack — Methods

### 1. `push(Object obj)`

Adds an element to the top of the Stack.

```java
s.push(10);
```

### 2. `pop()`

Removes and returns the top element.

```java
Object x = s.pop();
```

### 3. `peek()`

Returns the top element without removing it.

```java
Object x = s.peek();
```

### 4. `search(Object obj)`

Searches for an object.

Returns:

- position from the top if found
- `-1` if not found

Example:

```java
s.search(20);
```

If the Stack is:

```text
30  ← top
20
10
```

then:

```text
search(30) → 1
search(20) → 2
search(10) → 3
search(50) → -1
```

### 5. `empty()`

Checks whether the Stack is empty.

```java
boolean b = s.empty();
```

---

## 17. Stack — LIFO Example

```text
push(10)
push(20)
push(30)

Stack:

30 ← top
20
10
```

Now:

```java
pop();
```

returns:

```text
30
```

Then:

```text
20 ← top
10
```

---

## 18. RandomAccess

`RandomAccess` is a marker interface in `java.util`.

```java
public interface RandomAccess
```

It indicates that a List supports efficient random/index-based access.

Both `ArrayList` and `Vector` implement it.

```text
ArrayList → RandomAccess
Vector    → RandomAccess
LinkedList → No RandomAccess
```

---

## 19. Quick Revision

```text
ArrayList
   |
   +-- Resizable/Growable Array
   +-- Non-synchronized
   +-- Java 1.2
   +-- Non-legacy
   +-- RandomAccess
   +-- Good for retrieval

Vector
   |
   +-- Resizable/Growable Array
   +-- Synchronized
   +-- Java 1.0
   +-- Legacy
   +-- RandomAccess
   +-- Thread-safe for individual synchronized operations

Stack
   |
   +-- Child class of Vector
   +-- LIFO
   +-- push()
   +-- pop()
   +-- peek()
   +-- search()
   +-- empty()
```

---

## 20. Exam-Oriented One-Liners

- **ArrayList:** Non-synchronized, resizable-array implementation of `List`.
- **ArrayList introduced:** Java 1.2.
- **Vector introduced:** Java 1.0.
- **Vector:** Legacy collection class.
- **ArrayList:** Non-legacy collection class.
- **RandomAccess:** Marker interface for efficient random/index-based access.
- **ArrayList:** Implements `RandomAccess`.
- **Vector:** Implements `RandomAccess`.
- **Stack:** Child class of `Vector`.
- **Stack follows:** LIFO.
- **`push()`** → adds element at top.
- **`pop()`** → removes and returns top element.
- **`peek()`** → returns top element without removing.
- **`search()`** → returns position from top, or `-1`.
- **`empty()`** → checks whether Stack is empty.
- **`size()`** → current number of elements.
- **`capacity()`** → current storage capacity of Vector.
- **`elements()`** → returns an `Enumeration` for Vector traversal.
- **Synchronized ArrayList:** `Collections.synchronizedList(list)`.
- **Synchronized Set:** `Collections.synchronizedSet(set)`.
- **Synchronized Map:** `Collections.synchronizedMap(map)`.

