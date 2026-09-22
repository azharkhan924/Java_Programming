# Java String, String Pool, Immutability & StringBuffer

## 1. String in Java

`String` is a class in Java used to represent a sequence of characters.

```java
String s = "abc";
```

A `String` object is **immutable**.

That means once a String object is created, its internal character sequence cannot be changed.

```java
String s = "abc";
s.concat("xyz");

System.out.println(s);   // abc
```

`concat()` creates a new String; it does not modify the existing object.

To keep the new value:

```java
s = s.concat("xyz");
System.out.println(s);   // abcxyz
```

---

# 2. String vs StringBuffer

| Feature | String | StringBuffer |
|---|---|---|
| Nature | Immutable | Mutable |
| Modification | Creates a new object | Modifies the same object |
| Thread safety | Immutable, therefore safe to share | Synchronized methods |
| Performance for repeated changes | Comparatively slower | Better for repeated modifications |
| Use case | Fixed text | Frequently changing text |

Example:

```java
String s = "abc";
s = s.concat("xyz");
```

A new String object is created.

With `StringBuffer`:

```java
StringBuffer sb = new StringBuffer("abc");
sb.append("xyz");
```

The existing `StringBuffer` object is modified.

Output:

```text
abcxyz
```

---

# 3. StringBuilder vs StringBuffer

Both are mutable character sequences.

### StringBuffer

- Mutable
- Synchronized
- Thread-safe for its individual operations
- Generally slower than StringBuilder because of synchronization

```java
StringBuffer sb = new StringBuffer("abc");
sb.append("xyz");
```

### StringBuilder

- Mutable
- Not synchronized
- Generally faster in single-threaded code
- Preferred when thread safety is not required

```java
StringBuilder sb = new StringBuilder("abc");
sb.append("xyz");
```

### Quick difference

```text
String        → Immutable
StringBuffer  → Mutable + Synchronized
StringBuilder → Mutable + Not Synchronized
```

---

# 4. `==` vs `equals()`

This is an important interview concept.

## With String

`String` overrides `equals()` to perform **content comparison**.

```java
String s1 = new String("abc");
String s2 = new String("abc");

System.out.println(s1 == s2);       // false
System.out.println(s1.equals(s2));  // true
```

Why?

- `==` compares references when used with objects.
- `equals()` in `String` compares the contents.

Conceptually:

```text
s1 ──→ "abc"
s2 ──→ "abc"

s1 == s2       → false
s1.equals(s2)  → true
```

## With StringBuffer

`StringBuffer` does not override `equals()` for content comparison.

Therefore:

```java
StringBuffer sb1 = new StringBuffer("abc");
StringBuffer sb2 = new StringBuffer("abc");

System.out.println(sb1 == sb2);       // false
System.out.println(sb1.equals(sb2));  // false
```

Both references point to different objects.

`StringBuffer.equals()` therefore follows the inherited `Object.equals()` behavior, which is reference-based.

### Remember

```text
String.equals()       → content comparison
StringBuffer.equals() → reference comparison
```

---

# 5. Why is String Immutable?

Once a String object is created, its value cannot be changed.

For example:

```java
String s = "abc";

s.concat("xyz");
```

The original `"abc"` object remains unchanged.

If we write:

```java
s = s.concat("xyz");
```

then `s` is made to refer to a **new String object** containing `"abcxyz"`.

This gives String several useful properties:

- Safe sharing between multiple references
- Suitable for String Pool reuse
- Naturally thread-safe because the object cannot be changed
- Useful as a key in hash-based collections
- Helps security-sensitive APIs where values must not change unexpectedly

---

# 6. String Constant Pool (SCP)

The **String Constant Pool (SCP)** is a special area associated with the JVM heap where pooled String objects are maintained.

Example:

```java
String s1 = "abc";
String s2 = "abc";
```

The JVM can reuse the same pooled String object:

```text
s1 ──┐
     ├──→ "abc"
s2 ──┘
```

Therefore:

