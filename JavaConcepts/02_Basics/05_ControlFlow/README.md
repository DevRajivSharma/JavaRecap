# Control Flow in Java

Control flow statements in Java determine the order in which statements are executed in a program. They allow you to make decisions, repeat actions, and branch to different parts of code based on conditions.

## Conditional Statements

### 1. if Statement

The `if` statement executes a block of code if a specified condition is true.

```java
int age = 18;

if (age >= 18) {
    System.out.println("You are an adult");
}
```

### 2. if-else Statement

The `if-else` statement executes one block of code if a condition is true and another block if the condition is false.

```java
int age = 16;

if (age >= 18) {
    System.out.println("You are an adult");
} else {
    System.out.println("You are a minor");
}
```

### 3. if-else-if Statement

The `if-else-if` statement allows you to check multiple conditions in sequence.

```java
int score = 85;

if (score >= 90) {
    System.out.println("Grade: A");
} else if (score >= 80) {
    System.out.println("Grade: B");
} else if (score >= 70) {
    System.out.println("Grade: C");
} else if (score >= 60) {
    System.out.println("Grade: D");
} else {
    System.out.println("Grade: F");
}
```

### 4. Nested if Statements

You can place `if` statements inside other `if` statements to create nested conditions.

```java
int age = 25;
boolean hasLicense = true;

if (age >= 18) {
    if (hasLicense) {
        System.out.println("You can drive");
    } else {
        System.out.println("You need to get a license");
    }
} else {
    System.out.println("You are too young to drive");
}
```

### 5. switch Statement

The `switch` statement selects one of many code blocks to be executed based on the value of an expression.

```java
int day = 3;
String dayName;

switch (day) {
    case 1:
        dayName = "Monday";
        break;
    case 2:
        dayName = "Tuesday";
        break;
    case 3:
        dayName = "Wednesday";
        break;
    case 4:
        dayName = "Thursday";
        break;
    case 5:
        dayName = "Friday";
        break;
    case 6:
        dayName = "Saturday";
        break;
    case 7:
        dayName = "Sunday";
        break;
    default:
        dayName = "Invalid day";
}

System.out.println(dayName);  // Output: Wednesday
```

The `break` statement is used to exit the switch block. Without it, execution would continue to the next case.

#### Enhanced switch (Java 12+)

Java 12 introduced an enhanced `switch` statement with a more concise syntax and the ability to return values.

```java
// Switch expression (Java 12+)
int day = 3;
String dayName = switch (day) {
    case 1 -> "Monday";
    case 2 -> "Tuesday";
    case 3 -> "Wednesday";
    case 4 -> "Thursday";
    case 5 -> "Friday";
    case 6 -> "Saturday";
    case 7 -> "Sunday";
    default -> "Invalid day";
};

System.out.println(dayName);  // Output: Wednesday
```

#### Multiple case labels

You can handle multiple cases with the same code block.

```java
int month = 8;
String season;

switch (month) {
    case 12:
    case 1:
    case 2:
        season = "Winter";
        break;
    case 3:
    case 4:
    case 5:
        season = "Spring";
        break;
    case 6:
    case 7:
    case 8:
        season = "Summer";
        break;
    case 9:
    case 10:
    case 11:
        season = "Fall";
        break;
    default:
        season = "Invalid month";
}

System.out.println(season);  // Output: Summer
```

With enhanced switch (Java 12+):

```java
int month = 8;
String season = switch (month) {
    case 12, 1, 2 -> "Winter";
    case 3, 4, 5 -> "Spring";
    case 6, 7, 8 -> "Summer";
    case 9, 10, 11 -> "Fall";
    default -> "Invalid month";
};

System.out.println(season);  // Output: Summer
```

## Looping Statements

### 1. for Loop

The `for` loop executes a block of code a specified number of times.

```java
// Basic for loop
for (int i = 0; i < 5; i++) {
    System.out.println("Count: " + i);
}

// Output:
// Count: 0
// Count: 1
// Count: 2
// Count: 3
// Count: 4
```

The `for` loop has three parts:
- Initialization: Executed once before the loop starts
- Condition: Evaluated before each iteration
- Increment/Decrement: Executed after each iteration

#### Variations of for loop

```java
// Multiple variables
for (int i = 0, j = 10; i < j; i++, j--) {
    System.out.println("i = " + i + ", j = " + j);
}

// Infinite loop
for (;;) {
    // Code to be executed indefinitely
    // Use break to exit the loop
    break;
}
```

### 2. Enhanced for Loop (for-each)

The enhanced `for` loop simplifies iterating over arrays and collections.

```java
// Array iteration
int[] numbers = {1, 2, 3, 4, 5};

for (int number : numbers) {
    System.out.println(number);
}

// Collection iteration
List<String> fruits = Arrays.asList("Apple", "Banana", "Orange");

for (String fruit : fruits) {
    System.out.println(fruit);
}
```

### 3. while Loop

The `while` loop executes a block of code as long as a specified condition is true.

```java
int count = 0;

while (count < 5) {
    System.out.println("Count: " + count);
    count++;
}

// Output:
// Count: 0
// Count: 1
// Count: 2
// Count: 3
// Count: 4
```

### 4. do-while Loop

The `do-while` loop is similar to the `while` loop, but it executes the code block at least once before checking the condition.

