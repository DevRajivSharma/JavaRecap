# Java Design Patterns

Design patterns are typical solutions to common problems in software design. They represent best practices evolved over time by experienced software developers. This document covers the most important design patterns in Java, organized by category.

## Creational Patterns

Creational patterns provide mechanisms for object creation that increase flexibility and reuse of existing code.

### Singleton Pattern

Ensures a class has only one instance and provides a global point of access to it.

```java
public class Singleton {
    // Private static instance - eager initialization
    private static final Singleton INSTANCE = new Singleton();
    
    // Private constructor prevents instantiation from other classes
    private Singleton() {
        // Initialization code
    }
    
    // Public static method to get the instance
    public static Singleton getInstance() {
        return INSTANCE;
    }
    
    // Business methods
    public void doSomething() {
        System.out.println("Singleton is doing something");
    }
}
```

Lazy initialization version:

```java
public class LazySingleton {
    // Private static instance - lazy initialization
    private static LazySingleton instance;
    
    // Private constructor
    private LazySingleton() {
        // Initialization code
    }
    
    // Public static method with synchronized for thread safety
    public static synchronized LazySingleton getInstance() {
        if (instance == null) {
            instance = new LazySingleton();
        }
        return instance;
    }
}
```

Thread-safe with double-checked locking:

```java
public class DoubleCheckedSingleton {
    // Volatile ensures visibility across threads
    private static volatile DoubleCheckedSingleton instance;
    
    private DoubleCheckedSingleton() {
        // Initialization code
    }
    
    public static DoubleCheckedSingleton getInstance() {
        // First check (no synchronization)
        if (instance == null) {
            // Synchronize only when instance is null
            synchronized (DoubleCheckedSingleton.class) {
                // Second check (with synchronization)
                if (instance == null) {
                    instance = new DoubleCheckedSingleton();
                }
            }
        }
        return instance;
    }
}
```

Enum-based Singleton (recommended approach in Java):

```java
public enum EnumSingleton {
    INSTANCE;
    
    // Business methods
    public void doSomething() {
        System.out.println("Enum Singleton is doing something");
    }
}

// Usage
// EnumSingleton.INSTANCE.doSomething();
```

### Factory Method Pattern

Defines an interface for creating an object, but lets subclasses decide which class to instantiate.

```java
// Product interface
interface Product {
    void operation();
}

// Concrete products
class ConcreteProductA implements Product {
    @Override
    public void operation() {
        System.out.println("ConcreteProductA operation");
    }
}

class ConcreteProductB implements Product {
    @Override
    public void operation() {
        System.out.println("ConcreteProductB operation");
    }
}

// Creator abstract class
abstract class Creator {
    // Factory method
    public abstract Product createProduct();
    
    // Template method that uses the factory method
    public void someOperation() {
        Product product = createProduct();
        product.operation();
    }
}

// Concrete creators
class ConcreteCreatorA extends Creator {
    @Override
    public Product createProduct() {
        return new ConcreteProductA();
    }
}

class ConcreteCreatorB extends Creator {
    @Override
    public Product createProduct() {
        return new ConcreteProductB();
    }
}

// Client code
public class FactoryMethodExample {
    public static void main(String[] args) {
        Creator creatorA = new ConcreteCreatorA();
        creatorA.someOperation(); // Creates and uses ConcreteProductA
        
        Creator creatorB = new ConcreteCreatorB();
        creatorB.someOperation(); // Creates and uses ConcreteProductB
    }
}
```

### Abstract Factory Pattern

Provides an interface for creating families of related or dependent objects without specifying their concrete classes.

```java
// Abstract products
interface Button {
    void render();
    void onClick();
}

interface Checkbox {
    void render();
    void onSelect();
}

// Concrete products for Windows
class WindowsButton implements Button {
    @Override
    public void render() {
        System.out.println("Rendering a Windows button");
    }
    
    @Override
    public void onClick() {
        System.out.println("Windows button clicked");
    }
}

class WindowsCheckbox implements Checkbox {
    @Override
    public void render() {
        System.out.println("Rendering a Windows checkbox");
    }
    
    @Override
    public void onSelect() {
        System.out.println("Windows checkbox selected");
    }
}

// Concrete products for macOS
class MacOSButton implements Button {
    @Override
    public void render() {
        System.out.println("Rendering a macOS button");
    }
    
    @Override
    public void onClick() {
        System.out.println("macOS button clicked");
    }
}

class MacOSCheckbox implements Checkbox {
    @Override
    public void render() {
        System.out.println("Rendering a macOS checkbox");
    }
    
    @Override
    public void onSelect() {
        System.out.println("macOS checkbox selected");
    }
}

// Abstract factory
interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}

// Concrete factories
class WindowsFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new WindowsButton();
    }
    
    @Override
    public Checkbox createCheckbox() {
        return new WindowsCheckbox();
    }
}

class MacOSFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new MacOSButton();
    }
    
    @Override
    public Checkbox createCheckbox() {
        return new MacOSCheckbox();
    }
}

// Client code
class Application {
    private Button button;
    private Checkbox checkbox;
    
    public Application(GUIFactory factory) {
        button = factory.createButton();
        checkbox = factory.createCheckbox();
    }
    
    public void render() {
        button.render();
        checkbox.render();
    }
    
    public void click() {
        button.onClick();
    }
    
    public void select() {
        checkbox.onSelect();
    }
}

public class AbstractFactoryExample {
    public static void main(String[] args) {
        // Create a Windows application
        Application windowsApp = new Application(new WindowsFactory());
        windowsApp.render();
        windowsApp.click();
        windowsApp.select();
        
        System.out.println();
        
        // Create a macOS application
        Application macOSApp = new Application(new MacOSFactory());
        macOSApp.render();
        macOSApp.click();
        macOSApp.select();
    }
}
```

### Builder Pattern

Separates the construction of a complex object from its representation, allowing the same construction process to create different representations.

```java
// Product class
class Pizza {
    private String dough;
    private String sauce;
    private String topping;
    
    public void setDough(String dough) {
        this.dough = dough;
    }
    
    public void setSauce(String sauce) {
        this.sauce = sauce;
    }
    
    public void setTopping(String topping) {
        this.topping = topping;
    }
    
    @Override
    public String toString() {
        return "Pizza with " + dough + " dough, " + sauce + " sauce, and " + topping + " topping";
    }
}

// Abstract builder
interface PizzaBuilder {
    void buildDough();
    void buildSauce();
    void buildTopping();
    Pizza getPizza();
}

// Concrete builders
class HawaiianPizzaBuilder implements PizzaBuilder {
    private Pizza pizza;
    
    public HawaiianPizzaBuilder() {
        this.pizza = new Pizza();
    }
    
    @Override
    public void buildDough() {
        pizza.setDough("thin");
    }
    
    @Override
    public void buildSauce() {
        pizza.setSauce("mild");
    }
    
    @Override
    public void buildTopping() {
        pizza.setTopping("ham and pineapple");
    }
    
    @Override
    public Pizza getPizza() {
        return pizza;
    }
}

class SpicyPizzaBuilder implements PizzaBuilder {
    private Pizza pizza;
    
    public SpicyPizzaBuilder() {
        this.pizza = new Pizza();
    }
    
    @Override
    public void buildDough() {
        pizza.setDough("thick");
    }
    
    @Override
    public void buildSauce() {
        pizza.setSauce("hot");
    }
    
    @Override
    public void buildTopping() {
        pizza.setTopping("pepperoni and jalapenos");
    }
    
    @Override
    public Pizza getPizza() {
        return pizza;
    }
}

// Director
class Cook {
    private PizzaBuilder pizzaBuilder;
    
    public void setPizzaBuilder(PizzaBuilder pizzaBuilder) {
        this.pizzaBuilder = pizzaBuilder;
    }
    
    public Pizza getPizza() {
        return pizzaBuilder.getPizza();
    }
    
    public void constructPizza() {
        pizzaBuilder.buildDough();
        pizzaBuilder.buildSauce();
        pizzaBuilder.buildTopping();
    }
}

// Client code
public class BuilderExample {
    public static void main(String[] args) {
        Cook cook = new Cook();
        
        // Prepare a Hawaiian pizza
        PizzaBuilder hawaiianBuilder = new HawaiianPizzaBuilder();
        cook.setPizzaBuilder(hawaiianBuilder);
        cook.constructPizza();
        Pizza hawaiianPizza = cook.getPizza();
        System.out.println(hawaiianPizza);
        
        // Prepare a spicy pizza
        PizzaBuilder spicyBuilder = new SpicyPizzaBuilder();
        cook.setPizzaBuilder(spicyBuilder);
        cook.constructPizza();
        Pizza spicyPizza = cook.getPizza();
        System.out.println(spicyPizza);
    }
}
```

Modern Builder Pattern with fluent interface (commonly used in Java):