```java
System.out.println(s1 == s2);  // true
```

because both references can point to the same pooled object.

---

# 7. `new String()` vs String Literal

## String literal

```java
String s = "abc";
```

The JVM checks the String Pool.

- If `"abc"` already exists, the existing object can be reused.
- Otherwise, a new pooled String object is created.

## Using `new`

```java
String s = new String("abc");
```

This explicitly creates a new String object on the heap, even if `"abc"` already exists in the pool.

So:

```java
String s1 = "abc";
String s2 = new String("abc");

System.out.println(s1 == s2);       // false
System.out.println(s1.equals(s2));  // true
```

---

# 8. String Pool Location — Important Correction

Older Java notes often say:

```text
String Pool → Method Area
```

Historically, the String pool was associated with the PermGen area.

From **Java 7 onward**, the String pool was moved to the **heap**.

Also, in Java 8, **PermGen was removed and replaced by Metaspace** for class metadata.

So for modern Java:

```text
String objects / String Pool → Heap
Class metadata → Metaspace
```

Do not memorize the old statement that the String Pool is in the Method Area for current Java versions.

---

# 9. `intern()`

`intern()` returns the canonical pooled representation of a String.

Example:

```java
String s1 = new String("abc");
String s2 = s1.intern();

System.out.println(s2 == "abc");  // true
```

`intern()` checks the String Pool and returns the pooled reference.

---

# 10. String Constructors

Common constructors include:

```java
String s1 = new String();
String s2 = new String("abc");
String s3 = new String(someStringBuffer);
String s4 = new String(someStringBuilder);
String s5 = new String(charArray);
String s6 = new String(byteArray);
```

Examples:

```java
char[] ch = {'a', 'b', 'c'};
String s = new String(ch);

System.out.println(s);  // abc
```

Using a byte array:

```java
byte[] b = {65, 66, 67};
String s = new String(b);

System.out.println(s);  // ABC
```

---

# 11. StringBuffer Constructors

## 1. Default constructor

```java
StringBuffer sb = new StringBuffer();
```

Default capacity:

```text
16
```

## 2. String constructor

```java
StringBuffer sb = new StringBuffer("abc");
```

Initial capacity:

```text
length of string + 16
```

For `"abc"`:

```text
3 + 16 = 19
```

## 3. Capacity constructor

```java
StringBuffer sb = new StringBuffer(100);
```

Initial capacity is `100`.

---

# 12. Length vs Capacity

This distinction is important.

### Length

Number of characters currently stored.

```java
StringBuffer sb = new StringBuffer("abc");
```

```text
length = 3
```

### Capacity

Amount of character storage available before expansion is required.

For:

```java
StringBuffer sb = new StringBuffer("abc");
```

```text
capacity = 3 + 16 = 19
```

So:

```text
length   → current characters
capacity → available internal storage
```

---

# 13. StringBuffer Capacity Growth

When more capacity is required, the capacity is increased.

A commonly used growth rule is:

```text
newCapacity = (oldCapacity + 1) * 2
```

which is equivalent to:

```text
newCapacity = oldCapacity * 2 + 2
```

Example:

```java
StringBuffer sb = new StringBuffer("abc");
```

Initial capacity:

```text
19
```

If expansion is required:

```text
new capacity = (19 + 1) * 2
             = 40
```

The exact implementation details can depend on the JDK implementation, so capacity should not be treated as a fixed performance guarantee.

---

# 14. Important StringBuffer Methods

## 1. `length()`

Returns the number of characters.

```java
StringBuffer sb = new StringBuffer("abc");

System.out.println(sb.length());
```

Output:

```text
3
```

---

## 2. `capacity()`

Returns the current capacity.

```java
StringBuffer sb = new StringBuffer("abc");

System.out.println(sb.capacity());
```

For the standard constructor:

```text
19
```

---

## 3. `charAt(int index)`

Returns the character at the specified index.

```java
StringBuffer sb = new StringBuffer("abc");

System.out.println(sb.charAt(1));
```

