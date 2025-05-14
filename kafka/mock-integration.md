# Unit Test for `OrderService`

This unit test demonstrates how to test the `OrderService` class using **TestRestTemplate**, **KafkaTemplate**, **private methods**, and **static methods**. The example also integrates **Kafka message verification** and **REST API testing**.

## Scenario Overview:
- **REST API**: Using `TestRestTemplate` to call the `placeOrder` endpoint.
- **Kafka interactions**: Mocking `KafkaTemplate` to verify Kafka messages are being sent.
- **Private method**: The `isValidUser` private method will be tested indirectly.
- **Static method**: The `DiscountUtil.applyDiscount` static method will be mocked.

### Code for `OrderService`, `OrderController`, and `DiscountUtil`

#### `OrderService.java`
```java
@Service
public class OrderService {

    private final KafkaTemplate<String, String> kafkaTemplate;

    public OrderService(KafkaTemplate<String, String> kafkaTemplate) {
        this.kafkaTemplate = kafkaTemplate;
    }

    public String placeOrder(String userId, int quantity) {
        if (!isValidUser(userId)) return "Invalid User";

        double total = quantity * 10.0;
        String discountMsg = DiscountUtil.applyDiscount(total);
        String message = String.format("Order by %s for ₹%.2f. %s", userId, total, discountMsg);
        kafkaTemplate.send("order-topic", message);
        return "Order placed. " + discountMsg;
    }

    // Private method to be tested indirectly
    private boolean isValidUser(String userId) {
        return userId != null && userId.startsWith("user_");
    }
}
````

#### `DiscountUtil.java`

```java
public class DiscountUtil {

    public static String applyDiscount(double total) {
        return total > 100 ? "10% discount applied." : "No discount.";
    }
}
```

#### `OrderController.java`

```java
@RestController
@RequestMapping("/api/orders")
public class OrderController {

    private final OrderService orderService;

    public OrderController(OrderService orderService) {
        this.orderService = orderService;
    }

    @GetMapping("/place")
    public ResponseEntity<String> placeOrder(
            @RequestParam String userId,
            @RequestParam int quantity) {
        String result = orderService.placeOrder(userId, quantity);
        return ResponseEntity.ok(result);
    }
}
```

---

## Unit Test: `OrderServiceTest.java`

This test uses **Mockito**, **TestRestTemplate**, and **Awaitility** for mocking Kafka interactions and testing REST endpoints. It also mocks the static method `DiscountUtil.applyDiscount` and verifies the behavior of the `OrderService`.

```java
import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.*;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.ResponseEntity;

import org.awaitility.Awaitility;
import java.time.Duration;

@ExtendWith(SpringExtension.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class OrderServiceTest {

    @Mock
    private KafkaTemplate<String, String> kafkaTemplate;

    @InjectMocks
    private OrderService orderService;

    @Autowired
    private TestRestTemplate restTemplate;

    @Mock
    private OrderConsumer orderConsumer;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testPlaceOrder_ValidUser() {
        // Arrange
        String userId = "user_123";
        int quantity = 10;

        // Mock static method DiscountUtil.applyDiscount
        try (MockedStatic<DiscountUtil> mockedStatic = mockStatic(DiscountUtil.class)) {
            mockedStatic.when(() -> DiscountUtil.applyDiscount(anyDouble())).thenReturn("10% discount applied.");

            // Act
            String result = orderService.placeOrder(userId, quantity);

            // Assert
            assertEquals("Order placed. 10% discount applied.", result);
            verify(kafkaTemplate).send(eq("order-topic"), anyString());
        }
    }

    @Test
    void testPlaceOrder_InvalidUser() {
        // Arrange
        String userId = "invalidUser";
        int quantity = 10;

        // Act
        String result = orderService.placeOrder(userId, quantity);

        // Assert
        assertEquals("Invalid User", result);
        verify(kafkaTemplate, times(0)).send(anyString(), anyString());
    }

    @Test
    void testPlaceOrderViaRest() {
        // Arrange
        String url = "/api/orders/place?userId=user_123&quantity=10";

        // Mock static method for DiscountUtil
        try (MockedStatic<DiscountUtil> mockedStatic = mockStatic(DiscountUtil.class)) {
            mockedStatic.when(() -> DiscountUtil.applyDiscount(anyDouble())).thenReturn("10% discount applied.");

            // Act
            ResponseEntity<String> response = restTemplate.getForEntity(url, String.class);

            // Assert
            assertEquals(200, response.getStatusCodeValue());
            assertTrue(response.getBody().contains("Order placed."));

            // Mock Kafka Consumer for verification
            Awaitility.await().atMost(Duration.ofSeconds(5)).until(() -> orderConsumer.getReceivedMessages().size() > 0);
            String message = orderConsumer.getReceivedMessages().get(0);
            assertTrue(message.contains("user_123"));
        }
    }
}
```

---

## Key Test Concepts

1. **`TestRestTemplate`**:

    * Used for testing REST API endpoints, such as the `/api/orders/place` endpoint in this example.
    * Verifies that the response contains the expected content.

2. **Mocking Static Methods**:

    * The `applyDiscount` static method from `DiscountUtil` is mocked using `mockStatic()`.
    * We control its return value within the test, allowing us to test different scenarios without actual logic.

3. **`KafkaTemplate` Mocking**:

    * We mock `KafkaTemplate` to ensure no actual Kafka interactions happen during testing.
    * The `send()` method of `KafkaTemplate` is verified to check that the message is being sent.

4. **Private Method Testing**:

    * The private method `isValidUser` is not tested directly. Instead, we test the public method `placeOrder` that indirectly calls this private method.
    * The expected behavior of the public method verifies the correct behavior of the private method.

5. **Kafka Consumer**:

    * Kafka messages are verified using **Awaitility** to wait for the message to be received and then checking its content.
    * This ensures that the `OrderService` successfully interacts with Kafka as expected.

---

## Maven Dependencies

Ensure that the following dependencies are added to your `pom.xml` for testing.

```xml
<dependencies>
    <!-- Spring Boot Starter Test -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>

    <!-- Mockito for mocking -->
    <dependency>
        <groupId>org.mockito</groupId>
        <artifactId>mockito-core</artifactId>
        <version>3.11.2</version>
        <scope>test</scope>
    </dependency>

    <!-- Spring Kafka Test (for Kafka testing) -->
    <dependency>
        <groupId>org.springframework.kafka</groupId>
        <artifactId>spring-kafka-test</artifactId>
        <version>2.8.0</version>
        <scope>test</scope>
    </dependency>

    <!-- Awaitility for async test waits -->
    <dependency>
        <groupId>org.awaitility</groupId>
        <artifactId>awaitility</artifactId>
        <version>4.2.0</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

---

## Summary

This unit test covers:

* **REST API testing** using `TestRestTemplate`.
* **Mocking static methods** with `mockStatic` to control their behavior during tests.
* **Kafka interaction testing** with `KafkaTemplate`.
* **Private method testing** indirectly by validating public method behavior.