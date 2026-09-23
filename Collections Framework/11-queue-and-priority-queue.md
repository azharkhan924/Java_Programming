# Java Collection Framework — Queue, PriorityQueue, NavigableSet, NavigableMap & Utility Classes

> **Revision-friendly notes:** Concepts → Rules → Methods → Examples → Important Exceptions → Interview Traps

---

# 1. Queue

## Definition

`Queue` is a **child interface of `Collection`**.

A Queue is used when we want to represent a group of individual objects **prior to processing**.

The usual processing order of a Queue is:

```text
FIFO → First In First Out
```

### Real-life example: SMS delivery

Suppose we have to send SMS to several mobile numbers.

If mobile numbers are stored in the same order in which the SMS should be delivered, then **Queue** is a suitable data structure because it follows FIFO.

```text
Mobile numbers:

9876 → 9123 → 8888 → 7654

Processing:

9876 → 9123 → 8888 → 7654
  ↑
First In                  First Out
```

### Important

Normally, Queue follows FIFO.

However, depending on the requirement, we can process elements according to a **priority** instead of insertion order.

For this purpose, Java provides `PriorityQueue`.

---

## Queue Hierarchy

```text
Collection
    |
   Queue
    |
    +------------------+
    |                  |
 LinkedList        PriorityQueue
```

`LinkedList` implements the `Queue` interface from Java 1.5 onward.

```java
Queue<String> q = new LinkedList<>();
```

`LinkedList` can be used as a Queue and its normal Queue operations follow FIFO order.

> **Interview point:** `LinkedList` is not the only Queue implementation. `PriorityQueue` is another important implementation, but its processing order is based on priority rather than normal FIFO.

---

# 2. Queue Interface Specific Methods

Queue provides two groups of methods for insertion, retrieval and removal.

| Operation | Method | If Queue is empty/full |
|---|---|---|
| Insert | `offer(e)` | Returns `false` if insertion cannot be performed |
| Insert | `add(e)` | Throws `IllegalStateException` if insertion cannot be performed |
| Retrieve head | `peek()` | Returns `null` if empty |
| Retrieve head | `element()` | Throws `NoSuchElementException` if empty |
| Remove head | `poll()` | Returns `null` if empty |
| Remove head | `remove()` | Throws `NoSuchElementException` if empty |

### 2.1 `offer(Object o)`

Used to **insert an object into the Queue**.

```java
boolean offer(Object o);
```

Returns:

```text
true  → insertion successful
false → insertion unsuccessful
```

---

### 2.2 `peek()`

Used to **return the head element without removing it**.

```java
Object peek();
```

If Queue is empty:

```text
returns null
```

---

### 2.3 `element()`

Used to **return the head element without removing it**.

```java
Object element();
```

If Queue is empty:

```text
NoSuchElementException
```

---

### 2.4 `poll()`

Used to **remove and return the head element**.

```java
Object poll();
```

If Queue is empty:

```text
returns null
```

---

### 2.5 `remove()`

Used to **remove and return the head element**.

```java
Object remove();
```

If Queue is empty:

```text
NoSuchElementException
```

### Easy memory trick

```text
peek()   → see head
element()→ see head + exception if empty

poll()   → remove head
remove() → remove head + exception if empty
```

---

# 3. PriorityQueue

## Definition

If we want to represent a group of individual objects and process them according to **some priority**, we should go for `PriorityQueue`.

```java
PriorityQueue
```

The priority can be decided by:

1. **Default natural sorting order**
2. **Customized sorting order using Comparator**

---

## Important Properties of PriorityQueue

### 1. Insertion order is NOT preserved

PriorityQueue does not process elements according to their insertion order.

It processes elements according to priority.

```text
Insertion:
50 → 10 → 30 → 20

Processing:
10 → 20 → 30 → 50
```

The exact internal arrangement should not be assumed to be fully sorted when simply iterating over the queue. The priority guarantee is about the head/queue processing behavior.

---

### 2. Duplicate objects ARE allowed

This is an important correction.

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();

pq.offer(10);
pq.offer(10);
pq.offer(20);
```

Duplicates are allowed.

---

### 3. `null` is not allowed

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();

pq.offer(null);   // NullPointerException
```

`null` is not allowed even as the first element.

---

### 4. Natural ordering

If we use the default natural sorting order, the elements must be **mutually comparable**.

