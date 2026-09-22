# Comparable & Comparator — Sorting in Java

> **Summary:** Complete comparison between `Comparable` and `Comparator`, `compareTo()` vs `compare()`, natural vs customized sorting, sorting predefined and custom classes, Java 8 lambda/method reference enhancements, and interview edge cases.

---

## 1. Comparable vs Comparator — Overview

Java provides two interfaces to sort objects:

| Feature | `Comparable` | `Comparator` |
|---------|--------------|--------------|
| **Package** | `java.lang` | `java.util` |
| **Primary Method** | `int compareTo(T o)` (1 parameter) | `int compare(T o1, T o2)` (2 parameters) |
| **Sorting Intent** | **Natural / Default** sorting order | **Customized / Alternative** sorting order |
| **Implementation Location** | Implemented directly by the class being sorted | Implemented in separate classes, lambdas, or factory methods |
| **Modification Needed** | Requires modifying class source code | Does **not** require modifying original class code |
| **Number of Strategies** | Single sorting sequence per class | Unlimited multiple sorting strategies |
| **Primary Usage** | `Collections.sort(list)`, `new TreeSet<>()` | `Collections.sort(list, comp)`, `new TreeSet<>(comp)` |

---

## 2. Architectural Relationship

```text
 Your Entity Class (e.g., Employee)
 |
 --------------------------------------------
 | |
 Class Author Class Consumer
 | |
 implements Comparable passes Comparator
 | |
 Natural Sorting Custom Sorting
 (e.g., sort by ID ascending) (e.g., sort by Name / Salary)
```

---

## 3. When to Use Which?

### Case 1: Predefined Comparable Classes
Classes like `String`, `Integer`, `Double`, `Character`, `Date` already implement `Comparable`:
```java
TreeSet<String> ts = new TreeSet<>();
ts.add("Banana");
ts.add("Apple");
ts.add("Cherry");
System.out.println(ts); // [Apple, Banana, Cherry] (lexicographical natural order)
```
If this natural ordering is not what you need (e.g., reverse alphabetical or by string length), define a `Comparator`.

### Case 2: Predefined Non-Comparable Classes
Classes like `StringBuffer` and `StringBuilder` do **not** implement `Comparable`:
```java
TreeSet<StringBuffer> ts = new TreeSet<>();
ts.add(new StringBuffer("A")); // Throws ClassCastException!
```
To sort `StringBuffer` objects, you **must** supply a custom `Comparator`:
```java
TreeSet<StringBuffer> ts = new TreeSet<>((sb1, sb2) -> sb1.toString().compareTo(sb2.toString()));
ts.add(new StringBuffer("Banana"));
ts.add(new StringBuffer("Apple"));
System.out.println(ts); // [Apple, Banana] (works perfectly!)
```

### Case 3: Custom Domain Classes
The developer who creates the entity defines its default identity sorting via `Comparable`. Any consumer who requires a different order writes a `Comparator`.

---

## 4. `Comparable` Deep Dive

The `Comparable<T>` interface has one method:

```java
public int compareTo(T o);
```

### Sign Contract:
- **Negative integer (`< 0`):** Current object (`this`) comes **before** specified object (`o`).
- **Zero (`0`):** Both objects are considered **equal** for sorting purposes.
- **Positive integer (`> 0`):** Current object (`this`) comes **after** specified object (`o`).

### Custom Class Example:
```java
public class Employee implements Comparable<Employee> {
 private int id;
 private String name;

 public Employee(int id, String name) {
 this.id = id;
 this.name = name;
 }

 public int getId() { return id; }
 public String getName() { return name; }

 @Override
 public int compareTo(Employee other) {
 // Natural ordering: ascending by ID
 return Integer.compare(this.id, other.id);
 }

 @Override
 public String toString() {
 return id + ":" + name;
 }
}
```

Usage:
```java
List<Employee> list = new ArrayList<>();
list.add(new Employee(103, "Sara"));
list.add(new Employee(101, "Azhar"));
list.add(new Employee(102, "Rahul"));

Collections.sort(list); // Uses Employee's compareTo()
System.out.println(list); // [101:Azhar, 102:Rahul, 103:Sara]
```

---

## 5. `Comparator` Deep Dive

The `Comparator<T>` interface defines:

```java
int compare(T o1, T o2);
boolean equals(Object obj); // Inherited from Object, overriding is optional
```

### Sign Contract:
- **Negative integer (`< 0`):** `o1` should appear **before** `o2`.
- **Zero (`0`):** `o1` and `o2` are equal in sort precedence.
- **Positive integer (`> 0`):** `o1` should appear **after** `o2`.

### Implementing Multiple Sorting Strategies:

#### Traditional Class Implementation:
```java
import java.util.Comparator;

public class EmployeeNameComparator implements Comparator<Employee> {
 @Override
 public int compare(Employee e1, Employee e2) {
 return e1.getName().compareTo(e2.getName());
 }
}
```