```java
class Person {
    // Required parameters
    private final String firstName;
    private final String lastName;
    
    // Optional parameters
    private final int age;
    private final String phone;
    private final String address;
    
    private Person(Builder builder) {
        this.firstName = builder.firstName;
        this.lastName = builder.lastName;
        this.age = builder.age;
        this.phone = builder.phone;
        this.address = builder.address;
    }
    
    // Getters
    public String getFirstName() { return firstName; }
    public String getLastName() { return lastName; }
    public int getAge() { return age; }
    public String getPhone() { return phone; }
    public String getAddress() { return address; }
    
    @Override
    public String toString() {
        return "Person{" +
                "firstName='" + firstName + '\'' +
                ", lastName='" + lastName + '\'' +
                ", age=" + age +
                ", phone='" + phone + '\'' +
                ", address='" + address + '\'' +
                '}';
    }
    
    // Builder class
    public static class Builder {
        // Required parameters
        private final String firstName;
        private final String lastName;
        
        // Optional parameters - initialized with default values
        private int age = 0;
        private String phone = "";
        private String address = "";
        
        public Builder(String firstName, String lastName) {
            this.firstName = firstName;
            this.lastName = lastName;
        }
        
        public Builder age(int age) {
            this.age = age;
            return this;
        }
        
        public Builder phone(String phone) {
            this.phone = phone;
            return this;
        }
        
        public Builder address(String address) {
            this.address = address;
            return this;
        }
        
        public Person build() {
            return new Person(this);
        }
    }
}

// Client code
public class ModernBuilderExample {
    public static void main(String[] args) {
        Person person1 = new Person.Builder("John", "Doe")
                .age(30)
                .phone("123-456-7890")
                .address("123 Main St")
                .build();
        
        Person person2 = new Person.Builder("Jane", "Smith")
                .age(25)
                .build();
        
        System.out.println(person1);
        System.out.println(person2);
    }
}
```

### Prototype Pattern

Specifies the kinds of objects to create using a prototypical instance, and creates new objects by copying this prototype.

```java
// Prototype interface
interface Prototype extends Cloneable {
    Prototype clone();
}

// Concrete prototype
class ConcretePrototype implements Prototype {
    private String field;
    
    public ConcretePrototype(String field) {
        this.field = field;
    }
    
    public void setField(String field) {
        this.field = field;
    }
    
    public String getField() {
        return field;
    }
    
    @Override
    public Prototype clone() {
        try {
            return (Prototype) super.clone();
        } catch (CloneNotSupportedException e) {
            // This shouldn't happen since we're Cloneable
            throw new RuntimeException(e);
        }
    }
    
    @Override
    public String toString() {
        return "ConcretePrototype{field='" + field + "'}";
    }
}

// Client code
public class PrototypeExample {
    public static void main(String[] args) {
        // Create a prototype instance
        ConcretePrototype prototype = new ConcretePrototype("initial");
        System.out.println("Original: " + prototype);
        
        // Clone the prototype and modify it
        ConcretePrototype clone = (ConcretePrototype) prototype.clone();
        clone.setField("modified");
        
        System.out.println("Clone: " + clone);
        System.out.println("Original after cloning: " + prototype);
    }
}
```

## Structural Patterns

Structural patterns explain how to assemble objects and classes into larger structures, while keeping these structures flexible and efficient.

### Adapter Pattern

Allows objects with incompatible interfaces to collaborate.

```java
// Target interface
interface Target {
    void request();
}

// Adaptee (the class that needs adapting)
class Adaptee {
    public void specificRequest() {
        System.out.println("Adaptee's specific request");
    }
}

// Adapter (class adapter using inheritance)
class ClassAdapter extends Adaptee implements Target {
    @Override
    public void request() {
        specificRequest();
    }
}

// Adapter (object adapter using composition)
class ObjectAdapter implements Target {
    private Adaptee adaptee;
    
    public ObjectAdapter(Adaptee adaptee) {
        this.adaptee = adaptee;
    }
    
    @Override
    public void request() {
        adaptee.specificRequest();
    }
}

// Client code
public class AdapterExample {
    public static void main(String[] args) {
        // Using class adapter
        Target classAdapter = new ClassAdapter();
        classAdapter.request();
        
        // Using object adapter
        Adaptee adaptee = new Adaptee();
        Target objectAdapter = new ObjectAdapter(adaptee);
        objectAdapter.request();
    }
}
```

Real-world example:

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Enumeration;
import java.util.Iterator;

public class IteratorToEnumerationAdapter {
    public static void main(String[] args) {
        // Create a list and get its iterator
        ArrayList<String> list = new ArrayList<>(Arrays.asList("One", "Two", "Three"));
        Iterator<String> iterator = list.iterator();
        
        // Adapt the Iterator to an Enumeration
        Enumeration<String> enumeration = new IteratorEnumerationAdapter<>(iterator);
        
        // Use the Enumeration
        while (enumeration.hasMoreElements()) {
            System.out.println(enumeration.nextElement());
        }
    }
    
    // Adapter class
    static class IteratorEnumerationAdapter<E> implements Enumeration<E> {
        private Iterator<E> iterator;
        
        public IteratorEnumerationAdapter(Iterator<E> iterator) {
            this.iterator = iterator;
        }
        
        @Override
        public boolean hasMoreElements() {
            return iterator.hasNext();
        }
        
        @Override
        public E nextElement() {
            return iterator.next();
        }
    }
}
```

### Bridge Pattern

Separates an abstraction from its implementation so that the two can vary independently.

```java
// Implementor interface
interface DrawingAPI {
    void drawCircle(double x, double y, double radius);
}

// Concrete implementors
class DrawingAPI1 implements DrawingAPI {
    @Override
    public void drawCircle(double x, double y, double radius) {
        System.out.printf("API1.circle at (%.1f,%.1f) with radius %.1f\n", x, y, radius);
    }
}

class DrawingAPI2 implements DrawingAPI {
    @Override
    public void drawCircle(double x, double y, double radius) {
        System.out.printf("API2.circle at (%.1f,%.1f) with radius %.1f\n", x, y, radius);
    }
}

// Abstraction
abstract class Shape {
    protected DrawingAPI drawingAPI;
    
    protected Shape(DrawingAPI drawingAPI) {
        this.drawingAPI = drawingAPI;
    }
    
    public abstract void draw();
    public abstract void resizeByPercentage(double percentage);
}

// Refined abstraction
class Circle extends Shape {
    private double x, y, radius;
    
    public Circle(double x, double y, double radius, DrawingAPI drawingAPI) {
        super(drawingAPI);
        this.x = x;
        this.y = y;
        this.radius = radius;
    }
    
    @Override
    public void draw() {
        drawingAPI.drawCircle(x, y, radius);
    }
    
    @Override
    public void resizeByPercentage(double percentage) {
        radius *= (1.0 + percentage / 100.0);
    }
}

// Client code
public class BridgeExample {
    public static void main(String[] args) {
        // Create shapes with different drawing APIs
        Shape circle1 = new Circle(1, 2, 3, new DrawingAPI1());
        Shape circle2 = new Circle(5, 7, 11, new DrawingAPI2());
        
        // Draw the shapes
        circle1.draw();
        circle2.draw();
        
        // Resize and draw again
        circle1.resizeByPercentage(50);
        circle2.resizeByPercentage(25);
        
        circle1.draw();
        circle2.draw();
    }
}
```

### Composite Pattern

Composes objects into tree structures to represent part-whole hierarchies. It lets clients treat individual objects and compositions of objects uniformly.

```java
import java.util.ArrayList;
import java.util.List;

// Component interface
interface Component {
    void operation();
    void add(Component component);
    void remove(Component component);
    Component getChild(int index);
}

// Leaf class
class Leaf implements Component {
    private String name;
    
    public Leaf(String name) {
        this.name = name;
    }
    
    @Override
    public void operation() {
        System.out.println("Leaf " + name + " operation");
    }
    
    @Override
    public void add(Component component) {
        // Leaf nodes cannot have children
        throw new UnsupportedOperationException();
    }
    
    @Override
    public void remove(Component component) {
        // Leaf nodes cannot have children
        throw new UnsupportedOperationException();
    }
    
    @Override
    public Component getChild(int index) {
        // Leaf nodes cannot have children
        throw new UnsupportedOperationException();
    }
}

// Composite class
class Composite implements Component {
    private String name;
    private List<Component> children = new ArrayList<>();
    
    public Composite(String name) {
        this.name = name;
    }
    
    @Override
    public void operation() {
        System.out.println("Composite " + name + " operation");
        
        // Perform operation on all children
        for (Component component : children) {
            component.operation();
        }
    }
    
    @Override
    public void add(Component component) {
        children.add(component);
    }
    
    @Override
    public void remove(Component component) {
        children.remove(component);
    }
    
    @Override
    public Component getChild(int index) {
        return children.get(index);
    }
}

