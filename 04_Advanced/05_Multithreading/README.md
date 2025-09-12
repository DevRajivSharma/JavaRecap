# Multithreading in Java

Multithreading is a core feature of Java that allows concurrent execution of two or more parts of a program for maximum utilization of CPU. This document covers the fundamentals of multithreading in Java, including thread creation, synchronization, and best practices.

## Introduction to Threads

A thread is the smallest unit of processing that can be scheduled by an operating system. Java provides built-in support for multithreaded programming.

### Thread Lifecycle

A thread in Java goes through various states during its lifecycle:

1. **New**: Thread is created but not yet started
2. **Runnable**: Thread is ready to run and waiting for CPU allocation
3. **Blocked**: Thread is temporarily inactive (waiting for a monitor lock)
4. **Waiting**: Thread is temporarily inactive (waiting for another thread)
5. **Timed Waiting**: Thread is temporarily inactive (waiting for a specified time)
6. **Terminated**: Thread has completed execution

```java
// Diagram of thread lifecycle
/*
    New ──────────> Runnable <───────────────┐
     │                 │ ↑                   │
     │                 │ │                   │
     │                 ↓ │                   │
     │               Running ────────────────┤
     │                 │                     │
     │                 │                     │
     │                 ↓                     │
     └───> Blocked/Waiting/Timed Waiting     │
                       │                     │
                       │                     │
                       ↓                     │
                   Terminated <──────────────┘
*/
```

## Creating Threads

There are two primary ways to create a thread in Java:

### 1. Extending the Thread Class

```java
public class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread is running: " + Thread.currentThread().getName());
        // Thread logic here
        for (int i = 0; i < 5; i++) {
            System.out.println(i);
            try {
                Thread.sleep(500);  // Pause for 500 milliseconds
            } catch (InterruptedException e) {
                System.out.println("Thread interrupted");
            }
        }
    }
}

// Usage
public class ThreadExample {
    public static void main(String[] args) {
        MyThread thread1 = new MyThread();
        thread1.setName("MyThread-1");
        thread1.start();  // Start the thread
        
        // Creating and starting another thread
        MyThread thread2 = new MyThread();
        thread2.setName("MyThread-2");
        thread2.start();
        
        System.out.println("Main thread continues execution");
    }
}
```

### 2. Implementing the Runnable Interface

```java
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("Thread is running: " + Thread.currentThread().getName());
        // Thread logic here
        for (int i = 0; i < 5; i++) {
            System.out.println(i);
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                System.out.println("Thread interrupted");
            }
        }
    }
}

// Usage
public class RunnableExample {
    public static void main(String[] args) {
        MyRunnable runnable = new MyRunnable();
        
        Thread thread1 = new Thread(runnable);
        thread1.setName("MyRunnable-1");
        thread1.start();
        
        Thread thread2 = new Thread(runnable);
        thread2.setName("MyRunnable-2");
        thread2.start();
        
        System.out.println("Main thread continues execution");
    }
}
```

### 3. Using Lambda Expressions (Java 8+)

```java
public class LambdaThreadExample {
    public static void main(String[] args) {
        // Creating a thread using lambda expression
        Thread thread1 = new Thread(() -> {
            System.out.println("Thread is running: " + Thread.currentThread().getName());
            for (int i = 0; i < 5; i++) {
                System.out.println(i);
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    System.out.println("Thread interrupted");
                }
            }
        });
        
        thread1.setName("Lambda-Thread");
        thread1.start();
        
        System.out.println("Main thread continues execution");
    }
}
```

## Thread Methods

The `Thread` class provides several important methods:

