# Hibernate & JPA -- Relationships, Cascade & Fetch

> **Topics:** Entity lifecycle deep dive (persist/merge/update/evict/refresh/flush), Flush modes, Relationship types (OneToOne, OneToMany, ManyToOne, ManyToMany), `mappedBy`, Cascade types (ALL, PERSIST, MERGE, REMOVE), `orphanRemoval`, EAGER vs LAZY fetch, N+1 problem, `@JoinColumn`

---

# 86. Ultra-Short Revision

```text
JDBC problems
↓
Too much boilerplate + manual SQL + manual mapping
↓
Object-Relational Impedance Mismatch
↓
ORM
↓
Hibernate

ORM:
Class → Table
Object → Row
Field → Column

JPA:
Specification

Hibernate:
Implementation/Provider

Hibernate flow:
Configuration
→ SessionFactory
→ Session
→ Transaction
→ Entity
→ Persistence Context
→ SQL
→ JDBC
→ DB

Entity:
@Entity
@Id
@Table
@Column
@GeneratedValue

Extra:
@Transient
@Temporal
@Enumerated
@Lob
@CreationTimestamp
@UpdateTimestamp

CRUD:
Create → persist()
Read → get()/find()
Update → modify managed object + commit
Delete → remove()

Persistence Context:
Tracks managed objects
↓
Dirty checking
↓
DB synchronization
```

---

## Final Takeaway

Hibernate is not simply a tool that "removes SQL."

Its main purpose is to let the application work with
**objects/entities**, while Hibernate manages much of the translation
between the object model and relational database model.

The key idea is:

> **You work with entities; Hibernate manages their persistence and
> translates their state into database operations.** \# 87. Entity
> Lifecycle States --- Complete Explanation

Hibernate/JPA entity lifecycle ko samajhna bahut important hai because
Hibernate decide karta hai ki kaunsa object database se associated hai,
kaunsa object track ho raha hai, aur kis object ke changes automatically
database mein synchronize honge.

The four commonly discussed entity states are:

```text
        new Student()
             |
             v
        TRANSIENT
             |
         persist()
             |
             v
        PERSISTENT
          /     \
         /       \
 session.close()  remove()
       |            |
       v            v
   DETACHED       REMOVED
```

> **Important correction:** `DETACHED → REMOVED` direct lifecycle
> transition nahi hai. `remove()` normally managed/persistent entity par
> apply hota hai. Detached entity ko pehle `merge()` karke managed
> instance obtain karke, ya appropriate Hibernate-specific operation se
> managed state mein laakar remove kiya ja sakta hai.

---

## 87.1 Transient State

Transient object ka initial state hota hai.

Example:

```java
Student s = new Student();

s.setName("AAA");
```At this point:

```text
Stored in database?       No
Tracked by Hibernate?     No
Inside Persistence Context? No
Associated with Session?  No
Exists in Java memory?    Yes
```

### Simple definition

> A transient object exists only in Java memory and is not currently
> associated with a Hibernate persistence context.

### Flow

```text
new Student()
     |
     v
Java Memory
     |
     v
TRANSIENT
```No SQL is generated merely because:

```java
Student s = new Student();
```or:

```java
s.setName("AAA");
```

---

# 88. Moving from Transient → Persistent

Use:

```java
session.persist(s);
```Example:

```java
Session session = sessionFactory.openSession();

Transaction tx = session.beginTransaction();

Student s = new Student();

s.setName("AAA");

session.persist(s);

tx.commit();

session.close();
```After:

```java
session.persist(s);
```the entity becomes **managed/persistent** in the current persistence
context.

Conceptually:

```text
Transient Object
      |
      | persist()
      v
Persistence Context
      |
      v
Persistent / Managed Object
```

### Characteristics

  Property                      Persistent
  ----------------------------- -----------------------------------
  Exists in Java memory         Yes
  Stored/synchronized with DB   Yes, as part of persistence/flush
  Tracked by Hibernate          Yes
  Inside Persistence Context    Yes
  Dirty checking                Yes
  Automatic synchronization     Yes