For example:

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();

pq.offer(30);
pq.offer(10);
pq.offer(20);
```

Processing:

```text
10 → 20 → 30
```

If incompatible objects are supplied, a `ClassCastException` can occur.

---

### 5. Customized ordering

We can provide a `Comparator` to define our own priority.

```java
PriorityQueue<Integer> pq =
        new PriorityQueue<>(new MyComparator());
```

In this case, the Comparator determines how elements are compared.

> **Important:** The objects do not necessarily need to implement `Comparable` when an appropriate Comparator is supplied. However, the Comparator itself must be capable of comparing the objects supplied to it.

---

# 4. PriorityQueue Constructors

`PriorityQueue` provides several constructors.

## 1. Default constructor

```java
PriorityQueue()
```

Creates an empty PriorityQueue with:

```text
Initial capacity = 11
Ordering = natural ordering
```

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();
```

---

## 2. Initial capacity

```java
PriorityQueue(int initialCapacity)
```

Example:

```java
PriorityQueue<Integer> pq = new PriorityQueue<>(20);
```

Creates an empty PriorityQueue with the specified initial capacity.

---

## 3. Comparator

```java
PriorityQueue(Comparator<? super E> comparator)
```

Example:

```java
PriorityQueue<Integer> pq =
        new PriorityQueue<>(new MyComparator());
```

---

## 4. Initial capacity + Comparator

```java
PriorityQueue(int initialCapacity,
              Comparator<? super E> comparator)
```

Example:

```java
PriorityQueue<Integer> pq =
        new PriorityQueue<>(20, new MyComparator());
```

---

## 5. Collection

```java
PriorityQueue(Collection<? extends E> c)
```

Creates a PriorityQueue containing the elements of the specified collection.

---

## 6. PriorityQueue

```java
PriorityQueue(PriorityQueue<? extends E> c)
```

Creates a PriorityQueue containing the elements of another PriorityQueue.

---

## 7. SortedSet

```java
PriorityQueue(SortedSet<? extends E> c)
```

Creates a PriorityQueue containing the elements of a SortedSet.

> **Exam point:** Java's `PriorityQueue` has more than the five commonly demonstrated constructors; the complete API includes the above seven constructor forms.

---

# 5. PriorityQueue Example

## Natural Ordering

```java
import java.util.*;

class Test {
    public static void main(String[] args) {

        PriorityQueue<Integer> q = new PriorityQueue<>();

        q.offer(15);
        q.offer(0);
        q.offer(20);
        q.offer(10);
        q.offer(5);

        System.out.println(q);

        System.out.println(q.peek());
        System.out.println(q.poll());
        System.out.println(q.poll());
    }
}
```

The smallest element gets the highest priority under natural ordering.

```text
Priority:

0 → highest priority
5
10
15
20
```

---

## PriorityQueue with Comparator

Suppose we want the largest element to get the highest priority.

```java
import java.util.*;

class MyComparator implements Comparator<Integer> {

    public int compare(Integer obj1, Integer obj2) {
        return obj2.compareTo(obj1);
    }
}

class Test {
    public static void main(String[] args) {

        PriorityQueue<Integer> q =
                new PriorityQueue<>(new MyComparator());

        q.offer(15);
        q.offer(0);
        q.offer(20);
        q.offer(10);
        q.offer(5);

        System.out.println(q.peek());
    }
}
```

Here:

```text
20 → highest priority
15
10
5
0
```

---

# 6. Platform Note

Some platforms/JVM implementations may not provide identical support or scheduling behavior for **thread priorities**.

Do not confuse this with `PriorityQueue`: a PriorityQueue is a data structure whose ordering is controlled by its priority rules.

---

# 7. NavigableSet

## Java Version

`NavigableSet` was introduced as a **Java 6 enhancement** to the Collection Framework.

```text
Collection
    |
   Set
    |
 SortedSet
    |
NavigableSet
    |
  TreeSet
```

`NavigableSet` is a child interface of `SortedSet`.

It defines methods useful for **navigation/search around a given element**.

---

# 8. NavigableSet Methods

Consider:

```java
NavigableSet<Integer> set = new TreeSet<>();

set.add(10);
set.add(20);
set.add(30);
set.add(40);
set.add(50);
```

Visual representation:

```text
10    20    30    40    50
      ↑
   target = 30
```

