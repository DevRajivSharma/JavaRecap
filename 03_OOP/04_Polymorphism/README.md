# Polymorphism in Java

Polymorphism is a core concept in object-oriented programming that allows objects of different classes to be treated as objects of a common superclass. The word "polymorphism" means "many forms," and it enables a single interface to represent different underlying forms (data types).

## Types of Polymorphism

Java supports two types of polymorphism:

### 1. Compile-time Polymorphism (Static Binding)

Also known as method overloading, this occurs when multiple methods in the same class have the same name but different parameters.

```java
public class Calculator {
    // Method overloading
    public int add(int a, int b) {
        return a + b;
    }
    
    public double add(double a, double b) {
        return a + b;
    }
    
    public int add(int a, int b, int c) {
        return a + b + c;
    }
}
```

The compiler determines which method to call based on the method signature (method name and parameter types).

### 2. Runtime Polymorphism (Dynamic Binding)

Also known as method overriding, this occurs when a subclass provides a specific implementation of a method that is already defined in its superclass.

```java
public class Animal {
    public void makeSound() {
        System.out.println("Animal makes a sound");
    }
}

public class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Dog barks");
    }
}

public class Cat extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Cat meows");
    }
}
```

The JVM determines which method to call at runtime based on the actual object type.

## Polymorphic References

A reference variable of a superclass type can refer to an object of any subclass type.

```java
Animal animal1 = new Animal();  // Animal reference, Animal object
Animal animal2 = new Dog();     // Animal reference, Dog object
Animal animal3 = new Cat();     // Animal reference, Cat object

animal1.makeSound();  // Output: "Animal makes a sound"
animal2.makeSound();  // Output: "Dog barks"
animal3.makeSound();  // Output: "Cat meows"
```

This is a powerful feature that allows for flexible and extensible code.

## Polymorphism with Interfaces

Interfaces are a key mechanism for achieving polymorphism in Java. A class can implement multiple interfaces, allowing objects to take on multiple forms.

```java
public interface Shape {
    double calculateArea();
    double calculatePerimeter();
}

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
}

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
}
```

Using polymorphic references with interfaces:

```java
Shape circle = new Circle(5.0);
Shape rectangle = new Rectangle(4.0, 6.0);

System.out.println("Circle area: " + circle.calculateArea());
System.out.println("Rectangle area: " + rectangle.calculateArea());

// We can also use polymorphism in collections
List<Shape> shapes = new ArrayList<>();
shapes.add(new Circle(3.0));
shapes.add(new Rectangle(2.0, 4.0));
shapes.add(new Circle(7.0));

for (Shape shape : shapes) {
    System.out.println("Area: " + shape.calculateArea());
    System.out.println("Perimeter: " + shape.calculatePerimeter());
}
```

## Benefits of Polymorphism

1. **Code Reusability**: Write code that can work with objects of multiple types
2. **Flexibility**: Add new classes without changing existing code
3. **Extensibility**: Extend functionality through inheritance and interfaces
4. **Maintainability**: Simplify code structure and reduce redundancy

## Practical Examples

### Example 1: Payment Processing System

```java
public interface PaymentMethod {
    boolean processPayment(double amount);
}

public class CreditCardPayment implements PaymentMethod {
    private String cardNumber;
    private String expiryDate;
    
    public CreditCardPayment(String cardNumber, String expiryDate) {
        this.cardNumber = cardNumber;
        this.expiryDate = expiryDate;
    }
    
    @Override
    public boolean processPayment(double amount) {
        // Credit card payment processing logic
        System.out.println("Processing credit card payment of $" + amount);
        return true;
    }
}

public class PayPalPayment implements PaymentMethod {
    private String email;
    
    public PayPalPayment(String email) {
        this.email = email;
    }
    
    @Override
    public boolean processPayment(double amount) {
        // PayPal payment processing logic
        System.out.println("Processing PayPal payment of $" + amount);
        return true;
    }
}

public class PaymentProcessor {
    public void processOrder(double amount, PaymentMethod paymentMethod) {
        // The payment processor doesn't need to know the specific payment type
        boolean success = paymentMethod.processPayment(amount);
        if (success) {
            System.out.println("Payment successful");
        } else {
            System.out.println("Payment failed");
        }
    }
}
```

### Example 2: Drawing Application

```java
public abstract class GraphicObject {
    private int x, y;
    
    public abstract void draw();
    public abstract void resize();
    
    // Common methods for all graphic objects
    public void moveTo(int newX, int newY) {
        this.x = newX;
        this.y = newY;
        System.out.println("Object moved to coordinates (" + x + ", " + y + ")");
    }
}

public class Circle extends GraphicObject {
    @Override
    public void draw() {
        System.out.println("Drawing a circle");
    }
    
    @Override
    public void resize() {
        System.out.println("Resizing a circle");
    }
}

public class Rectangle extends GraphicObject {
    @Override
    public void draw() {
        System.out.println("Drawing a rectangle");
    }
    
    @Override
    public void resize() {
        System.out.println("Resizing a rectangle");
    }
}

// Usage
public class DrawingApp {
    public static void main(String[] args) {
        List<GraphicObject> objects = new ArrayList<>();
        objects.add(new Circle());
        objects.add(new Rectangle());
        
        // Draw all objects
        for (GraphicObject object : objects) {
            object.draw();
        }
        
        // Move all objects
        for (GraphicObject object : objects) {
            object.moveTo(10, 20);
        }
    }
}
```

## Best Practices

1. Design for polymorphism by identifying common behaviors
2. Use interfaces to define contracts for polymorphic behavior
3. Prefer interfaces over abstract classes when possible
4. Use the most general type possible for variable declarations
5. Apply the Liskov Substitution Principle (a subclass should be substitutable for its superclass)
6. Document the expected behavior of polymorphic methods
7. Use the `@Override` annotation to ensure proper method overriding
8. Be cautious with downcasting (use `instanceof` to check type before casting)

```java
// Example of proper downcasting
public void processAnimal(Animal animal) {
    animal.makeSound();  // Polymorphic method call
    
    // If we need to access Dog-specific methods
    if (animal instanceof Dog) {
        Dog dog = (Dog) animal;  // Safe downcasting
        dog.fetch();  // Dog-specific method
    }
}
```