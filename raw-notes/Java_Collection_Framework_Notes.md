# Java Collection Framework

> **Interview Note:** Collection Framework is a very important and frequently asked topic in Java interviews.

---

## 1. Need / Purpose of Collection Framework

Suppose we have three values:

```java
int x = 1;
int y = 2;
int z = 3;
```

Here, we have **3 values → 3 variables**.

Now suppose we have **10,000 values**.

Creating 10,000 separate variables is a **bad / worst programming practice** because it makes the program difficult to manage and maintain.

### Solution: Arrays

We can use an array:

```java
Student[] s = new Student[10000];
```

The array allows us to represent a **large number of values using a single variable**.

### Advantages of Arrays

- We can represent a huge number of values using a single variable.
- **Readability is improved.**
- Array elements can be accessed using an index.

---

# 2. Arrays

### Definition

> **An array is an indexed collection of a fixed number of homogeneous data elements.**

Example:

```java
int[] a = new int[5];
```

Here, `a` represents 5 integer values using a single variable.

### Main Advantage

The main advantage of an array is:

> **We can represent multiple values using a single variable.**

---

## 3. Limitations of Arrays

### 1. Fixed Size

The size of an array is fixed once it is created.

```java
int[] a = new int[5];
```

The array can hold exactly 5 elements. Its size cannot be directly increased or decreased.

### 2. Homogeneous Data

An array normally stores elements of the same declared type.

```java
int[] a = {10, 20, 30};
```

All elements are of type `int`.

> **Note:** An `Object[]` can hold objects of different classes because every class ultimately extends `Object`.

Example:

```java
Object[] arr = {
    new Student(),
    new Customer(),
    "Azhar",
    100
};
```

### 3. No Underlying Standard Data Structure

The array concept is not implemented on top of a standard collection data structure.

Therefore, arrays do not provide a rich set of predefined collection methods for common operations.

For many requirements, we have to write the required logic explicitly.

---

# 4. Array vs Collection

| Basis | Array | Collection |
|---|---|---|
| **Size** | Fixed in size | Growable in nature |
| **Memory** | Generally less memory overhead | Generally more memory overhead |
| **Performance** | Generally better for simple indexed access | Generally more overhead than arrays |
| **Data type** | Normally homogeneous | Can hold homogeneous and, where type permits, heterogeneous objects |
| **Underlying structure** | No standard collection data structure | Collection implementations are based on data structures |
| **Ready-made methods** | Very limited | Many predefined methods are available |
| **Primitive types** | Can hold primitives and objects | Collection types hold objects, not primitives directly |
| **Flexibility** | Less flexible because size is fixed | More flexible because size can grow/shrink |

### Memory vs Performance

- **With respect to memory:** Arrays are generally preferred because they have lower overhead.
- **With respect to performance:** Arrays are generally preferred, especially for direct indexed access.
- **With respect to flexibility:** Collections are preferred because their size can change dynamically.

> These are general comparisons. The actual choice depends on the requirement and implementation.

---

# 5. What is a Collection?

### Definition

> **If we want to represent a group of individual objects as a single entity, then we should go for a collection.**

Example:

```java
List<String> names = new ArrayList<>();
```

Here, multiple `String` objects are represented as a single collection object.

---

# 6. What is Collection Framework?

### Definition

> **The Collection Framework defines a set of interfaces and classes that provide a standard architecture for representing and manipulating groups of objects.**

It provides ready-made data structures and methods for common operations such as:

- Adding elements
- Removing elements
- Searching elements
- Sorting elements
- Iterating through elements
- Checking size

### Java vs C++

| Java | C++ |
|---|---|
| Collection Framework | STL (Standard Template Library) |
| Collection interfaces/classes | Containers and STL components |

---

# 7. Collection Interface

```java
java.util.Collection
```

### Definition

> **Collection is an interface used to represent a group of individual objects as a single entity.**

The `Collection` interface defines common methods that are applicable to collection objects.

Some common methods are:

```java
add()
remove()
contains()
size()
isEmpty()
clear()
```

### Important Points

- `Collection` is an **interface**.
- It is generally considered the **root interface of the collection hierarchy** for groups of objects.
- Interfaces such as `List`, `Set`, and `Queue` extend `Collection`.
- There is no concrete class that directly implements `Collection` as the standard general-purpose implementation; concrete classes implement subinterfaces such as `List`, `Set`, or `Queue`.

### Basic Hierarchy

```text
                 Iterable
                    |
               Collection
              /     |      \
            List    Set    Queue
```

> `Map` is part of the Java Collections Framework, but **Map does not extend Collection**.

---

# 8. Collection vs Collections

These two terms are commonly confused.

## Collection

`Collection` is an **interface**.

```java
java.util.Collection
```

It represents a group of individual objects as a single entity.

Example:

```java
Collection<String> c = new ArrayList<>();
```

---

## Collections

`Collections` is a **utility class** in the `java.util` package.

```java
java.util.Collections
```

It provides static utility methods for working with collection objects.

Examples:

```java
Collections.sort(list);
Collections.reverse(list);
Collections.shuffle(list);
Collections.max(list);
Collections.min(list);
```

### Remember

```text
Collection  → Interface
Collections → Utility class
```

---

# 9. Important Collection Interfaces

The Java Collections Framework contains several important interfaces.

The commonly discussed core interfaces include:

- `Collection`
- `List`
- `Set`
- `Queue`
- `Deque`
- `Map`
- `SortedSet`
- `NavigableSet`
- `SortedMap`
- `NavigableMap`

> **Important:** `Map` is not a child of `Collection`. It represents **key-value mappings** separately within the Collections Framework.

---

# 10. List Interface

```java
java.util.List
```

### Definition

> **List is an interface and a child interface of `Collection`.**

If we want to represent a group of individual objects as a single entity where:

- **Duplicates are allowed**
- **Insertion order is preserved**

then we should go for a **List**.

Example:

```java
List<String> names = new ArrayList<>();

names.add("A");
names.add("B");
names.add("A");
```

Output/order:

```text
A
B
A
```

The duplicate `"A"` is allowed, and insertion order is maintained.

### Common List Implementations

```text
List
 |
 +-- ArrayList
 +-- LinkedList
 +-- Vector
      |
      +-- Stack
```

---

# Quick Revision

### Array

> Fixed-size, indexed collection of homogeneous elements.

### Collection

> Represents a group of individual objects as a single entity.

### Collection Framework

> A standard architecture of interfaces and classes for storing and manipulating groups of objects.

### Collection

> Interface.

### Collections

> Utility class containing static methods for collection operations.

### List

> Collection where **duplicates are allowed** and **insertion order is preserved**.

---

## Interview One-Liners

**Q. Why do we need Collection Framework?**

To represent and manipulate a group of objects efficiently using ready-made data structures and methods.

**Q. What is the main limitation of an array?**

Its size is fixed.

**Q. Can a collection store primitive values directly?**

No. Collections store objects. Primitive values are handled through their corresponding wrapper classes using autoboxing.

Example:

```java
List<Integer> numbers = new ArrayList<>();
numbers.add(10); // int 10 is autoboxed to Integer
```

**Q. Difference between Collection and Collections?**

`Collection` is an interface, whereas `Collections` is a utility class containing static methods for operating on collections.

**Q. Is Map a child of Collection?**

No. `Map` is a separate interface in the Java Collections Framework for storing key-value pairs.

**Q. What are the main properties of List?**

Duplicates are allowed and insertion order is preserved.
