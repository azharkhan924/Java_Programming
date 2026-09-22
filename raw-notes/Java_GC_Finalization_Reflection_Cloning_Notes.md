# Java Notes — Garbage Collection, Finalization, Reflection & Cloning

> **Style:** Revision-friendly Hinglish + English  
> **Focus:** GC, `finalize()`, `Runtime`, Reflection API, Object Cloning, Shallow vs Deep Cloning, `toString()`

---

## 1. Garbage Collection — कब Object GC के पास पहुँचता है?

जब कोई object **unreferenced** हो जाता है, यानी उस object को refer करने वाला कोई live reference नहीं बचता, तो वह **eligible for Garbage Collection** हो जाता है.

### Important Point

> **Unreferenced object तुरंत destroy नहीं होता.**

JVM जब appropriate समझती है तब Garbage Collector को run कर सकती है और eligible objects को reclaim कर सकती है.

### GC की कोई fixed guarantee नहीं

JVM यह guarantee नहीं देती कि:

- GC exactly कब run होगा
- कौन-सा eligible object exactly कब reclaim होगा

इसलिए:

```java
Object o = new Object();

o = null;
```

अब object GC के लिए **eligible** है, लेकिन इसका मतलब यह नहीं है कि object उसी समय destroy हो गया.

---

## 2. JVM को GC के लिए Request कैसे करें?

हम JVM से GC run करने की **request** कर सकते हैं.

दो common ways:

### Method 1 — `System.gc()`

```java
System.gc();
```

### Method 2 — `Runtime.getRuntime().gc()`

```java
Runtime.getRuntime().gc();
```

### Important

दोनों methods **request** करती हैं; JVM को GC तुरंत run करने के लिए force नहीं करतीं.

```java
System.gc();
```

को internally `Runtime.getRuntime().gc()` के equivalent request के रूप में समझ सकते हैं.

> **Exam Point:** GC invocation is not guaranteed.

---

# 3. Runtime Class

`Runtime` class JVM runtime environment को represent करती है.

### Runtime object कैसे प्राप्त करें?

```java
Runtime r = Runtime.getRuntime();
```

`getRuntime()` हमें `Runtime` class का runtime object देता है.

### `new Runtime()` क्यों नहीं?

```java
Runtime r = new Runtime();   // Error
```

क्योंकि `Runtime` का constructor private है.

इसलिए:

```java
Runtime.getRuntime()
```

का use किया जाता है.

### Singleton Concept

`Runtime` class को commonly **Singleton design** के example के रूप में समझाया जाता है — application को Runtime का एक shared runtime instance मिलता है through:

```java
Runtime.getRuntime()
```

### Important Terms

| Method | Type | Purpose |
|---|---|---|
| `getRuntime()` | `static` factory/access method | Runtime object देता है |
| `gc()` | instance method | GC के लिए request करता है |

Example:

```java
Runtime r = Runtime.getRuntime();
r.gc();
```

या directly:

```java
Runtime.getRuntime().gc();
```

---

# 4. `System.gc()` vs `Runtime.getRuntime().gc()`

```java
System.gc();
```

को internally Runtime के GC request mechanism से जोड़कर समझ सकते हैं.

Conceptually:

```java
System.gc();
```

→ Runtime instance प्राप्त  
→ `gc()` request

इसलिए दोनों का purpose same है:

```java
System.gc();
```

```java
Runtime.getRuntime().gc();
```

> **Important:** किसी भी method से GC को force नहीं किया जा सकता.

---

# 5. `finalize()` और Finalization

Garbage collection से related एक पुराना mechanism है:

```java
finalize()
```

Historically, JVM eligible object को reclaim करने से पहले उसके `finalize()` method को invoke कर सकती थी.

इस process को **finalization** कहा जाता था.

### `finalize()` कहाँ defined है?

`finalize()` historically `Object` class में defined था.

Traditional declaration:

```java
protected void finalize() throws Throwable
```

इसकी default implementation effectively कोई cleanup work नहीं करती थी.

### अगर subclass में override किया है

```java
class Test {

    @Override
    protected void finalize() throws Throwable {
        System.out.println("Cleanup");
    }
}
```