> `persist()` makes the entity managed. The actual `INSERT` SQL is
> synchronized with the database during flush; it is not necessary to
> assume that `persist()` itself always means the SQL is executed
> immediately.

---

# 89. Persistent State and Dirty Checking

This is one of the most important Hibernate concepts.

Example:

```java
Student s =
    session.get(Student.class, 1);

s.setName("BBB");
```Suppose database initially contains:

```text
id = 1
name = "AAA"
```Hibernate loads the entity:

```text
Database
   |
   | SELECT
   v
Student object
   |
   v
Persistence Context
   |
   +--> Current object state
   |
   +--> Snapshot/original state
```Original state:

```text
name = "AAA"
```Application changes:

```java
s.setName("BBB");
```Now:

```text
Old/Snapshot value = AAA
Current value       = BBB
```Hibernate detects the difference during dirty checking.

Conceptually:

```text
Entity loaded
    ↓
Hibernate stores tracking/snapshot information
    ↓
Entity modified
    ↓
Dirty Checking
    ↓
Difference detected
    ↓
UPDATE SQL generated
    ↓
Flush
    ↓
Database
```Example generated SQL:

```sql
UPDATE student
SET name = 'BBB'
WHERE id = 1;
```The exact generated SQL depends on mappings, dialect, dynamic-update
settings and provider/version.

---

# 90. Why No Manual UPDATE Query?

Because the object is managed.

```java
Student s =
    session.get(Student.class, 1);

s.setName("BBB");

tx.commit();
```We did not write:

```sql
UPDATE student SET name = 'BBB' WHERE id = 1;
```Hibernate can generate it automatically because:

```text
Managed Entity
      +
Dirty Checking
      +
Flush
      =
Database Synchronization
```

---

# 91. Detached State

An entity becomes detached when it is no longer associated with the
current persistence context.

Common example:

```java
Student s =
    session.get(Student.class, 1);

session.close();

s.setName("CCC");
```After:

```java
session.close();
```the object still exists in Java memory, but the Session/persistence
context that managed it is gone.

### Characteristics

  Property                               Detached
  -------------------------------------- -------------
  Exists in Java memory                  Yes
  Previously persisted                   Usually yes
  Tracked by current Hibernate Session   No
  Inside current Persistence Context     No
  Automatic dirty checking               No
  Changes automatically synchronized?    No

### Flow

```text
Persistent Entity
      |
      | session.close()
      v
Detached Entity
      |
      | s.setName("CCC")
      v
No automatic UPDATE
```

---

# 92. Important: Detached ≠ Deleted

Detached means:

> Hibernate is no longer currently managing this object.

It does **not** mean:

> The row has been deleted from the database.

For example:

```java
session.close();
```makes the object detached.

The database row can still exist.

---

# 93. How to Reattach / Merge a Detached Object

Suppose:

```java
Student s =
    session1.get(Student.class, 1);

session1.close();

s.setName("CCC");
```Now `s` is detached.

Modern JPA-style approach:

```java
Student managedStudent = session2.merge(s);
```Conceptually:

```text
Detached Object
      |
      | merge()
      v
Managed Copy
      |
      v
Persistence Context
      |
      v
Dirty Checking
      |
      v
UPDATE during flush
```

### Very important

`merge()` does **not necessarily make the original detached object
managed**.

Instead, it copies the detached object's state into a managed instance
and returns that managed instance.

Therefore:

```java
Student managed = session.merge(detached);
```is the important pattern.

---

# 94. `update()` vs `merge()`

Hibernate-native API historically provides:

```java
session.update(detached);
```while JPA standard provides:

```java
entityManager.merge(detached);
```

### `update()`

Conceptually:

```text
Detached object
      ↓
update()
      ↓
associated with Session
```It can fail if another instance with the same identifier is already
associated with that Session.

Example situation:

```java
Student s1 = session.get(Student.class, 1);

Student s2 = detachedStudentWithId1;

session.update(s2);
```The Session already contains an entity with ID `1`, so Hibernate can
report a conflict such as a non-unique object/identifier conflict.

### `merge()`

```java
Student managed = session.merge(detached);
```Hibernate finds/creates the appropriate managed instance and copies
state into it.