Output:

```text
b
```

Indexes start from `0`.

---

## 4. `setCharAt(int index, char ch)`

Changes one character.

```java
StringBuffer sb = new StringBuffer("abc");

sb.setCharAt(1, 'x');

System.out.println(sb);
```

Output:

```text
axc
```

---

## 5. `append()`

Adds data at the end.

```java
StringBuffer sb = new StringBuffer("abc");

sb.append("xyz");

System.out.println(sb);
```

Output:

```text
abcxyz
```

`append()` is overloaded and accepts many types such as:

```text
String
char
byte
short
int
long
float
double
boolean
char[]
Object
```

---

## 6. `insert()`

Inserts data at a specified position.

```java
StringBuffer sb = new StringBuffer("abc");

sb.insert(1, "XYZ");

System.out.println(sb);
```

Output:

```text
aXYZbc
```

`insert()` is also overloaded for many data types.

---

## 7. `delete(int start, int end)`

Deletes characters from `start` (inclusive) to `end` (exclusive).

```java
StringBuffer sb = new StringBuffer("abcdef");

sb.delete(1, 4);

System.out.println(sb);
```

Output:

```text
aef
```

Characters at indexes `1`, `2`, and `3` are removed.

---

## 8. `deleteCharAt(int index)`

Deletes one character.

```java
StringBuffer sb = new StringBuffer("abc");

sb.deleteCharAt(1);

System.out.println(sb);
```

Output:

```text
ac
```

---

## 9. `reverse()`

Reverses the character sequence.

```java
StringBuffer sb = new StringBuffer("abc");

sb.reverse();

System.out.println(sb);
```

Output:

```text
cba
```

---

## 10. `setLength(int newLength)`

Changes the length.

```java
StringBuffer sb = new StringBuffer("abcdef");

sb.setLength(3);

System.out.println(sb);
```

Output:

```text
abc
```

If the new length is greater than the current length, the additional positions are filled with the null character (`\u0000`).

---

## 11. `ensureCapacity(int minimumCapacity)`

Ensures that the buffer has at least the requested capacity.

```java
StringBuffer sb = new StringBuffer();

System.out.println(sb.capacity());  // 16

sb.ensureCapacity(1000);

System.out.println(sb.capacity());
```

The capacity becomes large enough to hold at least 1000 characters.

---

## 12. `trimToSize()`

Reduces the capacity to the current length.

```java
StringBuffer sb = new StringBuffer("abc");

sb.ensureCapacity(1000);
System.out.println(sb.capacity());  // >= 1000

sb.trimToSize();

System.out.println(sb.capacity());  // 3
```

Use this when reducing unused internal storage is useful.

---

# 15. `final` vs Immutability

These two concepts are **not the same**.

## `final` variable

A final reference cannot be reassigned.

```java
final StringBuffer sb = new StringBuffer("abc");

sb.append("xyz");     // allowed
// sb = new StringBuffer("hello");  // not allowed
```

The reference is final, but the object can still be mutable.

Therefore:

```text
final reference ≠ immutable object
```

---

# 16. `final` Object vs Immutable Object

Consider:

```java
final StringBuffer sb = new StringBuffer("abc");
```

Here:

- `sb` cannot refer to another object.
- The StringBuffer object itself can still be changed.

```java
sb.append("xyz");
```

is valid.

But:

```java
sb = new StringBuffer("hello");
```

is invalid.

### Key idea

```text
final → prevents reassignment of the variable/reference

immutable → object state cannot be changed
```

---

# 17. Why String Is Immutable but StringBuffer Is Mutable

### String

```java
String s = "abc";
s.concat("xyz");
```

The original object remains `"abc"`.

### StringBuffer

```java
StringBuffer sb = new StringBuffer("abc");
sb.append("xyz");
```

The same object becomes `"abcxyz"`.

Therefore:

```text
String       → immutable
StringBuffer → mutable
```

---

# 18. Custom Immutable Class

A class can be designed to behave immutably.

Basic rules:

