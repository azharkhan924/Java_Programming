# Hibernate & JPA -- Caching & Advanced Topics

> **Topics:** First-Level (L1) cache, Second-Level (L2) cache, Query Cache, EHCache configuration, Cache statistics, When NOT to cache, Complete entity lifecycle diagrams, Relationship + Fetch + Cascade mental models, Interview golden points, Practical checklists

---

# 151. Why Cache Is Important

Database access can be expensive compared with reading data already
available in memory/cache.

Caching can reduce:

-   Database round trips
-   Query execution
-   Network traffic
-   Repeated object loading
-   Overall latency

But caching also introduces:

-   Memory usage
-   Invalidation complexity
-   Stale-data considerations
-   Configuration complexity

Therefore, cache should be used according to workload.

---

# 152. Hibernate Cache Levels

The important cache mechanisms to understand are:

```text
1. First-Level Cache (L1)
2. Second-Level Cache (L2)
3. Query Cache
4. Collection Cache
5. Natural-ID Cache
6. Statistics/Monitoring
```

L1 is fundamental and built into the Session/persistence context.

L2 and query caching require appropriate configuration/provider support
and should not be treated as automatically enabled in every modern
Hibernate application.

---

# 153. First-Level Cache --- L1

```text
Every Session
      |
      +--> Its own Persistence Context
               |
               +--> L1 Cache
```

### Scope

```text
Session / Persistence Context
```

### Default

Yes, it is fundamental to Hibernate's persistence context.

### Example

```java
Student s1 =
    session.get(Student.class, 1);

Student s2 =
    session.get(Student.class, 1);
```

Potentially:

```text
First get  → DB
Second get → L1 cache
```

---

# 154. L1 Cache Flow

```text
session.get(Student, 1)
          |
          v
     L1 Cache?
       /    \
     HIT    MISS
      |       |
      v       v
 return     SELECT DB
 object        |
               v
          Store in L1
               |
               v
          Return object
```

---

# 155. Why L1 Cache Exists

L1 cache provides:

1.  Faster repeated access within a Session.
2.  Identity guarantee for managed entities.
3.  Reduced duplicate database queries.
4.  Support for dirty checking.
5.  Efficient persistence-context management.

---

# 156. Second-Level Cache --- L2

L2 cache is shared across Sessions associated with the same
SessionFactory/EntityManagerFactory.

Conceptually:

```text
                Session 1
                    |
                    v
                 L1 Cache
                    |
                    |
                    v
              +-------------+
              | L2 Cache    |
              +-------------+
                    ^
                    |
                    |
                 L1 Cache
                    ^
                    |
                Session 2
```

### Key difference

```text
L1 → Session-specific
L2 → Shared across Sessions
```

---

# 157. L2 Cache Example

Suppose Session 1 loads:

```java
Student #1
```

Flow:

```text
Session 1
   ↓
L1 MISS
   ↓
L2 MISS
   ↓
Database
   ↓
L2
   ↓
L1
   ↓
Application
```

Later Session 2 requests Student #1:

```text
Session 2
   ↓
L1 MISS
   ↓
L2 HIT
   ↓
No DB query needed
   ↓
Session 2 L1
   ↓
Application
```

This is the major reason L2 cache can reduce repeated database access
across Sessions.

---

# 158. L1 vs L2 Diagram

```text
                  Application
                 /           \
                /             \
           Session 1       Session 2
              |                |
             L1               L1
              \                /
               \              /
                +------------+
                | L2 Cache   |
                +------------+
                      |
                      |
                   Database
```

---

# 159. L2 Cache Is Not Automatically "Always On"

Modern Hibernate applications generally need explicit second-level cache
configuration and a supported caching provider/integration.

Typical conceptual stack:

```text
Hibernate
   ↓
JCache / Supported Cache Provider
   ↓
Cache Implementation
```

Examples of cache technologies/integrations can vary by Hibernate
version and application architecture.

Always follow the cache provider and Hibernate version documentation for
the exact setup.

---

# 160. L2 Cache Setup --- Conceptual Steps

A typical setup process is:

```text
1. Add Hibernate cache integration dependency
2. Add a cache provider/implementation
3. Configure Hibernate second-level cache
4. Configure cache regions/provider
5. Mark required entities/collections cacheable
6. Start application
7. Verify cache hits/misses using statistics/monitoring
```

The exact dependency and configuration names depend on Hibernate version
and cache provider.

---

# 161. Cacheable Entity

Conceptually, an entity can be configured as cacheable.

With Hibernate annotations, one common pattern is:

```java
@Entity
@Cacheable
@org.hibernate.annotations.Cache(
    usage = CacheConcurrencyStrategy.READ_WRITE
)
public class Student {

    @Id
    private Long id;

    private String name;
}
```

### Important

`@Cacheable` and Hibernate cache annotations should be used according to
the selected cache integration and consistency strategy.

---

# 162. Cache Concurrency Strategies

Hibernate supports cache concurrency strategies such as:

```text
READ_ONLY
NONSTRICT_READ_WRITE
READ_WRITE
TRANSACTIONAL
```

### READ_ONLY

Good for data that does not change.

Example:

```text
Country
Currency
Static reference data
```

### NONSTRICT_READ_WRITE

Allows a weaker consistency model for data that changes infrequently.

### READ_WRITE

Provides stronger consistency semantics for many normal mutable cached
entities.

### TRANSACTIONAL

For environments supporting transactional cache semantics.

The appropriate strategy depends on application consistency requirements
and provider support.

---

# 163. Query Cache

The query cache caches query result information rather than simply
caching entity objects.

Example:

```java
Query query =
    session.createQuery(
        "from Student where department.id = :id"
    );
```

Conceptually:

```text
Query + Parameters
       ↓
Query Cache
      / \
   HIT   MISS
    |      |
    |      v
    |    Database
    |      |
    +------+
       |
       v
 Query result information
```

---

# 164. Query Cache Important Point

Query cache is **not a replacement for entity cache**.

A cached query result can contain identifiers/keys needed to retrieve
entities.

Therefore, query caching often works together with second-level entity
cache.

Conceptually:

```text
Query Cache
    ↓
Entity IDs
    ↓
L2 Entity Cache
    ↓
Entity Objects
```

If the required entities are not available in cache, Hibernate may still
need database access.

---

# 165. Enabling Query Cache --- Conceptual

Query cache must be explicitly enabled/configured.

Then an individual query can be marked as cacheable.

Example:

```java
Query<Student> query =
    session.createQuery(
        "from Student",
        Student.class
    );

query.setHint(
    "org.hibernate.cacheable",
    true
);
```

Depending on Hibernate API/version, Hibernate-specific query APIs may
also expose cacheable methods.

---

# 166. Query Cache Flow

### First execution

```text
Query
 ↓
Query Cache MISS
 ↓
DB query
 ↓
Result identifiers/data
 ↓
Cache
```

### Second execution

```text
Same query + same parameters
          ↓
Query Cache HIT
          ↓
Use cached result information
          ↓
Resolve entities from L2/DB as required
```

---

# 167. Query Cache and Parameters

Query cache entries are associated with query structure and relevant
parameters.

For example:

```text
Query:
from Student where department.id = :id

id = 10
```

and:

```text
id = 20
```

represent different parameterized results.

---

# 168. Collection Cache

Hibernate can also cache collections representing entity associations.

Example:

```text
Department
   |
   +--> students collection
```

A collection cache can help remember which child entity identifiers
belong to a parent.

Conceptually:

```text
Department #10
      |
      v
Collection Cache
      |
      +--> Student #1
      +--> Student #2
      +--> Student #3
```

The actual entity state may still come from L2 entity cache or the
database.

---

# 169. Collection Cache vs Entity Cache

### Entity Cache

```text
Student #1 → Student data
```

### Collection Cache

```text
Department #10
    →
[Student #1, Student #2, Student #3]
```

Collection cache stores the association/collection information, not
simply a complete duplicate of all child entity state.

---

# 170. Natural-ID Cache

Hibernate supports a Natural ID concept for entities that have a
domain-level unique identifier.

Example:

```text
Student
naturalId = email
```

Instead of:

```text
Primary key → 101
```

the application may identify the entity using:

```text
email → azhar@example.com
```

Hibernate can provide natural-id lookup/cache mechanisms.