अगर JVM finalization mechanism के through इस object के `finalize()` को invoke करती है, तो overridden method execute हो सकती है.

अगर subclass ने override नहीं किया, तो inherited `Object` implementation लागू होती थी.

---

## 6. Important: `finalize()` की Current Java Status

> **Modern Java में `finalize()` deprecated है and should not be used for resource cleanup.**

`finalize()` को Java 9 में deprecated किया गया था और Java 18 में **deprecated for removal** किया गया.

आज resource cleanup के लिए preferred approaches हैं:

- `try-with-resources`
- `AutoCloseable`
- Explicit `close()` methods

Example:

```java
try (FileInputStream fis = new FileInputStream("data.txt")) {
    // use resource
}
```

इसलिए पुराने Java notes में `finalize()` का concept पढ़ना useful है, लेकिन production code में इसे use नहीं करना चाहिए.

---

# 7. `finalize()` — JVM Call vs Manual Call

`finalize()` को technically manually भी call किया जा सकता है:

```java
obj.finalize();
```

लेकिन **manual call और JVM द्वारा finalization invocation अलग concepts हैं.**

## Manual Call

अगर हम manually call करते हैं:

```java
obj.finalize();
```

तो यह एक **normal method call** की तरह behave करता है.

इसका मतलब:

- यह object को destroy नहीं करता.
- यह object को GC के लिए eligible नहीं बनाता.
- इसे multiple times manually call किया जा सकता है.
- Exception handling normal Java rules के according होगी.

Example:

```java
obj.finalize();
obj.finalize();
```

दोनों calls normal method calls हैं.

---

## JVM Finalization

Historical finalization mechanism में JVM eligible object के लिए `finalize()` invoke कर सकती थी.

Important conceptual points:

- इसका purpose cleanup-related work था.
- Object reclamation का timing guaranteed नहीं था.
- एक object के लिए finalization invocation को repeatedly rely नहीं करना चाहिए.
- exceptions thrown during finalization were handled by the JVM/runtime mechanism rather than propagated like an ordinary caller's method invocation.

> **Exam distinction:** Manual `finalize()` call ≠ Garbage Collection.

---

# 8. `OutOfMemoryError`

अगर application बहुत ज्यादा memory allocate करने की कोशिश करे और JVM required memory provide न कर पाए, तो:

```text
java.lang.OutOfMemoryError
```

आ सकता है.

Example:

```java
public class Demo {
    public static void main(String[] args) {

        int[][] arr = new int[100000][100000];

    }
}
```

इस तरह की huge allocation `OutOfMemoryError` trigger कर सकती है, depending on JVM heap configuration and available memory.

### Important

`OutOfMemoryError` एक `Error` है, `Exception` नहीं.

लेकिन technically `Error` को `Throwable` की तरह catch किया जा सकता है:

```java
try {
    // memory-intensive operation
} catch (OutOfMemoryError e) {
    System.out.println("Out of memory");
}
```

> **Practical point:** `OutOfMemoryError` को normally recoverable application condition की तरह treat नहीं करना चाहिए.

---

# 9. `StackOverflowError`

बहुत deep या infinite recursion से stack memory exhaust हो सकती है.

Example:

```java
public class Demo {

    static void test() {
        test();
    }

    public static void main(String[] args) {
        test();
    }
}
```

Output/error:

```text
java.lang.StackOverflowError
```

यह भी `Error` category में आता है.

---

# 10. Reflection API

Java में runtime पर class के बारे में information inspect करने के लिए **Reflection API** का use किया जाता है.

Package:

```java
java.lang.reflect
```

Reflection के through हम class के:

- methods
- fields
- constructors
- modifiers
- class information

आदि inspect कर सकते हैं.

---

# 11. किसी Class के सभी Declared Methods कैसे Print करें?

`Class` class का method:

```java
getDeclaredMethods()
```

use कर सकते हैं.

Example:

```java
import java.lang.reflect.Method;

class Demo {

    void show() {
        System.out.println("Hello");
    }

    public static void main(String[] args) {

        Class c = Demo.class;

        Method[] methods = c.getDeclaredMethods();

        for (Method m : methods) {
            System.out.println(m);
        }
    }
}
```