```java
public class ThreadMethodsExample {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                System.out.println("Thread running: " + i);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    System.out.println("Thread interrupted");
                    return;  // Exit the thread
                }
            }
        });
        
        // Setting thread name
        thread.setName("DemoThread");
        
        // Setting thread priority (1-10, default is 5)
        thread.setPriority(Thread.MAX_PRIORITY);  // 10
        
        // Start the thread
        thread.start();
        
        // Check if thread is alive
        System.out.println("Is thread alive? " + thread.isAlive());
        
        // Get thread ID
        System.out.println("Thread ID: " + thread.getId());
        
        // Get thread name
        System.out.println("Thread name: " + thread.getName());
        
        // Get thread priority
        System.out.println("Thread priority: " + thread.getPriority());
        
        // Get thread state
        System.out.println("Thread state: " + thread.getState());
        
        // Wait for 2 seconds
        Thread.sleep(2000);
        
        // Interrupt the thread
        thread.interrupt();
        
        // Wait for thread to complete
        thread.join();
        
        System.out.println("Thread state after completion: " + thread.getState());
    }
}
```

## Thread Synchronization

When multiple threads access shared resources, synchronization is necessary to prevent race conditions and ensure data consistency.

### Synchronized Methods

```java
public class Counter {
    private int count = 0;
    
    // Synchronized method
    public synchronized void increment() {
        count++;
    }
    
    public synchronized int getCount() {
        return count;
    }
}

public class SynchronizedMethodExample {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        // Create two threads that increment the counter
        Thread thread1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        Thread thread2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        thread1.start();
        thread2.start();
        
        // Wait for both threads to complete
        thread1.join();
        thread2.join();
        
        System.out.println("Final count: " + counter.getCount());
        // Output will be 2000 because the increment method is synchronized
    }
}
```

### Synchronized Blocks

```java
public class BlockCounter {
    private int count = 0;
    private final Object lock = new Object();  // Lock object
    
    public void increment() {
        // Synchronized block
        synchronized (lock) {
            count++;
        }
    }
    
    public int getCount() {
        synchronized (lock) {
            return count;
        }
    }
}

public class SynchronizedBlockExample {
    public static void main(String[] args) throws InterruptedException {
        BlockCounter counter = new BlockCounter();
        
        Thread thread1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        Thread thread2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        thread1.start();
        thread2.start();
        
        thread1.join();
        thread2.join();
        
        System.out.println("Final count: " + counter.getCount());
    }
}
```

### Volatile Keyword

The `volatile` keyword ensures that a variable is always read from and written to main memory, not from thread-local caches.

```java
public class VolatileExample {
    private static volatile boolean flag = false;
    
    public static void main(String[] args) throws InterruptedException {
        Thread writerThread = new Thread(() -> {
            try {
                Thread.sleep(1000);
                flag = true;
                System.out.println("Flag set to true");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        
        Thread readerThread = new Thread(() -> {
            while (!flag) {
                // Busy-wait until flag becomes true
            }
            System.out.println("Flag is now true");
        });
        
        readerThread.start();
        writerThread.start();
        
        writerThread.join();
        readerThread.join();
    }
}
```

## Thread Communication

Threads can communicate with each other using `wait()`, `notify()`, and `notifyAll()` methods.

```java
public class MessageQueue {
    private String message;
    private boolean empty = true;
    
    public synchronized String receive() {
        // Wait until a message is available
        while (empty) {
            try {
                wait();  // Release lock and wait
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
        
        // Message received, mark as empty
        empty = true;
        notifyAll();  // Notify waiting threads
        return message;
    }
    
    public synchronized void send(String message) {
        // Wait until message has been received
        while (!empty) {
            try {
                wait();  // Release lock and wait
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
        
        // Store message
        empty = false;
        this.message = message;
        notifyAll();  // Notify waiting threads
    }
}

public class WaitNotifyExample {
    public static void main(String[] args) {
        MessageQueue queue = new MessageQueue();
        
        Thread producer = new Thread(() -> {
            String[] messages = {"Hello", "World", "How", "Are", "You"};
            for (String msg : messages) {
                queue.send(msg);
                System.out.println("Sent: " + msg);
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
            queue.send("DONE");  // Signal end of messages
        });
        
        Thread consumer = new Thread(() -> {
            String message;
            do {
                message = queue.receive();
                System.out.println("Received: " + message);
            } while (!"DONE".equals(message));
        });
        
        consumer.start();
        producer.start();
    }
}
```