---

## 8.1 `floor(e)`

Returns the **greatest element less than or equal to `e`**.

```java
set.floor(35);
```

Output:

```text
30
```

Because:

```text
30 <= 35
```

If no such element exists:

```text
null
```

---

## 8.2 `lower(e)`

Returns the **greatest element strictly less than `e`**.

```java
set.lower(30);
```

Output:

```text
20
```

`30` itself is not considered.

---

## 8.3 `ceiling(e)`

Returns the **smallest element greater than or equal to `e`**.

```java
set.ceiling(35);
```

Output:

```text
40
```

---

## 8.4 `higher(e)`

Returns the **smallest element strictly greater than `e`**.

```java
set.higher(30);
```

Output:

```text
40
```

---

## 8.5 `pollFirst()`

Removes and returns the **first (lowest) element**.

```java
set.pollFirst();
```

If the set is empty:

```text
null
```

---

## 8.6 `pollLast()`

Removes and returns the **last (highest) element**.

```java
set.pollLast();
```

If the set is empty:

```text
null
```

---

## 8.7 `descendingSet()`

Returns a view of the set in **reverse order**.

```java
NavigableSet<Integer> reverse = set.descendingSet();
```

Example:

```text
Original:
10 20 30 40 50

Descending view:
50 40 30 20 10
```

---

# 9. NavigableSet Example

```java
import java.util.*;

class Test {
    public static void main(String[] args) {

        NavigableSet<Integer> set = new TreeSet<>();

        set.add(10);
        set.add(20);
        set.add(30);
        set.add(40);
        set.add(50);

        System.out.println(set.floor(35));   // 30
        System.out.println(set.lower(30));   // 20
        System.out.println(set.ceiling(35)); // 40
        System.out.println(set.higher(30));  // 40

        System.out.println(set.pollFirst()); // 10
        System.out.println(set.pollLast());  // 50

        System.out.println(set.descendingSet());
    }
}
```

### Quick Revision

```text
floor(x)    → <= x, greatest
lower(x)    → <  x, greatest
ceiling(x)  → >= x, smallest
higher(x)   → >  x, smallest

pollFirst() → remove + return lowest
pollLast()  → remove + return highest

descendingSet() → reverse-order view
```

---

# 10. NavigableMap

## Hierarchy

```text
Map
 |
SortedMap
 |
NavigableMap
 |
TreeMap
```

`NavigableMap` is a child interface of `SortedMap`.

It defines several methods for **navigation purposes** based on keys.

---

# 11. NavigableMap Methods

Consider:

```java
NavigableMap<Integer, String> map = new TreeMap<>();

map.put(10, "A");
map.put(20, "B");
map.put(30, "C");
map.put(40, "D");
map.put(50, "E");
```

---

## 11.1 `floorKey(key)`

Returns the **greatest key less than or equal to the given key**.

```java
map.floorKey(35);
```

Output:

```text
30
```

---

## 11.2 `lowerKey(key)`

Returns the **greatest key strictly less than the given key**.

```java
map.lowerKey(30);
```

Output:

```text
20
```

---

## 11.3 `ceilingKey(key)`

Returns the **smallest key greater than or equal to the given key**.

```java
map.ceilingKey(35);
```

Output:

```text
40
```

---

## 11.4 `higherKey(key)`

Returns the **smallest key strictly greater than the given key**.

```java
map.higherKey(30);
```

Output:

```text
40
```

---

## 11.5 `pollFirstEntry()`

Removes and returns the entry having the **lowest key**.

```java
map.pollFirstEntry();
```

Example:

```text
10=A
```

---

## 11.6 `pollLastEntry()`

Removes and returns the entry having the **highest key**.

```java
map.pollLastEntry();
```

Example:

```text
50=E
```

---

## 11.7 `descendingMap()`

Returns a **reverse-order view of the map**.

```java
map.descendingMap();
```

Example:

```text
Normal:
10=A
20=B
30=C
40=D
50=E

Descending:
50=E
40=D
30=C
20=B
10=A
```

---

# 12. NavigableMap Example