// Client code
public class CompositeExample {
    public static void main(String[] args) {
        // Create a tree structure
        Composite root = new Composite("root");
        root.add(new Leaf("Leaf A"));
        root.add(new Leaf("Leaf B"));
        
        Composite comp = new Composite("Composite X");
        comp.add(new Leaf("Leaf XA"));
        comp.add(new Leaf("Leaf XB"));
        
        root.add(comp);
        root.add(new Leaf("Leaf C"));
        
        // Add and remove a leaf
        Leaf leaf = new Leaf("Leaf D");
        root.add(leaf);
        root.remove(leaf);
        
        // Perform operation on the entire tree
        root.operation();
    }
}
```

Real-world example (file system):

```java
import java.util.ArrayList;
import java.util.List;

// Component interface
interface FileSystemComponent {
    String getName();
    void printStructure(String indent);
    long getSize();
}

// Leaf class
class File implements FileSystemComponent {
    private String name;
    private long size;
    
    public File(String name, long size) {
        this.name = name;
        this.size = size;
    }
    
    @Override
    public String getName() {
        return name;
    }
    
    @Override
    public void printStructure(String indent) {
        System.out.println(indent + "- " + name + " (" + size + " bytes)");
    }
    
    @Override
    public long getSize() {
        return size;
    }
}

// Composite class
class Directory implements FileSystemComponent {
    private String name;
    private List<FileSystemComponent> children = new ArrayList<>();
    
    public Directory(String name) {
        this.name = name;
    }
    
    public void addComponent(FileSystemComponent component) {
        children.add(component);
    }
    
    public void removeComponent(FileSystemComponent component) {
        children.remove(component);
    }
    
    @Override
    public String getName() {
        return name;
    }
    
    @Override
    public void printStructure(String indent) {
        System.out.println(indent + "+ " + name + " (" + getSize() + " bytes)");
        for (FileSystemComponent component : children) {
            component.printStructure(indent + "  ");
        }
    }
    
    @Override
    public long getSize() {
        long totalSize = 0;
        for (FileSystemComponent component : children) {
            totalSize += component.getSize();
        }
        return totalSize;
    }
}

// Client code
public class FileSystemExample {
    public static void main(String[] args) {
        // Create a file system structure
        Directory root = new Directory("root");
        
        Directory home = new Directory("home");
        home.addComponent(new File("file1.txt", 1000));
        home.addComponent(new File("file2.txt", 2000));
        
        Directory documents = new Directory("documents");
        documents.addComponent(new File("doc1.pdf", 3000));
        documents.addComponent(new File("doc2.pdf", 4000));
        
        home.addComponent(documents);
        root.addComponent(home);
        
        Directory usr = new Directory("usr");
        usr.addComponent(new File("bin", 5000));
        usr.addComponent(new File("lib", 6000));
        
        root.addComponent(usr);
        
        // Print the file system structure
        root.printStructure("");
    }
}
```

### Decorator Pattern

Attaches additional responsibilities to an object dynamically. Decorators provide a flexible alternative to subclassing for extending functionality.

```java
// Component interface
interface Component {
    String operation();
}

// Concrete component
class ConcreteComponent implements Component {
    @Override
    public String operation() {
        return "ConcreteComponent";
    }
}

// Base decorator
abstract class Decorator implements Component {
    protected Component component;
    
    public Decorator(Component component) {
        this.component = component;
    }
    
    @Override
    public String operation() {
        return component.operation();
    }
}

// Concrete decorators
class ConcreteDecoratorA extends Decorator {
    public ConcreteDecoratorA(Component component) {
        super(component);
    }
    
    @Override
    public String operation() {
        return "ConcreteDecoratorA(" + super.operation() + ")";
    }
}

class ConcreteDecoratorB extends Decorator {
    public ConcreteDecoratorB(Component component) {
        super(component);
    }
    
    @Override
    public String operation() {
        return "ConcreteDecoratorB(" + super.operation() + ")";
    }
}

// Client code
public class DecoratorExample {
    public static void main(String[] args) {
        // Create a simple component
        Component component = new ConcreteComponent();
        System.out.println("Simple component: " + component.operation());
        
        // Decorate it with ConcreteDecoratorA
        Component decoratorA = new ConcreteDecoratorA(component);
        System.out.println("Decorated with A: " + decoratorA.operation());
        
        // Decorate it with ConcreteDecoratorB
        Component decoratorB = new ConcreteDecoratorB(decoratorA);
        System.out.println("Decorated with B (after A): " + decoratorB.operation());
    }
}
```

Real-world example (Java I/O):

```java
import java.io.*;

