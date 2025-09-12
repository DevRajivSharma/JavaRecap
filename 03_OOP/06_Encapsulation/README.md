# Encapsulation in Java

Encapsulation is one of the four fundamental OOP concepts in Java. It is the technique of wrapping data (variables) and code acting on the data (methods) together as a single unit. In encapsulation, the variables of a class are hidden from other classes and can be accessed only through the methods of their current class.

## Key Principles of Encapsulation

1. **Data Hiding**: Restricting direct access to class variables/attributes
2. **Access Control**: Using access modifiers to control the visibility of class members
3. **Getters and Setters**: Providing public methods to access and modify private variables
4. **Validation**: Implementing validation logic in setter methods to ensure data integrity

## Implementing Encapsulation

### 1. Declare Variables as Private

The first step in encapsulation is to declare the class variables as private, making them inaccessible from outside the class.

```java
public class Person {
    private String name;
    private int age;
    private String address;
    
    // Constructor, getters, and setters will go here
}
```

### 2. Provide Public Getter and Setter Methods

Create public methods to access (getters) and modify (setters) the private variables.

```java
public class Person {
    private String name;
    private int age;
    private String address;
    
    // Constructor
    public Person(String name, int age, String address) {
        this.name = name;
        this.age = age;
        this.address = address;
    }
    
    // Getter methods
    public String getName() {
        return name;
    }
    
    public int getAge() {
        return age;
    }
    
    public String getAddress() {
        return address;
    }
    
    // Setter methods
    public void setName(String name) {
        this.name = name;
    }
    
    public void setAge(int age) {
        this.age = age;
    }
    
    public void setAddress(String address) {
        this.address = address;
    }
}
```

### 3. Add Validation in Setter Methods

Enhance setter methods with validation logic to ensure data integrity.

```java
public class Person {
    private String name;
    private int age;
    private String address;
    
    // Constructor
    public Person(String name, int age, String address) {
        this.setName(name);  // Using setter for validation
        this.setAge(age);    // Using setter for validation
        this.setAddress(address);
    }
    
    // Getter methods
    public String getName() {
        return name;
    }
    
    public int getAge() {
        return age;
    }
    
    public String getAddress() {
        return address;
    }
    
    // Setter methods with validation
    public void setName(String name) {
        if (name == null || name.isEmpty()) {
            throw new IllegalArgumentException("Name cannot be empty");
        }
        this.name = name;
    }
    
    public void setAge(int age) {
        if (age < 0 || age > 120) {
            throw new IllegalArgumentException("Age must be between 0 and 120");
        }
        this.age = age;
    }
    
    public void setAddress(String address) {
        this.address = address;
    }
}
```

## Access Modifiers in Java

Java provides four access modifiers to control the visibility of classes, methods, and fields:

1. **private**: Accessible only within the same class
2. **default** (no modifier): Accessible within the same package
3. **protected**: Accessible within the same package and by subclasses
4. **public**: Accessible from any class

```java
public class AccessModifiersExample {
    private int privateVar;       // Accessible only within this class
    int defaultVar;              // Accessible within the same package
    protected int protectedVar;  // Accessible within the same package and subclasses
    public int publicVar;        // Accessible from anywhere
    
    private void privateMethod() {
        // Accessible only within this class
    }
    
    void defaultMethod() {
        // Accessible within the same package
    }
    
    protected void protectedMethod() {
        // Accessible within the same package and subclasses
    }
    
    public void publicMethod() {
        // Accessible from anywhere
    }
}
```

## Benefits of Encapsulation

1. **Data Hiding**: Protects the internal state of an object by hiding its attributes
2. **Increased Flexibility**: Implementation details can be changed without affecting the code that uses the class
3. **Reusability**: Encapsulated code can be reused in different parts of an application
4. **Testing**: Easier to test encapsulated code as it has well-defined interfaces
5. **Maintenance**: Easier to maintain and modify code without affecting other parts of the application

## Real-World Examples

### Example 1: Bank Account

```java
public class BankAccount {
    private String accountNumber;
    private String accountHolder;
    private double balance;
    private boolean active;
    
    public BankAccount(String accountNumber, String accountHolder) {
        this.accountNumber = accountNumber;
        this.accountHolder = accountHolder;
        this.balance = 0.0;
        this.active = true;
    }
    
    // Getters
    public String getAccountNumber() {
        return accountNumber;
    }
    
    public String getAccountHolder() {
        return accountHolder;
    }
    
    public double getBalance() {
        return balance;
    }
    
    public boolean isActive() {
        return active;
    }
    
    // Setters
    public void setAccountHolder(String accountHolder) {
        if (accountHolder == null || accountHolder.isEmpty()) {
            throw new IllegalArgumentException("Account holder name cannot be empty");
        }
        this.accountHolder = accountHolder;
    }
    
    public void setActive(boolean active) {
        this.active = active;
    }
    
    // Business methods
    public void deposit(double amount) {
        if (!active) {
            throw new IllegalStateException("Account is not active");
        }
        
        if (amount <= 0) {
            throw new IllegalArgumentException("Deposit amount must be positive");
        }
        
        balance += amount;
    }
    
    public void withdraw(double amount) {
        if (!active) {
            throw new IllegalStateException("Account is not active");
        }
        
        if (amount <= 0) {
            throw new IllegalArgumentException("Withdrawal amount must be positive");
        }
        
        if (amount > balance) {
            throw new IllegalArgumentException("Insufficient funds");
        }
        
        balance -= amount;
    }
}
```