### Important

```java
Demo.class
```

से `Demo` class का `Class` object मिलता है.

फिर:

```java
c.getDeclaredMethods();
```

उस class में declared methods की information देता है.

---

# 12. `getClass()` का Use करके Reflection

Object के पास `getClass()` method होता है.

Example:

```java
class Demo {

    void show() {
        System.out.println("Hello");
    }

    public static void main(String[] args) {

        Demo d = new Demo();

        Class c = d.getClass();

        java.lang.reflect.Method[] methods =
                c.getDeclaredMethods();

        for (java.lang.reflect.Method m : methods) {
            System.out.println(m);
        }
    }
}
```

### दोनों approaches

```java
Demo.class
```

और

```java
d.getClass()
```

दोनों से `Class` object प्राप्त किया जा सकता है.

---

# 13. Object Cloning

### Cloning का मतलब

**Cloning = same content वाला अलग object बनाना.**

यानी:

> Same data/content + Separate object/memory

Example:

```java
A a1 = new A();
A a2 = (A) a1.clone();
```

यहाँ `a1` और `a2` अलग objects होंगे.

---

# 14. `Cloneable` Interface

जिस class के object का cloning support करना है, उस class को `Cloneable` implement करना चाहिए when using `Object.clone()`.

```java
class A implements Cloneable {
    // ...
}
```

अगर object cloning के लिए class `Cloneable` implement नहीं करती और `Object.clone()` का mechanism use होता है, तो:

```text
CloneNotSupportedException
```

आ सकता है.

---

# 15. `Cloneable` एक Marker Interface है

**Marker Interface** वह interface है जिसके अंदर कोई method नहीं होती.

Example:

```java
Cloneable
```

`Cloneable` interface में methods नहीं हैं.

इसलिए इसे **Marker Interface** कहा जाता है.

### Location

```java
java.lang.Cloneable
```

### `clone()` कहाँ है?

`clone()` method `Object` class में defined है.

Traditional declaration:

```java
protected native Object clone() throws CloneNotSupportedException
```

> `clone()` का return type `Object` है, इसलिए अक्सर type casting करनी पड़ती है.

Example:

```java
A a2 = (A) a1.clone();
```

---

# 16. Basic Cloning Example

```java
class A implements Cloneable {

    int x;
    int y;

    void get(int x, int y) {
        this.x = x;
        this.y = y;
    }

    void show() {
        System.out.println(x + " " + y);
    }

    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

### Main Method

```java
class Demo {

    public static void main(String[] args)
            throws CloneNotSupportedException {

        A a1 = new A();

        a1.get(10, 20);

        A a2 = (A) a1.clone();

        a1.show();
        a2.show();

        a2.x = 55;

        a1.show();
        a2.show();
    }
}
```

### Output

```text
10 20
10 20
10 20
55 20
```

### समझो

Initially:

```text
a1 → [ x = 10, y = 20 ]

a2 → [ x = 10, y = 20 ]
```

दोनों में same content है लेकिन memory में अलग objects हैं.

जब:

```java
a2.x = 55;
```

करते हैं:

```text
a1 → [ x = 10, y = 20 ]

a2 → [ x = 55, y = 20 ]
```

इससे prove होता है कि cloned object अलग object है.

---

# 17. Shallow Cloning

`Object.clone()` का default cloning behavior **shallow copy** होता है.

### Shallow Copy में

Primitive fields की values copy होती हैं.

लेकिन reference fields में referenced object की reference copy होती है.

Example:

```text
Original Object
      |
      |----> Referenced Object

Cloned Object
      |
      |----> Same Referenced Object
```

यानि दोनों objects के अंदर reference field same object को point कर सकती है.

---

# 18. Shallow Cloning Example

```java
class B {

    int i;

    B(int i) {
        this.i = i;
    }
}

class A implements Cloneable {

    B b1;
    int j;
    int k;

    A(B b1, int j, int k) {
        this.b1 = b1;
        this.j = j;
        this.k = k;
    }

