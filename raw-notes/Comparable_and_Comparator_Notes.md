# Comparable and Comparator

## 1. Comparable vs Comparator — Basic Idea

### Comparable

- Used for **default/natural sorting order**.
- `Comparable` interface belongs to the `java.lang` package.
- It contains only one important method:

```java
public int compareTo(Object obj)
```

- Classes such as **String** and wrapper classes already implement `Comparable`, so their natural sorting order is available.

### Comparator

- Used for **customized sorting order**.
- `Comparator` interface belongs to the `java.util` package.
- Important methods:

```java
public int compare(Object obj1, Object obj2)
public boolean equals(Object obj)
```

- We use `Comparator` when the default/natural sorting order is not suitable or when we want a different sorting criterion.

---

# 2. When to Use Comparable and Comparator?

## Case 1: Predefined Comparable Classes

Examples:

- `String`
- Wrapper classes such as `Integer`, `Double`, etc.

These classes already provide a default natural sorting order.

For example, `String` has alphabetical natural ordering.

If we are not satisfied with this default ordering, we can define our own sorting using a `Comparator`.

---

## Case 2: Predefined Non-Comparable Classes

Example:

- `StringBuffer`

`StringBuffer` does not provide a natural sorting order through `Comparable`.

Therefore, if we want to sort `StringBuffer` objects, we can define the required sorting using a `Comparator`.

---

## Case 3: Our Own Classes

Examples:

- `Employee`
- `Student`
- `Customer`

The person who writes the class can define its **default natural sorting order** by implementing `Comparable`.

If another person uses that class and is not satisfied with the default sorting order, they can define their own sorting using a `Comparator`.

### Concept

```text
                 Our Own Class
                      |
                   Employee
                      |
          -------------------------
          |                       |
     Class Writer             Class User
          |                       |
   Comparable             Comparator
          |                       |
 Natural Sorting          Custom Sorting
```

---

# 3. Employee Example Using Comparable

Here, `Employee` defines its natural sorting order based on `eid`.

```java
import java.util.*;

class Employee implements Comparable {

    String name;
    int eid;

    Employee(String name, int eid) {
        this.name = name;
        this.eid = eid;
    }

    public String toString() {
        return name + "-" + eid;
    }

    public int compareTo(Object obj) {

        int eid1 = this.eid;
        Employee e = (Employee) obj;
        int eid2 = e.eid;

        if (eid1 < eid2)
            return -1;
        else if (eid1 > eid2)
            return 1;
        else
            return 0;
    }
}
```

### Meaning

The natural sorting order of `Employee` objects is based on `eid`:

```text
Smaller eid  → Smaller position
Larger eid   → Larger position
```

So the objects are sorted in **ascending order of employee ID**.

---

# 4. TreeSet with Comparable

```java
import java.util.*;

class Test {
    public static void main(String[] args) {

        TreeSet t = new TreeSet();

        t.add(new Employee("Azhar", 30));
        t.add(new Employee("Rahul", 10));
        t.add(new Employee("Aman", 20));

        System.out.println(t);
    }
}
```

Output:

```text
[Rahul-10, Aman-20, Azhar-30]
```

The `TreeSet` uses the `compareTo()` method because no `Comparator` is supplied.

---

# 5. TreeSet with Comparator

If we pass a `Comparator` object while creating the `TreeSet`, the `TreeSet` uses the `compare()` method for sorting.

```java
TreeSet t = new TreeSet(new MyComparator());
```

Example:

```java
import java.util.*;

class MyComparator implements Comparator {

    public int compare(Object obj1, Object obj2) {

        Integer i1 = (Integer) obj1;
        Integer i2 = (Integer) obj2;

        if (i1 < i2)
            return 1;
        else if (i1 > i2)
            return -1;
        else
            return 0;
    }
}

class Test {
    public static void main(String[] args) {

        TreeSet t = new TreeSet(new MyComparator());

        t.add(10);
        t.add(0);
        t.add(15);
        t.add(20);
        t.add(20);

        System.out.println(t);
    }
}
```

Output:

```text
[20, 15, 10, 0]
```

The duplicate `20` is not inserted because `compare()` returns `0` for equal elements.

---

# 6. compareTo() vs compare()

## Without Comparator

```java
TreeSet t = new TreeSet();
```

The `TreeSet` uses the object's natural ordering through:

```java
compareTo()
```

Natural ordering for `Integer` is **ascending order**.

---

## With Comparator

```java
TreeSet t = new TreeSet(new MyComparator());
```

The `TreeSet` uses:

```java
compare()
```

This allows us to define a **customized sorting order**.

