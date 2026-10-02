# Spring Framework -- Transaction Management & Batch Processing

> **Topics:** What is a Transaction, ACID properties, `@Transactional`, Transaction configuration in XML, Batch update with JdbcTemplate, `BatchPreparedStatementSetter`, Named Parameter batch, Exception handling in transactions

---

# PART E --- Transaction Management

# 15. Transaction Kya Hai?

Transaction database operations ka ek **logical unit of work** hai.

Example:

```text
A se ₹10,000 debit
        +
B ko ₹10,000 credit
```

Dono operations ko ek logical unit maana chahiye.

Agar debit successful ho aur credit fail ho jaye, toh data inconsistent
ho sakta hai.

Transaction ensure karta hai ki configured transaction semantics ke
according operation:

```text
All successful → COMMIT
Failure → ROLLBACK
```

---

# 16. Transaction Management Kya Hai?

Transaction Management ka matlab:

-   transaction start karna;
-   database operations ko transaction ke andar execute karna;
-   successful completion par commit karna;
-   failure par rollback karna.

Spring transaction management ek consistent abstraction provide karta
hai across JDBC, JPA, Hibernate etc. citeturn0search2

---

# 17. Real-Life Example --- Money Transfer

Suppose:

```text
AAA balance = ₹50,000
BBB balance = ₹30,000
```

Transfer:

```text
AAA → BBB
₹10,000
```

Required:

```text
AAA = ₹40,000
BBB = ₹40,000
```

Agar AAA se amount deduct ho gaya:

```text
AAA = ₹40,000
```

lekin BBB ko credit karne se pehle exception aa gaya:

```text
BBB = ₹30,000
```

toh total data inconsistent ho sakta hai.

Transaction ke saath:

```text
Debit
  ↓
Credit
  ↓
Exception?
  ↓
ROLLBACK
  ↓
AAA = ₹50,000
BBB = ₹30,000
```

---

# 18. ACID Properties

Database transactions ko generally ACID properties ke through explain
kiya jata hai.

## A --- Atomicity

Transaction ko **all-or-nothing** unit ki tarah treat karta hai.

```text
10 operations
   ↓
all succeed → commit
any required operation fails → rollback
```

---

## C --- Consistency

Transaction ke baad database valid state mein rehna chahiye, subject to
its constraints/business rules.

Example:

```text
Money transfer:
Debit ₹10,000
Credit ₹10,000
```

Data logically consistent rehna chahiye.

---

## I --- Isolation

Concurrent transactions ek dusre ke intermediate/uncommitted state ko
unwanted way mein interfere na karein.

Isolation level ke according visibility/concurrency behavior change ho
sakta hai.

---

## D --- Durability

Commit hone ke baad data ko durable storage par preserve karne ka
guarantee database system provide karta hai, subject to the
database/storage system.

---

# 19. Without Transaction --- Employee Example

Suppose table:

```sql
CREATE TABLE employee (
    user_name VARCHAR(50),
    user_salary DOUBLE
);
```

Data:

```text
AAA   50000
BBB   50000
```

Service:

```java
@Component
public class EmployeeService {

    @Autowired
    private JdbcTemplate jt;

    public void service() {

        jt.update(
            "UPDATE employee " +
            "SET user_salary = user_salary - ? " +
            "WHERE user_name = ?",
            10000, "AAA"
        );

        jt.update(
            "UPDATE employee " +
            "SET user_salary = user_salary + ? " +
            "WHERE user_name = ?",
            10000, "BBB"
        );
    }
}
```

### Result

```text
AAA = 40000
BBB = 60000
```

assuming both started at 50000.

---

# 20. Configuration --- Basic JDBC

`applicationContext.xml`:

```xml
<context:component-scan
    base-package="mycomponent"/>

<bean id="dataSource"
      class="org.springframework.jdbc.datasource.DriverManagerDataSource">

    <property name="driverClassName"
              value="com.mysql.cj.jdbc.Driver"/>

    <property name="url"
              value="jdbc:mysql://localhost:3306/testdb"/>

    <property name="username"
              value="root"/>

    <property name="password"
              value="root"/>
</bean>

<bean id="jdbcTemplate"
      class="org.springframework.jdbc.core.JdbcTemplate">

    <property name="dataSource"
              ref="dataSource"/>
</bean>
```

---

# 21. Main Method

```java
ApplicationContext ac =
    new ClassPathXmlApplicationContext(
        "applicationContext.xml"
    );

EmployeeService es =
    ac.getBean(EmployeeService.class);

es.service();
```

---

# 22. Without Transaction --- Exception in Between

Now:

```java
@Component
public class EmployeeService {

    @Autowired
    private JdbcTemplate jt;

    public void service() {

        jt.update(
            "UPDATE employee " +
            "SET user_salary = user_salary - ? " +
            "WHERE user_name = ?",
            10000, "AAA"
        );

        System.out.println(10 / 0);

        jt.update(
            "UPDATE employee " +
            "SET user_salary = user_salary + ? " +
            "WHERE user_name = ?",
            10000, "BBB"
        );
    }
}
```

Flow:

```text
AAA debit
   ↓
AAA update successful
   ↓
10 / 0
   ↓
ArithmeticException
   ↓
method stops
   ↓
BBB update is not executed
```

With the common default JDBC auto-commit behavior, the first statement
may already be committed before the exception. Therefore the database
can become:

```text
AAA = 40000
BBB = 50000
```

This is exactly the type of partial-update problem transactions are
intended to prevent.

---

# 23. Solution --- `@Transactional`

Spring mein:

```java
@Transactional
```

use kar sakte hain.

```java
@Component
public class EmployeeService {

    @Autowired
    private JdbcTemplate jt;

    @Transactional
    public void service() {

        jt.update(
            "UPDATE employee " +
            "SET user_salary = user_salary - ? " +
            "WHERE user_name = ?",
            10000, "AAA"
        );

        System.out.println(10 / 0);

        jt.update(
            "UPDATE employee " +
            "SET user_salary = user_salary + ? " +
            "WHERE user_name = ?",
            10000, "BBB"
        );
    }
}
```

If the exception causes rollback:

```text
AAA debit
   ↓
exception
   ↓
ROLLBACK
   ↓
AAA original salary
BBB original salary
```

Spring's default `@Transactional` behavior rolls back for
`RuntimeException` and `Error`, while checked exceptions do not trigger
rollback by default. `ArithmeticException` is a `RuntimeException`, so
it normally triggers rollback. citeturn0search1

---

# 24. Important --- `@Transactional` Alone Is Not Enough

Sirf:

```java
@Transactional
```

likhne se transaction infrastructure automatically active nahi ho jata
in classic XML configuration.

Annotation-driven transaction management enable karna hota hai.

XML:

```xml
<tx:annotation-driven
    transaction-manager="TXM"/>
```

and a transaction manager bean:

```xml
<bean id="TXM"
      class="org.springframework.jdbc.datasource.DataSourceTransactionManager">

    <property name="dataSource"
              ref="dataSource"/>
</bean>
```

Spring documentation explicitly notes that the annotation is metadata
and runtime transaction infrastructure must be enabled.
citeturn0search1turn0search6

---

# 25. Complete XML Transaction Configuration

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd

        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd

        http://www.springframework.org/schema/tx
        https://www.springframework.org/schema/tx/spring-tx.xsd">

    <context:component-scan
        base-package="mycomponent"/>

    <bean id="dataSource"
          class="org.springframework.jdbc.datasource.DriverManagerDataSource">

        <property name="driverClassName"
                  value="com.mysql.cj.jdbc.Driver"/>

        <property name="url"
                  value="jdbc:mysql://localhost:3306/testdb"/>

        <property name="username"
                  value="root"/>

        <property name="password"
                  value="root"/>
    </bean>

    <bean id="jdbcTemplate"
          class="org.springframework.jdbc.core.JdbcTemplate">

        <property name="dataSource"
                  ref="dataSource"/>
    </bean>

    <bean id="TXM"
          class="org.springframework.jdbc.datasource.DataSourceTransactionManager">

        <property name="dataSource"
                  ref="dataSource"/>
    </bean>

    <tx:annotation-driven
        transaction-manager="TXM"/>

</beans>
```

Spring's XML configuration uses `<tx:annotation-driven>` to activate
annotation-based transactional behavior and a transaction manager such
as `DataSourceTransactionManager`. citeturn0search1

---

# 26. Transaction Flow

```text
Main
 ↓
EmployeeService bean
 ↓
Spring AOP Proxy
 ↓
@Transactional detected
 ↓
TransactionManager
 ↓
BEGIN TRANSACTION
 ↓
JdbcTemplate update 1
 ↓
JdbcTemplate update 2
 ↓
Method successful?
 ├── YES → COMMIT
 └── NO  → ROLLBACK
```

Spring's declarative transaction support uses AOP proxies and a
transaction interceptor around transactional method invocations.
citeturn0search0

---

# 27. Transaction with Multiple Inserts

Suppose:

```java
@Component
public class EmployeeService {

    @Autowired
    private JdbcTemplate jt;

    @Transactional
    public void insert(int i) {

        String Q =
            "INSERT INTO employee VALUES (?, ?)";

        for (i = 1; i <= 10; i++) {

            jt.update(
                Q,
                "E" + i,
                10000 * i
            );
        }
    }
}
```

If all operations succeed:

```text
E1  10000
E2  20000
E3  30000
...
E10 100000
```

all belong to one transaction.

---

# 28. Exception Inside the Loop

```java
@Component
public class EmployeeService {

