# File I/O in Java

Java provides comprehensive support for file input and output operations through various classes and APIs. This document covers the different approaches to file I/O in Java, from the traditional `java.io` package to the modern `java.nio.file` API introduced in Java 7.

## Java I/O Streams

The `java.io` package provides stream-based I/O, where data is handled as a continuous flow of bytes or characters.

### Byte Streams

Byte streams handle I/O of raw binary data.

#### FileInputStream and FileOutputStream

```java
// Reading bytes from a file
try (FileInputStream fis = new FileInputStream("input.txt")) {
    byte[] buffer = new byte[1024];
    int bytesRead;
    while ((bytesRead = fis.read(buffer)) != -1) {
        // Process bytes in buffer
        System.out.println("Read " + bytesRead + " bytes");
    }
} catch (IOException e) {
    e.printStackTrace();
}

// Writing bytes to a file
try (FileOutputStream fos = new FileOutputStream("output.txt")) {
    String data = "Hello, World!";
    byte[] bytes = data.getBytes();
    fos.write(bytes);
} catch (IOException e) {
    e.printStackTrace();
}
```

#### BufferedInputStream and BufferedOutputStream

Buffered streams add a layer of buffering for improved performance.

```java
// Reading with buffering
try (BufferedInputStream bis = new BufferedInputStream(
        new FileInputStream("input.txt"))) {
    byte[] buffer = new byte[1024];
    int bytesRead;
    while ((bytesRead = bis.read(buffer)) != -1) {
        // Process bytes in buffer
    }
} catch (IOException e) {
    e.printStackTrace();
}

// Writing with buffering
try (BufferedOutputStream bos = new BufferedOutputStream(
        new FileOutputStream("output.txt"))) {
    String data = "Hello, World!";
    byte[] bytes = data.getBytes();
    bos.write(bytes);
} catch (IOException e) {
    e.printStackTrace();
}
```

### Character Streams

Character streams handle I/O of character data, automatically handling character encoding.

#### FileReader and FileWriter

```java
// Reading characters from a file
try (FileReader reader = new FileReader("input.txt")) {
    char[] buffer = new char[1024];
    int charsRead;
    while ((charsRead = reader.read(buffer)) != -1) {
        // Process characters in buffer
        System.out.println(new String(buffer, 0, charsRead));
    }
} catch (IOException e) {
    e.printStackTrace();
}

// Writing characters to a file
try (FileWriter writer = new FileWriter("output.txt")) {
    writer.write("Hello, World!");
} catch (IOException e) {
    e.printStackTrace();
}
```

#### BufferedReader and BufferedWriter

Buffered character streams for improved performance and additional functionality.

```java
// Reading lines from a file
try (BufferedReader reader = new BufferedReader(
        new FileReader("input.txt"))) {
    String line;
    while ((line = reader.readLine()) != null) {
        // Process each line
        System.out.println(line);
    }
} catch (IOException e) {
    e.printStackTrace();
}

// Writing lines to a file
try (BufferedWriter writer = new BufferedWriter(
        new FileWriter("output.txt"))) {
    writer.write("Hello, World!");
    writer.newLine();  // Add a line separator
    writer.write("This is a new line.");
} catch (IOException e) {
    e.printStackTrace();
}
```

### Data Streams

Data streams allow reading and writing of primitive data types and strings.

```java
// Writing primitive data types
try (DataOutputStream dos = new DataOutputStream(
        new FileOutputStream("data.bin"))) {
    dos.writeInt(42);
    dos.writeDouble(3.14);
    dos.writeUTF("Hello, World!");
} catch (IOException e) {
    e.printStackTrace();
}

// Reading primitive data types
try (DataInputStream dis = new DataInputStream(
        new FileInputStream("data.bin"))) {
    int intValue = dis.readInt();
    double doubleValue = dis.readDouble();
    String stringValue = dis.readUTF();
    System.out.println(intValue + ", " + doubleValue + ", " + stringValue);
} catch (IOException e) {
    e.printStackTrace();
}
```

### Object Streams

Object streams allow reading and writing of objects (serialization and deserialization).

```java
// Writing objects
try (ObjectOutputStream oos = new ObjectOutputStream(
        new FileOutputStream("objects.bin"))) {
    Person person = new Person("Alice", 30);
    oos.writeObject(person);
} catch (IOException e) {
    e.printStackTrace();
}

// Reading objects
try (ObjectInputStream ois = new ObjectInputStream(
        new FileInputStream("objects.bin"))) {
    Person person = (Person) ois.readObject();
    System.out.println(person.getName() + ", " + person.getAge());
} catch (IOException | ClassNotFoundException e) {
    e.printStackTrace();
}

// Serializable class
class Person implements Serializable {
    private static final long serialVersionUID = 1L;
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
}
```

## Java NIO (New I/O)