```java
import java.util.*;

class Test {
    public static void main(String[] args) {

        NavigableMap<Integer, String> map = new TreeMap<>();

        map.put(10, "A");
        map.put(20, "B");
        map.put(30, "C");
        map.put(40, "D");
        map.put(50, "E");

        System.out.println(map.floorKey(35));    // 30
        System.out.println(map.lowerKey(30));    // 20
        System.out.println(map.ceilingKey(35));  // 40
        System.out.println(map.higherKey(30));   // 40

        System.out.println(map.pollFirstEntry()); // 10=A
        System.out.println(map.pollLastEntry());  // 50=E

        System.out.println(map.descendingMap());
    }
}
```

### Quick Revision

```text
floorKey(x)    → <= x, greatest key
lowerKey(x)    → <  x, greatest key
ceilingKey(x)  → >= x, smallest key
higherKey(x)   → >  x, smallest key

pollFirstEntry() → remove + return lowest-key entry
pollLastEntry()  → remove + return highest-key entry

descendingMap() → reverse-order map view
```

---

# 13. Collections Utility Class

`Collections` is a utility class in the Collection Framework.

It defines several utility methods for **Collection objects**, especially List-related operations such as:

- Sorting
- Searching
- Reversing
- Shuffling
- Finding minimum/maximum
- Other collection utilities

### Important distinction

```text
Collections → utility methods for Collection objects
Arrays      → utility methods for arrays
```

---

# 14. Sorting Elements of List

`Collections` provides overloaded `sort()` methods.

## Method 1: Natural Sorting Order

```java
public static <T extends Comparable<? super T>>
void sort(List<T> list)
```

Used to sort a List according to **default natural sorting order**.

For example:

```java
List<Integer> list = new ArrayList<>();

list.add(20);
list.add(10);
list.add(30);

Collections.sort(list);

System.out.println(list);
```

Output:

```text
[10, 20, 30]
```

### Requirements

For natural sorting:

1. Elements should be mutually comparable.
2. `null` elements are not permitted because comparison requires ordering.

Otherwise, runtime exceptions such as `ClassCastException` or `NullPointerException` can occur depending on the data.

---

# 15. Sorting List Using Comparator

## Method

```java
public static <T> void sort(
        List<T> list,
        Comparator<? super T> c
)
```

Used for **customized sorting order**.

Example:

```java
import java.util.*;

class MyComparator implements Comparator<Integer> {

    public int compare(Integer x, Integer y) {
        return y.compareTo(x);
    }
}

class Test {
    public static void main(String[] args) {

        List<Integer> list = new ArrayList<>();

        list.add(10);
        list.add(20);
        list.add(5);
        list.add(15);

        Collections.sort(list, new MyComparator());

        System.out.println(list);
    }
}
```

Output:

```text
[20, 15, 10, 5]
```

---

# 16. Searching Elements — `Collections.binarySearch()`

`Collections` provides `binarySearch()` methods for searching elements in a List.

## Natural Ordering

```java
public static <T> int binarySearch(
        List<? extends Comparable<? super T>> list,
        T key
)
```

Used when the List is sorted according to natural ordering.

---

## Customized Ordering

```java
public static <T> int binarySearch(
        List<? extends T> list,
        T key,
        Comparator<? super T> c
)
```

Used when the List is sorted according to a Comparator.

---

# 17. Rules of `binarySearch()`

### Rule 1 — List must already be sorted

Before calling `binarySearch()`, the List must be sorted.

Otherwise:

```text
Result is unpredictable.
```

---

### Rule 2 — Same ordering must be used

If the List was sorted using a Comparator:

```java
Collections.sort(list, comparator);
```

then use the **same Comparator ordering** during binary search:

```java
Collections.binarySearch(list, key, comparator);
```

Otherwise, the result is unpredictable.

---

### Rule 3 — Binary search internally uses binary search algorithm

For a sorted List of `n` elements:

```text
Time complexity ≈ O(log n)
```

---

# 18. Binary Search — Successful and Unsuccessful Result

Suppose:

```java
List<String> list =
        Arrays.asList("A", "K", "Z");
```

Indexes:

```text
Element:     A       K       Z
Index:       0       1       2
```

### Successful search

If target is present, result is an index:

```text
A → 0
K → 1
Z → 2
```

So successful result range:

```text
0 to n - 1
```

For 3 elements:

```text
0 to 2
```

---

## Unsuccessful search

If target is absent, Java returns:

```text
-(insertion point) - 1
```

The insertion point is the position at which the target would be inserted to maintain sorted order.

