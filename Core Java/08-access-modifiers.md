# 🔐 Access Modifiers

---

## 1. Four Access Modifiers

Java me **4 main access modifiers** hain:

| # | Modifier | Keyword | Scope |
|---|----------|---------|-------|
| 1 | **Private** | `private` | Same class only |
| 2 | **Default** | *(no keyword)* | Same package |
| 3 | **Protected** | `protected` | Same package + subclasses (other packages) |
| 4 | **Public** | `public` | Everywhere |

---

## 2. Access Levels — Detailed

### 🔒 `private`

```java
private int x;
```

- Accessible **only inside the same class**
- Other classes (even same package) access nahi kar sakti

### 📦 default (no modifier)

```java
int x;    // no keyword — default access
```

- Accessible within the **same package**
- Dusre package se access nahi kar sakte

### 🛡️ `protected`

```java
protected int x;
```

- Same package ke andar → fully accessible
- **Different package** me sirf **subclass** ke through accessible (via inheritance)

### 🌐 `public`

```java
public int x;
```

- **Everywhere** accessible (subject to class accessibility)

---

## 3. Access Scope Table

| Modifier | Same Class | Same Package | Subclass (Other Package) | Other Package |
|----------|-----------|-------------|------------------------|--------------|
| `private` | ✅ | ❌ | ❌ | ❌ |
| default | ✅ | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

---

## 4. Access Modifier Rule for Method Overriding

Overriding ke time child method ka access level **equal ya wider** hona chahiye:

```text
Parent: private   → not overridden (not inherited)
Parent: default   → child: default / protected / public
Parent: protected → child: protected / public
Parent: public    → child: public only
```

> **Cannot reduce access** — sirf maintain ya increase kar sakte hain.

```java
// ❌ Invalid — weaker access
interface A {
    void show();    // public abstract
}

class B implements A {
    void show() { }  // ❌ attempting to assign weaker access (default < public)
}

// ✅ Valid
class B implements A {
    public void show() { }
}
```

---

## 🧠 Memory Trick

```text
Access Level (narrow → wide):

private → default → protected → public
  🔒        📦         🛡️         🌐
```

> Overriding me: **Left → Right** allowed, **Right → Left** ❌ not allowed.

---

## ⚡ Quick Revision

```text
private   → same class only
default   → same package
protected → same package + subclass inheritance
public    → everywhere

Overriding Rule:
→ child can maintain or INCREASE access
→ child CANNOT reduce access
```

---

[⬅️ Previous: Polymorphism](./07-polymorphism.md) · [📖 Back to Core Java Index](./README.md) · [Next → Varargs ➡️](./09-varargs.md)