Example mapping concept:

```java
@NaturalId
@Column(unique = true)
private String email;
```

Natural ID must represent a stable/unique business identifier
appropriate for the domain.

---

# 171. Natural-ID Cache Flow

```text
Natural ID
   |
   v
Natural-ID Cache / Resolution
   |
   v
Entity identifier
   |
   v
L2 Entity Cache
   |
   v
Entity
```

If cache misses occur, Hibernate may access the database.

---

# 172. Cache Performance Flow

## Without Cache

```text
Request
  ↓
Application
  ↓
Database
  ↓
SQL execution
  ↓
Network/DB processing
  ↓
Result
  ↓
Application
```

Repeated request:

```text
Request
  ↓
Database AGAIN
```

---

# 173. With L1 Cache

```text
Request
  ↓
Session
  ↓
L1 Cache
  ↓
HIT
  ↓
Return object
```

If MISS:

```text
L1 MISS
  ↓
Database
  ↓
L1
  ↓
Return
```

---

# 174. With L2 Cache

```text
Request
   ↓
L1 Cache
   ↓
MISS
   ↓
L2 Cache
   ↓
HIT
   ↓
Return
```

Only if both miss:

```text
L1 MISS
   ↓
L2 MISS
   ↓
Database
```

---

# 175. Complete Cache Flow

```text
                 Request
                    |
                    v
               L1 Cache?
              /         \
            HIT          MISS
             |             |
             v             v
          Return       L2 Cache?
                       /       \
                     HIT       MISS
                      |          |
                      v          v
                   Return     Database
                                 |
                                 v
                               L2
                                 |
                                 v
                                L1
                                 |
                                 v
                              Return
```

---

# 176. First-Level vs Second-Level Cache

  -------------------------------------------------------------------------------------
  Feature                 L1 Cache                L2 Cache
  ----------------------- ----------------------- -------------------------------------
  Scope                   Session                 SessionFactory/EntityManagerFactory

  Shared across Sessions  No                      Yes

  Default                 Fundamental/default     Must be configured

  Persistence Context     Yes                     No, shared cache layer

  Main purpose            Managed entity          Reuse data across Sessions
                          identity + repeated     
                          access in Session       

  Lifecycle               Session lifecycle       Factory/application cache lifecycle

  Setup                   Automatic               Provider/configuration required
  -------------------------------------------------------------------------------------

---

# 177. Query Cache vs L2 Cache

  -----------------------------------------------------------------------
  Feature                 L2 Entity Cache         Query Cache
  ----------------------- ----------------------- -----------------------
  Stores                  Entity state/cache      Query result
                          entries                 information

  Scope                   Shared cache            Shared cache

  Main purpose            Reuse entity data       Reuse results of
                                                  cacheable queries

  Works alone?            Entity cache can be     Often most useful with
                          used independently      L2 entity cache

  Invalidations           Entity changes          Query-result
                                                  invalidation can be
                                                  required
  -----------------------------------------------------------------------

---

# 178. L1 + L2 + Query Cache

```text
                 Application
                     |
                     v
                  Session
                     |
                     v
                    L1
                     |
                   MISS
                     v
                    L2
                     |
                   MISS
                     v
                Query/DB
```

More precisely, query cache is a separate mechanism used when a query is
configured as cacheable:

```text
Query
  |
  v
Query Cache
  |
  +--> Result IDs
          |
          v
      L2 Entity Cache
          |
          v
       Entities
```

---

# 179. Cache Invalidation

Caching creates a major question:

> What happens when database data changes?

Suppose:

```text
L2:
Student #1 = "AAA"
```

Database changes:

```text
Student #1 = "BBB"
```

The cache must be updated/invalidated appropriately.

Therefore:

```text
Cache
  +
Consistency
  +
Invalidation
```

must be considered together.

This is why caching is not simply "store everything in memory."

---

# 180. Cache Lifecycle

A general cache lifecycle:

```text
Application starts
      ↓
Cache initialized
      ↓
First request
      ↓
Cache MISS
      ↓
Database
      ↓
Cache populated
      ↓
Later request
      ↓
Cache HIT
      ↓
Faster response
      ↓
Entity changes
      ↓
Cache updated/invalidated
```