public class JavaIODecoratorExample {
    public static void main(String[] args) {
        try {
            // Create a simple FileInputStream
            InputStream fileInputStream = new FileInputStream("example.txt");
            
            // Decorate it with BufferedInputStream for efficiency
            InputStream bufferedInputStream = new BufferedInputStream(fileInputStream);
            
            // Further decorate it with DataInputStream for reading primitive data types
            DataInputStream dataInputStream = new DataInputStream(bufferedInputStream);
            
            // Use the decorated stream
            try {
                while (true) {
                    System.out.println(dataInputStream.readLine());
                }
            } catch (EOFException e) {
                // End of file reached
            }
            
            // Close the stream (closes all decorated streams)
            dataInputStream.close();
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### Facade Pattern

Provides a simplified interface to a complex subsystem of classes, making the subsystem easier to use.

```java
// Subsystem classes
class SubsystemA {
    public void operationA() {
        System.out.println("SubsystemA operation");
    }
}

class SubsystemB {
    public void operationB() {
        System.out.println("SubsystemB operation");
    }
}

class SubsystemC {
    public void operationC() {
        System.out.println("SubsystemC operation");
    }
}

// Facade
class Facade {
    private SubsystemA subsystemA;
    private SubsystemB subsystemB;
    private SubsystemC subsystemC;
    
    public Facade() {
        subsystemA = new SubsystemA();
        subsystemB = new SubsystemB();
        subsystemC = new SubsystemC();
    }
    
    // Simplified interface methods
    public void operation1() {
        System.out.println("Facade operation1:\n");
        subsystemA.operationA();
        subsystemB.operationB();
    }
    
    public void operation2() {
        System.out.println("\nFacade operation2:\n");
        subsystemB.operationB();
        subsystemC.operationC();
    }
}

// Client code
public class FacadeExample {
    public static void main(String[] args) {
        // Create a facade
        Facade facade = new Facade();
        
        // Use the simplified interface
        facade.operation1();
        facade.operation2();
    }
}
```

Real-world example (home theater):

```java
// Subsystem classes
class DVDPlayer {
    public void on() {
        System.out.println("DVD player on");
    }
    
    public void play(String movie) {
        System.out.println("Playing movie: " + movie);
    }
    
    public void stop() {
        System.out.println("DVD player stopped");
    }
    
    public void off() {
        System.out.println("DVD player off");
    }
}

class Amplifier {
    public void on() {
        System.out.println("Amplifier on");
    }
    
    public void setVolume(int level) {
        System.out.println("Setting volume to " + level);
    }
    
    public void off() {
        System.out.println("Amplifier off");
    }
}

class Projector {
    public void on() {
        System.out.println("Projector on");
    }
    
    public void wideScreenMode() {
        System.out.println("Projector in widescreen mode");
    }
    
    public void off() {
        System.out.println("Projector off");
    }
}

class Lights {
    public void dim(int level) {
        System.out.println("Dimming lights to " + level + "%");
    }
    
    public void on() {
        System.out.println("Lights on");
    }
}

// Facade
class HomeTheaterFacade {
    private DVDPlayer dvdPlayer;
    private Amplifier amplifier;
    private Projector projector;
    private Lights lights;
    
    public HomeTheaterFacade(DVDPlayer dvdPlayer, Amplifier amplifier, 
                            Projector projector, Lights lights) {
        this.dvdPlayer = dvdPlayer;
        this.amplifier = amplifier;
        this.projector = projector;
        this.lights = lights;
    }
    
    // Simplified interface for watching a movie
    public void watchMovie(String movie) {
        System.out.println("Get ready to watch a movie...");
        lights.dim(10);
        projector.on();
        projector.wideScreenMode();
        amplifier.on();
        amplifier.setVolume(5);
        dvdPlayer.on();
        dvdPlayer.play(movie);
    }
    
    // Simplified interface for ending a movie
    public void endMovie() {
        System.out.println("\nShutting down the home theater...");
        dvdPlayer.stop();
        dvdPlayer.off();
        amplifier.off();
        projector.off();
        lights.on();
    }
}

// Client code
public class HomeTheaterExample {
    public static void main(String[] args) {
        // Create subsystem components
        DVDPlayer dvdPlayer = new DVDPlayer();
        Amplifier amplifier = new Amplifier();
        Projector projector = new Projector();
        Lights lights = new Lights();
        
        // Create the facade
        HomeTheaterFacade homeTheater = 
            new HomeTheaterFacade(dvdPlayer, amplifier, projector, lights);
        
        // Use the simplified interface
        homeTheater.watchMovie("Inception");
        homeTheater.endMovie();
    }
}
```

### Flyweight Pattern

Uses sharing to support large numbers of fine-grained objects efficiently.

```java
import java.util.HashMap;
import java.util.Map;

// Flyweight interface
interface Flyweight {
    void operation(String extrinsicState);
}

// Concrete flyweight
class ConcreteFlyweight implements Flyweight {
    private String intrinsicState;
    
    public ConcreteFlyweight(String intrinsicState) {
        this.intrinsicState = intrinsicState;
    }
    
    @Override
    public void operation(String extrinsicState) {
        System.out.println("Intrinsic State: " + intrinsicState + 
                          ", Extrinsic State: " + extrinsicState);
    }
}

// Flyweight factory
class FlyweightFactory {
    private Map<String, Flyweight> flyweights = new HashMap<>();
    
    public Flyweight getFlyweight(String key) {
        if (!flyweights.containsKey(key)) {
            flyweights.put(key, new ConcreteFlyweight(key));
        }
        return flyweights.get(key);
    }
    
    public int getFlyweightCount() {
        return flyweights.size();
    }
}

// Client code
public class FlyweightExample {
    public static void main(String[] args) {
        FlyweightFactory factory = new FlyweightFactory();
        
        // Get flyweights with the same intrinsic state
        Flyweight flyweight1 = factory.getFlyweight("shared");
        Flyweight flyweight2 = factory.getFlyweight("shared");
        
        // Get a flyweight with a different intrinsic state
        Flyweight flyweight3 = factory.getFlyweight("unshared");
        
        // Verify that flyweight1 and flyweight2 are the same object
        System.out.println("flyweight1 == flyweight2: " + (flyweight1 == flyweight2));
        
        // Verify that flyweight1 and flyweight3 are different objects
        System.out.println("flyweight1 == flyweight3: " + (flyweight1 == flyweight3));
        
        // Use the flyweights with different extrinsic states
        flyweight1.operation("First call");
        flyweight2.operation("Second call");
        flyweight3.operation("Third call");
        
        // Check the number of flyweights created
        System.out.println("Number of flyweights created: " + factory.getFlyweightCount());
    }
}
```

Real-world example (text formatting):

```java
import java.awt.Color;
import java.util.HashMap;
import java.util.Map;

// Flyweight interface
interface TextFormat {
    void apply(String text);
}

// Concrete flyweight
class CharacterFormat implements TextFormat {
    private String fontFamily;
    private int fontSize;
    private Color color;
    
    public CharacterFormat(String fontFamily, int fontSize, Color color) {
        this.fontFamily = fontFamily;
        this.fontSize = fontSize;
        this.color = color;
    }
    
    @Override
    public void apply(String text) {
        System.out.println("Applying format (" + fontFamily + ", " + fontSize + ", " + 
                          colorToHex(color) + ") to text: " + text);
    }
    
    private String colorToHex(Color color) {
        return String.format("#%02X%02X%02X", color.getRed(), color.getGreen(), color.getBlue());
    }
}

// Flyweight factory
class TextFormatFactory {
    private Map<String, TextFormat> formats = new HashMap<>();
    
    public TextFormat getFormat(String fontFamily, int fontSize, Color color) {
        String key = fontFamily + "-" + fontSize + "-" + colorToHex(color);
        
        if (!formats.containsKey(key)) {
            formats.put(key, new CharacterFormat(fontFamily, fontSize, color));
        }
        
        return formats.get(key);
    }
    
    private String colorToHex(Color color) {
        return String.format("%02X%02X%02X", color.getRed(), color.getGreen(), color.getBlue());
    }
    
    public int getFormatCount() {
        return formats.size();
    }
}

// Client code
public class TextFormattingExample {
    public static void main(String[] args) {
        TextFormatFactory factory = new TextFormatFactory();
        
        // Create a document with many characters but few formats
        String[] words = {
            "Hello", "World", "Java", "Design", "Patterns", "are", "useful"
        };
        
        // Apply formats to words
        for (String word : words) {
            // Reuse the same format for multiple words
            TextFormat format;
            
            if (word.length() <= 4) {
                // Short words: Arial, 10pt, black
                format = factory.getFormat("Arial", 10, Color.BLACK);
            } else if (word.length() <= 6) {
                // Medium words: Times New Roman, 12pt, blue
                format = factory.getFormat("Times New Roman", 12, Color.BLUE);
            } else {
                // Long words: Verdana, 14pt, red
                format = factory.getFormat("Verdana", 14, Color.RED);
            }
            
            format.apply(word);
        }
        
        // Check the number of formats created
        System.out.println("\nNumber of format objects created: " + factory.getFormatCount());
    }
}
```

### Proxy Pattern

Provides a surrogate or placeholder for another object to control access to it.

```java
// Subject interface
interface Subject {
    void request();
}

// Real subject
class RealSubject implements Subject {
    @Override
    public void request() {
        System.out.println("RealSubject: Handling request");
    }
}

// Proxy
class Proxy implements Subject {
    private RealSubject realSubject;
    
    @Override
    public void request() {
        // Lazy initialization: create the real subject only when needed
        if (realSubject == null) {
            realSubject = new RealSubject();
        }
        
        // Pre-processing
        System.out.println("Proxy: Pre-processing request");
        
        // Delegating to the real subject
        realSubject.request();
        
        // Post-processing
        System.out.println("Proxy: Post-processing request");
    }
}

// Client code
public class ProxyExample {
    public static void main(String[] args) {
        // Create a proxy
        Subject proxy = new Proxy();
        
        // Use the proxy to make a request
        proxy.request();
    }
}
```

Types of proxies:

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

// Subject interface
interface Service {
    void performOperation();
    String getData();
}

// Real subject
class RealService implements Service {
    @Override
    public void performOperation() {
        System.out.println("RealService: Performing operation");
    }
    
    @Override
    public String getData() {
        return "Important data from RealService";
    }
}

// Protection proxy
class ProtectionProxy implements Service {
    private RealService realService;
    private String user;
    
    public ProtectionProxy(String user) {
        this.realService = new RealService();
        this.user = user;
    }
    
    @Override
    public void performOperation() {
        if (hasAccess()) {
            System.out.println("ProtectionProxy: Access granted to " + user);
            realService.performOperation();
        } else {
            System.out.println("ProtectionProxy: Access denied to " + user);
        }
    }
    
    @Override
    public String getData() {
        if (hasAccess()) {
            System.out.println("ProtectionProxy: Access granted to " + user);
            return realService.getData();
        } else {
            System.out.println("ProtectionProxy: Access denied to " + user);
            return "Access denied";
        }
    }
    
    private boolean hasAccess() {
        // Check if the user has access rights
        return "admin".equals(user);
    }
}

// Virtual proxy
class VirtualProxy implements Service {
    private RealService realService;
    
    @Override
    public void performOperation() {
        // Create the real service only when needed
        if (realService == null) {
            System.out.println("VirtualProxy: Creating RealService instance");
            realService = new RealService();
        }
        realService.performOperation();
    }
    
    @Override
    public String getData() {
        // Create the real service only when needed
        if (realService == null) {
            System.out.println("VirtualProxy: Creating RealService instance");
            realService = new RealService();
        }
        return realService.getData();
    }
}

// Dynamic proxy using Java's built-in Proxy class
class LoggingInvocationHandler implements InvocationHandler {
    private Object target;
    
    public LoggingInvocationHandler(Object target) {
        this.target = target;
    }
    
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("LoggingProxy: Before method " + method.getName());
        Object result = method.invoke(target, args);
        System.out.println("LoggingProxy: After method " + method.getName() + 
                          ", result: " + result);
        return result;
    }
}

// Client code
public class ProxyTypesExample {
    public static void main(String[] args) {
        // Using protection proxy
        System.out.println("\n=== Protection Proxy ===\n");
        Service adminProxy = new ProtectionProxy("admin");
        Service userProxy = new ProtectionProxy("user");
        
        adminProxy.performOperation();
        System.out.println("Admin data: " + adminProxy.getData());
        
        userProxy.performOperation();
        System.out.println("User data: " + userProxy.getData());
        
        // Using virtual proxy
        System.out.println("\n=== Virtual Proxy ===\n");
        Service virtualProxy = new VirtualProxy();
        
        System.out.println("First call:");
        virtualProxy.performOperation();
        
        System.out.println("\nSecond call:");
        System.out.println("Data: " + virtualProxy.getData());
        
        // Using dynamic proxy
        System.out.println("\n=== Dynamic Proxy ===\n");
        RealService realService = new RealService();
        
        Service dynamicProxy = (Service) Proxy.newProxyInstance(
            Service.class.getClassLoader(),
            new Class[] { Service.class },
            new LoggingInvocationHandler(realService));
        
        dynamicProxy.performOperation();
        System.out.println("Data: " + dynamicProxy.getData());
    }
}
```

## Behavioral Patterns

Behavioral patterns are concerned with algorithms and the assignment of responsibilities between objects.

### Chain of Responsibility Pattern

Passes a request along a chain of handlers. Upon receiving a request, each handler decides either to process the request or to pass it to the next handler in the chain.

```java
// Handler interface
interface Handler {
    void setNext(Handler handler);
    void handleRequest(Request request);
}

// Abstract handler
abstract class AbstractHandler implements Handler {
    private Handler nextHandler;
    
    @Override
    public void setNext(Handler handler) {
        this.nextHandler = handler;
    }
    
    protected void passToNext(Request request) {
        if (nextHandler != null) {
            nextHandler.handleRequest(request);
        } else {
            System.out.println("End of chain reached. Request unhandled.");
        }
    }
}

// Request class
class Request {
    private String type;
    private String content;
    
    public Request(String type, String content) {
        this.type = type;
        this.content = content;
    }
    
    public String getType() {
        return type;
    }
    
    public String getContent() {
        return content;
    }
}

// Concrete handlers
class TypeAHandler extends AbstractHandler {
    @Override
    public void handleRequest(Request request) {
        if ("TypeA".equals(request.getType())) {
            System.out.println("TypeAHandler handling request: " + request.getContent());
        } else {
            System.out.println("TypeAHandler passing request to next handler");
            passToNext(request);
        }
    }
}

class TypeBHandler extends AbstractHandler {
    @Override
    public void handleRequest(Request request) {
        if ("TypeB".equals(request.getType())) {
            System.out.println("TypeBHandler handling request: " + request.getContent());
        } else {
            System.out.println("TypeBHandler passing request to next handler");
            passToNext(request);
        }
    }
}

class DefaultHandler extends AbstractHandler {
    @Override
    public void handleRequest(Request request) {
        System.out.println("DefaultHandler handling request: " + request.getContent());
    }
}

// Client code
public class ChainOfResponsibilityExample {
    public static void main(String[] args) {
        // Create handlers
        Handler handlerA = new TypeAHandler();
        Handler handlerB = new TypeBHandler();
        Handler defaultHandler = new DefaultHandler();
        
        // Set up the chain
        handlerA.setNext(handlerB);
        handlerB.setNext(defaultHandler);
        
        // Create requests
        Request requestA = new Request("TypeA", "Process me with Handler A");
        Request requestB = new Request("TypeB", "Process me with Handler B");
        Request requestC = new Request("TypeC", "Process me with any available handler");
        
        // Process requests
        System.out.println("Processing request A:");
        handlerA.handleRequest(requestA);
        
        System.out.println("\nProcessing request B:");
        handlerA.handleRequest(requestB);
        
        System.out.println("\nProcessing request C:");
        handlerA.handleRequest(requestC);
    }
}
```

Real-world example (logging):

```java
// Abstract logger
abstract class Logger {
    public static final int INFO = 1;
    public static final int DEBUG = 2;
    public static final int ERROR = 3;
    
    protected int level;
    protected Logger nextLogger;
    
    public void setNextLogger(Logger nextLogger) {
        this.nextLogger = nextLogger;
    }
    
    public void logMessage(int level, String message) {
        if (this.level <= level) {
            write(message);
        }
        if (nextLogger != null) {
            nextLogger.logMessage(level, message);
        }
    }
    
    protected abstract void write(String message);
}

// Concrete loggers
class ConsoleLogger extends Logger {
    public ConsoleLogger(int level) {
        this.level = level;
    }
    
    @Override
    protected void write(String message) {
        System.out.println("Console Logger: " + message);
    }
}

class FileLogger extends Logger {
    public FileLogger(int level) {
        this.level = level;
    }
    
    @Override
    protected void write(String message) {
        System.out.println("File Logger: " + message);
    }
}

class ErrorLogger extends Logger {
    public ErrorLogger(int level) {
        this.level = level;
    }
    
    @Override
    protected void write(String message) {
        System.out.println("Error Logger: " + message);
    }
}

// Client code
public class LoggerChainExample {
    public static void main(String[] args) {
        // Create loggers
        Logger consoleLogger = new ConsoleLogger(Logger.INFO);
        Logger fileLogger = new FileLogger(Logger.DEBUG);
        Logger errorLogger = new ErrorLogger(Logger.ERROR);
        
        // Set up the chain
        consoleLogger.setNextLogger(fileLogger);
        fileLogger.setNextLogger(errorLogger);
        
        // Logger chain: consoleLogger -> fileLogger -> errorLogger
        
        // Log messages with different levels
        System.out.println("\n=== Logging INFO message ===\n");
        consoleLogger.logMessage(Logger.INFO, "This is an information message");
        
        System.out.println("\n=== Logging DEBUG message ===\n");
        consoleLogger.logMessage(Logger.DEBUG, "This is a debug message");
        
        System.out.println("\n=== Logging ERROR message ===\n");
        consoleLogger.logMessage(Logger.ERROR, "This is an error message");
    }
}
```

### Command Pattern

Turns a request into a stand-alone object that contains all information about the request. This transformation lets you pass requests as method arguments, delay or queue a request's execution, and support undoable operations.

```java
// Command interface
interface Command {
    void execute();
}

// Receiver
class Light {
    public void turnOn() {
        System.out.println("Light is on");
    }
    
    public void turnOff() {
        System.out.println("Light is off");
    }
}

// Concrete commands
class LightOnCommand implements Command {
    private Light light;
    
    public LightOnCommand(Light light) {
        this.light = light;
    }
    
    @Override
    public void execute() {
        light.turnOn();
    }
}

class LightOffCommand implements Command {
    private Light light;
    
    public LightOffCommand(Light light) {
        this.light = light;
    }
    
    @Override
    public void execute() {
        light.turnOff();
    }
}

// Invoker
class RemoteControl {
    private Command command;
    
    public void setCommand(Command command) {
        this.command = command;
    }
    
    public void pressButton() {
        command.execute();
    }
}

// Client code
public class CommandExample {
    public static void main(String[] args) {
        // Create receiver
        Light light = new Light();
        
        // Create commands
        Command lightOn = new LightOnCommand(light);
        Command lightOff = new LightOffCommand(light);
        
        // Create invoker
        RemoteControl remote = new RemoteControl();
        
        // Execute commands
        remote.setCommand(lightOn);
        remote.pressButton();
        
        remote.setCommand(lightOff);
        remote.pressButton();
    }
}
```

Command pattern with undo functionality:

```java
// Command interface with undo
interface Command {
    void execute();
    void undo();
}

// Receiver
class Light {
    private boolean isOn = false;
    
    public void turnOn() {
        isOn = true;
        System.out.println("Light is on");
    }
    
    public void turnOff() {
        isOn = false;
        System.out.println("Light is off");
    }
    
    public boolean isOn() {
        return isOn;
    }
}

// Concrete commands
class LightOnCommand implements Command {
    private Light light;
    
    public LightOnCommand(Light light) {
        this.light = light;
    }
    
    @Override
    public void execute() {
        light.turnOn();
    }
    
    @Override
    public void undo() {
        light.turnOff();
    }
}

class LightOffCommand implements Command {
    private Light light;
    
    public LightOffCommand(Light light) {
        this.light = light;
    }
    
    @Override
    public void execute() {
        light.turnOff();
    }
    
    @Override
    public void undo() {
        light.turnOn();
    }
}

// No operation command
class NoCommand implements Command {
    @Override
    public void execute() {
        // Do nothing
    }
    
    @Override
    public void undo() {
        // Do nothing
    }
}

// Invoker with undo functionality
class RemoteControlWithUndo {
    private Command onCommand;
    private Command offCommand;
    private Command undoCommand;
    
    public RemoteControlWithUndo() {
        // Initialize with no-op commands
        onCommand = new NoCommand();
        offCommand = new NoCommand();
        undoCommand = new NoCommand();
    }
    
    public void setCommand(Command onCommand, Command offCommand) {
        this.onCommand = onCommand;
        this.offCommand = offCommand;
    }
    
    public void onButtonPressed() {
        onCommand.execute();
        undoCommand = onCommand;
    }
    
    public void offButtonPressed() {
        offCommand.execute();
        undoCommand = offCommand;
    }
    
    public void undoButtonPressed() {
        undoCommand.undo();
    }
}

// Client code
public class CommandWithUndoExample {
    public static void main(String[] args) {
        // Create receiver
        Light light = new Light();
        
        // Create commands
        Command lightOn = new LightOnCommand(light);
        Command lightOff = new LightOffCommand(light);
        
        // Create invoker
        RemoteControlWithUndo remote = new RemoteControlWithUndo();
        remote.setCommand(lightOn, lightOff);
        
        // Execute commands
        System.out.println("Pressing ON button:");
        remote.onButtonPressed();
        
        System.out.println("Pressing OFF button:");
        remote.offButtonPressed();
        
        System.out.println("Pressing UNDO button:");
        remote.undoButtonPressed();
        
        System.out.println("Pressing UNDO button again:");
        remote.undoButtonPressed();
    }
}
```

### Iterator Pattern

Provides a way to access the elements of an aggregate object sequentially without exposing its underlying representation.

```java
import java.util.ArrayList;
import java.util.List;

// Iterator interface
interface Iterator<T> {
    boolean hasNext();
    T next();
}

// Aggregate interface
interface Aggregate<T> {
    Iterator<T> createIterator();
}

// Concrete iterator
class ConcreteIterator<T> implements Iterator<T> {
    private List<T> items;
    private int position = 0;
    
    public ConcreteIterator(List<T> items) {
        this.items = items;
    }
    
    @Override
    public boolean hasNext() {
        return position < items.size();
    }
    
    @Override
    public T next() {
        if (!hasNext()) {
            return null;
        }
        return items.get(position++);
    }
}

// Concrete aggregate
class ConcreteAggregate<T> implements Aggregate<T> {
    private List<T> items = new ArrayList<>();
    
    public void addItem(T item) {
        items.add(item);
    }
    
    @Override
    public Iterator<T> createIterator() {
        return new ConcreteIterator<>(items);
    }
}

// Client code
public class IteratorExample {
    public static void main(String[] args) {
        // Create aggregate and add items
        ConcreteAggregate<String> aggregate = new ConcreteAggregate<>();
        aggregate.addItem("Item 1");
        aggregate.addItem("Item 2");
        aggregate.addItem("Item 3");
        aggregate.addItem("Item 4");
        aggregate.addItem("Item 5");
        
        // Create iterator and iterate through items
        Iterator<String> iterator = aggregate.createIterator();
        
        System.out.println("Iterating through items:");
        while (iterator.hasNext()) {
            String item = iterator.next();
            System.out.println(item);
        }
    }
}
```

### Mediator Pattern

Defines an object that encapsulates how a set of objects interact. Mediator promotes loose coupling by keeping objects from referring to each other explicitly, and it lets you vary their interaction independently.

```java
// Mediator interface
interface Mediator {
    void notify(Component sender, String event);
}

// Component abstract class
abstract class Component {
    protected Mediator mediator;
    
    public Component(Mediator mediator) {
        this.mediator = mediator;
    }
}

// Concrete components
class Button extends Component {
    public Button(Mediator mediator) {
        super(mediator);
    }
    
    public void click() {
        System.out.println("Button clicked");
        mediator.notify(this, "click");
    }
}

class Textbox extends Component {
    private String text = "";
    
    public Textbox(Mediator mediator) {
        super(mediator);
    }
    
    public void setText(String text) {
        this.text = text;
        System.out.println("Textbox: text set to '" + text + "'");
        mediator.notify(this, "textChanged");
    }
    
    public String getText() {
        return text;
    }
}

class Checkbox extends Component {
    private boolean checked = false;
    
    public Checkbox(Mediator mediator) {
        super(mediator);
    }
    
    public void check() {
        checked = !checked;
        System.out.println("Checkbox: " + (checked ? "checked" : "unchecked"));
        mediator.notify(this, "checkChanged");
    }
    
    public boolean isChecked() {
        return checked;
    }
}

// Concrete mediator
class DialogMediator implements Mediator {
    private Button submitButton;
    private Textbox usernameTextbox;
    private Checkbox rememberMeCheckbox;
    
    public void setSubmitButton(Button submitButton) {
        this.submitButton = submitButton;
    }
    
    public void setUsernameTextbox(Textbox usernameTextbox) {
        this.usernameTextbox = usernameTextbox;
    }
    
    public void setRememberMeCheckbox(Checkbox rememberMeCheckbox) {
        this.rememberMeCheckbox = rememberMeCheckbox;
    }
    
    @Override
    public void notify(Component sender, String event) {
        if (sender == submitButton && event.equals("click")) {
            System.out.println("Mediator: Submit button clicked");
            if (!usernameTextbox.getText().isEmpty()) {
                System.out.println("Mediator: Form submitted with username: " + 
                                  usernameTextbox.getText() + 
                                  ", Remember me: " + rememberMeCheckbox.isChecked());
            } else {
                System.out.println("Mediator: Please enter a username");
            }
        } else if (sender == usernameTextbox && event.equals("textChanged")) {
            System.out.println("Mediator: Username text changed");
        } else if (sender == rememberMeCheckbox && event.equals("checkChanged")) {
            System.out.println("Mediator: Remember me checkbox changed");
        }
    }
}

// Client code
public class MediatorExample {
    public static void main(String[] args) {
        // Create mediator
        DialogMediator mediator = new DialogMediator();
        
        // Create components
        Button submitButton = new Button(mediator);
        Textbox usernameTextbox = new Textbox(mediator);
        Checkbox rememberMeCheckbox = new Checkbox(mediator);
        
        // Register components with mediator
        mediator.setSubmitButton(submitButton);
        mediator.setUsernameTextbox(usernameTextbox);
        mediator.setRememberMeCheckbox(rememberMeCheckbox);
        
        // Simulate user interaction
        System.out.println("=== Submitting with empty username ===\n");
        submitButton.click();
        
        System.out.println("\n=== Setting username ===\n");
        usernameTextbox.setText("john_doe");
        
        System.out.println("\n=== Checking 'Remember me' ===\n");
        rememberMeCheckbox.check();
        
        System.out.println("\n=== Submitting form ===\n");
        submitButton.click();
    }
}
```

### Memento Pattern

Captures and externalizes an object's internal state so that the object can be restored to this state later, without violating encapsulation.

```java
import java.util.ArrayList;
import java.util.List;

// Memento class
class Memento {
    private final String state;
    
    public Memento(String state) {
        this.state = state;
    }
    
    public String getState() {
        return state;
    }
}

// Originator class
class Editor {
    private String text;
    
    public void setText(String text) {
        this.text = text;
        System.out.println("Editor: Setting text to '" + text + "'");
    }
    
    public String getText() {
        return text;
    }
    
    public Memento save() {
        System.out.println("Editor: Saving state");
        return new Memento(text);
    }
    
    public void restore(Memento memento) {
        text = memento.getState();
        System.out.println("Editor: State restored to '" + text + "'");
    }
}

// Caretaker class
class History {
    private List<Memento> mementos = new ArrayList<>();
    private Editor editor;
    
    public History(Editor editor) {
        this.editor = editor;
    }
    
    public void save() {
        mementos.add(editor.save());
    }
    
    public void undo() {
        if (mementos.isEmpty()) {
            System.out.println("History: No saved states");
            return;
        }
        
        Memento memento = mementos.remove(mementos.size() - 1);
        System.out.println("History: Restoring to previous state");
        editor.restore(memento);
    }
}

// Client code
public class MementoExample {
    public static void main(String[] args) {
        // Create editor and history
        Editor editor = new Editor();
        History history = new History(editor);
        
        // Make changes and save states
        editor.setText("Hello, world!");
        history.save();
        
        editor.setText("Hello, world! This is a test.");
        history.save();
        
        editor.setText("Hello, world! This is a test. And more text.");
        
        // Undo changes
        System.out.println("\n=== Performing undo operations ===\n");
        history.undo();
        history.undo();
        
        // Try to undo again (no more saved states)
        history.undo();
    }
}
```

### Observer Pattern

Defines a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.

```java
import java.util.ArrayList;
import java.util.List;

// Observer interface
interface Observer {
    void update(String message);
}

// Subject interface
interface Subject {
    void attach(Observer observer);
    void detach(Observer observer);
    void notifyObservers();
}

// Concrete subject
class NewsAgency implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private String news;
    
    @Override
    public void attach(Observer observer) {
        observers.add(observer);
    }
    
    @Override
    public void detach(Observer observer) {
        observers.remove(observer);
    }
    
    @Override
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(news);
        }
    }
    
    public void setNews(String news) {
        this.news = news;
        System.out.println("NewsAgency: News set to '" + news + "'");
        notifyObservers();
    }
}

