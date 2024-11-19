Spring Boot leverages several **design patterns** to make development easier, more maintainable, and scalable. Here are some of the most famous and commonly used patterns in Spring Boot:

### 1. **Singleton Pattern**
   - **Description**: Ensures a class has only one instance and provides a global point of access to it.
   - **Usage in Spring Boot**: Spring beans are singleton by default, meaning only one instance of a class is created in the Spring container. This is useful for shared resources such as database connections or services.
   - **Example**:
     ```java
     @Service
     public class MyService {
         // This service will be a singleton by default
     }
     ```

### 2. **Factory Pattern**
   - **Description**: Provides an interface for creating instances of a class, with its subclasses deciding which class to instantiate.
   - **Usage in Spring Boot**: Spring provides factory beans and methods (e.g., `@Bean` methods) to create beans. You can define different types of objects based on conditions.
   - **Example**:
     ```java
     @Configuration
     public class AppConfig {
         @Bean
         public MyService myService() {
             return new MyServiceImpl();  // Factory method
         }
     }
     ```

### 3. **Proxy Pattern**
   - **Description**: Provides an object representing another object. The proxy controls access to the original object.
   - **Usage in Spring Boot**: Spring AOP (Aspect-Oriented Programming) uses proxies to intercept method calls and apply additional logic (like transaction management or logging) without modifying the original code.
   - **Example**: Spring uses proxies for aspects like `@Transactional`:
     ```java
     @Transactional
     public void someMethod() {
         // Transactional behavior will be applied using a proxy
     }
     ```

### 4. **Observer Pattern**
   - **Description**: Allows an object (subject) to notify other objects (observers) of state changes.
   - **Usage in Spring Boot**: Spring provides `ApplicationEventPublisher` and `@EventListener` to implement the observer pattern. It's useful for event-driven architecture, where components can react to events in the system.
   - **Example**:
     ```java
     @Component
     public class MyEventListener {
         @EventListener
         public void handleMyEvent(ApplicationEvent event) {
             // Handle the event
         }
     }
     ```

### 5. **Decorator Pattern**
   - **Description**: Allows you to dynamically add behavior to an object without modifying its structure.
   - **Usage in Spring Boot**: This is used when you need to enhance or modify the behavior of beans. Spring's `@Configuration` classes and `@PostConstruct` or `@PreDestroy` methods are examples of this pattern.
   - **Example**:
     ```java
     @Component
     public class MyServiceDecorator implements MyService {
         private final MyService myService;
         
         public MyServiceDecorator(MyService myService) {
             this.myService = myService;
         }

         @Override
         public void execute() {
             // Decorate behavior
             myService.execute();
         }
     }
     ```

### 6. **Template Method Pattern**
   - **Description**: Defines the skeleton of an algorithm, allowing subclasses to redefine specific steps of the algorithm.
   - **Usage in Spring Boot**: Spring uses the template method pattern in several places, such as `JdbcTemplate`, `RestTemplate`, and `MongoTemplate`, where common logic is abstracted, and subclasses can implement the specific logic.
   - **Example**:
     ```java
     public class MyJdbcTemplate extends JdbcTemplate {
         @Override
         public void executeQuery(String sql) {
             // Template method pattern: Custom logic + base class logic
             super.executeQuery(sql);
         }
     }
     ```

### 7. **Strategy Pattern**
   - **Description**: Allows a family of algorithms to be defined and encapsulated within a class, making them interchangeable.
   - **Usage in Spring Boot**: Spring Boot uses this pattern for defining beans that implement different strategies for a particular behavior. For example, in Spring Security, different authentication strategies (like form login or JWT-based login) can be easily switched by using different configurations.
   - **Example**:
     ```java
     public interface PaymentStrategy {
         void pay();
     }

     public class CreditCardPayment implements PaymentStrategy {
         @Override
         public void pay() {
             // Implement Credit Card Payment logic
         }
     }

     public class PayPalPayment implements PaymentStrategy {
         @Override
         public void pay() {
             // Implement PayPal Payment logic
         }
     }
     ```