---

# 181. Cache Statistics and Monitoring

Hibernate can expose statistics useful for understanding cache behavior.

Useful concepts include:

```text
Entity load count
Entity fetch count
Second-level cache hit count
Second-level cache miss count
Query cache hit count
Query cache miss count
Query execution count
Flush count
```

Statistics can help answer:

```text
Is cache actually helping?
How many cache hits?
How many misses?
How many DB queries?
Which queries are expensive?
```

---

# 182. Enabling Hibernate Statistics

A common configuration concept is:

```properties
hibernate.generate_statistics=true
```

Then Hibernate's statistics APIs can be used for diagnostics.

Example concept:

```java
SessionFactory sessionFactory = ...;

Statistics stats =
    sessionFactory.getStatistics();

System.out.println(
    stats.getSecondLevelCacheHitCount()
);
```

Exact API packages and methods depend on the Hibernate version.

---

# 183. Why Statistics Matter

Suppose:

```text
L2 cache hit count = 0
L2 cache miss count = 10,000
```

This suggests the cache may not be helping the workload.

If:

```text
L2 hit count = 9,500
L2 miss count = 500
```

there may be significant reuse.

But high hit rate alone does not guarantee overall application
performance; cache memory usage, invalidation, query patterns and DB
workload also matter.

---

# 184. Most Common Cache Usage

### L1 Cache

Almost every Hibernate application uses it implicitly.

```text
Session-level entity tracking
```

### L2 Cache

Useful for:

-   Frequently read data
-   Relatively stable reference data
-   Data shared across Sessions
-   Expensive-to-load entities

Examples:

```text
Country
State
Category
Product metadata
Application configuration/reference data
```

### Query Cache

Useful when:

-   Same query is executed repeatedly
-   Query result is relatively stable
-   Parameters repeat
-   Cache invalidation cost is acceptable

---

# 185. When NOT to Cache Aggressively

Caching may not be useful for:

-   Highly volatile data
-   Frequently changing rows
-   Huge datasets with low reuse
-   Queries with constantly changing parameters
-   Data with strict freshness requirements where cache complexity is
    not justified

---

# 186. Hibernate Cache Mental Model

Remember:

```text
L1 = Session's own cache

L2 = Shared cache across Sessions

Query Cache = Cache query-result information

Collection Cache = Cache collection/association information

Natural-ID Cache = Cache natural-id resolution

Statistics = Measure what is happening
```

---

# 187. Complete Hibernate Relationship + Cache Architecture

```text
                    JAVA APPLICATION
                           |
                           v
                    Entity Objects
                           |
                           v
                  Persistence Context
                           |
                           v
                      L1 CACHE
                           |
                     L1 MISS?
                           |
                           v
                      L2 CACHE
                           |
                     L2 MISS?
                           |
                           v
                    Query / JDBC
                           |
                           v
                       DATABASE

Relationships:

Student
   |
   +------ @ManyToOne ------> Department
   |
   +------ @ManyToMany -----> Course
   |
   +------ @OneToOne -------> Profile

Hibernate manages:
- Entity state
- Relationships
- Dirty checking
- Flush
- Transactions
- Cache layers
```

---

# 188. Important Interview Differences

## `clear()` vs `evict()`

```text
clear() → all managed entities detached
evict() → one entity detached
```

## `refresh()` vs `flush()`

```text
refresh() → DB → Java object
flush()   → Java state → DB
```

## `flush()` vs `commit()`

```text
flush()  → synchronize changes by executing SQL
commit() → complete transaction
```

## `merge()` vs `update()`

```text
merge()  → JPA standard, copies detached state into managed instance
update() → Hibernate-specific, associates detached instance; can conflict with existing managed identity
```

## `persist()` vs `merge()`

```text
persist() → normally used for new/transient entity
merge()   → commonly used for detached entity state
```

---

# 189. Important Lifecycle Correction

Do not memorize this as:

```text
Transient → Persistent → Detached → Removed
```

as though every object must always follow exactly this sequence.

A better model is:

```text
                    persist()
                       ↓
TRANSIENT ───────→ MANAGED/PERSISTENT
                       |
             +---------+---------+
             |                   |
        close/clear/evict     remove()
             |                   |
             v                   v
         DETACHED             REMOVED
             |
             | merge()
             v
          MANAGED
```

This is the more accurate mental model.

---

# 190. Complete Entity Lifecycle Diagram

```text
                         new Student()
                              |
                              v
                        +-----------+
                        | TRANSIENT  |
                        +-----------+
                              |
                          persist()
                              |
                              v
                    +-------------------+
                    | MANAGED/PERSISTENT|
                    +-------------------+
                       |             |
             close/clear/evict      remove()
                       |             |
                       v             v
                +-----------+    +---------+
                | DETACHED  |    | REMOVED |
                +-----------+    +---------+
                       |
                    merge()
                       |
                       v
                 +-----------+
                 |  MANAGED  |
                 +-----------+
```

---

# 191. Complete Persistence Context Flow

```text
                    SESSION
                       |
                       v
              PERSISTENCE CONTEXT
                       |
              +--------+--------+
              |                 |
              v                 v
          Entity #1         Entity #2
              |                 |
              v                 v
         Dirty Checking    Dirty Checking
              |                 |
              +--------+--------+
                       |
                       v
                     FLUSH
                       |
                       v
                  SQL Commands
                       |
                       v
                    JDBC
                       |
                       v
                   DATABASE
```

---

# 192. Complete Cache Flow

```text
                       Request
                          |
                          v
                     Hibernate
                          |
                          v
                      L1 Cache
                     /        \
                  HIT          MISS
                   |             |
                   v             v
                Return         L2 Cache
                              /        \
                           HIT          MISS
                            |             |
                            v             v
                         Return        Database
                                          |
                                          v
                                         L2
                                          |
                                          v
                                         L1
                                          |
                                          v
                                       Return
```

---

# 193. Relationship + Fetch + Cascade Mental Model

When reading a relationship annotation, ask four questions:

```text
1. What is the relationship?
2. Who owns it?
3. What should cascade?
4. When should related data load?
```

Example:

```java
@OneToMany(
    mappedBy = "department",
    cascade = CascadeType.ALL,
    fetch = FetchType.LAZY,
    orphanRemoval = true
)
private List<Student> students;
```

Interpretation:

```text
@OneToMany
    ↓
One Department has many Students

mappedBy = "department"
    ↓
Student.department owns FK mapping

cascade = ALL
    ↓
Lifecycle operations propagate

fetch = LAZY
    ↓
Students loaded when required

orphanRemoval = true
    ↓
Removed child from relationship can be deleted
```

---

# 194. One Complete Relationship Example

```java
@Entity
public class Department {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @OneToMany(
        mappedBy = "department",
        cascade = CascadeType.ALL,
        fetch = FetchType.LAZY,
        orphanRemoval = true
    )
    private List<Student> students = new ArrayList<>();

    public void addStudent(Student student) {
        students.add(student);
        student.setDepartment(this);
    }

    public void removeStudent(Student student) {
        students.remove(student);
        student.setDepartment(null);
    }
}
```

Student:

```java
@Entity
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "department_id")
    private Department department;

    public void setDepartment(Department department) {
        this.department = department;
    }
}
```

### Mental model

```text
Department
     |
     | 1
     |
     | @OneToMany
     |
     | LAZY
     |
     | Cascade ALL
     |
     v
  Students
     ^
     |
     | @ManyToOne
     |
Student.department
     |
     +--> department_id FK
```

---

# 195. Final Hibernate Near-Complete Roadmap

```text
JDBC
  ↓
JDBC Problems
  ↓
Object-Relational Impedance Mismatch
  ↓
ORM
  ↓
Hibernate
  ↓
JPA
  ↓
Configuration
  ↓
SessionFactory
  ↓
Session
  ↓
Transaction
  ↓
Entity
  ↓
Entity Lifecycle
  ↓
Persistence Context
  ↓
Dirty Checking
  ↓
Flush
  ↓
Commit
  ↓
Relationships
  ├── One-to-One
  ├── One-to-Many
  ├── Many-to-One
  └── Many-to-Many
  ↓
Ownership
  ↓
mappedBy
  ↓
@JoinColumn / @JoinTable
  ↓
Cascade
  ↓
orphanRemoval
  ↓
Fetch
  ├── EAGER
  └── LAZY
  ↓
Caching
  ├── L1
  ├── L2
  ├── Query Cache
  ├── Collection Cache
  └── Natural-ID Cache
  ↓
Statistics / Monitoring
  ↓
Performance Optimization
```