---

# 7. Different Ways of Writing compare()

Suppose:

```java
Integer i1 = (Integer) obj1;
Integer i2 = (Integer) obj2;
```

### Ascending Order

```java
return i1.compareTo(i2);
```

Example:

```text
0 10 15 20
```

---

### Descending Order

```java
return -i1.compareTo(i2);
```

or:

```java
return i2.compareTo(i1);
```

Example:

```text
20 15 10 0
```

---

### Ascending Order Using Negative of Reverse Comparison

```java
return -i2.compareTo(i1);
```

This gives:

```text
0 10 15 20
```

---

## Important Correction About `+1` and `-1`

`Comparator` does **not** preserve insertion order merely because we return `+1`, nor does it create a reversed insertion order merely because we return `-1`.

The contract is based on the **sign of the result**:

```text
negative → obj1 comes before obj2
zero     → obj1 and obj2 are considered equal for sorting
positive → obj1 comes after obj2
```

Therefore, the exact numeric value (`-1`, `+1`, `-5`, `+10`, etc.) is generally not important; the sign is.

---

# 8. Meaning of Returning 0

If the comparator returns `0` for every comparison:

```java
return 0;
```

then all elements are considered equal according to that comparator.

For a `TreeSet`, this means only the **first element** will normally be retained.

Example:

```java
class MyComparator implements Comparator {

    public int compare(Object obj1, Object obj2) {
        return 0;
    }
}
```

```java
TreeSet t = new TreeSet(new MyComparator());

t.add(10);
t.add(20);
t.add(30);

System.out.println(t);
```

Output:

```text
[10]
```

---

# 9. String Sorting Using Comparator

## Alphabetical Order

```java
class MyComparator implements Comparator {

    public int compare(Object obj1, Object obj2) {

        String s1 = (String) obj1;
        String s2 = (String) obj2;

        return s1.compareTo(s2);
    }
}
```

Example:

```text
Apple
Banana
Cat
Dog
```

---

## Reverse Alphabetical Order

```java
class MyComparator implements Comparator {

    public int compare(Object obj1, Object obj2) {

        String s1 = (String) obj1;
        String s2 = (String) obj2;

        return -s1.compareTo(s2);
    }
}
```

Example:

```text
Dog
Cat
Banana
Apple
```

---

# 10. StringBuffer Sorting Using Comparator

`StringBuffer` does not provide natural ordering through `Comparable`.

Therefore, if we want to sort `StringBuffer` objects, we can convert them into `String` and use `String.compareTo()`.

```java
class MyComparator implements Comparator {

    public int compare(Object obj1, Object obj2) {

        StringBuffer sb1 = (StringBuffer) obj1;
        StringBuffer sb2 = (StringBuffer) obj2;

        String s1 = sb1.toString();
        String s2 = sb2.toString();

        return s1.compareTo(s2);
    }
}
```

### Example

```java
TreeSet t = new TreeSet(new MyComparator());

t.add(new StringBuffer("Banana"));
t.add(new StringBuffer("Apple"));
t.add(new StringBuffer("Cat"));

System.out.println(t);
```

Output:

```text
[Apple, Banana, Cat]
```

---

# 11. Sorting Based on String Length

We can define our own sorting criterion using `Comparator`.

Requirement:

1. Sort strings according to **increasing length**.
2. If two strings have the **same length**, sort them alphabetically.

```java
class MyComparator implements Comparator {

    public int compare(Object obj1, Object obj2) {

        String s1 = (String) obj1;
        String s2 = (String) obj2;

        int l1 = s1.length();
        int l2 = s2.length();

        if (l1 < l2)
            return -1;
        else if (l1 > l2)
            return 1;
        else
            return s1.compareTo(s2);
    }
}
```

### Example

```java
TreeSet t = new TreeSet(new MyComparator());

t.add("Apple");
t.add("Dog");
t.add("Cat");
t.add("Banana");
t.add("Ant");

System.out.println(t);
```

Output:

```text
[Ant, Cat, Dog, Apple, Banana]
```

### Explanation

```text
Ant     → length 3
Cat     → length 3
Dog     → length 3
Apple   → length 5
Banana  → length 6
```

For strings having the same length, alphabetical order is used.

---

# 12. Important Point: Comparable Is Not Required with Comparator

If we are defining our own sorting using a `Comparator`, the objects **need not implement `Comparable`**.

Example:

```java
StringBuffer
```

does not provide natural ordering, but we can still sort `StringBuffer` objects by supplying a suitable `Comparator`.

---

# 13. Homogeneous / Heterogeneous Objects