```java
int count = 0;

do {
    System.out.println("Count: " + count);
    count++;
} while (count < 5);

// Output:
// Count: 0
// Count: 1
// Count: 2
// Count: 3
// Count: 4
```

Even if the condition is initially false, the code block is executed at least once:

```java
int count = 10;

do {
    System.out.println("Count: " + count);
    count++;
} while (count < 5);

// Output:
// Count: 10
```

## Jump Statements

### 1. break Statement

The `break` statement terminates the loop or switch statement and transfers execution to the statement immediately following the loop or switch.

```java
// Breaking out of a loop
for (int i = 0; i < 10; i++) {
    if (i == 5) {
        break;  // Exit the loop when i equals 5
    }
    System.out.println("Count: " + i);
}

// Output:
// Count: 0
// Count: 1
// Count: 2
// Count: 3
// Count: 4
```

### 2. continue Statement

The `continue` statement skips the current iteration of a loop and continues with the next iteration.

```java
// Skipping even numbers
for (int i = 0; i < 10; i++) {
    if (i % 2 == 0) {
        continue;  // Skip even numbers
    }
    System.out.println("Count: " + i);
}

// Output:
// Count: 1
// Count: 3
// Count: 5
// Count: 7
// Count: 9
```

### 3. return Statement

The `return` statement exits from the current method and optionally returns a value.

```java
public int findFirstEven(int[] numbers) {
    for (int number : numbers) {
        if (number % 2 == 0) {
            return number;  // Exit the method and return the first even number
        }
    }
    return -1;  // Return -1 if no even number is found
}
```

### 4. Labeled Statements

Labels can be used with `break` and `continue` statements to control which loop to break or continue.

```java
// Breaking out of nested loops
outer: for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        if (i == 1 && j == 1) {
            break outer;  // Break out of both loops
        }
        System.out.println("i = " + i + ", j = " + j);
    }
}

// Output:
// i = 0, j = 0
// i = 0, j = 1
// i = 0, j = 2
// i = 1, j = 0
```

```java
// Continuing outer loop
outer: for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        if (j == 1) {
            continue outer;  // Skip to the next iteration of the outer loop
        }
        System.out.println("i = " + i + ", j = " + j);
    }
}

// Output:
// i = 0, j = 0
// i = 1, j = 0
// i = 2, j = 0
```

## Exception Handling Control Flow

Exception handling also affects control flow in Java programs.

### try-catch-finally

```java
try {
    // Code that might throw an exception
    int result = 10 / 0;  // ArithmeticException
    System.out.println("This line won't be executed");
} catch (ArithmeticException e) {
    // Code to handle the exception
    System.out.println("Cannot divide by zero");
} finally {
    // Code that always executes, regardless of whether an exception occurred
    System.out.println("Finally block executed");
}

System.out.println("Program continues");

// Output:
// Cannot divide by zero
// Finally block executed
// Program continues
```

### try-with-resources (Java 7+)

```java
try (FileReader reader = new FileReader("file.txt");
     BufferedReader bufferedReader = new BufferedReader(reader)) {
    // Code that uses the resources
    String line = bufferedReader.readLine();
    System.out.println(line);
} catch (IOException e) {
    // Handle exceptions
    System.out.println("Error reading file: " + e.getMessage());
}
// Resources are automatically closed
```

## Best Practices

1. **Keep control structures simple**: Avoid deeply nested conditions and loops
2. **Use braces consistently**: Always use braces for blocks, even for single statements
3. **Choose the right loop**: Use the appropriate loop for each situation
4. **Avoid infinite loops**: Ensure that loop conditions will eventually become false
5. **Use meaningful variable names**: Choose descriptive names for loop counters and condition variables
6. **Limit the scope of variables**: Declare variables in the smallest scope possible
7. **Consider using enhanced switch**: Use the enhanced switch syntax for cleaner code (Java 12+)
8. **Prefer enhanced for loops**: Use enhanced for loops when iterating over collections or arrays
9. **Use break and continue judiciously**: Overuse can make code harder to understand
10. **Avoid labeled breaks and continues**: They can make code harder to follow

```java
// Good practices example
public class ControlFlowExample {
    public void processOrders(List<Order> orders) {
        // Use enhanced for loop for collections
        for (Order order : orders) {
            // Use early return for validation
            if (!isValidOrder(order)) {
                continue;
            }
            
            // Use switch expression for cleaner code (Java 12+)
            String status = switch (order.getStatus()) {
                case PENDING -> processNewOrder(order);
                case SHIPPED -> trackShipment(order);
                case DELIVERED -> completeOrder(order);
                case CANCELLED -> handleCancellation(order);
                default -> "Unknown status";
            };
            
            System.out.println("Order " + order.getId() + ": " + status);
        }
    }
    
    private boolean isValidOrder(Order order) {
        // Validation logic
        return order != null && order.getId() > 0;
    }
    
    private String processNewOrder(Order order) {
        // Processing logic
        return "Processed";
    }
    
    private String trackShipment(Order order) {
        // Tracking logic
        return "Tracked";
    }
    
    private String completeOrder(Order order) {
        // Completion logic
        return "Completed";
    }
    
    private String handleCancellation(Order order) {
        // Cancellation logic
        return "Cancelled";
    }
    
    // Example Order class
    private static class Order {
        private int id;
        private Status status;
        
        public int getId() { return id; }
        public Status getStatus() { return status; }
        
        enum Status { PENDING, SHIPPED, DELIVERED, CANCELLED }
    }
}
```