// Concrete observers
class NewsChannel implements Observer {
    private String name;
    private String news;
    
    public NewsChannel(String name) {
        this.name = name;
    }
    
    @Override
    public void update(String news) {
        this.news = news;
        System.out.println(name + " received news: '" + news + "'");
    }
    
    public String getNews() {
        return news;
    }
}

// Client code
public class ObserverExample {
    public static void main(String[] args) {
        // Create subject
        NewsAgency agency = new NewsAgency();
        
        // Create observers
        NewsChannel channel1 = new NewsChannel("Channel 1");
        NewsChannel channel2 = new NewsChannel("Channel 2");
        NewsChannel channel3 = new NewsChannel("Channel 3");
        
        // Register observers
        agency.attach(channel1);
        agency.attach(channel2);
        agency.attach(channel3);
        
        // Set news
        agency.setNews("Breaking news: Design patterns are awesome!");
        
        // Detach an observer
        System.out.println("\n=== Detaching Channel 2 ===\n");
        agency.detach(channel2);
        
        // Set more news
        agency.setNews("More breaking news: Observer pattern in action!");
    }
}
```

### State Pattern

Allows an object to alter its behavior when its internal state changes. The object will appear to change its class.

```java
// State interface
interface State {
    void handle(Context context);
    String getStateName();
}

