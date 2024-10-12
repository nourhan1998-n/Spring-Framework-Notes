# Spring-Framework-Notes

# Transaction Management and JPA in Spring Framework

## 1. What are the transaction management mechanisms in Spring framework data access?

Spring provides a wide range of transaction management mechanisms:

### 1.1 JDBC Transactions
- **DataSourceTransactionManager**: Manages transactions directly at the JDBC level, typically used with `JdbcTemplate` or plain JDBC.

### 1.2 Hibernate Transactions
- **HibernateTransactionManager**: Integrates Hibernate transactions with Spring, working directly with Hibernate’s `SessionFactory`.

### 1.3 JPA Transactions
- **JpaTransactionManager**: Used for managing transactions in a JPA context, integrating Spring with JPA’s `EntityManager`.

### 1.4 Spring’s LocalSessionFactoryBean
- Configures Hibernate's `SessionFactory` in Spring applications. Transactions were typically managed using the `@Transactional` annotation.

### 1.5 Global JTA Transactions
- **JtaTransactionManager**: Enables global (distributed) transactions across multiple resources (databases, JMS, etc.) using Java Transaction API (JTA). Typically used in enterprise applications that need multi-resource transactions.

### 1.6 Reactive Transactions
- **ReactiveTransactionManager**: Provides transaction management in a non-blocking, reactive programming environment, mainly used with R2DBC (Reactive Relational Database Connectivity).

### 1.7 Chained Transaction Management
- **ChainedTransactionManager**: Coordinates transactions across multiple resources (e.g., JDBC, JMS) by chaining multiple transaction managers.

### 1.8 JMS Transactions
- **JmsTransactionManager**: Handles transactions for Java Message Service (JMS), ensuring messaging operations are part of a transactional workflow.

### 1.9 MongoDB Transactions
- **MongoTransactionManager**: Manages transactions in MongoDB, primarily for multi-document transactions.

### Declarative vs. Programmatic Transaction Management
- **Declarative Transaction Management (@Transactional)**: Automatically defines transaction boundaries without manual handling of transaction logic.
- **Programmatic Transaction Management**: Offers explicit control over transactions using `TransactionTemplate` or directly managing transactions through `PlatformTransactionManager`.

---

## 2. How @Transactional works in Spring Framework?

### Summary of Flow
- A method is called on a transactional bean.
- The proxy checks for an existing transaction.
- A new transaction is started (if necessary).
- The method is executed.
- If no exception occurs, the transaction is committed; if an exception occurs, it is rolled back.
- Resources are cleaned up.

### 2.1 Proxy Creation
When you annotate a class or method with `@Transactional`, Spring creates a proxy around your bean. There are two types of proxies:
- **JDK Dynamic Proxies**: Created if the class implements one or more interfaces.
- **CGLIB Proxies**: Created if the class does not implement interfaces or is configured explicitly.

### 2.2 Interception of Method Calls
- Transaction context is checked when a method annotated with `@Transactional` is called.
- Based on propagation settings, the proxy either joins an existing transaction or creates a new one.

### 2.3 Starting a Transaction
- The proxy interacts with the transaction manager to start a transaction, acquire resources, and manage the lifecycle of the transaction.

### 2.4 Method Execution
- The proxy executes the actual method logic within the transactional context.

### 2.5 Exception Handling and Rollback
- If an exception occurs, the transaction is rolled back based on rollback rules (typically for unchecked exceptions).

### 2.6 Committing the Transaction
- If the method completes successfully, the transaction is committed.

---

## 3. What does @Transactional manage in Spring?

`@Transactional` offers fine-grained control over transaction behavior, including:

### 3.1 Transaction Propagation
- **REQUIRED**: Joins an existing transaction or creates a new one.
- **REQUIRES_NEW**: Suspends the current transaction and starts a new one.
- **SUPPORTS, MANDATORY, NEVER, NOT_SUPPORTED, NESTED**: Other propagation settings to define transaction behavior.

### 3.2 Transaction Isolation Levels
Defines visibility of changes across transactions:
- **READ_UNCOMMITTED**, **READ_COMMITTED**, **REPEATABLE_READ**, **SERIALIZABLE**.

### 3.3 Transaction Timeout
Sets the maximum duration for a transaction:
```java
@Transactional(timeout = 30)
```

### 3.4 Read-Only Transactions
Optimizes performance for read-only transactions:
```java
@Transactional(readOnly = true)
```

### 3.5 Rollback Rules
Specifies which exceptions trigger a rollback:
```java
@Transactional(rollbackFor = Exception.class)
```

### 3.6 Transaction Synchronization
Synchronizes transactional resources and ensures proper commit or rollback.

### 3.7 Nested Transactions
Allows creating savepoints within transactions using `NESTED` propagation.

### 3.8 Custom Transaction Managers
Specifies different transaction managers for different resources.

---

## 4. Explain Proxy creation with @Transactional.

When you annotate a class or method with `@Transactional`:

1. **Bean Registration**: Spring detects the `@Transactional` annotation.
2. **Proxy Generation**: Spring creates a proxy (either JDK Dynamic Proxy or CGLIB Proxy).
3. **Method Interception**: The proxy intercepts calls to the bean's methods and manages transaction behavior.

### Intercepting Method Calls
When a method on the proxy is invoked:
Advice Execution: The proxy executes any associated advice (in this case, transaction management logic) before and/or after the actual method invocation. This is where it checks transaction settings like propagation and isolation.

