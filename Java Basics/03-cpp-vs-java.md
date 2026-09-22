# C++ vs Java — Key Differences

---

## Comparison Table

| Feature | C++ | Java |
|---------|-----|------|
| **Platform** | Platform dependent (native code) | Platform independent (bytecode + JVM) |
| **OOP** | Not purely object-oriented | Strongly object-oriented (but not purely — primitives exist) |
| **Pointers** | Direct pointer arithmetic supported | No direct pointer arithmetic |
| **Memory Management** | Manual (`new`/`delete`, destructors) | Automatic — JVM + Garbage Collector |
| **Multiple Inheritance** | Supported (classes) | Not supported with classes (use interfaces instead) |
| **Access Modifiers** | `public`, `private`, `protected` | `public`, `private`, `protected` + **default** (package-private) |
| **`goto` Statement** | Available and usable | Reserved keyword but **not implemented** |
| **Operator Overloading** | Supported | Not supported (except `+` for String concatenation) |
| **Header Files** | Required (`#include`) | Not needed — uses `import` for packages |
| **Preprocessor** | `#define`, `#ifdef`, etc. | No preprocessor directives |
| **Compilation** | Compiles to native machine code | Compiles to bytecode (`.class`) |
| **Execution Speed** | Generally faster (native execution) | Slightly slower but JIT compiler helps close the gap |
| **Thread Support** | Library-dependent | Built-in (`Thread` class, `Runnable` interface) |
| **Exception Handling** | Optional (no checked exceptions) | Mandatory for checked exceptions (`try-catch` or `throws`) |

---

## Platform Dependence vs Independence

### C++ (Platform Dependent)
```text
  source.cpp  →  Compiler  →  native .exe/.o  →  Only runs on SAME OS
```

### Java (Platform Independent)
```text
  Demo.java  →  javac  →  Demo.class (bytecode)  →  Runs on ANY JVM
```

---

## Why Java Chose These Differences

| C++ Feature Removed | Reason |
|---------------------|--------|
| Pointers | Security + simplicity — prevents memory corruption bugs |
| Manual memory management | Garbage collector prevents memory leaks |
| Multiple class inheritance | Avoids diamond problem complexity |
| Operator overloading | Prevents confusing operator misuse |
| `goto` | Encourages structured programming |

---

## Important Points to Remember

- Java ko often **"C++ minus the complexities"** kaha jata hai
- Java ne C/C++ ka syntax adopt kiya but unsafe features hata diye
- C++ me `struct` aur `class` dono hain; Java me sirf `class` (aur `record` from Java 14+)
- Java me **garbage collection** automatic hai — `System.gc()` sirf suggestion hai, guarantee nahi

---

> ** Interview Tip:** "Java is platform independent but JVM is platform dependent" — ye line yaad rakhna, bahut common interview question hai!

[Back to Index](./README.md) | [Next: Java Features](./04-java-features.md)
