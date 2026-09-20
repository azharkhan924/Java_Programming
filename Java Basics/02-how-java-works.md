# ⚙️ How Java Works — Compilation & Execution

---

## 🔄 The Big Picture

Java ka fundamental concept: **source code → bytecode → JVM execution**

```text
  ┌──────────────┐
  │  Demo.java   │   Source Code (.java file)
  └──────┬───────┘
         ▼
  ┌──────────────┐
  │    javac     │   Java Compiler
  └──────┬───────┘
         ▼
  ┌──────────────┐
  │  Demo.class  │   Bytecode (.class file)
  └──────┬───────┘
         ▼
  ┌──────────────┐
  │     JVM      │   Java Virtual Machine
  └──────┬───────┘
         ▼
  ┌──────────────┐
  │   main()     │   Program Execution
  └──────────────┘
```

---

## 📝 Step-by-Step Flow

| Step | What Happens | Detail |
|------|-------------|--------|
| 1️⃣ | **Write** | `.java` file me source code likho |
| 2️⃣ | **Compile** | `javac` compiler source code ko **bytecode** me convert karta hai |
| 3️⃣ | **Store** | Bytecode `.class` file me store hota hai |
| 4️⃣ | **Load** | JVM class ko **ClassLoader** ke through load karti hai |
| 5️⃣ | **Verify** | **Bytecode Verifier** bytecode ki validity check karta hai |
| 6️⃣ | **Execute** | JVM bytecode ko execute karti hai (interpret + JIT compile) |

---

## 🚀 Entry Point — `main()` Method

Java application ka entry point hamesha:

```java
public static void main(String[] args) {
    // Program execution starts here
}
```

### Har keyword ka meaning:

| Keyword | Kyu? |
|---------|------|
| `public` | JVM ko access chahiye — kisi bhi jagah se callable hona chahiye |
| `static` | Object banaye bina call hona chahiye |
| `void` | Kuch return nahi karta |
| `main` | JVM isi naam ka method dhundhta hai |
| `String[] args` | Command-line arguments receive karne ke liye |

---

## 🏗️ Platform Independence — How?

```text
                    Demo.class (Bytecode)
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
    ┌──────────┐   ┌──────────┐   ┌──────────┐
    │ JVM for  │   │ JVM for  │   │ JVM for  │
    │ Windows  │   │  Linux   │   │  macOS   │
    └────┬─────┘   └────┬─────┘   └────┬─────┘
         ▼              ▼              ▼
      Windows         Linux          macOS
        ✅              ✅              ✅
```

> **Same `.class` bytecode** ko different OS ke JVM par run kiya ja sakta hai — **yahi hai WORA (Write Once, Run Anywhere)!**

---

## 🔑 Key Components

### JDK (Java Development Kit)
- Complete development toolkit
- Includes: `javac` compiler + JRE + development tools
- **For developers**

### JRE (Java Runtime Environment)
- Runtime environment for Java programs
- Includes: JVM + core libraries
- **For running Java applications**

### JVM (Java Virtual Machine)
- Bytecode execute karta hai
- Platform-specific hota hai (lekin bytecode platform-independent hai)
- **Heart of Java's platform independence**

```text
┌─────────────────────────────────┐
│             JDK                 │
│  ┌───────────────────────────┐  │
│  │           JRE             │  │
│  │  ┌─────────────────────┐  │  │
│  │  │        JVM          │  │  │
│  │  └─────────────────────┘  │  │
│  │  + Core Libraries         │  │
│  └───────────────────────────┘  │
│  + javac compiler               │
│  + Dev Tools (jdb, javadoc)     │
└─────────────────────────────────┘
```

---

> **💡 Yaad Rakho:** Java **"compile once"** approach use karta hai — source code → bytecode. JVM bytecode ko OS-specific instructions me translate karta hai at runtime.

[⬅️ Back to Index](./README.md) | [Next: C++ vs Java ➡️](./03-cpp-vs-java.md)