### Transaction Management Flow
1. Incoming method call is intercepted.
2. Proxy checks for an existing transaction.
3. Based on propagation, either joins or starts a new transaction.
4. Executes the target method.
5. Handles exceptions and performs rollback if necessary.
6. Cleans up resources after method execution.

---

## 5. Explain @PersistenceContext in Spring Data Access.

`@PersistenceContext` is used to inject an `EntityManager` in the data access layer when working with JPA.

### 5.1 Dependency Injection
Injects `EntityManager` automatically into your repositories or service classes.

### 5.2 Transaction Management
- The `EntityManager` is bound to the current transaction.
- Automatic flushing of changes before transaction commits.

### 5.3 Attributes of @PersistenceContext
- **unitName**: Specifies the persistence unit if multiple are defined.
- **type**: Can be `TRANSACTION` or `EXTENDED` for managing different scopes of the `EntityManager`.

---

## 6. What is Flushing in JPA in the context of JPA and Hibernate?

In the context of **JPA (Java Persistence API)** and **Hibernate**, **flushing** refers to the process of synchronizing the **in-memory state** of the persistence context (also known as the first-level cache) with the **database**. Specifically, it involves sending the changes made to managed entities (in memory) to the database for **execution** (such as `INSERT`, `UPDATE`, and `DELETE` statements). 

Here’s a detailed explanation of how **flushing** works and its significance:

### 1. **Persistence Context and Managed Entities**
- In JPA, when you perform operations on entities (like persisting, updating, or removing), these changes are not immediately reflected in the database.
- Instead, changes are made to the **in-memory representation** of these entities in the persistence context (managed by an `EntityManager` in JPA or `Session` in Hibernate).
- These changes reside in this context and are only propagated to the database when flushing occurs.

### 2. **Flushing in JPA/Hibernate**
- **Flushing** is the process by which the changes in the persistence context are written to the database (but not necessarily committed). This ensures that the database state matches the in-memory state.
- It includes generating SQL statements for the changes and executing them against the database.

### 3. **When Does Flushing Occur?**
Flushing can occur automatically or manually in JPA/Hibernate, depending on the specific conditions. Here are common scenarios:

#### **Automatic Flushing**
1. **Before Query Execution**: 
   - If there are pending changes (unflushed changes) in the persistence context and a query is about to be executed, JPA/Hibernate will automatically flush to ensure the query results reflect the most up-to-date data.
   
2. **Transaction Commit**: 
   - When a transaction is committed, all pending changes in the persistence context are flushed to the database automatically. After the flush, the transaction is committed (finalized in the database).
   
3. **EntityManager/Session Close**: 
   - When an `EntityManager` or `Session` is closed, it triggers a flush if there are unsaved changes to ensure data consistency.

#### **Manual Flushing**
- You can manually trigger a flush in JPA by calling:
  ```java
  entityManager.flush();
  ```
  - This forces the synchronization of in-memory changes with the database.

- In Hibernate, similarly, you can use:
  ```java
  session.flush();
  ```

### 4. **Flushing Does NOT Mean Committing**
- **Flushing** is not the same as **committing** a transaction. When a flush occurs, changes are sent to the database, but the transaction is still active and can be rolled back if necessary.
- **Commit** is the final step, where all flushed changes are made permanent in the database.

### 5. **Flushing Modes in Hibernate**
Hibernate provides different **flush modes** to control when flushing occurs. These can be configured via `FlushMode`:

1. **`FlushMode.AUTO` (default)**:
   - Flushes automatically before query execution or before transaction commit. This is the default behavior.
   
2. **`FlushMode.COMMIT`**:
   - Flushes only at the time of the transaction commit. No intermediate flushes before query execution.
   
3. **`FlushMode.MANUAL`**:
   - No automatic flushing occurs. Flushing must be done manually by explicitly calling `flush()`.
   
4. **`FlushMode.ALWAYS`**:
   - Flushes the persistence context after every modification operation, ensuring that the database is always up to date, but this can negatively affect performance.

### 6. **Why Flushing Matters**
Flushing helps maintain **data consistency** between the **application’s in-memory state** and the **actual database state**. It is especially important in scenarios where:
- You need to run queries that depend on the latest changes made to entities.
- You want to ensure that changes to the database occur in the correct order.
- The application requires precise control over the moment when changes are propagated to the database.

### 7. **Performance Considerations**
Flushing can be a **costly operation** because it generates and executes SQL statements. If done too frequently, it can negatively impact performance, particularly in high-transaction systems. For this reason, Hibernate provides flush modes to optimize when flushing should occur.

### 8. **Example of Flushing in Hibernate**
Consider a scenario where you have a Hibernate session:

```java
Session session = sessionFactory.openSession();
Transaction tx = session.beginTransaction();

EntityA entityA = new EntityA();
entityA.setValue("Initial Value");

// The entity is persisted, but not yet flushed to the database
session.save(entityA);

// Changes are still in-memory, not in the database
entityA.setValue("Updated Value");

// Flushing manually forces the update to be sent to the database
session.flush();

// Transaction commit makes the change permanent
tx.commit();

session.close();
```
In the above example, flushing occurs manually using `session.flush()`, ensuring that the update is reflected in the database before the transaction is committed.

### Conclusion:
- **Flushing** is the process of synchronizing the in-memory state of managed entities with the database.
- It ensures that changes are sent to the database, though those changes are not final until the transaction is committed.
- Flushing can occur automatically or manually, depending on the configuration and behavior of your application.
- Proper management of flushing is crucial for ensuring both **data consistency** and **performance** in JPA and Hibernate applications.