#### Modern Java 8+ Lambdas & Factory Methods:
```java
// Sort by name alphabetically
Comparator<Employee> byName = Comparator.comparing(Employee::getName);

// Sort by ID descending
Comparator<Employee> byIdDesc = Comparator.comparingInt(Employee::getId).reversed();

// Sort by Name, then by ID if names match
Comparator<Employee> byNameThenId = Comparator.comparing(Employee::getName)
 .thenComparingInt(Employee::getId);
```

Using in `TreeSet`:
```java
TreeSet<Employee> setByName = new TreeSet<>(Comparator.comparing(Employee::getName));
setByName.add(new Employee(103, "Sara"));
setByName.add(new Employee(101, "Azhar"));
setByName.add(new Employee(102, "Rahul"));

System.out.println(setByName); // [101:Azhar, 102:Rahul, 103:Sara]
```

---

## 6. Practical Comparator Examples

### Example 1: Sorting Strings by Length First, Then Alphabetically
Requirement:
1. Shorter strings appear before longer strings.
2. Strings of identical length appear in alphabetical order.

```java
import java.util.*;

public class StringLengthComparator implements Comparator<String> {
 @Override
 public int compare(String s1, String s2) {
 int lenDiff = Integer.compare(s1.length(), s2.length());
 if (lenDiff != 0) {
 return lenDiff;
 }
 return s1.compareTo(s2);
 }

 public static void main(String[] args) {
 TreeSet<String> set = new TreeSet<>(new StringLengthComparator());
 set.add("Apple");
 set.add("Dog");
 set.add("Cat");
 set.add("Banana");
 set.add("Ant");

 System.out.println(set); 
 // Output: [Ant, Cat, Dog, Apple, Banana]
 // Ant, Cat, Dog (length 3, sorted alphabetically)
 // Apple (length 5)
 // Banana (length 6)
 }
}
```

### Example 2: Reversing Natural Numeric Order
```java
Comparator<Integer> desc = (i1, i2) -> i2.compareTo(i1);
// Or simply:
Comparator<Integer> descBuiltIn = Comparator.reverseOrder();

TreeSet<Integer> ts = new TreeSet<>(descBuiltIn);
ts.add(10); ts.add(0); ts.add(15); ts.add(20);
System.out.println(ts); // [20, 15, 10, 0]
```

---

## 7. Deep Dive: The Significance of Returning `0`

In a `TreeSet` or `TreeMap`, uniqueness is determined **strictly by the comparator/compareTo result**, NOT by `equals()`!

```java
class AlwaysZeroComparator implements Comparator<Integer> {
 @Override
 public int compare(Integer o1, Integer o2) {
 return 0; // Every element is considered duplicate
 }
}

TreeSet<Integer> ts = new TreeSet<>(new AlwaysZeroComparator());
ts.add(10);
ts.add(20);
ts.add(30);

System.out.println(ts); // Output: [10]
```
> Note: Only `10` is stored! When `20` and `30` are added, `compare()` returns `0`, causing `TreeSet` to consider them identical duplicates of `10`.

---

## 8. Internationalization: `Collator` & `RuleBasedCollator`

Standard `String.compareTo()` operates on raw Unicode numerical values (ASCII/Unicode code points). For human-language-sensitive comparisons (e.g., handling accents, specific locales):

```java
import java.text.Collator;
import java.util.Locale;

Collator usCollator = Collator.getInstance(Locale.US);
int result = usCollator.compare("resume", "résumé");
```

- `Collator`: Abstract class in `java.text` for locale-sensitive string comparisons.
- `RuleBasedCollator`: Concrete subclass mapping specific character sequences to custom sorting tables.

---

## 9. Interview Quick Traps

| Question / Trap | Correct Answer |
|-----------------|----------------|
| Can a class implement both `Comparable` and be used with `Comparator`? | **Yes.** `Comparable` provides the default natural ordering, while `Comparator` can override it whenever custom sorting is needed. |
| Is `equals()` mandatory to override in `Comparator`? | **No.** `Comparator` declares `boolean equals(Object obj)`, but every class already inherits `equals()` from `java.lang.Object`. |
| Does returning `+1` preserve insertion order in a `TreeSet`? | **No.** A `Comparator` sign contract defines precedence, not insertion ordering. Returning constant `+1` causes an unbalanced or malformed tree traversal. |
| Why does `TreeSet` drop distinct elements if `compare()` returns `0`? | Because `TreeSet` uses `compare() == 0` to check for equality. If `compare()` returns `0`, the element is treated as duplicate and rejected. |
| Can `StringBuffer` be stored in a default `new TreeSet<>()`? | **No.** It throws `ClassCastException` because `StringBuffer` does not implement `Comparable`. Pass an explicit `Comparator` to allow it. |
| What happens if `compareTo()` is inconsistent with `equals()`? | The collection behaves correctly under sorting, but violates the general contract of `Set` (which specifies behavior based on `equals()`). |

---

[Previous: SortedSet & TreeSet](./08-sortedset-and-treeset.md) · [Back to Collections Index](./README.md) · [Next: Master Quick Revision](./10-quick-revision.md)