## Natural Sorting Order — Comparable

If we depend on the default natural sorting order:

- Objects should generally be **homogeneous**.
- Objects should implement `Comparable`.
- Otherwise, sorting may result in `ClassCastException`.

Example:

```java
TreeSet t = new TreeSet();

t.add(10);
t.add("A");
```

Here, `Integer` and `String` have incompatible natural ordering, so a `ClassCastException` can occur.

---

## Customized Sorting — Comparator

If we define our own sorting using a `Comparator`, the comparator can define how objects are compared.

Therefore, even objects that do not implement `Comparable` can be sorted, provided the comparator can handle the object types correctly.

A comparator can also be written to handle heterogeneous objects, but the comparison logic must explicitly support those different types.

---

# 14. Comparable vs Comparator — Quick Difference

| Comparable | Comparator |
|---|---|
| Default / natural sorting | Customized sorting |
| `java.lang` package | `java.util` package |
| Main method: `compareTo()` | Main method: `compare()` |
| Implemented by the class being sorted | Usually implemented as a separate class/object |
| Used when a class has a natural ordering | Used when different/custom orderings are required |
| Modifies the class to define natural ordering | Does not require modifying the class |
| One natural sorting strategy per class | Multiple comparator strategies can be created |

---

# 15. Comparator — Collator and RuleBasedCollator

`Collator` is an abstract class in the `java.text` package used for **language-sensitive text comparison**.

It is useful when ordinary `String.compareTo()` ordering is not appropriate for a particular language.

Example:

```java
import java.text.Collator;
import java.util.*;

class Test {
    public static void main(String[] args) {

        Collator c = Collator.getInstance();

        String s1 = "apple";
        String s2 = "banana";

        System.out.println(c.compare(s1, s2));
    }
}
```

### RuleBasedCollator

`RuleBasedCollator` is a concrete subclass of `Collator`.

It allows sorting/comparison rules to be defined using a set of rules.

Conceptually:

```text
Collator
   |
   └── RuleBasedCollator
```

It is useful when we need customized, language-sensitive ordering rules.

> **Note:** `Collator` is not the only implementation of `Comparator`. The Java API contains other classes that implement `Comparator`; `Collator` is a separate text-comparison utility that provides comparison functionality.

---

# 16. HashSet vs LinkedHashSet vs TreeSet

| Property | HashSet | LinkedHashSet | TreeSet |
|---|---|---|---|
| Underlying data structure | Hash table | Hash table + linked list | Balanced tree |
| Insertion order | Not preserved | Preserved | Not applicable |
| Sorting order | Not applicable | Not applicable | Applicable |
| Duplicate objects | Not allowed | Not allowed | Not allowed |
| Heterogeneous objects | Allowed | Allowed | Not allowed with natural ordering |
| `null` acceptance | Allowed | Allowed | First element may be `null` in some natural-order cases; subsequent insertions can cause `NullPointerException` |

### Remember

```text
HashSet
   ↓
Hash Table
   ↓
No insertion order
No sorting
No duplicates
Heterogeneous objects allowed
null allowed
```

```text
LinkedHashSet
   ↓
Hash Table + Linked List
   ↓
Insertion order preserved
No sorting
No duplicates
Heterogeneous objects allowed
null allowed
```

```text
TreeSet
   ↓
Balanced Tree
   ↓
Sorted order
No duplicates
Natural ordering requires mutually comparable elements
null is generally not supported by natural ordering
```

---

# 17. TreeSet — Natural Ordering vs Comparator

### Natural Ordering

```java
TreeSet t = new TreeSet();
```

The `TreeSet` uses:

```java
compareTo()
```

Example:

```java
t.add(10);
t.add(0);
t.add(15);
t.add(20);
```

Output:

```text
[0, 10, 15, 20]
```

---

### Customized Ordering

```java
TreeSet t = new TreeSet(new MyComparator());
```

The `TreeSet` uses:

```java
compare()
```

For descending order:

```java
class MyComparator implements Comparator {

    public int compare(Object obj1, Object obj2) {

        Integer i1 = (Integer) obj1;
        Integer i2 = (Integer) obj2;

        return i2.compareTo(i1);
    }
}
```

Output:

```text
[20, 15, 10, 0]
```

---

# 18. One-Line Revision

> **Comparable = default/natural sorting order.**

> **Comparator = customized sorting order.**

> **`compareTo()` → one object compares itself with another object.**

> **`compare()` → comparator compares two objects.**

> **TreeSet without Comparator → uses natural ordering.**

> **TreeSet with Comparator → uses customized ordering.**
