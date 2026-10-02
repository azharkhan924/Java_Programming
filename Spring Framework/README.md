<p align="center">
  <img src="https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white" alt="Java Badge"/>
  <img src="https://img.shields.io/badge/Spring-Framework-6DB33F?style=for-the-badge&logo=spring&logoColor=white" alt="Spring Badge"/>
  <img src="https://img.shields.io/badge/Spring_Boot-6DB33F?style=for-the-badge&logo=springboot&logoColor=white" alt="Spring Boot Badge"/>
  <img src="https://img.shields.io/badge/Status-Active-brightgreen?style=for-the-badge" alt="Status Badge"/>
</p>

# Spring Framework & Spring Boot -- Notes for Quick Revision

> **Style:** Notes are kept in **English + Hinglish** with clear explanations, structured diagrams, comparison tables, code examples, and interview-oriented content. Arranged in **beginner-to-advanced** order.

---

## Learning Path

| # | Topic | File | Description |
|---|-------|------|-------------|
| 1 | Spring Core & IoC Container | [01-spring-core-and-ioc-container.md](./01-spring-core-and-ioc-container.md) | Introduction, IoC & DI concepts, BeanFactory vs ApplicationContext, Container hierarchy, Reflection, Spring Exceptions, Bean Scopes (Singleton/Prototype) |
| 2 | Dependency Injection & Autowiring | [02-dependency-injection-and-autowiring.md](./02-dependency-injection-and-autowiring.md) | Lookup Method Injection, Circular Dependency, 3-Level Cache, Constructor/Setter/Field injection, XML Namespaces, Java Config, Collections, Autowiring annotations |
| 3 | Bean Inheritance & Bean ID | [03-bean-inheritance-and-bean-id.md](./03-bean-inheritance-and-bean-id.md) | CLOB file fetching, Bean Inheritance (parent-child), Abstract beans, Bean ID vs Bean Name/Alias |
| 4 | MVC Architecture & Layered Project | [04-spring-mvc-and-layered-architecture.md](./04-spring-mvc-and-layered-architecture.md) | MVC pattern, Request flow, 6-package project structure, Stereotype annotations (`@Component`, `@Repository`, `@Service`, `@Controller`) |
| 5 | Spring JDBC & JdbcTemplate | [05-spring-jdbc-and-jdbctemplate.md](./05-spring-jdbc-and-jdbctemplate.md) | DriverManagerDataSource, JdbcTemplate, RowMapper, Positional/Named params, `queryForList`, `queryForObject`, Alibaba Druid, File insert |
| 6 | SpEL, Stored Procedures & Batch | [06-spel-stored-procedures-and-batch.md](./06-spel-stored-procedures-and-batch.md) | Stored Procedures (IN/OUT/INOUT), SimpleJdbcCall, CallableStatement, `batchUpdate()`, Complete CRUD project, SpEL expressions |
| 7 | BLOB & Connection Pooling | [07-blob-connection-pooling.md](./07-blob-connection-pooling.md) | BLOB image/video storage, HikariCP, Apache DBCP2, Tomcat JDBC Pool, c3p0, Pool configuration |
| 8 | Transactions & ACID | [08-transactions-and-acid.md](./08-transactions-and-acid.md) | Transaction concept, ACID properties, `@Transactional`, XML transaction config, Batch with transactions, Exception handling |
| 9 | AOP Basics | [09-aop-basics.md](./09-aop-basics.md) | Aspect-Oriented Programming, Cross-cutting concerns, Advice types, Pointcut, JoinPoint, Weaving |
| 10 | Maven | [10-maven.md](./10-maven.md) | POM.xml, Maven lifecycle, Repositories, JAR/WAR packaging, SNAPSHOT vs Release, Plugins, Scopes |
| 11 | Spring MVC REST & Exception Handling | [11-spring-mvc-rest-and-exception-handling.md](./11-spring-mvc-rest-and-exception-handling.md) | DispatcherServlet lifecycle, `@PathVariable`, `@RequestParam`, `@RequestBody`, Interceptors, `@ExceptionHandler`, `@ControllerAdvice` |
| 12 | Spring Boot -- REST API | [12-spring-boot-rest-api.md](./12-spring-boot-rest-api.md) | API concepts, Spring Initializr, Project structure, Entity/Repository/Service/Controller layers, JPA, CRUD endpoints |
| 13 | Spring Boot -- CRUD, Validation, Logging | [13-spring-boot-crud-validation-logging.md](./13-spring-boot-crud-validation-logging.md) | Spring Container, Bean lifecycle, UPDATE/DELETE APIs, Validation, SLF4J, Logback, Profiles (dev/prod) |

---

## How to Use These Notes

1. **Start from 01** and read in numbered order -- each note builds on the previous one
2. Notes **01-10** cover **Spring Framework** fundamentals
3. Notes **11-13** cover **Spring MVC REST** and **Spring Boot**
4. For **Hibernate/JPA**, see the [Hibernate module](../Hibernate/)

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