### Example 2: Student Information System

```java
public class Student {
    private String id;
    private String name;
    private int age;
    private List<Course> enrolledCourses;
    private double gpa;
    
    public Student(String id, String name, int age) {
        this.setId(id);
        this.setName(name);
        this.setAge(age);
        this.enrolledCourses = new ArrayList<>();
        this.gpa = 0.0;
    }
    
    // Getters
    public String getId() {
        return id;
    }
    
    public String getName() {
        return name;
    }
    
    public int getAge() {
        return age;
    }
    
    public List<Course> getEnrolledCourses() {
        // Return a copy to prevent external modification
        return new ArrayList<>(enrolledCourses);
    }
    
    public double getGpa() {
        return gpa;
    }
    
    // Setters
    private void setId(String id) {
        if (id == null || id.isEmpty()) {
            throw new IllegalArgumentException("Student ID cannot be empty");
        }
        this.id = id;
    }
    
    public void setName(String name) {
        if (name == null || name.isEmpty()) {
            throw new IllegalArgumentException("Name cannot be empty");
        }
        this.name = name;
    }
    
    public void setAge(int age) {
        if (age < 16 || age > 100) {
            throw new IllegalArgumentException("Age must be between 16 and 100");
        }
        this.age = age;
    }
    
    // Business methods
    public void enrollCourse(Course course) {
        if (course == null) {
            throw new IllegalArgumentException("Course cannot be null");
        }
        
        if (!enrolledCourses.contains(course)) {
            enrolledCourses.add(course);
        }
    }
    
    public void dropCourse(Course course) {
        enrolledCourses.remove(course);
    }
    
    public void calculateGpa() {
        if (enrolledCourses.isEmpty()) {
            this.gpa = 0.0;
            return;
        }
        
        double totalPoints = 0.0;
        int totalCredits = 0;
        
        for (Course course : enrolledCourses) {
            totalPoints += course.getGrade() * course.getCredits();
            totalCredits += course.getCredits();
        }
        
        this.gpa = totalPoints / totalCredits;
    }
}

public class Course {
    private String code;
    private String name;
    private int credits;
    private double grade;
    
    // Constructor, getters, and setters
}
```

## Immutable Classes

Immutable classes are a special case of encapsulation where objects cannot be modified after creation. They are thread-safe and easier to reason about.

```java
public final class ImmutablePerson {
    private final String name;
    private final int age;
    private final String address;
    
    public ImmutablePerson(String name, int age, String address) {
        this.name = name;
        this.age = age;
        this.address = address;
    }
    
    // Only getters, no setters
    public String getName() {
        return name;
    }
    
    public int getAge() {
        return age;
    }
    
    public String getAddress() {
        return address;
    }
    
    // To "modify" an immutable object, create a new one
    public ImmutablePerson withName(String newName) {
        return new ImmutablePerson(newName, this.age, this.address);
    }
    
    public ImmutablePerson withAge(int newAge) {
        return new ImmutablePerson(this.name, newAge, this.address);
    }
    
    public ImmutablePerson withAddress(String newAddress) {
        return new ImmutablePerson(this.name, this.age, newAddress);
    }
}
```

## Best Practices

1. **Make instance variables private**: Always declare instance variables as private to restrict direct access
2. **Provide public getters and setters**: Use public methods to access and modify private variables
3. **Validate input in setters**: Add validation logic in setter methods to ensure data integrity
4. **Use appropriate access modifiers**: Choose the most restrictive access modifier that still allows the necessary access
5. **Consider immutability**: Make classes immutable when appropriate
6. **Return copies of mutable objects**: When returning mutable objects from getters, return copies to prevent external modification
7. **Use defensive copying in constructors**: When accepting mutable objects in constructors, create defensive copies
8. **Document the class contract**: Clearly document the expected behavior of your encapsulated classes

```java
// Example of defensive copying
public class SafeArrayList {
    private final List<String> items;
    
    public SafeArrayList(List<String> initialItems) {
        // Defensive copy in constructor
        this.items = new ArrayList<>(initialItems);
    }
    
    public List<String> getItems() {
        // Return a copy to prevent modification
        return new ArrayList<>(items);
    }
    
    public void addItem(String item) {
        items.add(item);
    }
}
```