This is generally safer for detached-state workflows.

### Comparison

  -----------------------------------------------------------------------
  Feature                 `update()`              `merge()`
  ----------------------- ----------------------- -----------------------
  API style               Hibernate-specific      JPA standard

  Works with detached     Yes                     Yes
  entity                                          

  Returns managed object  Typically no useful new Yes
                          copy                    

  Copies state            Not in the same way     Yes

  Same-ID managed object  Can occur               Designed to reconcile
  conflict                                        into managed instance

  Modern JPA preference   No                      Yes
  -----------------------------------------------------------------------

> Exact behavior and API availability can vary by Hibernate version. For
> portable modern code, prefer JPA `merge()`.

---

# 95. Removed State

Removed means the managed entity has been marked for deletion.

Example:

```java
Student s =
    session.get(Student.class, 1);

session.remove(s);

tx.commit();
```Conceptually:

```text
Managed Entity
      |
      | remove()
      v
REMOVED
      |
      | flush
      v
DELETE SQL
      |
      v
Database Row Deleted
```

### Important

`remove()` normally expects a managed entity.

For a detached object:

```java
session.remove(detachedStudent);
```is not the normal JPA lifecycle operation and can result in an
exception. A detached entity can first be merged:

```java
Student managed = session.merge(detachedStudent);
session.remove(managed);
```

---

# 96. Entity Lifecycle Complete Table

  --------------------------------------------------------------------------------
  State                   Java Memory     Managed by    Persistence      Automatic
                                             Session        Context Dirty Checking
  -------------------- -------------- -------------- -------------- --------------
  Transient                       Yes             No             No             No

  Persistent/Managed              Yes            Yes            Yes            Yes

  Detached                        Yes             No             No             No

  Removed                         Yes      Yes until     Marked for        Removal
                                          removal is        removal   synchronized
                                        synchronized                      on flush
  --------------------------------------------------------------------------------

---

# 97. Entity Lifecycle Example

```java
Session session = sessionFactory.openSession();

Transaction tx = session.beginTransaction();

// 1. TRANSIENT
Student s = new Student();
s.setName("AAA");

// 2. PERSISTENT
session.persist(s);

// Hibernate manages s
s.setName("BBB");

// Dirty checking can detect this change

// 3. REMOVED
// session.remove(s);

// Commit synchronizes pending changes
tx.commit();

// 4. DETACHED
session.close();
```A separate detached example:

```java
Student s =
    session.get(Student.class, 1);

session.close();

// s is detached
s.setName("CCC");
```

---

# 98. Persistence Context Internals

A persistence context can be thought of conceptually as a map-like
identity structure:

```text
Persistence Context
        |
        +--------------------------------+
        | Entity Identity / Primary Key |
        +--------------------------------+
        | (Student, 1) → Student object |
        | (Student, 2) → Student object |
        | (Department, 1) → Department  |
        +--------------------------------+
```The exact internal data structures are implementation details, but this
mental model is extremely useful.

### Why?

It helps explain:

-   Identity guarantee
-   First-level cache
-   Duplicate SELECT avoidance
-   Dirty checking
-   Managed entity tracking

---

# 99. First-Level Cache --- L1 Cache

Hibernate's first-level cache is associated with the persistence
context/Session.

### Key facts

```text
Default?       Yes
Scope?         Session / Persistence Context
Shared?        No, not across all Sessions
Automatic?     Yes
Need manual enabling? Usually no
```

---

# 100. First-Level Cache Example

```java
Student s1 =
    session.get(Student.class, 1);

Student s2 =
    session.get(Student.class, 1);
```First call:

```text
session.get(Student, 1)
        ↓
L1 Cache checked
        ↓
Not present
        ↓
SELECT DB
        ↓
Student object
        ↓
Stored in Persistence Context / L1
```Second call:

```text
session.get(Student, 1)
        ↓
L1 Cache checked
        ↓
Already present
        ↓
Return managed object
```Potentially only one database SELECT is needed.

---

# 101. `s1 == s2`

Within the same persistence context, repeated lookup of the same entity
identity normally returns the same managed instance:

```java
Student s1 =
    session.get(Student.class, 1);

Student s2 =
    session.get(Student.class, 1);

System.out.println(s1 == s2);
```Expected:

```text
true
```This is tied to the persistence context's identity map behavior.

---

# 102. L1 Cache Scope

Very important:

```text
Session 1
   |
   +--> L1 Cache
        Student #1

Session 2
   |
   +--> L1 Cache
        Student #1
```The two Sessions have separate first-level caches.

Therefore:

```text
L1 Cache ≠ Global Cache
```If Session 1 loads Student #1 and then Session 2 loads Student #1,
Session 2 may execute its own SELECT unless another cache layer is
involved.

---

# 103. L1 Cache Lifecycle

```text
Session opens
     ↓
Persistence Context created
     ↓
Entity loaded
     ↓
Entity enters L1 cache
     ↓
Entity reused during Session
     ↓
Session flush/commit
     ↓
Session closes
     ↓
Persistence Context ends
     ↓
L1 cache discarded
```

---

# 104. `session.clear()`

`clear()` removes all managed entities from the current persistence
context.

```java
session.clear();
```Conceptually:

```text
Persistence Context
   |
   +--> Student 1
   +--> Student 2
   +--> Department 1
   |
   | clear()
   v
Persistence Context emptied
```The entities become detached.

### Example

```java
Student s1 =
    session.get(Student.class, 1);

Student s2 =
    session.get(Student.class, 2);

session.clear();
```Now both are no longer managed by this Session.

---

# 105. `session.evict()`

`evict()` is a Hibernate-specific operation that removes one object from
the Session's persistence context.

Example:

```java
session.evict(s1);
```Conceptually:

```text
L1 Cache
   |
   +--> s1
   +--> s2
   +--> s3

evict(s1)

   ↓

L1 Cache
   |
   +--> s2
   +--> s3
```Only `s1` becomes detached.

---

# 106. `clear()` vs `evict()`

  Operation                 Effect
  ------------------------- -------------------------------
  `session.clear()`         Detaches all managed entities
  `session.evict(entity)`   Detaches one specified entity

---

# 107. `refresh()`

`refresh()` reloads the entity's state from the database.

Example:

```java
session.refresh(s);
```Conceptually:

```text
Java Entity
    |
    | refresh()
    v
Database SELECT
    |
    v
Latest DB State
    |
    v
Entity state refreshed
```

### Example

Suppose another transaction changes:

```text
DB:
name = "CCC"
```but your in-memory managed object currently has:

```text
name = "AAA"
```Then:

```java
session.refresh(s);
```reloads the database state into the entity.

> `refresh()` is different from dirty checking. Dirty checking sends
> managed object changes **to** the database; refresh brings database
> state **into** the entity.

---

# 108. `update()`

Hibernate's native:

```java
session.update(detachedObject);
```can associate a detached instance with a Session, subject to Session
identity/conflict rules.

For modern portable JPA code:

```java
entityManager.merge(detachedObject);
```is generally preferred.

---

# 109. Flush

## What is Flush?

Flush means:

> Synchronizing the pending changes in the persistence context with the
> database by executing the necessary SQL statements.

Example:

```java
s.setName("CCC");

session.flush();
```Hibernate may immediately send:

```sql
UPDATE student
SET name = 'CCC'
WHERE id = 1;
```

---

# 110. Flush ≠ Commit

This distinction is extremely important.

```text
flush()
   ↓
SQL sent/executed against DB
```while:

```text
commit()
   ↓
Transaction successfully completed
```Conceptually:

```text
Persistence Context
       |
       | flush()
       v
SQL sent to DB
       |
       | commit()
       v
Transaction committed
```Flush does not itself mean the transaction has permanently committed.

---

# 111. Example: Flush

```java
Transaction tx =
    session.beginTransaction();

Student s =
    session.get(Student.class, 1);

s.setName("CCC");

session.flush();

// UPDATE may already have been executed

tx.commit();
```If the transaction is later rolled back:

```java
tx.rollback();
```the database transaction may undo the SQL changes, depending on
transaction/database behavior.