## Thread Pools

Thread pools manage a pool of worker threads, reducing the overhead of thread creation.

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class ThreadPoolExample {
    public static void main(String[] args) {
        // Create a fixed thread pool with 5 threads
        ExecutorService executor = Executors.newFixedThreadPool(5);
        
        // Submit 10 tasks to the thread pool
        for (int i = 0; i < 10; i++) {
            final int taskId = i;
            executor.submit(() -> {
                System.out.println("Task " + taskId + " executed by " + 
                                   Thread.currentThread().getName());
                try {
                    // Simulate work
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
                return null;
            });
        }
        
        // Shutdown the executor
        executor.shutdown();
        try {
            // Wait for all tasks to complete or timeout after 60 seconds
            if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
                executor.shutdownNow();  // Force shutdown if tasks don't complete
            }
        } catch (InterruptedException e) {
            executor.shutdownNow();
            Thread.currentThread().interrupt();
        }
        
        System.out.println("All tasks completed");
    }
}
```

### Types of Thread Pools

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;

public class ThreadPoolTypesExample {
    public static void main(String[] args) {
        // 1. Fixed Thread Pool
        ExecutorService fixedPool = Executors.newFixedThreadPool(5);
        System.out.println("Fixed Thread Pool created");
        
        // 2. Cached Thread Pool
        ExecutorService cachedPool = Executors.newCachedThreadPool();
        System.out.println("Cached Thread Pool created");
        
        // 3. Single Thread Executor
        ExecutorService singleThreadExecutor = Executors.newSingleThreadExecutor();
        System.out.println("Single Thread Executor created");
        
        // 4. Scheduled Thread Pool
        ScheduledExecutorService scheduledPool = Executors.newScheduledThreadPool(5);
        System.out.println("Scheduled Thread Pool created");
        
        // Example of scheduled execution
        scheduledPool.schedule(() -> {
            System.out.println("Task executed after delay");
        }, 2, TimeUnit.SECONDS);  // Execute after 2 seconds delay
        
        scheduledPool.scheduleAtFixedRate(() -> {
            System.out.println("Task executed at fixed rate");
        }, 1, 3, TimeUnit.SECONDS);  // Initial delay 1s, then every 3s
        
        // Let the scheduled tasks run for a while
        try {
            Thread.sleep(10000);  // 10 seconds
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        
        // Shutdown all executors
        fixedPool.shutdown();
        cachedPool.shutdown();
        singleThreadExecutor.shutdown();
        scheduledPool.shutdown();
    }
}
```

## Concurrent Collections

Java provides thread-safe collections in the `java.util.concurrent` package.

```java
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.CopyOnWriteArrayList;

public class ConcurrentCollectionsExample {
    public static void main(String[] args) throws InterruptedException {
        // Regular HashMap (not thread-safe)
        Map<String, Integer> regularMap = new HashMap<>();
        
        // ConcurrentHashMap (thread-safe)
        Map<String, Integer> concurrentMap = new ConcurrentHashMap<>();
        
        // CopyOnWriteArrayList (thread-safe)
        CopyOnWriteArrayList<String> copyOnWriteList = new CopyOnWriteArrayList<>();
        
        // Thread to add elements to maps
        Thread writerThread = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                regularMap.put("Key" + i, i);  // May cause ConcurrentModificationException
                concurrentMap.put("Key" + i, i);  // Thread-safe
                copyOnWriteList.add("Item" + i);  // Thread-safe
            }
        });
        
        // Thread to read elements from maps
        Thread readerThread = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                try {
                    // May cause ConcurrentModificationException
                    for (String key : regularMap.keySet()) {
                        Integer value = regularMap.get(key);
                        if (value != null) {
                            // Process value
                        }
                    }
                    
                    // Thread-safe iteration
                    for (String key : concurrentMap.keySet()) {
                        Integer value = concurrentMap.get(key);
                        if (value != null) {
                            // Process value
                        }
                    }
                    
                    // Thread-safe iteration
                    for (String item : copyOnWriteList) {
                        // Process item
                    }
                } catch (Exception e) {
                    System.out.println("Exception: " + e.getMessage());
                }
            }
        });
        
        writerThread.start();
        readerThread.start();
        
        writerThread.join();
        readerThread.join();
        
        System.out.println("Regular Map size: " + regularMap.size());
        System.out.println("Concurrent Map size: " + concurrentMap.size());
        System.out.println("CopyOnWrite List size: " + copyOnWriteList.size());
    }
}
```