For 3 elements, possible unsuccessful results are:

```text
-1
-2
-3
-4
```

Therefore:

```text
Unsuccessful range:
-n-1 to -1
```

For `n = 3`:

```text
-4 to -1
```

### Total possible result range

```text
-n-1  to  n-1
```

For 3 elements:

```text
-4 to 2
```

> **Important correction:** The negative range is `-(n+1)` through `-1`, not `-n+1` through `-1`.

---

# 19. Binary Search Example

```java
import java.util.*;

class Test {
    public static void main(String[] args) {

        List<String> list = new ArrayList<>();

        list.add("Z");
        list.add("A");
        list.add("M");
        list.add("K");

        Collections.sort(list);

        System.out.println(list);
        // [A, K, M, Z]

        System.out.println(
            Collections.binarySearch(list, "M")
        );

        System.out.println(
            Collections.binarySearch(list, "B")
        );
    }
}
```

For `"M"`:

```text
Successful → index of M
```

For `"B"`:

```text
Unsuccessful → negative insertion-point result
```

---

# 20. Binary Search with Comparator

```java
List<Integer> list =
        Arrays.asList(15, 0, 20, 10, 5);

Comparator<Integer> c = new MyComparator();

Collections.sort(list, c);

System.out.println(
    Collections.binarySearch(list, 10, c)
);
```

### Important

The List and binary search must use the **same ordering**.

```text
Sort with Comparator C
        ↓
Binary Search with same Comparator C
```

---

# 21. Reversing Elements of List

`Collections` provides methods for reverse operations.

## 21.1 `reverse(List)`

```java
public static void reverse(List<?> list)
```

Used to **reverse the order of elements in the List**.

Example:

```java
List<Integer> list =
        new ArrayList<>(Arrays.asList(10, 20, 30, 40));

Collections.reverse(list);

System.out.println(list);
```

Output:

```text
[40, 30, 20, 10]
```

---

## 21.2 `reverseOrder()`

```java
public static <T> Comparator<T> reverseOrder()
```

Returns a Comparator that imposes the **reverse of natural ordering**.

Example:

```java
List<Integer> list =
        new ArrayList<>(Arrays.asList(10, 20, 30));

Collections.sort(list, Collections.reverseOrder());

System.out.println(list);
```

Output:

```text
[30, 20, 10]
```

### Important distinction

```text
Collections.reverse(list)
        ↓
Actually reverses the current List

Collections.reverseOrder()
        ↓
Returns a Comparator for reverse natural ordering
```

---

# 22. Collections vs Arrays

| Utility Class | Used For |
|---|---|
| `Collections` | Collection objects, especially Lists |
| `Arrays` | Array objects |

```text
Collection Framework
        |
        +---- Collections
        |       ↓
        |   Collection utility methods
        |
        +---- Arrays
                ↓
            Array utility methods
```

---

# 23. Arrays Utility Class

`Arrays` is a utility class that defines several utility methods for **array objects**.

It provides methods for:

- Sorting
- Searching
- Comparing
- Filling
- Converting arrays to Lists
- Other array operations

---

# 24. `Arrays.sort()`

Arrays provides overloaded `sort()` methods.

## 1. Primitive Array

```java
public static void sort(primitive[] a)
```

Sorts primitive arrays according to **natural/numeric ordering**.

Example:

```java
int[] a = {10, 5, 20, 0, 15};

Arrays.sort(a);

System.out.println(Arrays.toString(a));
```

Output:

```text
[0, 5, 10, 15, 20]
```

---

## 2. Object Array

```java
public static void sort(Object[] a)
```

Sorts an Object array according to natural ordering.

Example:

```java
String[] s = {"Z", "A", "M"};

Arrays.sort(s);

System.out.println(Arrays.toString(s));
```

Output:

```text
[A, M, Z]
```

Objects must be mutually comparable.

---

## 3. Object Array with Comparator

```java
public static <T> void sort(
        T[] a,
        Comparator<? super T> c
)
```

Used for customized sorting.

Example:

```java
String[] s = {"A", "Z", "M"};

Arrays.sort(s, new MyComparator());

System.out.println(Arrays.toString(s));
```

---

# 25. `Arrays.sort()` — Important Rule

```text
Primitive array
    ↓
Natural ordering only

Object array
    ↓
Natural ordering
       OR
Customized ordering using Comparator
```