---

# 112. Automatic Flush

Hibernate/JPA can flush automatically according to the flush mode and
circumstances.

Common case:

```java
tx.commit();
```Before transaction commit, pending changes are normally flushed.

With the default `AUTO` flush behavior, Hibernate may also flush before
executing certain queries when necessary to maintain query consistency.

Therefore:

```text
Manual flush:
session.flush()

Automatic flush:
before commit
and potentially before relevant queries
```Do not assume every query always causes a flush; it depends on flush
mode and query/provider behavior.

---

# 113. Flush Modes

Common JPA flush modes:

```text
AUTO
COMMIT
```Hibernate also has additional/native flush modes.

### `AUTO`

Provider automatically flushes when required.

### `COMMIT`

Provider aims to delay flushing until transaction commit, although
provider-specific behavior can still apply.

For normal applications, default behavior is commonly sufficient.

---

# 114. Complete Lifecycle + Flush Flow

```text
TRANSIENT
   |
   | persist()
   v
PERSISTENT / MANAGED
   |
   | modify object
   v
DIRTY
   |
   | dirty checking
   v
Pending SQL
   |
   | flush()
   v
SQL executed
   |
   | commit()
   v
Transaction committed
```If Session closes without committing/with rollback:

```text
Session / Transaction ends
        ↓
Changes may not be committed
```

---

# 115. Relationships Between Entities

Real applications rarely contain isolated entities.

Example:

```text
Student
   |
   | belongs to
   v
Department
```or:

```text
Student
   |
   | studies in
   v
College
```Relational databases represent relationships using:

-   Foreign keys
-   Join tables
-   Primary keys

Hibernate maps those relationships into Java object
references/collections.

---

# 116. Why Relationships Are Important in ORM

Without relationship mapping, ORM becomes incomplete for real-world
domain models.

Database:

```text
student
+----+-------------+--------+
| id | name        | dept_id|
+----+-------------+--------+
| 1  | Azhar       | 10     |
+----+-------------+--------+

department
+----+-------------+
| id | name        |
+----+-------------+
| 10 | CSE         |
+----+-------------+
```Java:

```java
class Student {
    private Department department;
}
```Hibernate maps:

```text
student.dept_id
       ↕
Department.id
```

---

# 117. Four Main Relationship Types

```text
1. One-to-One
2. One-to-Many
3. Many-to-One
4. Many-to-Many
```

---

# 118. One-to-One

One entity is associated with one entity.

Example:

```text
Person 1 ───── 1 Passport
```Another example:

```text
Student 1 ───── 1 StudentProfile
```

---

# 119. One-to-One Example

### Student

```java
@Entity
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @OneToOne
    @JoinColumn(name = "profile_id")
    private StudentProfile profile;
}
```

### StudentProfile

```java
@Entity
public class StudentProfile {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String phone;
}
```Database:

```text
student
+----+--------+------------+
| id | name   | profile_id |
+----+--------+------------+
| 1  | Azhar  | 101        |
+----+--------+------------+

student_profile
+-----+----------+
| id  | phone    |
+-----+----------+
| 101 | 99999999 |
+-----+----------+
```

---

# 120. Bidirectional One-to-One

Both entities know about each other.

```java
@Entity
public class Student {

    @OneToOne
    @JoinColumn(name = "profile_id")
    private StudentProfile profile;
}
```

```java
@Entity
public class StudentProfile {

    @OneToOne(mappedBy = "profile")
    private Student student;
}
```

### Ownership

`Student` owns the relationship because it contains:

```java
@JoinColumn(name = "profile_id")
```

`StudentProfile` uses:

```java
mappedBy = "profile"
```This means:

> The relationship is mapped by the `profile` field of `Student`.

---

# 121. `mappedBy`

This is one of the most important relationship concepts.

Example:

```java
@OneToMany(mappedBy = "department")
private List<Student> students;
```

`mappedBy` points to the **Java field/property on the owning entity**,
not directly to a database column.

```text
Department
   |
   | mappedBy = "department"
   v
Student.department
```It means:

> "The other side owns/manages the actual relationship mapping."

---