## Atomic Variables

Atomic variables provide thread-safe operations without using locks.

```java
import java.util.concurrent.atomic.AtomicInteger;

public class AtomicVariablesExample {
    public static void main(String[] args) throws InterruptedException {
        // Regular counter (not thread-safe)
        Counter regularCounter = new Counter();
        
        // Atomic counter (thread-safe)
        AtomicCounter atomicCounter = new AtomicCounter();
        
        // Create threads to increment counters
        Thread thread1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                regularCounter.increment();
                atomicCounter.increment();
            }
        });
        
        Thread thread2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                regularCounter.increment();
                atomicCounter.increment();
            }
        });
        
        thread1.start();
        thread2.start();
        
        thread1.join();
        thread2.join();
        
        System.out.println("Regular Counter: " + regularCounter.getCount());
        System.out.println("Atomic Counter: " + atomicCounter.getCount());
    }
    
    // Regular counter class
    static class Counter {
        private int count = 0;
        
        public void increment() {
            count++;  // Not thread-safe
        }
        
        public int getCount() {
            return count;
        }
    }
    
    // Atomic counter class
    static class AtomicCounter {
        private AtomicInteger count = new AtomicInteger(0);
        
        public void increment() {
            count.incrementAndGet();  // Thread-safe
        }
        
        public int getCount() {
            return count.get();
        }
    }
}
```

## Locks

Java provides more flexible locking mechanisms in the `java.util.concurrent.locks` package.

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class LocksExample {
    public static void main(String[] args) throws InterruptedException {
        // Example with ReentrantLock
        Counter counter = new Counter();
        
        Thread thread1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        Thread thread2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });
        
        thread1.start();
        thread2.start();
        
        thread1.join();
        thread2.join();
        
        System.out.println("Counter value: " + counter.getCount());
        
        // Example with ReadWriteLock
        SharedData data = new SharedData();
        
        Thread writerThread = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                data.write(i);
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        });
        
        Thread readerThread1 = new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                System.out.println("Reader 1: " + data.read());
                try {
                    Thread.sleep(50);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        });
        
        Thread readerThread2 = new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                System.out.println("Reader 2: " + data.read());
                try {
                    Thread.sleep(50);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        });
        
        writerThread.start();
        readerThread1.start();
        readerThread2.start();
        
        writerThread.join();
        readerThread1.join();
        readerThread2.join();
    }
    
    // Counter using ReentrantLock
    static class Counter {
        private int count = 0;
        private final Lock lock = new ReentrantLock();
        
        public void increment() {
            lock.lock();  // Acquire lock
            try {
                count++;
            } finally {
                lock.unlock();  // Release lock in finally block
            }
        }
        
        public int getCount() {
            lock.lock();
            try {
                return count;
            } finally {
                lock.unlock();
            }
        }
    }
    
    // Shared data using ReadWriteLock
    static class SharedData {
        private int data = 0;
        private final ReadWriteLock lock = new ReentrantReadWriteLock();
        private final Lock readLock = lock.readLock();
        private final Lock writeLock = lock.writeLock();
        
        public int read() {
            readLock.lock();  // Acquire read lock
            try {
                System.out.println(Thread.currentThread().getName() + 
                                   " is reading data: " + data);
                return data;
            } finally {
                readLock.unlock();  // Release read lock
            }
        }
        
        public void write(int newData) {
            writeLock.lock();  // Acquire write lock
            try {
                System.out.println(Thread.currentThread().getName() + 
                                   " is writing data: " + newData);
                data = newData;
            } finally {
                writeLock.unlock();  // Release write lock
            }
        }
    }
}
```

## CompletableFuture (Java 8+)

`CompletableFuture` provides a way to perform asynchronous computations.

```java
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeUnit;

