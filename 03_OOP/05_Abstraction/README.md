# Abstraction in Java

Abstraction is a fundamental concept in object-oriented programming that focuses on hiding implementation details while showing only the necessary features of an object. It allows programmers to create a simplified view of an object by exposing what an object does rather than how it does it.

## Implementing Abstraction

In Java, abstraction can be achieved in two main ways:

### 1. Abstract Classes

An abstract class is a class that cannot be instantiated and may contain abstract methods (methods without implementation) as well as concrete methods (methods with implementation).

```java
public abstract class Vehicle {
    // Abstract method (no implementation)
    public abstract void start();
    
    // Abstract method (no implementation)
    public abstract void stop();
    
    // Concrete method (with implementation)
    public void honk() {
        System.out.println("Beep Beep!");
    }
}

public class Car extends Vehicle {
    @Override
    public void start() {
        System.out.println("Car started");
    }
    
    @Override
    public void stop() {
        System.out.println("Car stopped");
    }
}
```

Key characteristics of abstract classes:
- Declared with the `abstract` keyword
- Cannot be instantiated directly
- May contain abstract and non-abstract methods
- Can have constructors, instance variables, and static methods
- Subclasses must implement all abstract methods or be declared abstract themselves

### 2. Interfaces

An interface is a completely abstract type that contains only abstract method declarations and constants. Since Java 8, interfaces can also contain default and static methods with implementations.

```java
public interface Drawable {
    // Abstract method (implicitly public and abstract)
    void draw();
    
    // Constant (implicitly public, static, and final)
    String CATEGORY = "Graphics";
    
    // Default method (added in Java 8)
    default void display() {
        System.out.println("Displaying the drawable object");
    }
    
    // Static method (added in Java 8)
    static void info() {
        System.out.println("Drawable interface for graphical objects");
    }
}

public class Circle implements Drawable {
    private double radius;
    
    public Circle(double radius) {
        this.radius = radius;
    }
    
    @Override
    public void draw() {
        System.out.println("Drawing a circle with radius " + radius);
    }
}
```

Key characteristics of interfaces:
- All methods are implicitly public and abstract (except default and static methods)
- All fields are implicitly public, static, and final
- A class can implement multiple interfaces
- Since Java 8, interfaces can have default and static methods with implementations
- Since Java 9, interfaces can have private methods

## Abstract Class vs. Interface

| Feature | Abstract Class | Interface |
|---------|---------------|----------|
| Instantiation | Cannot be instantiated | Cannot be instantiated |
| Methods | Can have abstract and concrete methods | Abstract methods (Java 7 and earlier), can include default and static methods (Java 8+) |
| Variables | Can have instance variables | Only constants (public static final) |
| Constructors | Can have constructors | Cannot have constructors |
| Inheritance | A class can extend only one abstract class | A class can implement multiple interfaces |
| Access Modifiers | Can use all access modifiers | Methods are implicitly public |
| Purpose | Partial implementation and common base | Define a contract for implementing classes |

## When to Use Abstract Classes vs. Interfaces

**Use Abstract Classes when:**
- You want to share code among several closely related classes
- You need to declare non-static or non-final fields
- Your base class provides a partial implementation
- You want to provide a common implementation for some methods

**Use Interfaces when:**
- You expect unrelated classes to implement your interface
- You want to specify the behavior of a particular data type but not concerned about who implements it
- You want to take advantage of multiple inheritance of type

## Real-World Examples

### Example 1: Database Abstraction

