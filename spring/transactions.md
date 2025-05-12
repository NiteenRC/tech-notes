# Spring Transaction Management - Real-Time Use Cases & Implementations

## Use Case 1: Bank Transfer (Basic Transaction with Rollback)

**Scenario:**  
A typical use case in banking applications where you need to transfer funds between two accounts. If anything goes
wrong (like insufficient funds, or DB error), the entire transaction should be rolled back.

### Implementation:

```java
@Service
public class BankingService {

    @Transactional(rollbackFor = Exception.class)  // Ensures rollback on any exception
    public void transferFunds(Long fromAccountId, Long toAccountId, BigDecimal amount) throws InsufficientFundsException {
        debit(fromAccountId, amount);   // DB Call 1: Debit from source account
        credit(toAccountId, amount);    // DB Call 2: Credit to target account
        logTransaction(fromAccountId, toAccountId, amount); // DB Call 3: Log transaction

        // Simulate error (for testing rollback)
        if (amount.compareTo(BigDecimal.ZERO) <= 0) {
            throw new InsufficientFundsException("Insufficient funds");
        }
    }

    private void debit(Long accountId, BigDecimal amount) {
        // Logic to debit the account
    }

    private void credit(Long accountId, BigDecimal amount) {
        // Logic to credit the account
    }

    private void logTransaction(Long fromAccountId, Long toAccountId, BigDecimal amount) {
        // Log the transaction to a DB
    }
}
```

### Key Insights:

- **Rollback Mechanism:** The `@Transactional` ensures that if an exception is thrown (like insufficient funds or any DB
  issue), **all changes** (debit, credit, log) are rolled back automatically.
- **Exception Handling:** `rollbackFor` ensures even **checked exceptions** will trigger a rollback.

---

## Use Case 2: Nested Transactions (Handling Multiple Layers)

**Scenario:**  
You have a service layer where a method calls another service method, and you want the inner method to run as a *
*separate transaction** (i.e., independent of the outer one).

### Implementation:

```java
@Service
public class OrderService {

    @Transactional
    public void placeOrder(Order order) {
        validateOrder(order);  // Validating order (Transaction 1)
        processPayment(order); // Processing payment (Transaction 2)
    }

    @Transactional(propagation = Propagation.REQUIRES_NEW)  // New transaction
    public void validateOrder(Order order) {
        // Validation logic
    }

    @Transactional(propagation = Propagation.REQUIRES_NEW)  // New transaction
    public void processPayment(Order order) {
        // Payment processing logic
    }
}
```

### Key Insights:

- **REQUIRES_NEW Propagation:** When you use `@Transactional(propagation = Propagation.REQUIRES_NEW)`, the inner method
  runs in **its own transaction**, independent of the outer method. If the outer method fails, the inner method's
  transaction will still commit if successful.
- **Real-World Use Case:** This is useful when you need isolated operations that should commit even if the outer
  transaction fails (e.g., logging, audit entries).

---

## Use Case 3: Transaction Isolation Levels (Preventing Dirty Reads)

**Scenario:**  
In a high-concurrency environment, you may need to control how transactions interact with each other, especially when *
*dirty reads**, **non-repeatable reads**, or **phantom reads** can occur. Using **transaction isolation** can help
prevent these.

### Implementation:

```java
@Service
public class ProductService {

    @Transactional(isolation = Isolation.SERIALIZABLE)
    public void updateProductStock(Long productId, int quantity) {
        // Perform stock update ensuring no other transactions can access it simultaneously
        updateStockInDb(productId, quantity);
    }

    private void updateStockInDb(Long productId, int quantity) {
        // DB Update logic to update stock quantity
    }
}
```

### Key Insights:

- **Isolation Levels:**
    - **`Isolation.READ_COMMITTED`** prevents dirty reads but allows non-repeatable reads.
    - **`Isolation.SERIALIZABLE`** is the strictest level, preventing dirty reads, non-repeatable reads, and phantom
      reads but can lead to performance degradation due to locking.
- **Real-World Use Case:** You use `SERIALIZABLE` for critical operations where **data consistency** is more important
  than **throughput** (e.g., banking or financial transactions).

---

## Use Case 4: Manual Transaction Management (JDBC)

**Scenario:**  
In cases where Spring's automatic transaction management doesn't fit (e.g., you need fine-grained control), you can use
**manual transaction management** using `PlatformTransactionManager`.

### Implementation:

```java
@Service
public class ManualTransactionService {

    private final PlatformTransactionManager transactionManager;

    public ManualTransactionService(PlatformTransactionManager transactionManager) {
        this.transactionManager = transactionManager;
    }

    public void manualTransactionExample() {
        DefaultTransactionDefinition def = new DefaultTransactionDefinition();
        def.setIsolationLevel(TransactionDefinition.ISOLATION_READ_COMMITTED);
        def.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);
        TransactionStatus status = transactionManager.getTransaction(def);

        try {
            // Perform multiple DB operations
            debit(...);
            credit(...);
            transactionManager.commit(status); // Commit if successful
        } catch (Exception ex) {
            transactionManager.rollback(status); // Rollback on error
            throw ex; // Rethrow the exception
        }
    }

    private void debit(Long accountId, BigDecimal amount) {
        // Debit logic here
    }

    private void credit(Long accountId, BigDecimal amount) {
        // Credit logic here
    }
}
```

### Key Insights:

- **Manual Transaction Management:** You can manually control when to commit or rollback using
  `PlatformTransactionManager`. This is useful when you need more control than `@Transactional` provides.
- **Real-World Use Case:** If you're dealing with complex transactions that span multiple systems (e.g., integrating
  legacy systems or multiple databases).

---

## Use Case 5: Microservices - Saga Pattern for Distributed Transactions

**Scenario:**  
In a microservices architecture, you may need to manage distributed transactions where one service fails after other
services have already committed their changes. The **Saga Pattern** helps handle this.

### Implementation:

- **Orchestration Saga**: One central service coordinates the transaction.
- **Choreography Saga**: Services communicate with each other to manage the transaction.

```java
// Order Service (Orchestration)
@Service
public class OrderService {

    private final PaymentService paymentService;

    @Transactional
    public void placeOrder(Order order) {
        // Step 1: Create order
        createOrder(order);
        
        // Step 2: Call Payment Service
        paymentService.processPayment(order);
    }
}

// Payment Service (Choreography)
@Service
public class PaymentService {

    public void processPayment(Order order) {
        // Payment processing logic
        if (paymentFails()) {
            // Call compensating transaction to cancel the order if payment fails
            cancelOrder(order);
        }
    }

    private void cancelOrder(Order order) {
        // Logic to compensate the order creation, like rollback or compensation steps
    }
}
```

### Key Insights:

- **Saga Pattern:** Helps maintain data consistency in **distributed systems** where **2-phase commit** is not feasible.
  Instead of rolling back everything in case of failure, compensating actions (like `cancelOrder`) are performed to undo
  the changes made by other services.
- **Real-World Use Case:** Useful in **e-commerce** or **order management systems** where services like **payment**, *
  *inventory**, and **shipping** are handled by separate microservices.

---

# Conclusion

These **real-time use cases** showcase how **Spring Transactions** can be applied in different scenarios, from simple
single-database transactions to complex distributed system transactions. Mastery of these will help you not only in *
*interviews** but also in **real-world production systems** where you must handle data consistency, isolation, and
rollback behaviors effectively.