    @Autowired
    private JdbcTemplate jt;

    @Transactional
    public void insert() {

        String Q =
            "INSERT INTO employee VALUES (?, ?)";

        for (int i = 1; i <= 10; i++) {

            jt.update(
                Q,
                "E" + i,
                10000 * i
            );

            if (i == 5) {
                System.out.println(10 / 0);
            }
        }
    }
}
```

Flow:

```text
E1 inserted
E2 inserted
E3 inserted
E4 inserted
E5 inserted
exception
↓
transaction rollback
↓
all transaction changes rolled back
```

Final result:

```text
Transaction ke andar ke E1–E5 changes bhi database mein nahi rahenge.
```

If the table already contained unrelated data outside this transaction,
that unrelated data is not rolled back.

---

# 29. Without `@Transactional`

Agar same loop mein `@Transactional` remove kar diya:

```java
public void insert() {

    String Q =
        "INSERT INTO employee VALUES (?, ?)";

    for (int i = 1; i <= 10; i++) {

        jt.update(
            Q,
            "E" + i,
            10000 * i
        );

        if (i == 5) {
            System.out.println(10 / 0);
        }
    }
}
```

Typical auto-commit behavior mein:

```text
E1 → committed
E2 → committed
E3 → committed
E4 → committed
E5 → statement executed and may be committed
Exception
E6-E10 → not executed
```

Exact result can depend on where the exception occurs and
transaction/autocommit configuration.

---

# PART F --- Batch Update

# 30. `update()` vs `batchUpdate()`

## `update()`

`update()` generally executes **one SQL update operation**.

```java
int n = jt.update(
    "UPDATE employee SET user_salary = ? WHERE user_name = ?",
    50000, "AAA"
);
```

Return value:

```java
int
```

which represents the number of rows affected for that statement (subject
to JDBC/database semantics).

For many individual operations, repeatedly calling `update()` can create
more statement executions and can be slower than batching large amounts
of similar data.

---

# 31. `batchUpdate()`

`batchUpdate()` is used to execute a batch of similar SQL operations.

Example:

```java
String Q =
    "INSERT INTO employee VALUES (?, ?)";

List<Object[]> list =
    new ArrayList<>();

for (int i = 1; i <= 10; i++) {

    list.add(
        new Object[] {
            "E" + i,
            10000 * i
        }
    );
}

int[] result =
    jt.batchUpdate(Q, list);
```

Return type:

```java
int[]
```

Each element generally corresponds to an update count for a batch item,
subject to JDBC driver behavior.

---

# 32. Complete Batch Update Example

```java
@Component
public class EmployeeService {

    @Autowired
    private JdbcTemplate jt;

    public void insert() {

        String Q =
            "INSERT INTO employee VALUES (?, ?)";

        List<Object[]> list =
            new ArrayList<>();

        for (int i = 0; i < 10; i++) {

            list.add(
                new Object[] {
                    "E" + i,
                    10000 * i
                }
            );
        }

        int[] result =
            jt.batchUpdate(Q, list);

        System.out.println(
            Arrays.toString(result)
        );
    }
}
```

---

# 33. Batch Update + Transaction

```java
@Transactional
public void insert() {

    String Q =
        "INSERT INTO employee VALUES (?, ?)";

    List<Object[]> list =
        new ArrayList<>();

    for (int i = 0; i < 10; i++) {

        list.add(
            new Object[] {
                "E" + i,
                10000 * i
            }
        );
    }

    jt.batchUpdate(Q, list);
}
```

Agar transaction ke andar exception causes rollback, then the
transaction's database changes can be rolled back together.

---

# 34. Batch Update with an Invalid Record

Suppose batch has:

```text
E0
E1
E2
E3
E4
E5  ← invalid
E6
E7
E8
E9
```

### Important Correction

Notebook mein:

```text
E1-E4 inserted
E5 not inserted
E6-E10 inserted
```

jaisa fixed result likha hai, lekin **ye universal JDBC guarantee nahi
hai**.

Batch execution failure par exact partial-success behavior JDBC
driver/database aur batch execution mode par depend kar sakta hai.

Agar **atomic all-or-nothing behavior** chahiye, batch ko Spring
transaction ke andar run karna safer design hai:

```java
@Transactional
public void insert() {
    jt.batchUpdate(Q, list);
}
```

Then an appropriate exception causing rollback can roll back the
transaction's changes.

---

# PART G --- `BatchPreparedStatementSetter`

# 35. Why `BatchPreparedStatementSetter`?

Jab batch parameters ko manually `Object[]` list mein create karne ke
instead `PreparedStatement` ke through control karna ho,
`BatchPreparedStatementSetter` use kar sakte hain.

Package:

```java
org.springframework.jdbc.core.BatchPreparedStatementSetter
```

Iske important methods:

```java
int getBatchSize();