---

# 26. `Arrays.binarySearch()`

Arrays provides binary-search methods for arrays.

Important forms include:

```java
binarySearch(primitiveArray, primitiveKey)
```

```java
binarySearch(Object[] a, Object key)
```

```java
binarySearch(Object[] a, Object key, Comparator c)
```

---

# 27. Rules of `Arrays.binarySearch()`

The rules are essentially the same as `Collections.binarySearch()`.

### 1. Array must be sorted

```text
Before binary search:
sort first
```

Otherwise result is unpredictable.

### 2. Same ordering must be used

If Object array is sorted using a Comparator, binary search should use the same Comparator.

### 3. Successful search

Returns the index of the searched element.

### 4. Unsuccessful search

Returns:

```text
-(insertion point) - 1
```

---

# 28. `Arrays.binarySearch()` Example

```java
import java.util.*;

class Test {
    public static void main(String[] args) {

        int[] a = {10, 5, 20, 0, 15};

        Arrays.sort(a);

        System.out.println(Arrays.toString(a));
        // [0, 5, 10, 15, 20]

        System.out.println(
            Arrays.binarySearch(a, 15)
        );

        System.out.println(
            Arrays.binarySearch(a, 12)
        );
    }
}
```

---

# 29. `Arrays.asList()`

## Purpose

`Arrays.asList()` is used to obtain a **List view of an array**.

Conceptually:

```java
List<T> list = Arrays.asList(array);
```

It does **not create an independent, resizable List containing copied elements**.

Instead, it provides a **fixed-size List backed by the original array**.

---

# 30. Array → List View

Consider:

```java
String[] a = {"A", "B", "C"};

List<String> list = Arrays.asList(a);
```

Conceptually:

```text
             backed by
+---------------------------+
|        String[] a         |
|                           |
|  [ "A" ][ "B" ][ "C" ]   |
+---------------------------+
        ↑             ↑
        |             |
        |             |
   array reference  list view
                       |
                       v
                Arrays.asList(a)
```

The List is a **view backed by the array**.

---

# 31. Changes Through Array Reference

If we modify the array:

```java
String[] a = {"A", "B", "C"};

List<String> list = Arrays.asList(a);

a[0] = "X";

System.out.println(list);
```

Output:

```text
[X, B, C]
```

The change is visible through the List because the List is backed by the same array.

---

# 32. Changes Through List Reference

If we modify an element using `set()`:

```java
String[] a = {"A", "B", "C"};

List<String> list = Arrays.asList(a);

list.set(1, "Y");

System.out.println(Arrays.toString(a));
```

Output:

```text
[A, Y, C]
```

The change is reflected in the original array.

Therefore:

```text
Array change
      ↓
List reflects change

List.set()
      ↓
Array reflects change
```

---

# 33. Size-changing Operations Are Not Allowed

The List returned by `Arrays.asList()` is fixed-size.

Therefore:

```java
list.add("D");
```

or

```java
list.remove("A");
```

causes:

```text
UnsupportedOperationException
```

### Why?

Because changing the List size would require changing the size of the backing array, and Java arrays have fixed size.

---

# 34. `set()` Is Allowed

Although size-changing operations are not allowed, replacing an existing element is allowed.

```java
list.set(0, "X");
```

This works because the size remains unchanged.

```text
Before:
[A, B, C]

set(0, "X")

After:
[X, B, C]
```

---

# 35. ArrayStoreException with Incompatible Element

Suppose:

```java
String[] a = {"A", "B", "C"};

List list = Arrays.asList(a);

list.set(0, 100);
```

At runtime:

```text
ArrayStoreException
```

### Why?

The backing array is actually a:

```java
String[]
```

An `Integer` cannot be stored inside a `String[]`.

### Important distinction

With a properly typed List:

```java
List<String> list = Arrays.asList(a);

list.set(0, 100);
```

the compiler itself rejects the code because `100` is not a `String`.

The `ArrayStoreException` situation is especially relevant when raw/unchecked types allow an incompatible value to reach the backing array.

---

# 36. Complete `Arrays.asList()` Example