---

# 196. Final Revision: Entity Lifecycle

```text
Transient
= New object, not managed

persist()
= Make new entity managed

Persistent / Managed
= Hibernate tracks it

Dirty Checking
= Hibernate detects changes

flush()
= Synchronize changes with DB

commit()
= Complete transaction

Detached
= Object exists, but Session no longer manages it

merge()
= Copy detached state into a managed instance

remove()
= Mark managed entity for deletion

Removed
= Entity is scheduled for deletion
```

---

# 197. Final Revision: Relationships

```text
@OneToOne
1 ↔ 1

@OneToMany
1 → many

@ManyToOne
many → 1

@ManyToMany
many ↔ many
```

Remember:

```text
mappedBy = Java property on the owning side
@JoinColumn = FK mapping
@JoinTable = Join-table mapping
Owning side = controls relationship update
Inverse side = mappedBy
```

---

# 198. Final Revision: Cascade

```text
PERSIST
MERGE
REMOVE
REFRESH
DETACH
ALL
```

Remember:

```text
Cascade = Parent operation can propagate to child
```

But:

```text
Cascade ≠ orphanRemoval
```

---

# 199. Final Revision: Fetch

```text
EAGER
= Load association immediately/with initial entity retrieval plan

LAZY
= Load association when accessed
```

JPA common defaults:

```text
OneToOne   → EAGER
ManyToOne  → EAGER
OneToMany  → LAZY
ManyToMany → LAZY
```

---

# 200. Final Revision: Cache

```text
L1
→ Session scope
→ Default/fundamental
→ Persistence context

L2
→ Shared across Sessions
→ Requires configuration/provider

Query Cache
→ Query-result information
→ Separate from entity cache

Collection Cache
→ Association/collection information

Natural-ID Cache
→ Natural-ID resolution

Statistics
→ Measure cache/query behavior
```

---

# 201. Final Master Diagram

```text
                              APPLICATION
                                   |
                                   v
                              JPA / Hibernate
                                   |
                                   v
                              ENTITY OBJECT
                                   |
                                   v
                         PERSISTENCE CONTEXT
                                   |
                    +--------------+--------------+
                    |                             |
                    v                             v
                L1 CACHE                     ENTITY STATE
                    |                             |
                    |                       Dirty Checking
                    |                             |
                    |                             v
                    |                           Flush
                    |                             |
                    |                             v
                    |                            SQL
                    |                             |
                    +-------------+---------------+
                                  |
                                  v
                               L2 CACHE
                                  |
                               MISS |
                                  v
                               DATABASE

Relationships:

 Student ──ManyToOne──> Department
 Student <──OneToMany── Department

 Student ──OneToOne──> Profile

 Student <──ManyToMany──> Course
               |
               v
         Join Table

Lifecycle:

Transient
   |
persist()
   v
Managed
   |
   +---- dirty checking ----> flush ----> DB
   |
   +---- remove() ----------> Removed
   |
   +---- close/clear -------> Detached
                                  |
                                merge()
                                  |
                                  v
                                Managed
```

---

# 202. Exam/Interview Golden Points

1.  **JPA is a specification; Hibernate is an implementation/provider.**
2.  **Hibernate uses JDBC underneath.**
3.  **`@Entity` marks a persistent entity.**
4.  **Every normal JPA entity needs an identifier using `@Id`.**
5.  **A no-argument constructor is required by the persistence model.**
6.  **Persistence Context tracks managed entities.**
7.  **Dirty checking detects changes in managed entities.**
8.  **`flush()` synchronizes pending changes with the database.**
9.  **`flush()` is not the same as transaction `commit()`.**
10. **Detached objects are not automatically dirty-checked.**
11. **`merge()` returns a managed instance containing the detached
    state.**