# 122. Owning Side vs Inverse Side

Example:

```java
Student
   |
   | department_id
   v
Department
```If `Student` has:

```java
@ManyToOne
@JoinColumn(name = "department_id")
private Department department;
```then Student is the owning side.

Department may have:

```java
@OneToMany(mappedBy = "department")
private List<Student> students;
```Department is the inverse/non-owning side.

### Why?

The owning side controls the foreign-key relationship.

---

# 123. One-to-Many

One entity has many related entities.

Example:

```text
Department
    |
    +---- Student 1
    +---- Student 2
    +---- Student 3
```

```text
1 Department → Many Students
```

---

# 124. One-to-Many Code

### Department

```java
@Entity
public class Department {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @OneToMany(mappedBy = "department")
    private List<Student> students = new ArrayList<>();
}
```

### Student

```java
@Entity
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @ManyToOne
    @JoinColumn(name = "department_id")
    private Department department;
}
```Database:

```text
department
+----+-------+
| id | name  |
+----+-------+
| 10 | CSE   |
+----+-------+

student
+----+--------+------------+
| id | name   | department_id |
+----+--------+------------+
| 1  | Azhar  | 10         |
| 2  | Ali    | 10         |
| 3  | Sara   | 10         |
+----+--------+------------+
```

---

# 125. Why `@ManyToOne` Often Owns the Relationship

The foreign key normally resides on the "many" table.

```text
Student table
    |
    +--> department_id FK
```Therefore:

```java
@ManyToOne
@JoinColumn(name = "department_id")
private Department department;
```naturally represents the owning side.

---

# 126. Many-to-One

Many entities are related to one entity.

```text
Student  ──┐
Student  ──┤
Student  ──┼──> Department
Student  ──┤
Student  ──┘
```Code:

```java
@ManyToOne
@JoinColumn(name = "department_id")
private Department department;
```Use cases:

-   Many employees → One company
-   Many students → One department
-   Many orders → One customer
-   Many products → One category

---

# 127. Many-to-Many

Many entities on both sides can be related to many entities.

Example:

```text
Student ←→ Course
```A student can take many courses.

A course can have many students.

```text
Student 1 ── Course A
Student 1 ── Course B
Student 2 ── Course A
Student 2 ── Course C
```A relational database normally uses a join table.

---

# 128. Many-to-Many Database Structure

```text
student
+----+-------+
| id | name  |
+----+-------+
| 1  | Azhar |
| 2  | Ali   |
+----+-------+

course
+----+--------+
| id | name   |
+----+--------+
| 10 | Java   |
| 20 | Spring |
+----+--------+

student_course
+------------+-----------+
| student_id | course_id |
+------------+-----------+
| 1          | 10        |
| 1          | 20        |
| 2          | 10        |
+------------+-----------+
```

---

# 129. Many-to-Many Code

### Student

```java
@ManyToMany
@JoinTable(
    name = "student_course",
    joinColumns = @JoinColumn(name = "student_id"),
    inverseJoinColumns = @JoinColumn(name = "course_id")
)
private Set<Course> courses = new HashSet<>();
```

### Course

```java
@ManyToMany(mappedBy = "courses")
private Set<Student> students = new HashSet<>();
```

### Ownership

Student is the owning side because it defines:

```java
@JoinTable(...)
```Course is inverse side:

```java
mappedBy = "courses"
```

---

# 130. Many-to-Many Ownership

```text
Student
   |
   | OWNING SIDE
   |
   +---- student_course ----+
                            |
                            |
Course                      |
   ^                        |
   |                        |
   +---- inverse side ------+
```The owning side controls relationship updates.

If you modify only the inverse side and don't update the owning side,
the join-table relationship may not be synchronized as expected.

---

# 131. Bidirectional Relationship

Bidirectional means both entities contain references to each other.

Example:

```java
Department
    |
    +--> List<Student>
```and:

```java
Student
    |
    +--> Department
```So navigation is possible in both directions:

```java
student.getDepartment();

department.getStudents();
```

---

# 132. Unidirectional Relationship

Only one side knows about the other.

Example:

```java
Student
   |
   +--> Department
```but:

```java
Department
```does not contain:

```java
List<Student>
```Use it when navigation from the reverse side is not required.

---

# 133. Relationship Direction

### Unidirectional

```text
Student → Department
```

### Bidirectional

```text
Student ↔ Department
```

---

# 134. Relationship Mapping Summary

  Mapping         Meaning       Typical Example
  --------------- ------------- -----------------------
  `@OneToOne`     One ↔ One     Student ↔ Profile
  `@OneToMany`    One → Many    Department → Students
  `@ManyToOne`    Many → One    Students → Department
  `@ManyToMany`   Many ↔ Many   Students ↔ Courses

---

# 135. Cascade

Cascade tells Hibernate/JPA whether operations performed on one entity
should propagate to related entities.

Example:

```java
@OneToMany(cascade = CascadeType.ALL)
private List<Student> students;
```Conceptually:

```text
Department
    |
    | cascade
    v
Students
```If an operation is performed on the Department, the configured cascade
operation can propagate to the students.

---

# 136. Cascade Types

JPA defines:

```text
PERSIST
MERGE
REMOVE
REFRESH
DETACH
ALL
```

---

## 136.1 `CascadeType.PERSIST`

```java
cascade = CascadeType.PERSIST
```Persisting the parent cascades persist to the related entity.

Example:

```java
department
   |
   +--> student
```If:

```java
session.persist(department);
```the persist operation can cascade to the students.

---

## 136.2 `CascadeType.MERGE`

```java
cascade = CascadeType.MERGE
```Merge operation cascades.

Useful when detached parent and child entities need to be merged.

---

## 136.3 `CascadeType.REMOVE`

```java
cascade = CascadeType.REMOVE
```Removing the parent can cascade removal to related child entities.

### Caution

Do not use blindly.

Example:

```text
Department
  ↓ REMOVE
Students
```Deleting a department could delete all associated students if the
relationship is configured this way.

---

## 136.4 `CascadeType.REFRESH`

Refresh operation cascades.

```java
entityManager.refresh(parent);
```can refresh associated entities depending on the mapping.

---

## 136.5 `CascadeType.DETACH`

Detach operation cascades.

If parent is detached, associated entities can also be detached
according to the cascade configuration.

---

## 136.6 `CascadeType.ALL`

Equivalent conceptually to:

```text
PERSIST
MERGE
REMOVE
REFRESH
DETACH
```Example:

```java
@OneToMany(cascade = CascadeType.ALL)
private List<Student> students;
```

### Important

`CascadeType.ALL` does **not** mean:

```text
everything automatically forever
```It means all defined JPA cascade operations are propagated.

---

# 137. Cascade vs `orphanRemoval`

These are related but not identical.

### Cascade

Controls propagation of lifecycle operations.

```text
Parent operation
      ↓
Child operation
```

### `orphanRemoval = true`

Means a child that is removed from the parent's relationship can be
treated as an orphan and scheduled for deletion, for supported
one-to-one/one-to-many private-ownership use cases.

Example:

```java
@OneToMany(
    mappedBy = "department",
    cascade = CascadeType.ALL,
    orphanRemoval = true
)
private List<Student> students;
```If:

```java
department.getStudents().remove(student);
```then Hibernate can schedule the orphaned student for deletion.

Use this when the child lifecycle is conceptually owned by the parent.

---

# 138. Cascade Example

```java
@OneToMany(
    mappedBy = "department",
    cascade = CascadeType.ALL,
    orphanRemoval = true
)
private List<Student> students = new ArrayList<>();
```This commonly expresses:

```text
Department
    |
    +--> owns Students
    |
    +--> persist/merge/remove can cascade
    |
    +--> removing a child from collection can delete orphan
```Use carefully because it creates strong lifecycle coupling.

---

# 139. Helper Methods for Bidirectional Relationships

When working with bidirectional relationships, update both sides in
Java.

Example:

```java
public void addStudent(Student student) {
    students.add(student);
    student.setDepartment(this);
}

public void removeStudent(Student student) {
    students.remove(student);
    student.setDepartment(null);
}
```This keeps the Java object graph consistent.

