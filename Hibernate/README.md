<p align="center">
  <img src="https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white" alt="Java Badge"/>
  <img src="https://img.shields.io/badge/Hibernate-59666C?style=for-the-badge&logo=hibernate&logoColor=white" alt="Hibernate Badge"/>
  <img src="https://img.shields.io/badge/JPA-6DB33F?style=for-the-badge&logo=spring&logoColor=white" alt="JPA Badge"/>
  <img src="https://img.shields.io/badge/Status-Active-brightgreen?style=for-the-badge" alt="Status Badge"/>
</p>

# Hibernate & JPA -- Complete Notes for Quick Revision

> **Style:** Notes are kept in **English + Hinglish** with clear explanations, structured diagrams, comparison tables, code examples, and interview-oriented content. Arranged in **beginner-to-advanced** order.

---

## Learning Path

| # | Topic | File | Description |
|---|-------|------|-------------|
| 1 | Hibernate & JPA Fundamentals | [01-hibernate-and-jpa-fundamentals.md](./01-hibernate-and-jpa-fundamentals.md) | JDBC problems, ORM concept, Object-Relational Impedance Mismatch, Hibernate architecture, JPA specification, SessionFactory, Session, Configuration, Maven setup, First project |
| 2 | Entity Mapping & Annotations | [02-entity-mapping-and-annotations.md](./02-entity-mapping-and-annotations.md) | `@Entity`, `@Table`, `@Column`, `@Id`, `@GeneratedValue`, `@Temporal`, `@Transient`, `@Lob`, Auto timestamps, Persistence Context, Dirty Checking, Entity lifecycle |
| 3 | Relationships, Cascade & Fetch | [03-relationships-and-mappings.md](./03-relationships-and-mappings.md) | Entity states deep dive, OneToOne, OneToMany, ManyToOne, ManyToMany, `mappedBy`, Cascade types, `orphanRemoval`, EAGER vs LAZY, N+1 problem, `@JoinColumn` |
| 4 | Caching & Advanced Topics | [04-persistence-context-and-caching.md](./04-persistence-context-and-caching.md) | L1 Cache, L2 Cache, Query Cache, EHCache, Cache statistics, Complete lifecycle diagrams, Interview golden points, Practical checklists |

---

## Prerequisites

Before studying Hibernate, you should be comfortable with:
- **Core Java** (OOP, Collections, Exceptions)
- **JDBC basics** (see [Core Java](../Core%20Java/) and [Spring JDBC](../Spring%20Framework/05-spring-jdbc-and-jdbctemplate.md))
- **SQL basics** (CREATE, INSERT, SELECT, UPDATE, DELETE, JOINs)

---

## How to Use These Notes

1. **Start from 01** and read in numbered order
2. **01** covers the "why" -- why Hibernate exists, what problem it solves
3. **02** covers entity mapping -- how Java classes map to database tables
4. **03** covers relationships -- how entities relate to each other
5. **04** covers caching and advanced topics -- performance optimization

---

## Contributing

These notes are being continuously improved. Feel free to:
- Fix typos or mistakes
- Add more examples
- Suggest better explanations

---

<p align="center">
  <b>Made with care for quick Java revision</b>
</p>
