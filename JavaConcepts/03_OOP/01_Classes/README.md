# Classes in Java

A class in Java is a blueprint or template that defines the attributes and behaviors of objects. It serves as the foundation for object-oriented programming in Java.

## Class Structure

```java
public class ClassName {
    // Fields (attributes)
    private DataType fieldName1;
    private DataType fieldName2;
    
    // Constructor
    public ClassName(DataType param1, DataType param2) {
        this.fieldName1 = param1;
        this.fieldName2 = param2;
    }
    
    // Methods (behaviors)
    public ReturnType methodName(DataType param) {
        // Method body
        return value;
    }
}
```

## Class Components

### 1. Fields (Attributes)

Fields represent the state or attributes of a class. They can be:

- **Instance Variables**: Belong to each instance of the class
- **Static Variables**: Shared among all instances of the class

```java
public class Student {
    // Instance variables
    private String name;
    private int age;
    
    // Static variable
    private static String schoolName = "Java Academy";
}
```

### 2. Constructors

Constructors are special methods used to initialize objects. They have the same name as the class and no return type.

```java
public class Student {
    private String name;
    private int age;
    
    // Default constructor
    public Student() {
        this.name = "Unknown";
        this.age = 0;
    }
    
    // Parameterized constructor
    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

### 3. Methods

Methods define the behavior of a class. They can be:

- **Instance Methods**: Operate on instance variables
- **Static Methods**: Operate on static variables or perform utility functions

```java
public class Calculator {
    // Instance method
    public int add(int a, int b) {
        return a + b;
    }
    
    // Static method
    public static int multiply(int a, int b) {
        return a * b;
    }
}
```

## Access Modifiers

Access modifiers control the visibility of classes, fields, and methods:

- **public**: Accessible from any class
- **protected**: Accessible within the same package and subclasses
- **default** (no modifier): Accessible only within the same package
- **private**: Accessible only within the same class

## Class Types

### 1. Concrete Class

A regular class that can be instantiated.

```java
public class Car {
    private String model;
    
    public void drive() {
        System.out.println("Driving the car");
    }
}
```

### 2. Abstract Class

A class that cannot be instantiated and may contain abstract methods.

```java
public abstract class Vehicle {
    private String type;
    
    // Concrete method
    public void startEngine() {
        System.out.println("Engine started");
    }
    
    // Abstract method
    public abstract void drive();
}
```

### 3. Final Class

A class that cannot be extended (subclassed).

```java
public final class ImmutableString {
    private final String value;
    
    public ImmutableString(String value) {
        this.value = value;
    }
    
    public String getValue() {
        return value;
    }
}
```

### 4. Inner Class

A class defined within another class.

```java
public class OuterClass {
    private int outerField;
    
    // Inner class
    public class InnerClass {
        public void accessOuterField() {
            System.out.println(outerField);
        }
    }
    
    // Static nested class
    public static class StaticNestedClass {
        // Cannot access non-static members of OuterClass
    }
}
```

## Best Practices

1. Follow naming conventions (PascalCase for class names)
2. Keep classes focused on a single responsibility
3. Encapsulate fields with private access and provide getters/setters as needed
4. Initialize fields in constructors
5. Document classes with Javadoc comments

```java
/**
 * Represents a bank account with basic operations.
 * @author Your Name
 */
public class BankAccount {
    private String accountNumber;
    private double balance;
    
    /**
     * Creates a new bank account with the specified account number.
     * @param accountNumber The unique account identifier
     */
    public BankAccount(String accountNumber) {
        this.accountNumber = accountNumber;
        this.balance = 0.0;
    }
    
    /**
     * Deposits the specified amount into the account.
     * @param amount The amount to deposit
     * @return The new balance after deposit
     * @throws IllegalArgumentException if amount is negative
     */
    public double deposit(double amount) {
        if (amount < 0) {
            throw new IllegalArgumentException("Amount cannot be negative");
        }
        balance += amount;
        return balance;
    }
}
```