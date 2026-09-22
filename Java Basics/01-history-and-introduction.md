# Java History & Introduction

---

## What is Java?

Java ek **high-level, class-based, object-oriented** programming language hai jo **platform independence** ke liye design ki gayi thi.

### Java ke Main Uses

| Domain | Examples |
|--------|----------|
|  Software Development | Desktop apps, tools, utilities |
|  Web Applications | Spring Boot, JSP, Servlets |
|  Enterprise Applications | Banking systems, ERP, CRM |
| ⚙ Backend Development | REST APIs, microservices |
|  Mobile Development | Android apps (historically) |
| ☁ Cloud & Big Data | Hadoop, Spark, Kafka |

---

## The Green Project — How It All Started

### Timeline

```text
December 1990 → Sun Microsystems me project start hua
       Goal   → Aisi technology/language banana jo electronic devices ke liye useful ho
  Project Name → Green Project
```

### 1991 — The Core Team

| Member | Role |
|--------|------|
| **James Gosling** | Programming language / technical development |
| **Mike Sheridan** | Business development |
| **Patrick Naughton** | Graphics / system side |

---

## Why Not C/C++?

Sabse pehle C/C++ ko consider kiya gaya, lekin ek major problem thi:

> **C/C++ platform/system dependent hain** — ek platform par compile ki gayi executable file doosre platform par directly run nahi hoti.

```text
  ┌─────────────┐
  │   first.c   │    Source Code
  └──────┬──────┘
         ▼
  ┌─────────────┐
  │  Compiler   │    Platform-Specific Compiler
  └──────┬──────┘
         ▼
  ┌─────────────┐
  │  first.obj  │    Native Object File
  └──────┬──────┘
         │
    ┌────┴────┐
    ▼         ▼
 Windows   Linux
  Runs     Fails      Same .obj file doosre OS par nahi chalegi
```

Is problem ki wajah se **platform-independent approach** ki zarurat hui.

---

## Oak → Java

1. James Gosling ne ek **new language** develop ki
2. Initially naam **Oak** rakha (ek oak tree ke naam par jo office ke bahar tha)
3. "Oak" naam already **trademarked** tha, isliye naam change karna pada
4. Final naam **Java** rakha gaya

> ☕ Java naam ka connection: Team members coffee peete waqt Java Island (Indonesia) ki coffee se inspired hue — isliye Java!

---

## JDK 1.0 Release

| Detail | Info |
|--------|------|
| **Release Date** | 23 January 1996 |
| **Released By** | Sun Microsystems |
| **Current Owner** | Oracle Corporation (acquired Sun in 2010) |

### Important Milestones

```text
1991 → Green Project started
1995 → Java officially announced at SunWorld
1996 → JDK 1.0 released
2006 → Java open-sourced (OpenJDK)
2010 → Oracle acquired Sun Microsystems
2024 → Java 22 (latest LTS: Java 21)
```

---

> ** Yaad Rakho:** Java ka motto hai — **"Write Once, Run Anywhere" (WORA)**

[Back to Index](./README.md) | [Next: How Java Works](./02-how-java-works.md)
