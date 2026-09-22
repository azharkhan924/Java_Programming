# ⚖️ Comparable & Comparator — Sorting in Java

> **Summary:** `Comparable` vs `Comparator` ka complete difference, `compareTo()` vs `compare()`, natural vs custom sorting, TreeSet/Collections.sort() ke sath usage, String/StringBuffer sorting, aur return value ka meaning.

---

## 1. Comparable vs Comparator — Basic Idea

Java me objects ko sort karne ke liye do approaches hain:

| Feature | `Comparable` | `Comparator` |
|---------|--------------|--------------|
| **Package** | `java.lang` | `java.util` |
| **Interface Method** | `compareTo(T o)` → 1 parameter | `compare(T o1, T o2)` → 2 parameters |
| **Sorting Type** | **Natural / Default** sorting order | **Custom / User-defined** sorting order |
| **Class Modification** | Class khud implement karti hai (`implements Comparable`) | External/separate class implement karti hai |
| **Use Case** | Jab sorting logic class ka inherent behavior ho | Jab multiple alag-alag sorting strategies chahiyen |

---

## 2. When to Use Which?

### Case 1: Predefined Comparable Classes (String, Integer, etc.)
```java
TreeSet<String> ts = new TreeSet<>();
ts.add("Banana"); ts.add("Apple"); ts.add("Cherry");
System.out.println(ts); // [Apple, Banana, Cherry] → Natural alphabetical order
```
> `String`, `Integer`, `Double`, `Character` already `Comparable` implement karte hain. Seedha TreeSet/Collections.sort() me daal do — automatically sort ho jaayega!

### Case 2: Custom Class — Define Your Own `Comparable`
```java
class Employee implements Comparable<Employee> {
    int id;
    String name;
    
    Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public int compareTo(Employee other) {
        return this.id - other.id; // Sort by ID ascending
    }
}

TreeSet<Employee> ts = new TreeSet<>();
ts.add(new Employee(3, "Sara"));
ts.add(new Employee(1, "Azhar"));
ts.add(new Employee(2, "Rahul"));
// Result: [1-Azhar, 2-Rahul, 3-Sara] → Sorted by ID!
```

### Case 3: Multiple Sorting Strategies — Use `Comparator`
```java
// Sort by Name (alphabetical)
Comparator<Employee> byName = (e1, e2) -> e1.name.compareTo(e2.name);

// Sort by ID descending
Comparator<Employee> byIdDesc = (e1, e2) -> e2.id - e1.id;

TreeSet<Employee> byNameSet = new TreeSet<>(byName);   // Name se sort hoga
TreeSet<Employee> byIdDescSet = new TreeSet<>(byIdDesc); // ID descending me sort hoga
```

---

## 3. `compareTo()` vs `compare()` — Return Value Meaning

Dono methods ek `int` return karte hain jiska meaning same hai:

| Return Value | Meaning | Sorting Effect |
|-------------|---------|----------------|
| **Negative** (`< 0`) | Current object **chhota** hai dusre se | Current object pehle aayega |
| **Zero** (`== 0`) | Dono objects **equal** hain | TreeSet me duplicate maana jayega! |
| **Positive** (`> 0`) | Current object **bada** hai dusre se | Current object baad me aayega |

### compareTo() Example (Comparable):
```java
// Inside Employee class:
@Override
public int compareTo(Employee other) {
    // Ascending order by ID
    return this.id - other.id;
    // Descending: return other.id - this.id;
}
```

### compare() Example (Comparator):
```java
// External Comparator:
Comparator<Employee> byId = (e1, e2) -> e1.id - e2.id; // Ascending
Comparator<Employee> byIdDesc = (e1, e2) -> e2.id - e1.id; // Descending
```

---

## 4. Different Ways to Write a Comparator

```java
// Way 1: Anonymous Inner Class
Comparator<Integer> comp1 = new Comparator<Integer>() {
    @Override
    public int compare(Integer a, Integer b) {
        return a - b; // Ascending
    }
};

// Way 2: Lambda Expression (Java 8+) — Recommended! ✅
Comparator<Integer> comp2 = (a, b) -> a - b;

// Way 3: Method Reference (for natural order)
Comparator<Integer> comp3 = Integer::compareTo;

// Way 4: Comparator utility methods (Java 8+)
Comparator<Employee> comp4 = Comparator.comparingInt(e -> e.id);
Comparator<Employee> comp5 = Comparator.comparing(e -> e.name);
Comparator<Employee> comp6 = Comparator.comparingInt(Employee::getId).reversed();
```

---

## 5. ⚠️ Return 0 ka Special Significance in TreeSet

TreeSet me agar `compareTo()` ya `compare()` ka result **0** aaye, toh TreeSet us element ko **duplicate** maan kar **reject** kar deta hai!