// Context class
class Context {
    private State state;
    
    public Context(State initialState) {
        this.state = initialState;
        System.out.println("Context: Initial state is " + state.getStateName());
    }
    
    public void setState(State state) {
        System.out.println("Context: Changing state to " + state.getStateName());
        this.state = state;
    }
    
    public void request() {
        System.out.println("Context: Handling request in " + state.getStateName() + " state");
        state.handle(this);
    }
}

// Concrete states
class ConcreteStateA implements State {
    @Override
    public void handle(Context context) {
        System.out.println("ConcreteStateA: Handling request and changing state to B");
        context.setState(new ConcreteStateB());
    }
    
    @Override
    public String getStateName() {
        return "State A";
    }
}

class ConcreteStateB implements State {
    @Override
    public void handle(Context context) {
        System.out.println("ConcreteStateB: Handling request and changing state to C");
        context.setState(new ConcreteStateC());
    }
    
    @Override
    public String getStateName() {
        return "State B";
    }
}

class ConcreteStateC implements State {
    @Override
    public void handle(Context context) {
        System.out.println("ConcreteStateC: Handling request and changing state to A");
        context.setState(new ConcreteStateA());
    }
    
    @Override
    public String getStateName() {
        return "State C";
    }
}

// Client code
public class StateExample {
    public static void main(String[] args) {
        // Create context with initial state
        Context context = new Context(new ConcreteStateA());
        
        // Make requests that trigger state changes
        context.request();  // State A -> State B
        context.request();  // State B -> State C
        context.request();  // State C -> State A
        context.request();  // State A -> State B
    }
}
```

Real-world example (vending machine):

```java
// State interface
interface VendingMachineState {
    void insertCoin(VendingMachine machine);
    void selectProduct(VendingMachine machine);
    void dispense(VendingMachine machine);
    String getStateName();
}

