# 📦 Arrays in Java

---

## What is an Array?

Array ek **indexed collection** hai jisme **same data type** ke elements store hote hain.

```java
int[] x = new int[5];
```

Ye 5 integers ke liye space create karta hai.

```text
Index:  0   1   2   3   4
Value: [0] [0] [0] [0] [0]
```

> ⚠️ Invalid index access karne par **`ArrayIndexOutOfBoundsException`** milta hai.

```java
int[] x = new int[5];
System.out.println(x[5]); // ❌ ArrayIndexOutOfBoundsException
```

---

## 🎯 Default Values in Arrays

Jab array create hota hai, elements ko automatically **default values** milte hain.

| Type | Default Value |
|------|--------------|
| `byte` | `0` |
| `short` | `0` |
| `int` | `0` |
| `long` | `0L` |
| `float` | `0.0f` |
| `double` | `0.0d` |
| `char` | `'\u0000'` (null character) |
| `boolean` | `false` |
| Reference types | `null` |

---

## 🖨️ Array Printing Behavior

```java
int[] x = {1, 2, 3, 4};
System.out.println(x);
```

Output kuch aisa hoga:

```text
[I@1b6d3586
```

### Array Type Codes

| Code | Meaning |
|------|---------|
| `[I` | `int[]` |
| `[F` | `float[]` |
| `[J` | `long[]` |
| `[D` | `double[]` |

### ✅ Correct Way to Print

```java
System.out.println(Arrays.toString(x));
// Output: [1, 2, 3, 4]
```

---

## 🔤 char[] — Special Case

`char[]` array println karne par **characters directly print** hote hain (object representation nahi):

```java
char[] x = {50, 51, 52};
System.out.println(x);
```

Output:

```text
234
```

Because:

```text
50 → '2'    51 → '3'    52 → '4'
```

> `System.out.println(char[])` ke liye special overloaded method hai jo characters print karta hai.

---

## 📐 1D and 2D Arrays

### 1D Array

```java
int[] x = {10, 20, 30};
```

### 2D Array

Array ke elements khud arrays hote hain:

```java
int[][] x = new int[3][5];   // 3 rows × 5 columns
```

### Valid Declaration Styles

```java
int[][] x;     // ✅ most common
int [][]x;     // ✅
int x[][];     // ✅
int[] x[];     // ✅
```

---

## 🔀 Jagged (Irregular) Array

Rows ki length alag-alag ho sakti hai:

```java
int[][] x = new int[3][];   // ✅ valid — rows undefined initially

x[0] = new int[2];
x[1] = new int[5];
x[2] = new int[3];
```

Result:

```text
[ ][ ]
[ ][ ][ ][ ][ ]
[ ][ ][ ]
```

### ⚠️ NullPointerException Trap

```java
int[][] x = new int[3][];
System.out.println(x[0][0]); // ❌ NullPointerException
```

`x[0]` abhi `null` hai — row allocate nahi hui.

> **Sirf `new int[3][]` karne se NPE nahi aata.** NPE tab aata hai jab uninitialized row ko **dereference** karein.

---

## 🕵️ Anonymous Array

Array ko bina variable ke create kar sakte hain:

```java
print(new int[]{10, 20, 30});
```

### ⚠️ Important Rule

```java
new int[3]{10, 20, 30};    // ❌ invalid — size + values dono nahi de sakte
new int[]{10, 20, 30};     // ✅ valid
```

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| `int[] println` kya print karta hai? | `[I@hashcode` type representation |
| `char[] println` kya print karta hai? | Characters directly |
| `new int[3][]` valid hai? | ✅ Yes — jagged array |
| `new int[3][]` se NPE aata hai? | ❌ No — dereference par aata hai |
| Anonymous array me size specify kar sakte hain? | ❌ No |
| Array me default values milti hain? | ✅ Yes — type ke according |

---

[📖 Back to Core Java Index](./README.md) · [Next → Variables & Type Casting ➡️](./02-variables-and-typecasting.md)