```java
import java.util.*;

class Test {
    public static void main(String[] args) {

        String[] a = {"A", "B", "C"};

        List<String> list = Arrays.asList(a);

        System.out.println(list);
        // [A, B, C]

        // Array → List
        a[0] = "X";
        System.out.println(list);
        // [X, B, C]

        // List → Array
        list.set(1, "Y");
        System.out.println(Arrays.toString(a));
        // [X, Y, C]

        // Not allowed: changes size
        // list.add("D");       // UnsupportedOperationException
        // list.remove("X");   // UnsupportedOperationException
    }
}
```

---

# 37. Quick Comparison — Queue Methods

| Method | Operation | Empty Queue |
|---|---|---|
| `offer()` | Insert | `false` if insertion fails |
| `add()` | Insert | `IllegalStateException` if insertion fails |
| `peek()` | Retrieve without removal | `null` |
| `element()` | Retrieve without removal | `NoSuchElementException` |
| `poll()` | Remove + return | `null` |
| `remove()` | Remove + return | `NoSuchElementException` |

---

# 38. Quick Comparison — NavigableSet

| Method | Meaning |
|---|---|
| `floor(e)` | Greatest element `<= e` |
| `lower(e)` | Greatest element `< e` |
| `ceiling(e)` | Smallest element `>= e` |
| `higher(e)` | Smallest element `> e` |
| `pollFirst()` | Remove + return lowest element |
| `pollLast()` | Remove + return highest element |
| `descendingSet()` | Reverse-order view |

---

# 39. Quick Comparison — NavigableMap

| Method | Meaning |
|---|---|
| `floorKey(k)` | Greatest key `<= k` |
| `lowerKey(k)` | Greatest key `< k` |
| `ceilingKey(k)` | Smallest key `>= k` |
| `higherKey(k)` | Smallest key `> k` |
| `pollFirstEntry()` | Remove + return lowest-key entry |
| `pollLastEntry()` | Remove + return highest-key entry |
| `descendingMap()` | Reverse-order map view |

---

# 40. Important Interview Traps

### Queue

```text
Queue → FIFO by default
PriorityQueue → priority-based processing
LinkedList → implements Queue
```

### PriorityQueue

```text
Duplicates → allowed
null → not allowed
Insertion order → not preserved
Natural ordering → mutually comparable elements required
Comparator → custom priority/order
```

### NavigableSet

```text
floor  → <=
lower  → <
ceiling → >=
higher → >
```

### NavigableMap

Same navigation rules apply to **keys**:

```text
floorKey
lowerKey
ceilingKey
higherKey
```

### Binary Search

```text
List/array must be sorted first.

Successful:
0 to n-1

Unsuccessful:
-(n+1) to -1

Result:
-(insertion point) - 1
```

### `Collections` vs `Arrays`

```text
Collections → Collection objects
Arrays      → Array objects
```

### `Arrays.asList()`

```text
Array ↔ fixed-size List view

Array modification
        ↕
List modification using set()

add/remove → UnsupportedOperationException
incompatible value reaching backing array → ArrayStoreException
```

---

# 41. One-Page Revision Map

```text
Collection Framework
│
├── Queue
│   │
│   ├── LinkedList
│   │     └── FIFO
│   │
│   └── PriorityQueue
│         ├── Priority-based processing
│         ├── Duplicates allowed
│         ├── null not allowed
│         ├── Natural ordering
│         └── Comparator ordering
│
├── Set
│   └── SortedSet
│       └── NavigableSet
│           └── TreeSet
│               ├── floor
│               ├── lower
│               ├── ceiling
│               ├── higher
│               ├── pollFirst
│               ├── pollLast
│               └── descendingSet
│
└── Map
    └── SortedMap
        └── NavigableMap
            └── TreeMap
                ├── floorKey
                ├── lowerKey
                ├── ceilingKey
                ├── higherKey
                ├── pollFirstEntry
                ├── pollLastEntry
                └── descendingMap


Utility Classes
│
├── Collections
│   ├── sort()
│   ├── binarySearch()
│   ├── reverse()
│   └── reverseOrder()
│
└── Arrays
    ├── sort()
    ├── binarySearch()
    └── asList()
```

---

# 42. Final Revision

```text
Queue
→ normally FIFO

PriorityQueue
→ process according to priority

NavigableSet
→ navigation around elements

NavigableMap
→ navigation around keys

Collections
→ utility methods for Collection objects

Arrays
→ utility methods for arrays

binarySearch()
→ sorted data required

Arrays.asList()
→ fixed-size List view backed by array
```