// Context class
class VendingMachine {
    private VendingMachineState state;
    private int coins = 0;
    
    public VendingMachine() {
        this.state = new NoCoinState();
        System.out.println("Vending Machine: Started in " + state.getStateName() + " state");
    }
    
    public void setState(VendingMachineState state) {
        System.out.println("Vending Machine: Changing to " + state.getStateName() + " state");
        this.state = state;
    }
    
    public void insertCoin() {
        System.out.println("Vending Machine: Coin inserted");
        coins++;
        state.insertCoin(this);
    }
    
    public void selectProduct() {
        System.out.println("Vending Machine: Product selected");
        state.selectProduct(this);
    }
    
    public void dispense() {
        System.out.println("Vending Machine: Dispensing product");
        state.dispense(this);
        if (coins > 0) {
            coins--;
        }
    }
    
    public int getCoins() {
        return coins;
    }
}

// Concrete states
class NoCoinState implements VendingMachineState {
    @Override
    public void insertCoin(VendingMachine machine) {
        machine.setState(new HasCoinState());
    }
    
    @Override
    public void selectProduct(VendingMachine machine) {
        System.out.println("NoCoinState: Please insert a coin first");
    }
    
    @Override
    public void dispense(VendingMachine machine) {
        System.out.println("NoCoinState: Please insert a coin first");
    }
    
    @Override
    public String getStateName() {
        return "No Coin";
    }
}

class HasCoinState implements VendingMachineState {
    @Override
    public void insertCoin(VendingMachine machine) {
        System.out.println("HasCoinState: Coin already inserted, you now have " + 
                          machine.getCoins() + " coins");
    }
    
    @Override
    public void selectProduct(VendingMachine machine) {
        machine.setState(new ProductSelectedState());
    }
    
    @Override
    public void dispense(VendingMachine machine) {
        System.out.println("HasCoinState: Please select a product first");
    }
    
    @Override
    public String getStateName() {
        return "Has Coin";
    }
}

class ProductSelectedState implements VendingMachineState {
    @Override
    public void insertCoin(VendingMachine machine) {
        System.out.println("ProductSelectedState: Product already selected, dispensing");
        machine.dispense();
    }
    
    @Override
    public void selectProduct(VendingMachine machine) {
        System.out.println("ProductSelectedState: Product already selected, dispensing");
        machine.dispense();
    }
    
    @Override
    public void dispense(VendingMachine machine) {
        if (machine.getCoins() > 0) {
            System.out.println("ProductSelectedState: Dispensing product");
            machine.setState(new NoCoinState());
        } else {
            System.out.println("ProductSelectedState: No coins available");
            machine.setState(new NoCoinState());
        }
    }
    
    @Override
    public String getStateName() {
        return "Product Selected";
    }
}

// Client code
public class VendingMachineExample {
    public static void main(String[] args) {
        // Create vending machine
        VendingMachine machine = new VendingMachine();
        
        // Try to select product without inserting coin
        System.out.println("\n=== Selecting product without coin ===\n");
        machine.selectProduct();
        
        // Insert coin and select product
        System.out.println("\n=== Inserting coin and selecting product ===\n");
        machine.insertCoin();
        machine.selectProduct();
        machine.dispense();
        
        // Try to dispense again
        System.out.println("\n=== Trying to dispense again ===\n");
        machine.dispense();
        
        // Insert multiple coins
        System.out.println("\n=== Inserting multiple coins ===\n");
        machine.insertCoin();
        machine.insertCoin();
        machine.selectProduct();
        machine.dispense();
    }
}
```

### Strategy Pattern

Defines a family of algorithms, encapsulates each one, and makes them interchangeable. Strategy lets the algorithm vary independently from clients that use it.

```java
// Strategy interface
interface SortStrategy {
    void sort(int[] array);
}

// Concrete strategies
class BubbleSort implements SortStrategy {
    @Override
    public void sort(int[] array) {
        System.out.println("Sorting array using bubble sort");
        // Bubble sort implementation
        for (int i = 0; i < array.length - 1; i++) {
            for (int j = 0; j < array.length - i - 1; j++) {
                if (array[j] > array[j + 1]) {
                    // Swap elements
                    int temp = array[j];
                    array[j] = array[j + 1];
                    array[j + 1] = temp;
                }
            }
        }
    }
}

class QuickSort implements SortStrategy {
    @Override
    public void sort(int[] array) {
        System.out.println("Sorting array using quick sort");
        // Quick sort implementation (simplified)
        quickSort(array, 0, array.length - 1);
    }
    
    private void quickSort(int[] array, int low, int high) {
        if (low < high) {
            int pivotIndex = partition(array, low, high);
            quickSort(array, low, pivotIndex - 1);
            quickSort(array, pivotIndex + 1, high);
        }
    }
    
    private int partition(int[] array, int low, int high) {
        int pivot = array[high];
        int i = low - 1;
        
        for (int j = low; j < high; j++) {
            if (array[j] <= pivot) {
                i++;
                // Swap elements
                int temp = array[i];
                array[i] = array[j];
                array[j] = temp;
            }
        }
        
        // Swap pivot element
        int temp = array[i + 1];
        array[i + 1] = array[high];
        array[high] = temp;
        
        return i + 1;
    }
}

class MergeSort implements SortStrategy {
    @Override
    public void sort(int[] array) {
        System.out.println("Sorting array using merge sort");
        // Merge sort implementation (simplified)
        mergeSort(array, 0, array.length - 1);
    }
    
    private void mergeSort(int[] array, int left, int right) {
        if (left < right) {
            int middle = (left + right) / 2;
            mergeSort(array, left, middle);
            mergeSort(array, middle + 1, right);
            merge(array, left, middle, right);
        }
    }
    
    private void merge(int[] array, int left, int middle, int right) {
        int n1 = middle - left + 1;
        int n2 = right - middle;
        
        int[] leftArray = new int[n1];
        int[] rightArray = new int[n2];
        
        // Copy data to temp arrays
        for (int i = 0; i < n1; i++) {
            leftArray[i] = array[left + i];
        }
        for (int j = 0; j < n2; j++) {
            rightArray[j] = array[middle + 1 + j];
        }
        
        // Merge the temp arrays
        int i = 0, j = 0, k = left;
        while (i < n1 && j < n2) {
            if (leftArray[i] <= rightArray[j]) {
                array[k] = leftArray[i];
                i++;
            } else {
                array[k] = rightArray[j];
                j++;
            }
            k++;
        }
        
        // Copy remaining elements
        while (i < n1) {
            array[k] = leftArray[i];
            i++;
            k++;
        }
        while (j < n2) {
            array[k] = rightArray[j];
            j++;
            k++;
        }
    }
}