    void show() {
        System.out.println("i = " + b1.i);
        System.out.println("j = " + j);
        System.out.println("k = " + k);
    }

    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

### Main

```java
class Demo {

    public static void main(String[] args)
            throws CloneNotSupportedException {

        B b2 = new B(10);

        A a1 = new A(b2, 20, 30);

        A a2 = (A) a1.clone();

        a1.show();
        a2.show();

        a2.b1.i = 555;
        a2.j = 666;

        a1.show();
        a2.show();
    }
}
```

### Concept

Before modification:

```text
a1 ──────┐
         ↓
       B object
       i = 10
         ↑
a2 ──────┘
```

`a1.b1` और `a2.b1` **same B object** को refer कर रहे हैं.

इसलिए:

```java
a2.b1.i = 555;
```

के बाद `a1.b1.i` भी:

```text
555
```

हो जाएगा.

लेकिन:

```java
a2.j = 666;
```

करने पर `a1.j` change नहीं होगा, क्योंकि `j` primitive field है.

---

# 19. Deep Cloning

**Deep Cloning** में केवल outer object की copy नहीं बनती, बल्कि उसके referenced objects की भी अलग copies बनाई जाती हैं.

### Shallow

```text
Original Object ──┐
                  ├──> Same Referenced Object
Cloned Object ────┘
```

### Deep

```text
Original Object ──> Original Referenced Object

Cloned Object ────> New Referenced Object
```

इसलिए deep clone में nested/reference objects भी independent होते हैं.

---

# 20. Deep Cloning Example

```java
class B implements Cloneable {

    int i;

    B(int i) {
        this.i = i;
    }

    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

```java
class A implements Cloneable {

    B b1;
    int j;
    int k;

    A(B b1, int j, int k) {
        this.b1 = b1;
        this.j = j;
        this.k = k;
    }

    void show() {
        System.out.println("i = " + b1.i);
        System.out.println("j = " + j);
        System.out.println("k = " + k);
    }

    @Override
    public Object clone() throws CloneNotSupportedException {

        B b3 = (B) b1.clone();

        A a3 = new A(b3, j, k);

        return a3;
    }
}
```

### Main

```java
class Demo {

    public static void main(String[] args)
            throws CloneNotSupportedException {

        B b2 = new B(10);

        A a1 = new A(b2, 20, 30);

        A a2 = (A) a1.clone();

        a1.show();
        a2.show();

        a2.b1.i = 555;
        a2.j = 666;

        a1.show();
        a2.show();
    }
}
```

### Deep Cloning में क्या होगा?

`a1.clone()` के अंदर:

```java
B b3 = (B) b1.clone();
```

से नया `B` object बनाया जाता है.

फिर:

```java
A a3 = new A(b3, j, k);
```

से नया `A` object बनाया जाता है.

अब structure:

```text
a1 ──> B1
       i = 10

a2 ──> B3
       i = 10
```

`B1` और `B3` अलग objects हैं.

इसलिए:

```java
a2.b1.i = 555;
```

करने पर:

```text
a1.b1.i = 10
a2.b1.i = 555
```

रहेगा.

---

# 21. Shallow vs Deep Cloning

| Point | Shallow Cloning | Deep Cloning |
|---|---|---|
| Outer object | New object | New object |
| Primitive fields | Values copied | Values copied |
| Reference fields | Reference copied | Referenced object भी copy |
| Nested object | Same object share हो सकता है | Separate object |
| Independence | Partial | Greater independence |
| Default `Object.clone()` | Shallow copy | Deep copy नहीं |

---

# 22. `Object` Class का `toString()`

`Object` class में `toString()` method भी होती है.

Traditional implementation conceptually:

```java
public String toString() {
    return getClass().getName()
            + "@"
            + Integer.toHexString(hashCode());
}
```

इसलिए अगर हम किसी object को print करते हैं:

```java
System.out.println(obj);
```

तो internally:

```java
System.out.println(obj.toString());
```

जैसा behavior होता है.

### Example

```java
class Demo {