### 8. **Singleton Factory Pattern**
   - **Description**: Combines both the Singleton and Factory patterns. It ensures a class has only one instance, and it provides a method to create or retrieve that instance.
   - **Usage in Spring Boot**: Spring's `@Bean` methods create Singleton beans, and the container manages the lifecycle.
   - **Example**:
     ```java
     @Bean
     public MySingletonService mySingletonService() {
         return new MySingletonServiceImpl();
     }
     ```

### 9. **Chain of Responsibility Pattern**
   - **Description**: Allows multiple handlers to process a request, with each handler deciding whether to process the request or pass it to the next handler.
   - **Usage in Spring Boot**: Spring Security uses this pattern for request handling and authorization. Each filter in the chain can either process the request or pass it along.
   - **Example**: Spring Security filter chain, where multiple filters handle the authentication and authorization logic.

### 10. **Adapter Pattern**
   - **Description**: Converts one interface into another interface expected by the client.
   - **Usage in Spring Boot**: Spring Boot uses adapters to wrap existing functionality with new functionality. For example, `JpaRepository` adapts CRUD operations for different repositories.
   - **Example**:
     ```java
     @Repository
     public interface UserRepository extends JpaRepository<User, Long> {
         // Adapter pattern: Adds extra methods for specific repositories
     }
     ```

---

### Summary
Spring Boot is designed with many design patterns to make development easier, more flexible, and maintainable. These patterns allow developers to create well-structured applications that are easy to extend and modify over time. Some of the most commonly used patterns in Spring Boot include:

- Singleton
- Factory
- Proxy
- Observer
- Template Method
- Strategy
- Adapter
- Chain of Responsibility

Each pattern has its own specific use case and helps in building robust and scalable applications.

 

| **Design Pattern**      | **Description**                                                                 | **Usage in Spring Boot**                                                                                  |
|--------------------------|---------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|
| **Singleton**            | Ensures only one instance of a class is created throughout the application.    | Spring Beans are singletons by default, managed by the Spring Container.                                |
| **Factory Method**       | Provides an interface to create objects without specifying their exact classes. | `BeanFactory` and `ApplicationContext` create and manage Spring Beans.                                  |
| **Prototype**            | Creates a new instance of a bean for every request.                           | Used in Spring for prototype-scoped beans when different instances are required.                        |
| **Proxy**                | Provides a proxy object to control access to the original object.              | Spring AOP uses proxies to apply cross-cutting concerns like logging and security.                      |
| **Template Method**      | Defines a skeleton of an algorithm, letting subclasses override steps.         | `JdbcTemplate`, `RestTemplate`, and `JpaTemplate` provide predefined templates for database operations. |
| **Observer**             | Implements a publisher-subscriber model where observers react to changes.     | Spring’s `ApplicationEvent` and `ApplicationListener` for event-driven programming.                     |
| **Builder**              | Constructs complex objects step by step.                                      | Used in creating immutable objects, such as `ResponseEntity` in Spring MVC.                             |
| **Strategy**             | Defines a family of algorithms, letting clients choose one at runtime.        | Spring’s `@Qualifier` and DI allow selecting different bean implementations dynamically.                |
| **Decorator**            | Adds new functionality to objects dynamically.                                | Used in filters or request/response processing in Spring MVC.                                           |
| **Front Controller**     | A single handler for all incoming requests.                                   | `DispatcherServlet` in Spring MVC acts as the Front Controller.                                         |
| **Dependency Injection** | Injects dependencies rather than hardcoding them.                             | Core concept of Spring; achieved via `@Autowired`, constructor injection, or setter injection.          |
| **MVC (Model-View-Controller)** | Separates application logic, user interface, and data access.            | Spring MVC uses `Controller`, `View`, and `Model` to structure web applications.                        |
 