1. Make the class `final` so it cannot be subclassed.
2. Make instance fields `private`.
3. Make fields `final`.
4. Initialize fields through the constructor.
5. Do not provide setters that modify state.
6. If a field refers to a mutable object, use defensive copies.

Example:

```java
final class Test {

    private final int i;

    Test(int i) {
        this.i = i;
    }

    public int getI() {
        return i;
    }
}
```

The state of a `Test` object cannot be changed after construction.

---

# 19. Immutable Update Pattern

If a value needs to be "changed", an immutable object creates/returns another object instead of modifying itself.

Example idea:

```java
final class Test {

    private final int i;

    Test(int i) {
        this.i = i;
    }

    public Test modify(int newValue) {
        if (this.i == newValue) {
            return this;
        }

        return new Test(newValue);
    }
}
```

Usage:

```java
Test t1 = new Test(10);

Test t2 = t1.modify(100);
Test t3 = t1.modify(10);
```

Here:

```text
t2 → new object with 100
t3 → same object as t1
```

because changing from `10` to `10` is unnecessary.

> Note: For true immutability, the field should be `final`, and the class should prevent subclassing (for example, by making it `final`).

---

# 20. Defensive Copying

If an immutable class contains a mutable object such as:

```java
StringBuffer
ArrayList
Date
```

simply making the reference `final` is not enough.

Example:

```java
private final StringBuffer sb;
```

The reference cannot be reassigned, but the StringBuffer itself can change.

For an immutable design, use a defensive copy when accepting or returning mutable objects.

Example:

```java
final class Example {

    private final StringBuffer sb;

    Example(StringBuffer input) {
        this.sb = new StringBuffer(input);
    }

    public StringBuffer getSb() {
        return new StringBuffer(sb);
    }
}
```

This prevents outside code from directly modifying the internal buffer.

---

# 21. Important Interview Points

### String

```text
Immutable
```

### StringBuffer

```text
Mutable
Synchronized
```

### StringBuilder

```text
Mutable
Not synchronized
```

### `==`

For object references:

```text
Reference comparison
```

### `String.equals()`

```text
Content comparison
```

### `StringBuffer.equals()`

```text
Reference-based comparison
```

### `final`

```text
Prevents reassignment of a variable/reference
```

It does **not** automatically make the referenced object immutable.

### String Pool

```text
Modern Java → String Pool is on the heap
```

### StringBuffer

```text
Default capacity = 16
String constructor capacity = string length + 16
```

---

# 22. Quick Revision Table

| Concept | Key Point |
|---|---|
| String | Immutable |
| StringBuffer | Mutable + synchronized |
| StringBuilder | Mutable + not synchronized |
| `==` | Reference comparison for objects |
| `String.equals()` | Content comparison |
| `StringBuffer.equals()` | Reference-based comparison |
| String Pool | Pooled String objects; modern Java heap |
| `new String("abc")` | Creates a distinct String object |
| `intern()` | Returns canonical pooled String |
| StringBuffer default capacity | 16 |
| StringBuffer(String) capacity | `length + 16` |
| `length()` | Current character count |
| `capacity()` | Current storage capacity |
| `append()` | Adds at end |
| `insert()` | Adds at specified index |
| `delete()` | Deletes a range |
| `deleteCharAt()` | Deletes one character |
| `reverse()` | Reverses sequence |
| `setCharAt()` | Replaces one character |
| `setLength()` | Changes logical length |
| `ensureCapacity()` | Ensures minimum capacity |
| `trimToSize()` | Reduces capacity to current length |
| `final` reference | Cannot be reassigned |
| Immutable object | State cannot be changed |

---

# 23. One-Line Memory Trick

```text
String        → Change नहीं कर सकते
StringBuffer  → Change कर सकते + synchronized
StringBuilder → Change कर सकते + faster in normal single-threaded use

final         → Reference बदल नहीं सकते
immutable     → Object की state बदल नहीं सकती

==            → Reference
equals()      → String में Content
```
