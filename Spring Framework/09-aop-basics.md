# Spring Framework -- AOP (Aspect-Oriented Programming)

> **Topics:** AOP concepts, Cross-cutting concerns, Advice types, Pointcut, JoinPoint, Weaving, Transaction management benefits & use cases

---

# PART I --- AOP

# 44. AOP Kya Hai?

**AOP = Aspect-Oriented Programming**

AOP ka purpose cross-cutting concerns ko business logic se separate
karna hai.

Cross-cutting concerns examples:

-   Logging
-   Security
-   Transaction Management
-   Auditing
-   Performance monitoring
-   Exception-related cross-cutting logic

---

# 45. AOP Example

Without AOP:

```java
public void service() {

    logStart();

    // business logic

    beginTransaction();

    // database work

    commitTransaction();

    logEnd();
}
```

Har method mein repeated code aa sakta hai.

AOP ke saath:

```java
@Transactional
public void service() {

    // only business logic
}
```

Spring AOP proxy/interceptor transaction handling ko method invocation
ke around apply kar sakta hai.

Spring documentation explains that declarative transaction management is
implemented through AOP proxies and transaction interceptors.
citeturn0search0

---

# 46. AOP Terms

## Aspect

Cross-cutting concern ka modular representation.

Example:

```text
Transaction aspect
Logging aspect
Security aspect
```

## Join Point

Execution ka point jahan aspect apply ho sakta hai.

## Pointcut

Define karta hai ki kis method/execution points par advice apply hoga.

## Advice

Actual additional behavior:

```text
before
after
around
after-returning
after-throwing
```

## Proxy

Spring runtime par target bean ke around proxy create karke advice apply
kar sakta hai.

---

# 47. `@Transactional` and AOP

Flow:

```text
Caller
  ↓
Spring Proxy
  ↓
Transaction Interceptor
  ↓
Transaction begin
  ↓
Target Service Method
  ↓
Success? ── YES → Commit
    │
    NO
    ↓
Rollback according to transaction rules
```

---

# 48. Important `@Transactional` Proxy Rule

Default proxy-based transaction management mein **external calls through
the Spring proxy** intercept hote hain.

Agar ek method same object ke andar directly dusre `@Transactional`
method ko call kare:

```java
this.otherMethod();
```

toh self-invocation normally proxy se pass nahi hota, isliye
`otherMethod()` ki separate transactional interception apply nahi hoti.

Spring docs explicitly call this out for proxy mode. citeturn0search6

---

# PART J --- Transaction Management: Benefits & Use Cases

# 49. Why Transaction Management?

Useful when multiple database operations logically ek unit hon.

Examples:

### Money Transfer

```text
Debit + Credit
```

### Order Placement

```text
Create Order
+
Create Order Items
+
Reduce Stock
```

### Bank / Wallet

```text
Debit
+
Credit
+
Transaction History
```

### Employee Payroll

```text
Salary update
+
Payroll record
+
Audit record
```

---

# 50. Benefits

1.  Atomicity / all-or-nothing business operation.
2.  Rollback on failure.
3.  Better consistency.
4.  Less repetitive transaction code.
5.  Declarative programming through `@Transactional`.
6.  Integration with Spring JDBC/JPA/Hibernate.
7.  Centralized transaction configuration.
8.  Business code cleaner rehta hai.

Spring specifically provides declarative transaction management so
transaction demarcation can be separated from repetitive business logic.
citeturn0search2turn0search5

---

# 51. Steps to Enable Transaction Management in Spring XML

### Step 1 --- Add Spring transaction dependency

Project setup mein Spring transaction support / required Spring modules
available hone chahiye.

### Step 2 --- DataSource configure karo

```xml
<bean id="dataSource"
      class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    ...
</bean>
```

### Step 3 --- JdbcTemplate configure karo

```xml
<bean id="jdbcTemplate"
      class="org.springframework.jdbc.core.JdbcTemplate">

    <property name="dataSource"
              ref="dataSource"/>
</bean>
```

### Step 4 --- TransactionManager configure karo

```xml
<bean id="TXM"
      class="org.springframework.jdbc.datasource.DataSourceTransactionManager">

    <property name="dataSource"
              ref="dataSource"/>
</bean>
```

### Step 5 --- Annotation transaction management enable karo

```xml
<tx:annotation-driven
    transaction-manager="TXM"/>
```

### Step 6 --- Service method par annotation

```java
@Transactional
public void service() {
    ...
}
```

### Step 7 --- Service ko Spring bean ke through obtain/call karo

```java
EmployeeService es =
    ac.getBean(EmployeeService.class);

es.service();
```

---

# 52. Java Configuration Version

XML ke alternative mein:

```java
@Configuration
@EnableTransactionManagement
public class AppConfig {

    @Bean
    public PlatformTransactionManager txManager(
            DataSource dataSource) {

        return new DataSourceTransactionManager(
            dataSource
        );
    }
}
```

Then:

```java
@Transactional
public void service() {
    ...
}
```

Spring's official annotation configuration uses
`@EnableTransactionManagement` together with a
`PlatformTransactionManager` bean. citeturn0search1

---

# 53. Default `@Transactional` Behavior

Important defaults:

```text
Propagation       = REQUIRED
Isolation         = DEFAULT
Read-only         = false
Timeout            = underlying system default
RuntimeException  = rollback
Error             = rollback
Checked Exception = no rollback by default
```

Spring docs specify these defaults. citeturn0search1

---

# 54. Checked Exception par Rollback

Agar checked exception par bhi rollback chahiye:

```java
@Transactional(
    rollbackFor = Exception.class
)
public void service() throws Exception {
    ...
}
```

Now matching checked exceptions can trigger rollback according to the
specified rollback rule.

---