public class CompletableFutureExample {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // Create a CompletableFuture
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                throw new IllegalStateException(e);
            }
            return "Hello";
        });
        
        // Chain transformations
        CompletableFuture<String> transformedFuture = future
            .thenApply(s -> s + " World")  // Transform the result
            .thenApply(String::toUpperCase);  // Transform again
        
        // Block and get the result
        System.out.println(transformedFuture.get());  // HELLO WORLD
        
        // Combine two futures
        CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> {
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                throw new IllegalStateException(e);
            }
            return "Hello";
        });
        
        CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> {
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                throw new IllegalStateException(e);
            }
            return "World";
        });
        
        CompletableFuture<String> combinedFuture = future1.thenCombine(
            future2, (s1, s2) -> s1 + " " + s2);
        
        System.out.println(combinedFuture.get());  // Hello World
        
        // Handle exceptions
        CompletableFuture<String> errorFuture = CompletableFuture.supplyAsync(() -> {
            if (true) throw new RuntimeException("Computation error!");
            return "Result";
        }).exceptionally(ex -> "Error: " + ex.getMessage());
        
        System.out.println(errorFuture.get());  // Error: java.lang.RuntimeException: Computation error!
        
        // Execute tasks in parallel and wait for all to complete
        CompletableFuture<String> future3 = CompletableFuture.supplyAsync(() -> {
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                throw new IllegalStateException(e);
            }
            return "Task 1";
        });
        
        CompletableFuture<String> future4 = CompletableFuture.supplyAsync(() -> {
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                throw new IllegalStateException(e);
            }
            return "Task 2";
        });
        
        CompletableFuture<Void> allFutures = CompletableFuture.allOf(future3, future4);
        
        // Wait for all futures to complete
        allFutures.get();
        
        // Results are available in the individual futures
        System.out.println(future3.get());  // Task 1
        System.out.println(future4.get());  // Task 2
    }
}
```

## Thread-Local Variables

Thread-local variables provide thread-confined variables.

```java
public class ThreadLocalExample {
    // Thread-local variable
    private static final ThreadLocal<Integer> threadId = new ThreadLocal<Integer>() {
        @Override
        protected Integer initialValue() {
            return 0;  // Default value
        }
    };
    
    // Thread-local variable with lambda initializer (Java 8+)
    private static final ThreadLocal<String> threadName = 
        ThreadLocal.withInitial(() -> Thread.currentThread().getName());
    
    public static void main(String[] args) {
        // Create and start three threads
        for (int i = 1; i <= 3; i++) {
            final int id = i;
            Thread thread = new Thread(() -> {
                // Set thread-local value
                threadId.set(id);
                
                // Access thread-local values
                System.out.println("Thread ID: " + threadId.get() + 
                                   ", Thread Name: " + threadName.get());
                
                // Simulate some processing
                process();
                
                // Clean up thread-local variables
                threadId.remove();
                threadName.remove();
            });
            
            thread.start();
        }
    }
    
