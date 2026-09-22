# Variables & Naming Rules

---

## What is a Variable?

Variable ek **named storage location/container** hai jo value hold karta hai.

```java
int age = 21;
// ↑ ↑ ↑
// type name value
```

| Part | Description |
|------|-------------|
| `int` | Data type — kitni memory lagegi aur kya store hoga |
| `age` | Variable name — identifier |
| `21` | Value — stored data |

> Note: **Every variable ko use karne se pehle** suitable data type ke saath **declare** karna zaroori hai.

---

## Variable Declaration & Initialization

```java
// Declaration only
int marks;

// Declaration + Initialization
int marks = 95;

// Multiple declarations
int a, b, c;

// Multiple declarations with initialization
int x = 10, y = 20, z = 30;
```

---

## Types of Variables

| Type | Where Declared | Scope | Default Value |
|------|---------------|-------|---------------|
| **Local Variable** | Inside a method/block | Within that method/block only | No default — must initialize |
| **Instance Variable** | Inside class, outside methods | Per-object — each object gets its own copy | Has defaults (0, null, false) |
| **Static Variable** | Inside class with `static` keyword | Shared across all objects of the class | Has defaults (0, null, false) |

```java
class Student {
 static String school = "ABC School"; // Static variable
 String name; // Instance variable
 
 void display() {
 int marks = 95; // Local variable
 System.out.println(name + ": " + marks);
 }
}
```

---

## Identifier Naming Rules

Java identifiers ke rules:

| Rule | Example | Valid? |
|------|---------|--------|
| Can start with letter | `age` | Yes |
| Can start with `_` | `_marks` | Yes |
| Can start with `$` | `$total` | Yes |
| Cannot start with digit | `2name` | No |
| No spaces allowed | `student name` | No |
| No special chars (except `_`, `$`) | `my@var` | No |
| Cannot be a Java keyword | `class` | No |
| Case-sensitive | `Age ≠ age ≠ AGE` | (all different) |

### Valid Examples
```java
age
studentName
_marks
$total
myVariable123
```

### Invalid Examples
```java
2name // starts with digit
student name // contains space
class // reserved keyword
my-var // hyphen not allowed
```

---

## Naming Conventions

Java me naming conventions follow karna **best practice** hai (mandatory nahi, but highly recommended):

| What | Convention | Example |
|------|-----------|---------|
| **Variables** | camelCase | `studentName`, `totalMarks` |
| **Constants** | UPPER_SNAKE_CASE | `MAX_VALUE`, `PI` |
| **Methods** | camelCase | `getAge()`, `calculateTotal()` |
| **Classes** | PascalCase | `Student`, `BankAccount` |
| **Packages** | all lowercase | `com.example.app` |
| **Interfaces** | PascalCase | `Serializable`, `Comparable` |

```java
// Good naming examples
int studentAge = 20;
String firstName = "Azhar";
final double MAX_SPEED = 120.5;

// Bad naming examples
int x = 20; // Not descriptive
int STUDENTAGE = 20; // Wrong convention for variable
String s = "Azhar"; // Too short
```

---

## Java Reserved Keywords (Cannot Use as Identifiers)

```text
abstract assert boolean break byte
case catch char class const*
continue default do double else
enum extends final finally float
for goto* if implements import
instanceof int interface long native
new package private protected public
return short static strictfp super
switch synchronized this throw throws
transient try void volatile while

* const and goto are reserved but not used in Java
```

> ** Total: 50 reserved keywords** (including `true`, `false`, `null` which are technically literals, not keywords)

---

[ Back to Index](./README.md) | [Next: Number Literals](./07-number-literals.md)