12. **`clear()` detaches all entities in the persistence context.**
13. **`evict()` is Hibernate-specific and detaches one entity.**
14. **`refresh()` reloads entity state from the database.**
15. **L1 cache is Session/persistence-context scoped.**
16. **L2 cache is shared across Sessions associated with the same
    factory.**
17. **Query cache caches query-result information, not simply full
    entity objects.**
18. **`mappedBy` refers to the Java field/property that owns the
    relationship.**
19. **The owning side controls the relationship mapping.**
20. **`@JoinColumn` commonly represents a foreign-key column.**
21. **`@JoinTable` is commonly used for many-to-many mappings.**
22. **Cascade propagates selected lifecycle operations.**
23. **`orphanRemoval` deals with orphaned child lifecycle/deletion and
    is different from cascade.**
24. **LAZY loading can help performance but can cause lazy
    initialization problems outside the persistence context.**
25. **Making everything EAGER is not a general solution to performance
    problems.**
26. **N+1 query problems should be solved using appropriate
    fetching/query strategies, not blindly by changing everything to
    EAGER.**
27. **Second-level and query caching require deliberate configuration
    and should be measured using statistics/monitoring.**

---

# 203. One-Minute Hibernate Revision

```text
Hibernate = ORM

JPA = Specification
Hibernate = Provider
JDBC = Low-level DB connectivity

Entity
↓
Persistence Context
↓
L1 Cache
↓
Dirty Checking
↓
Flush
↓
SQL
↓
JDBC
↓
DB

Lifecycle:
Transient
→ persist()
→ Managed
→ close/clear/evict
→ Detached
→ merge()
→ Managed

Managed
→ remove()
→ Removed

Relationships:
1:1
1:N
N:1
N:M

Ownership:
@JoinColumn / @JoinTable
        ↓
Owning side

mappedBy:
        ↓
Inverse side

Cascade:
PERSIST
MERGE
REMOVE
REFRESH
DETACH
ALL

Fetch:
EAGER
LAZY

Cache:
L1 → Session
L2 → Shared
Query → Query result information
Collection → Association
Natural ID → Natural-ID resolution
Statistics → Monitoring
```

---

# 204. Most Important Conceptual Distinction

If you remember only one thing about Hibernate's internal behavior,
remember this:

```text
                 MANAGED ENTITY
                       |
             Hibernate tracks it
                       |
                Object modified
                       |
                Dirty Checking
                       |
                Change detected
                       |
                    FLUSH
                       |
                  SQL executes
                       |
                  TRANSACTION
                       |
                    COMMIT
                       |
                    DATABASE
```

And:

```text
DETACHED ENTITY
       |
       X
       |
No automatic dirty checking
       |
       |
     merge()
       |
       v
MANAGED ENTITY
```

This distinction explains a large portion of Hibernate's behavior.

---

# 205. Practical Checklist for Any Hibernate Relationship

Whenever you create a relationship, check:

```text
[ ] Correct cardinality?
[ ] Unidirectional or bidirectional?
[ ] Which side owns relationship?
[ ] Correct mappedBy?
[ ] Correct @JoinColumn?
[ ] Join table required?
[ ] Cascade required?
[ ] orphanRemoval required?
[ ] LAZY or EAGER?
[ ] N+1 risk?
[ ] Serialization/JSON recursion risk?
[ ] Transaction boundary correct?
```

This checklist prevents many common Hibernate relationship problems.

---

# 206. Final Concept

Hibernate is best understood as a system that manages the lifecycle and
synchronization of Java entities with relational database data.

The complete picture is:

```text
              OBJECT WORLD
                   |
              Java Entity
                   |
              Persistence
                Context
                   |
        +----------+----------+
        |                     |
      L1 Cache           Entity Lifecycle
                              |
                +-------------+-------------+
                |             |             |
             Managed       Detached       Removed
                |
          Dirty Checking
                |
              Flush
                |
               SQL
                |
              JDBC
                |
          +-----+------+
          |            |
        L2 Cache    Database
          |
        Query/
      Collection/
      Natural-ID
       caching
```

> **Core principle:** Hibernate allows the developer to work with entity
> objects and object relationships while the ORM layer manages much of
> the translation, tracking, caching, SQL generation and synchronization
> required to persist those objects in a relational database.