---

# 140. Why `mappedBy` Causes Confusion

Remember:

```java
@OneToMany(mappedBy = "department")
private List<Student> students;
```The value:

```text
"department"
```is NOT:

```text
department_id
```It is the Java property:

```java
private Department department;
```inside `Student`.

Think:

```text
mappedBy
   ↓
Java field/property name
```

---

# 141. Join Column

Example:

```java
@JoinColumn(name = "department_id")
private Department department;
```This means the foreign-key column is:

```text
department_id
```in the owning table.

---

# 142. `@JoinTable`

Used when a relationship is represented through a join table.

Most commonly seen with:

```java
@ManyToMany
```Example:

```java
@JoinTable(
    name = "student_course",
    joinColumns = @JoinColumn(name = "student_id"),
    inverseJoinColumns = @JoinColumn(name = "course_id")
)
```

---

# 143. Relationship Diagram

```text
                DATABASE

+------------+       +----------------+
| department |       |    student     |
+------------+       +----------------+
| id         | <---- | department_id  |
| name       |       | id             |
+------------+       | name           |
                     +----------------+

              JAVA / ORM

Department
   |
   | 1
   |
   | @OneToMany
   v
Students

Student
   |
   | @ManyToOne
   v
Department
```

---

# 144. Fetch Types

Fetch type controls when associated data is loaded.

Two major strategies:

```text
EAGER
LAZY
```

---

# 145. EAGER Loading

Eager means the association is expected to be available immediately when
the entity is loaded.

Conceptually:

```text
Load Student
      ↓
Load Department too
```Example:

```java
@ManyToOne(fetch = FetchType.EAGER)
private Department department;
```

### Advantages

-   Related data available immediately.
-   Can be convenient for small, always-needed relationships.

### Disadvantages

-   Can load unnecessary data.
-   Can create expensive joins or additional SQL.
-   Can cause performance problems in large object graphs.

---

# 146. LAZY Loading

Lazy means associated data is loaded only when needed.

Example:

```java
@OneToMany(fetch = FetchType.LAZY)
private List<Student> students;
```Conceptually:

```text
Load Department
      ↓
Students not loaded yet
      ↓
department.getStudents()
      ↓
Hibernate loads students
```

### Advantages

-   Avoids unnecessary data loading.
-   Better for large collections.
-   Can reduce initial query cost.

### Disadvantages

If the persistence context/session is already closed:

```java
session.close();

department.getStudents();
```the required lazy data may not be available and can result in a lazy
initialization error.

---

# 147. JPA Default Fetch Types

For JPA mappings, the commonly relevant defaults are:

  Association     JPA Default
  --------------- -------------
  `@OneToOne`     EAGER
  `@ManyToOne`    EAGER
  `@OneToMany`    LAZY
  `@ManyToMany`   LAZY

It is often good practice to explicitly choose fetch behavior based on
the use case rather than relying blindly on defaults.

---

# 148. EAGER vs LAZY Diagram

```text
EAGER

Student loaded
     |
     +----> Department loaded immediately


LAZY

Student loaded
     |
     +----> Department not initialized yet
                 |
                 | getDepartment()
                 v
            Load when required
```

---

# 149. Fetch Type and N+1 Problem

Lazy loading can contribute to the N+1 query problem when a collection
of entities is loaded and then each entity's relationship is accessed
individually.

Example:

```text
1 query:
SELECT students ...

Then:
Student 1 → department query
Student 2 → department query
Student 3 → department query
...
```Conceptually:

```text
1 + N queries
```Solutions may include:

-   Fetch joins
-   Entity graphs
-   Batch fetching
-   Appropriate query design
-   DTO projections

Do not solve every N+1 issue simply by making everything EAGER.

---

# 150. Hibernate Caching

Hibernate caching improves performance by reducing repeated database
access.

## What is Cache?

A cache is a faster storage layer that keeps previously fetched/derived
data so that future requests can avoid expensive work.

Conceptually:

```text
Application
    |
    v
Cache
  /   \
hit    miss
 |      |
 v      v
Return  Database
         |
         v
       Cache
```

---