void setValues(
    PreparedStatement ps,
    int i
) throws SQLException;
```

---

# 36. `MyBatchPreparedStatementSetter`

```java
import java.sql.PreparedStatement;
import java.sql.SQLException;

import org.springframework.jdbc.core.BatchPreparedStatementSetter;

public class MyBatchPreparedStatementSetter
        implements BatchPreparedStatementSetter {

    @Override
    public int getBatchSize() {
        return 10;
    }

    @Override
    public void setValues(
            PreparedStatement ps,
            int i) throws SQLException {

        ps.setString(1, "E" + i);
        ps.setInt(2, 10000 * i);
    }
}
```

---

# 37. Service using `BatchPreparedStatementSetter`

```java
@Component
public class EmployeeService {

    @Autowired
    private JdbcTemplate jt;

    public void insert() {

        String Q =
            "INSERT INTO employee VALUES (?, ?)";

        MyBatchPreparedStatementSetter my =
            new MyBatchPreparedStatementSetter();

        jt.batchUpdate(Q, my);
    }
}
```

---

# 38. BatchPreparedStatementSetter with `@Transactional`

```java
@Component
public class EmployeeService {

    @Autowired
    private JdbcTemplate jt;

    @Transactional
    public void insert() {

        String Q =
            "INSERT INTO employee VALUES (?, ?)";

        MyBatchPreparedStatementSetter my =
            new MyBatchPreparedStatementSetter();

        jt.batchUpdate(Q, my);
    }
}
```

---

# 39. Wrong Data at i = 5

Suppose:

```java
@Override
public void setValues(
        PreparedStatement ps,
        int i) throws SQLException {

    if (i == 5) {

        ps.setString(1, "H" + i);
        ps.setString(2, "wrong");

    } else {

        ps.setString(1, "E" + i);
        ps.setInt(2, 10000 * i);
    }
}
```

### Important

Agar `"wrong"` database column ke type ke liye valid hai, toh
**exception zaroori nahi hai**.

Ho sakta hai:

```text
E0 → inserted
E1 → inserted
...
E4 → inserted
H5 → inserted with wrong data
E6 → inserted
...
E9 → inserted
```

Agar `"wrong"` invalid type/constraint violate karta hai, tab exception
aa sakta hai.

---

# 40. BatchPreparedStatementSetter --- Without Transaction

Without transaction:

```text
Batch execution
      ↓
driver processes batch
      ↓
some items may succeed
      ↓
one item fails
      ↓
exception
```

Exact rows that remain committed depend on:

-   JDBC driver;
-   database;
-   auto-commit/transaction settings;
-   batch execution behavior.

Isliye fixed rule:

```text
E0-E4 always inserted
E5 always failed
E6-E9 always inserted
```

nahi hai.

---

# 41. BatchPreparedStatementSetter --- With Transaction

With:

```java
@Transactional
public void insert() {
    ...
}
```

and a properly configured transaction manager:

```text
Batch
 ↓
Exception
 ↓
Transaction rollback
 ↓
Transaction ke andar ke changes rollback
```

This gives the desired **atomic all-or-nothing transaction semantics**.

---

# 42. Batch Update vs BatchPreparedStatementSetter

  -------------------------------------------------------------------------------------------
  Feature                 `batchUpdate(Q, List<Object[]>)`   `BatchPreparedStatementSetter`
  ----------------------- ---------------------------------- --------------------------------
  Parameters              Object array list                  PreparedStatement setter

  Control                 Simple                             More control

  Code                    Short                              More verbose

  Dynamic parameter logic Moderate                           Easy

  Large/custom batch      Useful                             Very useful
  logic                                                      

  Return                  `int[]`                            `int[]` from batchUpdate
  -------------------------------------------------------------------------------------------

---

# PART H --- Transaction + Batch + Exceptions

# 43. Three Important Cases

## Case 1 --- `update()` without transaction

```text
update 1 → committed
update 2 → committed
exception
```

Partial changes can remain.

---

## Case 2 --- `update()` with transaction

```text
update 1
update 2
exception
↓
rollback
```

Transaction changes can be rolled back.

---

## Case 3 --- Batch without transaction

```text
batch
↓
one item fails
↓
partial result may depend on driver/database
```

---

## Case 4 --- Batch with transaction

```text
batch
↓
failure
↓
rollback transaction
```

provided the exception reaches the transaction interceptor and causes
rollback.

---
