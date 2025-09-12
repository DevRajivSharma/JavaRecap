# Inheritance in Java

Inheritance is a fundamental concept in object-oriented programming that allows a class to inherit properties and behaviors from another class. It promotes code reuse and establishes an "is-a" relationship between classes.

## Basic Inheritance

In Java, inheritance is implemented using the `extends` keyword. The class that inherits is called the subclass (or derived class), and the class being inherited from is called the superclass (or base class).

```java
// Superclass
public class Animal {
    protected String name;
    protected int age;
    
    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public void eat() {
        System.out.println(name + " is eating");
    }
    
    public void sleep() {
        System.out.println(name + " is sleeping");
    }
}

// Subclass
public class Dog extends Animal {
    private String breed;
    
    public Dog(String name, int age, String breed) {
        super(name, age);  // Call to superclass constructor
        this.breed = breed;
    }
    
    public void bark() {
        System.out.println(name + " is barking");
    }
}
```

## Types of Inheritance

Java supports several types of inheritance:

### 1. Single Inheritance

A class inherits from only one superclass. This is the only class-to-class inheritance supported in Java.

```java
public class Employee extends Person { ... }
```

### 2. Multilevel Inheritance

A class inherits from a subclass, forming a chain of inheritance.

```java
public class Animal { ... }
public class Mammal extends Animal { ... }
public class Dog extends Mammal { ... }
```

### 3. Hierarchical Inheritance

Multiple classes inherit from a single superclass.

```java
public class Animal { ... }
public class Dog extends Animal { ... }
public class Cat extends Animal { ... }
public class Horse extends Animal { ... }
```

### 4. Multiple Inheritance (Through Interfaces)

Java doesn't support multiple inheritance of classes, but a class can implement multiple interfaces.

```java
public interface Swimmer { void swim(); }
public interface Flyer { void fly(); }

public class Duck extends Bird implements Swimmer, Flyer {
    @Override
    public void swim() { System.out.println("Duck is swimming"); }
    
    @Override
    public void fly() { System.out.println("Duck is flying"); }
}
```

## The `super` Keyword

The `super` keyword is used to refer to the superclass:

1. **Calling Superclass Constructor**: `super()` or `super(parameters)`
2. **Accessing Superclass Methods**: `super.methodName()`
3. **Accessing Superclass Fields**: `super.fieldName`

```java
public class Vehicle {
    protected String make;
    protected String model;
    
    public Vehicle(String make, String model) {
        this.make = make;
        this.model = model;
    }
    
    public void displayInfo() {
        System.out.println("Vehicle: " + make + " " + model);
    }
}

public class Car extends Vehicle {
    private int numDoors;
    
    public Car(String make, String model, int numDoors) {
        super(make, model);  // Call superclass constructor
        this.numDoors = numDoors;
    }
    
    @Override
    public void displayInfo() {
        super.displayInfo();  // Call superclass method
        System.out.println("Number of doors: " + numDoors);
    }
}
```

## Method Overriding

Method overriding allows a subclass to provide a specific implementation of a method that is already defined in its superclass.

```java
public class Shape {
    public double calculateArea() {
        return 0.0;
    }
}

public class Circle extends Shape {
    private double radius;
    
    public Circle(double radius) {
        this.radius = radius;
    }
    
    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }
}
```

Rules for method overriding:
1. The method in the subclass must have the same name as in the superclass
2. The method must have the same parameter list
3. The return type must be the same or a subtype of the return type in the superclass
4. The access level cannot be more restrictive than the superclass method
5. The method cannot throw broader checked exceptions

## The `final` Keyword in Inheritance

The `final` keyword can be used to prevent inheritance:

1. **Final Class**: Cannot be subclassed
   ```java
   public final class String { ... }
   ```

2. **Final Method**: Cannot be overridden in subclasses
   ```java
   public class Vehicle {
       public final void startEngine() {
           System.out.println("Engine started");
       }
   }
   ```

## Abstract Classes and Methods

Abstract classes provide a common base for subclasses but cannot be instantiated themselves. They may contain abstract methods that must be implemented by concrete subclasses.

```java
public abstract class Shape {
    protected String color;
    
    public Shape(String color) {
        this.color = color;
    }
    
    // Concrete method
    public String getColor() {
        return color;
    }
    
    // Abstract method - no implementation
    public abstract double calculateArea();
    public abstract double calculatePerimeter();
}

public class Rectangle extends Shape {
    private double length;
    private double width;
    
    public Rectangle(String color, double length, double width) {
        super(color);
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
}
```

## Object Class - The Ultimate Superclass

In Java, every class implicitly extends the `Object` class if it doesn't explicitly extend another class. The `Object` class provides several methods that are inherited by all classes:

```java
public class MyClass {  // Implicitly extends Object
    // Methods inherited from Object:
    // toString(), equals(), hashCode(), getClass(), clone(), etc.
}
```

## Best Practices

1. Use inheritance to model "is-a" relationships
2. Favor composition over inheritance when appropriate
3. Keep inheritance hierarchies shallow (avoid deep inheritance chains)
4. Design for inheritance or prohibit it (make classes final)
5. Document intended inheritance design
6. Override `toString()`, `equals()`, and `hashCode()` when needed
7. Use the `@Override` annotation when overriding methods
8. Don't override methods in constructors (can lead to unexpected behavior)

```java
// Example of favoring composition over inheritance
// Instead of: class ElectricCar extends Car

public class Engine {
    public void start() { System.out.println("Engine started"); }
    public void stop() { System.out.println("Engine stopped"); }
}

public class ElectricEngine extends Engine {
    private int batteryLevel;
    
    @Override
    public void start() { System.out.println("Electric engine started silently"); }
}

public class Car {
    private Engine engine;  // Composition
    
    public Car(Engine engine) {
        this.engine = engine;
    }
    
    public void start() {
        engine.start();
    }
}

// Usage
Car electricCar = new Car(new ElectricEngine());
electricCar.start();  // "Electric engine started silently"
```