// Context
class Sorter {
    private SortStrategy strategy;
    
    public void setStrategy(SortStrategy strategy) {
        this.strategy = strategy;
    }
    
    public void sort(int[] array) {
        strategy.sort(array);
    }
}

// Client code
public class StrategyExample {
    public static void main(String[] args) {
        // Create context
        Sorter sorter = new Sorter();
        
        // Create array to sort
        int[] array = {64, 34, 25, 12, 22, 11, 90};
        
        // Print original array
        System.out.println("Original array: " + arrayToString(array));
        
        // Use bubble sort strategy
        sorter.setStrategy(new BubbleSort());
        sorter.sort(array);
        System.out.println("Sorted array (bubble sort): " + arrayToString(array));
        
        // Shuffle array
        shuffleArray(array);
        System.out.println("\nShuffled array: " + arrayToString(array));
        
        // Use quick sort strategy
        sorter.setStrategy(new QuickSort());
        sorter.sort(array);
        System.out.println("Sorted array (quick sort): " + arrayToString(array));
        
        // Shuffle array
        shuffleArray(array);
        System.out.println("\nShuffled array: " + arrayToString(array));
        
        // Use merge sort strategy
        sorter.setStrategy(new MergeSort());
        sorter.sort(array);
        System.out.println("Sorted array (merge sort): " + arrayToString(array));
    }
    
    private static String arrayToString(int[] array) {
        StringBuilder sb = new StringBuilder("[");
        for (int i = 0; i < array.length; i++) {
            sb.append(array[i]);
            if (i < array.length - 1) {
                sb.append(", ");
            }
        }
        sb.append("]");
        return sb.toString();
    }
    
    private static void shuffleArray(int[] array) {
        // Simple shuffle implementation
        for (int i = 0; i < array.length; i++) {
            int randomIndex = (int) (Math.random() * array.length);
            int temp = array[i];
            array[i] = array[randomIndex];
            array[randomIndex] = temp;
        }
    }
}
```

### Template Method Pattern

Defines the skeleton of an algorithm in a method, deferring some steps to subclasses. Template Method lets subclasses redefine certain steps of an algorithm without changing the algorithm's structure.

```java
// Abstract class with template method
abstract class DataProcessor {
    // Template method
    public final void processData() {
        readData();
        processData();
        saveData();
        hookMethod();
    }
    
    // Abstract methods that must be implemented by subclasses
    protected abstract void readData();
    protected abstract void processData();
    protected abstract void saveData();
    
    // Hook method (optional override)
    protected void hookMethod() {
        // Default implementation does nothing
    }
}

// Concrete implementations
class DatabaseProcessor extends DataProcessor {
    @Override
    protected void readData() {
        System.out.println("Reading data from database");
    }
    
    @Override
    protected void processData() {
        System.out.println("Processing data from database");
    }
    
    @Override
    protected void saveData() {
        System.out.println("Saving processed data to database");
    }
}

class FileProcessor extends DataProcessor {
    @Override
    protected void readData() {
        System.out.println("Reading data from file");
    }
    
    @Override
    protected void processData() {
        System.out.println("Processing data from file");
    }
    
    @Override
    protected void saveData() {
        System.out.println("Saving processed data to file");
    }
    
    @Override
    protected void hookMethod() {
        System.out.println("Sending notification that file processing is complete");
    }
}

// Client code
public class TemplateMethodExample {
    public static void main(String[] args) {
        System.out.println("=== Database Processor ===\n");
        DataProcessor dbProcessor = new DatabaseProcessor();
        dbProcessor.processData();
        
        System.out.println("\n=== File Processor ===\n");
        DataProcessor fileProcessor = new FileProcessor();
        fileProcessor.processData();
    }
}
```

### Visitor Pattern

Represents an operation to be performed on the elements of an object structure. Visitor lets you define a new operation without changing the classes of the elements on which it operates.

```java
import java.util.ArrayList;
import java.util.List;

// Visitor interface
interface Visitor {
    void visit(Circle circle);
    void visit(Rectangle rectangle);
    void visit(Triangle triangle);
}

// Element interface
interface Shape {
    void accept(Visitor visitor);
}

// Concrete elements
class Circle implements Shape {
    private double radius;
    
    public Circle(double radius) {
        this.radius = radius;
    }
    
    public double getRadius() {
        return radius;
    }
    
    @Override
    public void accept(Visitor visitor) {
        visitor.visit(this);
    }
}

class Rectangle implements Shape {
    private double width;
    private double height;
    
    public Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }
    
    public double getWidth() {
        return width;
    }
    
    public double getHeight() {
        return height;
    }
    
    @Override
    public void accept(Visitor visitor) {
        visitor.visit(this);
    }
}

class Triangle implements Shape {
    private double base;
    private double height;
    
    public Triangle(double base, double height) {
        this.base = base;
        this.height = height;
    }
    
    public double getBase() {
        return base;
    }
    
    public double getHeight() {
        return height;
    }
    
    @Override
    public void accept(Visitor visitor) {
        visitor.visit(this);
    }
}

// Concrete visitors
class AreaVisitor implements Visitor {
    private double totalArea = 0;
    
    @Override
    public void visit(Circle circle) {
        double area = Math.PI * circle.getRadius() * circle.getRadius();
        System.out.println("Circle area: " + area);
        totalArea += area;
    }
    
    @Override
    public void visit(Rectangle rectangle) {
        double area = rectangle.getWidth() * rectangle.getHeight();
        System.out.println("Rectangle area: " + area);
        totalArea += area;
    }
    
    @Override
    public void visit(Triangle triangle) {
        double area = 0.5 * triangle.getBase() * triangle.getHeight();
        System.out.println("Triangle area: " + area);
        totalArea += area;
    }
    
    public double getTotalArea() {
        return totalArea;
    }
}

class PerimeterVisitor implements Visitor {
    private double totalPerimeter = 0;
    
    @Override
    public void visit(Circle circle) {
        double perimeter = 2 * Math.PI * circle.getRadius();
        System.out.println("Circle perimeter: " + perimeter);
        totalPerimeter += perimeter;
    }
    
    @Override
    public void visit(Rectangle rectangle) {
        double perimeter = 2 * (rectangle.getWidth() + rectangle.getHeight());
        System.out.println("Rectangle perimeter: " + perimeter);
        totalPerimeter += perimeter;
    }
    
    @Override
    public void visit(Triangle triangle) {
        // Simplified calculation (assuming right triangle)
        double hypotenuse = Math.sqrt(Math.pow(triangle.getBase(), 2) + 
                                     Math.pow(triangle.getHeight(), 2));
        double perimeter = triangle.getBase() + triangle.getHeight() + hypotenuse;
        System.out.println("Triangle perimeter: " + perimeter);
        totalPerimeter += perimeter;
    }
    
    public double getTotalPerimeter() {
        return totalPerimeter;
    }
}

// Object structure
class Drawing {
    private List<Shape> shapes = new ArrayList<>();
    
    public void addShape(Shape shape) {
        shapes.add(shape);
    }
    
    public void accept(Visitor visitor) {
        for (Shape shape : shapes) {
            shape.accept(visitor);
        }
    }
}

// Client code
public class VisitorExample {
    public static void main(String[] args) {
        // Create object structure
        Drawing drawing = new Drawing();
        drawing.addShape(new Circle(5));
        drawing.addShape(new Rectangle(4, 6));
        drawing.addShape(new Triangle(3, 4));
        
        // Calculate areas
        System.out.println("=== Calculating Areas ===\n");
        AreaVisitor areaVisitor = new AreaVisitor();
        drawing.accept(areaVisitor);
        System.out.println("Total area: " + areaVisitor.getTotalArea());
        
        // Calculate perimeters
        System.out.println("\n=== Calculating Perimeters ===\n");
        PerimeterVisitor perimeterVisitor = new PerimeterVisitor();
        drawing.accept(perimeterVisitor);
        System.out.println("Total perimeter: " + perimeterVisitor.getTotalPerimeter());
    }
}
```

## Conclusion

Design patterns are essential tools in a Java developer's toolkit. They provide proven solutions to common design problems and help create more maintainable, flexible, and robust code. The patterns covered in this document represent the most widely used patterns in Java development, but there are many more specialized patterns that can be useful in specific contexts.

When using design patterns, remember:

1. **Don't force patterns**: Use patterns only when they provide a clear benefit to your design.
2. **Understand the trade-offs**: Each pattern comes with its own advantages and disadvantages.
3. **Combine patterns**: Real-world applications often use multiple patterns together.
4. **Document your patterns**: Make it clear to other developers which patterns you're using and why.

By mastering these design patterns, you'll be better equipped to create high-quality Java applications that are easier to maintain and extend over time.