    private static void process() {
        // Access thread-local values in another method
        System.out.println("Processing in thread " + threadId.get() + 
                           " (" + threadName.get() + ")");
    }
}
```

## Deadlocks

Deadlocks occur when two or more threads are blocked forever, waiting for each other.

```java
public class DeadlockExample {
    private static final Object lock1 = new Object();
    private static final Object lock2 = new Object();
    
    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> {
            synchronized (lock1) {
                System.out.println("Thread 1: Holding lock 1...");
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
                System.out.println("Thread 1: Waiting for lock 2...");
                synchronized (lock2) {
                    System.out.println("Thread 1: Holding lock 1 & 2...");
                }
            }
        });
        
        Thread thread2 = new Thread(() -> {
            synchronized (lock2) {
                System.out.println("Thread 2: Holding lock 2...");
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
                System.out.println("Thread 2: Waiting for lock 1...");
                synchronized (lock1) {
                    System.out.println("Thread 2: Holding lock 2 & 1...");
                }
            }
        });
        
        thread1.start();
        thread2.start();
    }
}
```

### Avoiding Deadlocks

```java
public class DeadlockAvoidanceExample {
    private static final Object lock1 = new Object();
    private static final Object lock2 = new Object();
    
    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> {
            // Always acquire locks in the same order
            synchronized (lock1) {
                System.out.println("Thread 1: Holding lock 1...");
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
                System.out.println("Thread 1: Waiting for lock 2...");
                synchronized (lock2) {
                    System.out.println("Thread 1: Holding lock 1 & 2...");
                }
            }
        });
        
        Thread thread2 = new Thread(() -> {
            // Acquire locks in the same order as thread1
            synchronized (lock1) {
                System.out.println("Thread 2: Holding lock 1...");
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
                System.out.println("Thread 2: Waiting for lock 2...");
                synchronized (lock2) {
                    System.out.println("Thread 2: Holding lock 1 & 2...");
                }
            }
        });
        
        thread1.start();
        thread2.start();
    }
}
```

## Best Practices

1. **Prefer higher-level concurrency utilities**: Use `java.util.concurrent` utilities instead of low-level `synchronized`, `wait()`, and `notify()`.

2. **Minimize lock scope**: Keep synchronized blocks as small as possible.

3. **Avoid blocking operations in synchronized blocks**: Don't perform I/O or long-running operations while holding locks.

4. **Use thread pools**: Reuse threads with thread pools instead of creating new threads.

5. **Prefer immutable objects**: Immutable objects are inherently thread-safe.

6. **Use atomic variables**: For simple counters and flags, use atomic variables instead of locks.

7. **Avoid deadlocks**: Always acquire locks in a consistent order.

8. **Document thread safety**: Clearly document the thread safety guarantees of your classes.

9. **Use thread-safe collections**: Use concurrent collections for multi-threaded access.

10. **Clean up resources**: Always clean up thread resources (e.g., with `ExecutorService.shutdown()`).

```java
// Example of good practices
public class ThreadingBestPractices {
    // Immutable class
    public static final class ImmutablePoint {
        private final int x;
        private final int y;
        
        public ImmutablePoint(int x, int y) {
            this.x = x;
            this.y = y;
        }
        
        public int getX() { return x; }
        public int getY() { return y; }
        
        // No setters - immutable
    }
    
    // Thread-safe with minimal synchronization
    public static class ThreadSafeCounter {
        private final AtomicInteger count = new AtomicInteger(0);
        
        public int increment() {
            return count.incrementAndGet();
        }
        
        public int getCount() {
            return count.get();
        }
    }
    
    // Using concurrent collections
    public static class ThreadSafeCache {
        private final ConcurrentHashMap<String, Object> cache = new ConcurrentHashMap<>();
        
        public Object get(String key) {
            return cache.get(key);
        }
        
        public void put(String key, Object value) {
            cache.put(key, value);
        }
    }
    
    // Proper resource management
    public static void processItems(List<String> items) {
        ExecutorService executor = Executors.newFixedThreadPool(5);
        try {
            for (String item : items) {
                executor.submit(() -> processItem(item));
            }
        } finally {
            executor.shutdown();
            try {
                if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
                    executor.shutdownNow();
                }
            } catch (InterruptedException e) {
                executor.shutdownNow();
                Thread.currentThread().interrupt();
            }
        }
    }
    
    private static void processItem(String item) {
        // Process the item
        System.out.println("Processing: " + item);
    }
}
```

## Conclusion

Multithreading in Java is a powerful feature that allows for concurrent execution of code, improving performance and responsiveness. Java provides a rich set of tools for thread creation, synchronization, and communication, from low-level constructs like `synchronized` and `wait()`/`notify()` to high-level utilities in the `java.util.concurrent` package.

By understanding these concepts and following best practices, you can write efficient, thread-safe, and maintainable concurrent code in Java.