The `java.nio` package provides a more modern approach to I/O operations, with features like non-blocking I/O and better performance.

### Buffers and Channels

NIO uses buffers and channels for I/O operations.

```java
// Reading a file using NIO
try {
    Path path = Paths.get("input.txt");
    ByteBuffer buffer = ByteBuffer.allocate(1024);
    
    try (FileChannel channel = FileChannel.open(path, StandardOpenOption.READ)) {
        int bytesRead;
        while ((bytesRead = channel.read(buffer)) != -1) {
            buffer.flip();  // Switch from writing to reading mode
            
            while (buffer.hasRemaining()) {
                System.out.print((char) buffer.get());
            }
            
            buffer.clear();  // Prepare for next read
        }
    }
} catch (IOException e) {
    e.printStackTrace();
}

// Writing to a file using NIO
try {
    Path path = Paths.get("output.txt");
    String data = "Hello, NIO World!";
    byte[] bytes = data.getBytes();
    ByteBuffer buffer = ByteBuffer.wrap(bytes);
    
    try (FileChannel channel = FileChannel.open(path, 
            StandardOpenOption.CREATE, StandardOpenOption.WRITE)) {
        channel.write(buffer);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

### Memory-Mapped Files

NIO allows mapping a file directly into memory for faster access.

```java
// Memory-mapped file
try {
    Path path = Paths.get("large-file.txt");
    
    try (FileChannel channel = FileChannel.open(path, StandardOpenOption.READ)) {
        long fileSize = channel.size();
        MappedByteBuffer buffer = channel.map(
                FileChannel.MapMode.READ_ONLY, 0, fileSize);
        
        // Access the file as if it were in memory
        for (int i = 0; i < fileSize; i++) {
            byte b = buffer.get(i);
            // Process byte
        }
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

## Java NIO.2 (Java 7+)

Java 7 introduced NIO.2 with the `java.nio.file` package, providing a more comprehensive file system API.

### Path and Files

The `Path` interface and `Files` utility class are the core of NIO.2.

```java
// Creating paths
Path path1 = Paths.get("file.txt");
Path path2 = Paths.get("/home", "user", "documents", "file.txt");
Path path3 = FileSystems.getDefault().getPath("file.txt");

// Path information
System.out.println("File name: " + path1.getFileName());
System.out.println("Parent: " + path1.getParent());
System.out.println("Root: " + path1.getRoot());
System.out.println("Absolute path: " + path1.toAbsolutePath());

// Path operations
Path path4 = Paths.get("/home/user");
Path path5 = Paths.get("documents/file.txt");
Path path6 = path4.resolve(path5);  // /home/user/documents/file.txt
Path path7 = path6.relativize(path4);  // ../../

// Normalizing paths
Path path8 = Paths.get("/home/./user/../user/documents");
Path path9 = path8.normalize();  // /home/user/documents
```

### Reading and Writing Files

The `Files` class provides simple methods for common file operations.

```java
// Reading all bytes from a file
try {
    byte[] bytes = Files.readAllBytes(Paths.get("input.txt"));
    System.out.println(new String(bytes));
} catch (IOException e) {
    e.printStackTrace();
}

// Reading all lines from a file
try {
    List<String> lines = Files.readAllLines(Paths.get("input.txt"));
    for (String line : lines) {
        System.out.println(line);
    }
} catch (IOException e) {
    e.printStackTrace();
}

// Writing bytes to a file
try {
    String data = "Hello, NIO.2 World!";
    Files.write(Paths.get("output.txt"), data.getBytes());
} catch (IOException e) {
    e.printStackTrace();
}

// Writing lines to a file
try {
    List<String> lines = Arrays.asList("Line 1", "Line 2", "Line 3");
    Files.write(Paths.get("output.txt"), lines);
} catch (IOException e) {
    e.printStackTrace();
}
```

### File Operations

The `Files` class provides methods for various file operations.

```java
// Checking file properties
Path path = Paths.get("file.txt");
boolean exists = Files.exists(path);
boolean isRegularFile = Files.isRegularFile(path);
boolean isDirectory = Files.isDirectory(path);
boolean isReadable = Files.isReadable(path);
boolean isWritable = Files.isWritable(path);
boolean isExecutable = Files.isExecutable(path);

// Getting file attributes
try {
    BasicFileAttributes attrs = Files.readAttributes(path, BasicFileAttributes.class);
    System.out.println("Creation time: " + attrs.creationTime());
    System.out.println("Last access time: " + attrs.lastAccessTime());
    System.out.println("Last modified time: " + attrs.lastModifiedTime());
    System.out.println("Size: " + attrs.size());
    System.out.println("Is directory: " + attrs.isDirectory());
    System.out.println("Is regular file: " + attrs.isRegularFile());
    System.out.println("Is symbolic link: " + attrs.isSymbolicLink());
} catch (IOException e) {
    e.printStackTrace();
}

// Creating directories
try {
    Path dirPath = Paths.get("new-directory");
    Files.createDirectory(dirPath);
    
    Path dirPath2 = Paths.get("parent/child/grandchild");
    Files.createDirectories(dirPath2);  // Creates parent directories if needed
} catch (IOException e) {
    e.printStackTrace();
}

// Copying files
try {
    Path source = Paths.get("source.txt");
    Path target = Paths.get("target.txt");
    Files.copy(source, target, StandardCopyOption.REPLACE_EXISTING);
} catch (IOException e) {
    e.printStackTrace();
}

// Moving files
try {
    Path source = Paths.get("source.txt");
    Path target = Paths.get("target.txt");
    Files.move(source, target, StandardCopyOption.REPLACE_EXISTING);
} catch (IOException e) {
    e.printStackTrace();
}

// Deleting files
try {
    Path path = Paths.get("file-to-delete.txt");
    Files.delete(path);  // Throws exception if file doesn't exist
    
    Path path2 = Paths.get("file-to-delete-2.txt");
    boolean deleted = Files.deleteIfExists(path2);  // Returns false if file doesn't exist
} catch (IOException e) {
    e.printStackTrace();
}
```

### Directory Operations

NIO.2 provides powerful methods for working with directories.

```java
// Listing directory contents
try {
    Path dirPath = Paths.get(".");
    try (DirectoryStream<Path> stream = Files.newDirectoryStream(dirPath)) {
        for (Path entry : stream) {
            System.out.println(entry.getFileName());
        }
    }
} catch (IOException e) {
    e.printStackTrace();
}

// Filtering directory contents
try {
    Path dirPath = Paths.get(".");
    try (DirectoryStream<Path> stream = 
            Files.newDirectoryStream(dirPath, "*.{java,class}")) {
        for (Path entry : stream) {
            System.out.println(entry.getFileName());  // Only Java files
        }
    }
} catch (IOException e) {
    e.printStackTrace();
}

// Walking a directory tree
try {
    Path startPath = Paths.get(".");
    Files.walkFileTree(startPath, new SimpleFileVisitor<Path>() {
        @Override
        public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) {
            System.out.println("File: " + file);
            return FileVisitResult.CONTINUE;
        }
        
        @Override
        public FileVisitResult visitFileFailed(Path file, IOException e) {
            System.err.println("Failed to access file: " + file);
            return FileVisitResult.CONTINUE;
        }
        
        @Override
        public FileVisitResult preVisitDirectory(Path dir, BasicFileAttributes attrs) {
            System.out.println("Directory: " + dir);
            return FileVisitResult.CONTINUE;
        }
    });
} catch (IOException e) {
    e.printStackTrace();
}

// Finding files
try {
    Path startPath = Paths.get(".");
    Files.find(startPath, Integer.MAX_VALUE,
            (path, attrs) -> attrs.isRegularFile() && 
                             path.toString().endsWith(".java"))
         .forEach(System.out::println);
} catch (IOException e) {
    e.printStackTrace();
}
```

### File Change Notification

NIO.2 provides a way to watch for changes to a directory.

```java
try {
    Path dir = Paths.get(".");
    WatchService watchService = FileSystems.getDefault().newWatchService();
    
    dir.register(watchService, 
            StandardWatchEventKinds.ENTRY_CREATE,
            StandardWatchEventKinds.ENTRY_MODIFY,
            StandardWatchEventKinds.ENTRY_DELETE);
    
    System.out.println("Watching directory: " + dir);
    
    while (true) {
        WatchKey key = watchService.take();  // Blocks until events occur
        
        for (WatchEvent<?> event : key.pollEvents()) {
            WatchEvent.Kind<?> kind = event.kind();
            
            if (kind == StandardWatchEventKinds.OVERFLOW) {
                continue;  // Events may have been lost or discarded
            }
            
            @SuppressWarnings("unchecked")
            WatchEvent<Path> pathEvent = (WatchEvent<Path>) event;
            Path fileName = pathEvent.context();
            
            System.out.println(kind + ": " + fileName);
        }
        
        boolean valid = key.reset();  // Reset the key for further events
        if (!valid) {
            break;  // Directory no longer accessible
        }
    }
} catch (IOException | InterruptedException e) {
    e.printStackTrace();
}
```

## Working with Properties Files

Java provides the `Properties` class for working with configuration files.

```java
// Creating and saving properties
Properties properties = new Properties();
properties.setProperty("db.url", "jdbc:mysql://localhost:3306/mydb");
properties.setProperty("db.username", "user");
properties.setProperty("db.password", "password");

try (OutputStream output = new FileOutputStream("config.properties")) {
    properties.store(output, "Database Configuration");
} catch (IOException e) {
    e.printStackTrace();
}

// Loading properties
Properties loadedProperties = new Properties();
try (InputStream input = new FileInputStream("config.properties")) {
    loadedProperties.load(input);
    
    String url = loadedProperties.getProperty("db.url");
    String username = loadedProperties.getProperty("db.username");
    String password = loadedProperties.getProperty("db.password");
    
    System.out.println("URL: " + url);
    System.out.println("Username: " + username);
    System.out.println("Password: " + password);
} catch (IOException e) {
    e.printStackTrace();
}

// Loading XML properties
Properties xmlProperties = new Properties();
try (InputStream input = new FileInputStream("config.xml")) {
    xmlProperties.loadFromXML(input);
    // Access properties as above
} catch (IOException e) {
    e.printStackTrace();
}

// Saving as XML
try (OutputStream output = new FileOutputStream("config.xml")) {
    properties.storeToXML(output, "Database Configuration");
} catch (IOException e) {
    e.printStackTrace();
}
```

## Character Encoding

Handling character encoding is important for text file I/O.

```java
// Reading with specific encoding
try (BufferedReader reader = new BufferedReader(
        new InputStreamReader(
                new FileInputStream("input.txt"), StandardCharsets.UTF_8))) {
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
} catch (IOException e) {
    e.printStackTrace();
}

// Writing with specific encoding
try (BufferedWriter writer = new BufferedWriter(
        new OutputStreamWriter(
                new FileOutputStream("output.txt"), StandardCharsets.UTF_8))) {
    writer.write("Hello, World with UTF-8 encoding!");
} catch (IOException e) {
    e.printStackTrace();
}

// Using Files class with encoding (Java 7+)
try {
    List<String> lines = Files.readAllLines(
            Paths.get("input.txt"), StandardCharsets.UTF_8);
    
    Files.write(Paths.get("output.txt"), lines, StandardCharsets.UTF_8);
} catch (IOException e) {
    e.printStackTrace();
}
```

## Best Practices

1. **Always close resources**: Use try-with-resources (Java 7+) or finally blocks to ensure resources are closed.

2. **Use buffered streams**: Buffered streams improve performance by reducing the number of I/O operations.

3. **Choose the right API**: Use NIO.2 (Java 7+) for modern applications, especially for file system operations.

4. **Handle exceptions properly**: File I/O can throw various exceptions; handle them appropriately.

5. **Consider character encoding**: Always specify the character encoding when working with text files.

6. **Use appropriate buffer sizes**: For buffered operations, choose an appropriate buffer size (typically 4-8 KB).

7. **Avoid excessive file operations**: Batch file operations when possible to improve performance.

8. **Use memory-mapped files for large files**: Memory-mapped files can provide better performance for large files.

9. **Be careful with file paths**: Use the `Path` API to handle file paths in a platform-independent way.

10. **Consider thread safety**: File I/O operations are not thread-safe; synchronize access if needed.

```java
// Example of good practices
public class FileIOExample {
    private static final int BUFFER_SIZE = 8192;
    
    public static List<String> readLines(Path path) throws IOException {
        // Use try-with-resources for automatic resource management
        try (BufferedReader reader = Files.newBufferedReader(path, StandardCharsets.UTF_8)) {
            List<String> lines = new ArrayList<>();
            String line;
            while ((line = reader.readLine()) != null) {
                lines.add(line);
            }
            return lines;
        }
    }
    
    public static void writeLines(Path path, List<String> lines) throws IOException {
        // Use try-with-resources and specify encoding
        try (BufferedWriter writer = Files.newBufferedWriter(path, StandardCharsets.UTF_8)) {
            for (String line : lines) {
                writer.write(line);
                writer.newLine();
            }
        }
    }
    
    public static void copyFile(Path source, Path target) throws IOException {
        // Use NIO.2 for file operations
        Files.copy(source, target, StandardCopyOption.REPLACE_EXISTING);
    }
    
    public static void processLargeFile(Path path) throws IOException {
        // Use appropriate buffer size for large files
        try (BufferedInputStream bis = new BufferedInputStream(
                Files.newInputStream(path), BUFFER_SIZE)) {
            byte[] buffer = new byte[BUFFER_SIZE];
            int bytesRead;
            while ((bytesRead = bis.read(buffer)) != -1) {
                // Process data in buffer
            }
        }
    }
}
```

## Conclusion

Java provides multiple APIs for file I/O, from the traditional stream-based approach to the modern NIO.2 API. Understanding these options allows you to choose the most appropriate approach for your specific requirements, whether you need simple file reading/writing or more complex file system operations.

For most modern applications, the NIO.2 API (Java 7+) is recommended due to its comprehensive feature set, improved performance, and more intuitive API design. However, the traditional I/O streams still have their place, especially for simple operations or when working with legacy code.