```java
// Comparator jo hamesha 0 return kare:
Comparator<String> alwaysEqual = (s1, s2) -> 0;

TreeSet<String> ts = new TreeSet<>(alwaysEqual);
ts.add("A");
ts.add("B");
ts.add("C");

System.out.println(ts.size()); // 1! Sirf "A" store hua, baaki sab "duplicate" maane gaye!
```

---

## 6. String Sorting with Comparator

### Alphabetical (Natural Order):
```java
TreeSet<String> ts = new TreeSet<>();
ts.add("Zebra"); ts.add("Apple"); ts.add("Mango");
System.out.println(ts); // [Apple, Mango, Zebra]
```

### Reverse Alphabetical:
```java
TreeSet<String> ts = new TreeSet<>(Comparator.reverseOrder());
ts.add("Zebra"); ts.add("Apple"); ts.add("Mango");
System.out.println(ts); // [Zebra, Mango, Apple]
```

### Sort by String Length:
```java
Comparator<String> byLength = (s1, s2) -> s1.length() - s2.length();

TreeSet<String> ts = new TreeSet<>(byLength);
ts.add("Banana");    // length 6
ts.add("Hi");        // length 2
ts.add("Apple");     // length 5
ts.add("Java");      // length 4

System.out.println(ts); // [Hi, Java, Apple, Banana]
```

---

## 7. StringBuffer Sorting — The Trap!

`StringBuffer` class `Comparable` implement **nahi** karti:

```java
TreeSet<StringBuffer> ts = new TreeSet<>();
ts.add(new StringBuffer("B"));
ts.add(new StringBuffer("A"));
// ❌ ClassCastException! StringBuffer is NOT Comparable!
```

**Fix:** Custom Comparator do:
```java
Comparator<StringBuffer> sbComp = (sb1, sb2) -> sb1.toString().compareTo(sb2.toString());

TreeSet<StringBuffer> ts = new TreeSet<>(sbComp);
ts.add(new StringBuffer("B"));
ts.add(new StringBuffer("A"));
System.out.println(ts); // [A, B] ✅ Works!
```

---

## 8. Comparable vs Comparator — Grand Comparison Table

| Feature | `Comparable` | `Comparator` |
|---------|--------------|--------------|
| **Package** | `java.lang` | `java.util` |
| **Method** | `compareTo(T o)` | `compare(T o1, T o2)` |
| **Parameters** | 1 (current object vs `o`) | 2 (both objects passed) |
| **Sorting Type** | Natural / Default sorting | Custom / External sorting |
| **Class Change Required?** | ✅ Yes (`implements Comparable`) | ❌ No (external class/lambda) |
| **Multiple Sort Orders?** | ❌ Only 1 natural order per class | ✅ Unlimited Comparators for different sortings |
| **TreeSet Default Constructor** | `compareTo()` use hota hai | — |
| **TreeSet Comparator Constructor** | — | `compare()` use hota hai |
| **Predefined Examples** | `String`, `Integer`, `Double`, `Date` | `Collator`, custom lambda expressions |

---

## 9. Homogeneous vs Heterogeneous Elements

### With Comparable (Natural Sorting) → Homogeneous Zaroori!
```java
TreeSet ts = new TreeSet();
ts.add("Hello");
ts.add(10); // ❌ ClassCastException! String aur Integer compare nahi ho sakte!
```

### With Comparator (Custom Sorting) → Heterogeneous Possible!
```java
Comparator comp = (o1, o2) -> o1.toString().compareTo(o2.toString());

TreeSet ts = new TreeSet(comp);
ts.add("Hello");
ts.add(10); // ✅ Works! Dono toString() ke through compare ho rahe hain
```

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| `Comparable` interface kaunse package me hai? | **`java.lang`** (isliye import ki zaroorat nahi padti). |
| `StringBuffer` Comparable implement karta hai? | ❌ **Nahi!** TreeSet me seedha use karne par `ClassCastException` aayega. |
| Agar `compare()` ya `compareTo()` 0 return kare toh TreeSet kya karega? | Element ko **duplicate** maan kar **reject** kar dega! |
| Ek class me kitni natural sorting orders ho sakti hain? | Sirf **1** (`compareTo()` ek hi baar override hota hai). Multiple sortings ke liye `Comparator` use karo. |
| `Comparator.reverseOrder()` kya karta hai? | Natural order ka **ulta** (descending) sort karta hai. |

---

[⬅️ Previous: SortedSet & TreeSet](./08-sortedset-and-treeset.md) · [📖 Back to Collections Index](./README.md) · [Next → Quick Revision ➡️](./10-quick-revision.md)