    public static void main(String[] args) {

        Demo d = new Demo();

        System.out.println(d);
    }
}
```

Possible output:

```text
Demo@5e2de80c
```

यहाँ:

- `Demo` → class name
- `@` → separator
- `5e2de80c` → hash code का hexadecimal representation

> Exact output अलग हो सकता है.

---

# 23. `toString()` Override क्यों करते हैं?

अगर हमें object का meaningful data print करना है, तो `toString()` override कर सकते हैं.

```java
class Student {

    int id;
    String name;

    Student(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public String toString() {
        return "Student{id=" + id +
               ", name='" + name + "'}";
    }
}
```

अब:

```java
Student s = new Student(101, "Azhar");

System.out.println(s);
```

meaningful output देगा:

```text
Student{id=101, name='Azhar'}
```

---

# 24. Quick Revision Map

```text
Garbage Collection
│
├── Unreferenced Object
│      └── GC Eligible
│
├── GC Timing
│      └── No fixed guarantee
│
├── Request GC
│      ├── System.gc()
│      └── Runtime.getRuntime().gc()
│
├── Runtime
│      ├── private constructor
│      └── getRuntime()
│
├── Finalization
│      └── finalize()  [Deprecated]
│
├── Reflection
│      ├── Demo.class
│      ├── obj.getClass()
│      └── getDeclaredMethods()
│
└── Cloning
       ├── Cloneable
       ├── clone()
       ├── Shallow Cloning
       └── Deep Cloning
```

---

# 25. Important Interview / Exam Questions

### Q1. क्या unreferenced object immediately destroy होता है?

**No.** Unreferenced object GC के लिए eligible होता है. Actual reclamation कब होगी इसकी fixed guarantee नहीं है.

### Q2. JVM को GC request कैसे करते हैं?

```java
System.gc();
```

या:

```java
Runtime.getRuntime().gc();
```

### Q3. क्या `System.gc()` GC को force करता है?

**No.** यह JVM को GC run करने की request करता है.

### Q4. `Runtime` का object कैसे प्राप्त करते हैं?

```java
Runtime.getRuntime();
```

### Q5. `new Runtime()` क्यों नहीं कर सकते?

क्योंकि `Runtime` का constructor private है.

### Q6. `Cloneable` क्या है?

`Cloneable` एक **marker interface** है.

### Q7. Marker interface क्या होता है?

ऐसा interface जिसमें कोई method नहीं होती.

### Q8. `clone()` कहाँ defined है?

`Object` class में.

### Q9. `clone()` का return type क्या है?

```java
Object
```

### Q10. Class `Cloneable` implement नहीं करती तो क्या हो सकता है?

`Object.clone()` का cloning mechanism use करने पर:

```text
CloneNotSupportedException
```

आ सकती है.

### Q11. Default `Object.clone()` किस type की copy करता है?

**Shallow copy.**

### Q12. Shallow और Deep cloning में main difference?

Shallow cloning में reference fields की references copy होती हैं, जबकि deep cloning में referenced objects की भी independent copies बनाई जाती हैं.

### Q13. Reflection में declared methods कैसे प्राप्त करेंगे?

```java
Method[] methods = Demo.class.getDeclaredMethods();
```

या:

```java
Method[] methods = obj.getClass().getDeclaredMethods();
```

### Q14. `System.out.println(obj)` क्या करता है?

Object के लिए string representation प्राप्त करने के लिए `toString()` mechanism का use होता है.

---

# 26. One-Line Revision

> **GC:** Unreferenced object → GC eligible → JVM decides when to reclaim.

> **GC Request:** `System.gc()` / `Runtime.getRuntime().gc()` → request, not guarantee.

> **Runtime:** Private constructor + `getRuntime()` access.

> **Finalization:** Old cleanup mechanism based on `finalize()`; modern Java में deprecated.

> **Reflection:** `Class` object + `getDeclaredMethods()` → methods inspect करें.

> **Cloning:** Same content + separate object.

> **Cloneable:** Marker interface.

> **`clone()`:** `Object` class में, returns `Object`.

> **Shallow Clone:** Nested/reference object shared हो सकता है.

> **Deep Clone:** Nested/reference objects की भी separate copies.

> **`toString()`:** Object की string representation देता है.