```java
// Abstract class for database operations
public abstract class Database {
    // Common fields
    protected String connectionString;
    protected boolean isConnected;
    
    // Constructor
    public Database(String connectionString) {
        this.connectionString = connectionString;
        this.isConnected = false;
    }
    
    // Common method with implementation
    public boolean isConnected() {
        return isConnected;
    }
    
    // Abstract methods to be implemented by specific database types
    public abstract void connect();
    public abstract void disconnect();
    public abstract void executeQuery(String query);
}

// Concrete implementation for MySQL
public class MySQLDatabase extends Database {
    public MySQLDatabase(String connectionString) {
        super(connectionString);
    }
    
    @Override
    public void connect() {
        // MySQL-specific connection logic
        System.out.println("Connecting to MySQL database...");
        isConnected = true;
    }
    
    @Override
    public void disconnect() {
        // MySQL-specific disconnection logic
        System.out.println("Disconnecting from MySQL database...");
        isConnected = false;
    }
    
    @Override
    public void executeQuery(String query) {
        // MySQL-specific query execution
        System.out.println("Executing MySQL query: " + query);
    }
}

// Concrete implementation for MongoDB
public class MongoDatabase extends Database {
    public MongoDatabase(String connectionString) {
        super(connectionString);
    }
    
    @Override
    public void connect() {
        // MongoDB-specific connection logic
        System.out.println("Connecting to MongoDB database...");
        isConnected = true;
    }
    
    @Override
    public void disconnect() {
        // MongoDB-specific disconnection logic
        System.out.println("Disconnecting from MongoDB database...");
        isConnected = false;
    }
    
    @Override
    public void executeQuery(String query) {
        // MongoDB-specific query execution
        System.out.println("Executing MongoDB query: " + query);
    }
}
```

### Example 2: Shape Hierarchy with Interface

```java
// Interface defining the contract for shapes
public interface Shape {
    double calculateArea();
    double calculatePerimeter();
    void draw();
}

// Circle implementation
public class Circle implements Shape {
    private double radius;
    
    public Circle(double radius) {
        this.radius = radius;
    }
    
    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }
    
    @Override
    public double calculatePerimeter() {
        return 2 * Math.PI * radius;
    }
    
    @Override
    public void draw() {
        System.out.println("Drawing a circle with radius " + radius);
    }
}

// Rectangle implementation
public class Rectangle implements Shape {
    private double length;
    private double width;
    
    public Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }
    
    @Override
    public double calculateArea() {
        return length * width;
    }
    
    @Override
    public double calculatePerimeter() {
        return 2 * (length + width);
    }
    
    @Override
    public void draw() {
        System.out.println("Drawing a rectangle with length " + length + " and width " + width);
    }
}
```

## Best Practices

1. Use abstraction to hide implementation details and expose only necessary functionality
2. Choose the right abstraction mechanism (abstract class or interface) based on your needs
3. Design your abstractions around behaviors rather than properties
4. Keep interfaces focused and cohesive (Single Responsibility Principle)
5. Use abstract classes when you want to provide a common base implementation
6. Use interfaces when you want to define a contract that can be implemented by unrelated classes
7. Consider using both abstract classes and interfaces in a complementary way
8. Document the expected behavior of abstract methods

```java
// Example of using both abstract class and interface
public interface Payable {
    double calculatePayment();
}

public abstract class Employee {
    protected String name;
    protected String id;
    
    public Employee(String name, String id) {
        this.name = name;
        this.id = id;
    }
    
    public String getName() {
        return name;
    }
    
    public String getId() {
        return id;
    }
    
    public abstract void work();
}

public class FullTimeEmployee extends Employee implements Payable {
    private double monthlySalary;
    
    public FullTimeEmployee(String name, String id, double monthlySalary) {
        super(name, id);
        this.monthlySalary = monthlySalary;
    }
    
    @Override
    public void work() {
        System.out.println(name + " is working full time");
    }
    
    @Override
    public double calculatePayment() {
        return monthlySalary;
    }
}

public class Contractor extends Employee implements Payable {
    private double hourlyRate;
    private int hoursWorked;
    
    public Contractor(String name, String id, double hourlyRate, int hoursWorked) {
        super(name, id);
        this.hourlyRate = hourlyRate;
        this.hoursWorked = hoursWorked;
    }
    
    @Override
    public void work() {
        System.out.println(name + " is working as a contractor");
    }
    
    @Override
    public double calculatePayment() {
        return hourlyRate * hoursWorked;
    }
}
```