# Java Networking

Java provides comprehensive support for network programming, allowing developers to create applications that communicate over networks. This document covers the fundamentals of Java networking, including sockets, URLs, and higher-level APIs.

## Introduction to Java Networking

Java's networking capabilities are primarily contained in the `java.net` package, with additional support from `java.io` and `java.nio` packages. These APIs enable various networking tasks:

- Client-server communication
- Web resource access
- Network interface management
- IP address handling
- Protocol implementation

## Socket Programming

Sockets provide a communication mechanism between two computers using TCP or UDP protocols.

### TCP Sockets

TCP (Transmission Control Protocol) provides reliable, ordered, and error-checked delivery of data.

#### TCP Server

```java
import java.io.*;
import java.net.*;

public class TCPServer {
    public static void main(String[] args) {
        int port = 8080;
        
        try (ServerSocket serverSocket = new ServerSocket(port)) {
            System.out.println("Server is listening on port " + port);
            
            while (true) {
                // Wait for client connection
                Socket clientSocket = serverSocket.accept();
                System.out.println("New client connected: " + clientSocket.getInetAddress().getHostAddress());
                
                // Create input and output streams
                BufferedReader in = new BufferedReader(
                    new InputStreamReader(clientSocket.getInputStream()));
                PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true);
                
                // Read message from client
                String message = in.readLine();
                System.out.println("Received from client: " + message);
                
                // Send response to client
                out.println("Server received: " + message);
                
                // Close the client socket
                clientSocket.close();
                System.out.println("Client connection closed");
            }
        } catch (IOException e) {
            System.out.println("Server exception: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

#### TCP Client

```java
import java.io.*;
import java.net.*;

public class TCPClient {
    public static void main(String[] args) {
        String hostname = "localhost";
        int port = 8080;
        
        try (Socket socket = new Socket(hostname, port)) {
            System.out.println("Connected to server");
            
            // Create input and output streams
            BufferedReader in = new BufferedReader(
                new InputStreamReader(socket.getInputStream()));
            PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
            
            // Send message to server
            String message = "Hello from client";
            out.println(message);
            System.out.println("Sent to server: " + message);
            
            // Read response from server
            String response = in.readLine();
            System.out.println("Received from server: " + response);
            
        } catch (UnknownHostException e) {
            System.out.println("Server not found: " + e.getMessage());
        } catch (IOException e) {
            System.out.println("I/O error: " + e.getMessage());
        }
    }
}
```

### UDP Sockets

UDP (User Datagram Protocol) provides a connectionless communication model with minimal overhead but no guarantee of delivery or order.

#### UDP Server

```java
import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetAddress;

public class UDPServer {
    public static void main(String[] args) {
        int port = 9090;
        
        try (DatagramSocket socket = new DatagramSocket(port)) {
            System.out.println("UDP server is listening on port " + port);
            
            byte[] buffer = new byte[1024];
            
            while (true) {
                // Create packet for receiving data
                DatagramPacket receivePacket = new DatagramPacket(buffer, buffer.length);
                
                // Receive data from client
                socket.receive(receivePacket);
                
                // Extract data, client address, and port
                String message = new String(receivePacket.getData(), 0, receivePacket.getLength());
                InetAddress clientAddress = receivePacket.getAddress();
                int clientPort = receivePacket.getPort();
                
                System.out.println("Received from client: " + message);
                System.out.println("Client address: " + clientAddress.getHostAddress());
                System.out.println("Client port: " + clientPort);
                
                // Prepare response
                String response = "Server received: " + message;
                byte[] responseData = response.getBytes();
                
                // Create packet for sending data
                DatagramPacket sendPacket = new DatagramPacket(
                    responseData, responseData.length, clientAddress, clientPort);
                
                // Send response to client
                socket.send(sendPacket);
                System.out.println("Response sent to client");
            }
        } catch (IOException e) {
            System.out.println("Server exception: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

#### UDP Client

```java
import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetAddress;

public class UDPClient {
    public static void main(String[] args) {
        String hostname = "localhost";
        int port = 9090;
        
        try (DatagramSocket socket = new DatagramSocket()) {
            InetAddress serverAddress = InetAddress.getByName(hostname);
            
            // Prepare message
            String message = "Hello from UDP client";
            byte[] sendData = message.getBytes();
            
            // Create packet for sending data
            DatagramPacket sendPacket = new DatagramPacket(
                sendData, sendData.length, serverAddress, port);
            
            // Send data to server
            socket.send(sendPacket);
            System.out.println("Sent to server: " + message);
            
            // Prepare for receiving response
            byte[] receiveData = new byte[1024];
            DatagramPacket receivePacket = new DatagramPacket(receiveData, receiveData.length);
            
            // Receive response from server
            socket.receive(receivePacket);
            
            // Extract response
            String response = new String(receivePacket.getData(), 0, receivePacket.getLength());
            System.out.println("Received from server: " + response);
            
        } catch (IOException e) {
            System.out.println("Client exception: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

### Socket Options

Both TCP and UDP sockets support various options to control their behavior:

```java
import java.net.Socket;
import java.net.SocketException;

public class SocketOptionsExample {
    public static void main(String[] args) {
        try (Socket socket = new Socket("example.com", 80)) {
            // Get current timeout
            int timeout = socket.getSoTimeout();
            System.out.println("Current timeout: " + timeout + " ms");
            
            // Set timeout to 5 seconds
            socket.setSoTimeout(5000);
            System.out.println("New timeout: " + socket.getSoTimeout() + " ms");
            
            // Get TCP no delay flag (Nagle's algorithm)
            boolean tcpNoDelay = socket.getTcpNoDelay();
            System.out.println("TCP no delay: " + tcpNoDelay);
            
            // Enable TCP no delay
            socket.setTcpNoDelay(true);
            System.out.println("TCP no delay (after): " + socket.getTcpNoDelay());
            
            // Get keep alive flag
            boolean keepAlive = socket.getKeepAlive();
            System.out.println("Keep alive: " + keepAlive);
            
            // Enable keep alive
            socket.setKeepAlive(true);
            System.out.println("Keep alive (after): " + socket.getKeepAlive());
            
            // Get receive buffer size
            int receiveBufferSize = socket.getReceiveBufferSize();
            System.out.println("Receive buffer size: " + receiveBufferSize + " bytes");
            
            // Set receive buffer size
            socket.setReceiveBufferSize(8192);
            System.out.println("Receive buffer size (after): " + 
                              socket.getReceiveBufferSize() + " bytes");
            
            // Get send buffer size
            int sendBufferSize = socket.getSendBufferSize();
            System.out.println("Send buffer size: " + sendBufferSize + " bytes");
            
            // Set send buffer size
            socket.setSendBufferSize(8192);
            System.out.println("Send buffer size (after): " + 
                              socket.getSendBufferSize() + " bytes");
            
        } catch (SocketException e) {
            System.out.println("Socket option error: " + e.getMessage());
        } catch (Exception e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```

## Multithreaded Server

To handle multiple clients simultaneously, servers typically use multithreading:

```java
import java.io.*;
import java.net.*;

public class MultithreadedServer {
    public static void main(String[] args) {
        int port = 8080;
        
        try (ServerSocket serverSocket = new ServerSocket(port)) {
            System.out.println("Server is listening on port " + port);
            
            while (true) {
                Socket clientSocket = serverSocket.accept();
                System.out.println("New client connected: " + 
                                  clientSocket.getInetAddress().getHostAddress());
                
                // Create a new thread to handle the client
                ClientHandler clientHandler = new ClientHandler(clientSocket);
                new Thread(clientHandler).start();
            }
        } catch (IOException e) {
            System.out.println("Server exception: " + e.getMessage());
            e.printStackTrace();
        }
    }
}

class ClientHandler implements Runnable {
    private final Socket clientSocket;
    
    public ClientHandler(Socket socket) {
        this.clientSocket = socket;
    }
    
    @Override
    public void run() {
        try {
            // Create input and output streams
            BufferedReader in = new BufferedReader(
                new InputStreamReader(clientSocket.getInputStream()));
            PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true);
            
            String line;
            while ((line = in.readLine()) != null) {
                System.out.println("Received from client: " + line);
                
                // Echo the message back to client
                out.println("Server echo: " + line);
                
                // Exit loop if client sends "bye"
                if (line.equalsIgnoreCase("bye")) {
                    break;
                }
            }
            
            // Close the client socket
            clientSocket.close();
            System.out.println("Client disconnected");
            
        } catch (IOException e) {
            System.out.println("Client handler exception: " + e.getMessage());
        }
    }
}
```

## URL and URLConnection

Java provides the `URL` and `URLConnection` classes for working with URLs and making HTTP requests.

### Reading from a URL

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.URL;

public class URLReader {
    public static void main(String[] args) {
        try {
            // Create URL object
            URL url = new URL("https://www.example.com");
            
            // Open connection and create reader
            BufferedReader reader = new BufferedReader(
                new InputStreamReader(url.openStream()));
            
            // Read content line by line
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
            
            // Close the reader
            reader.close();
            
        } catch (IOException e) {
            System.out.println("Error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

### URLConnection for HTTP Requests

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.net.URL;
import java.net.URLConnection;

public class URLConnectionExample {
    public static void main(String[] args) {
        try {
            // Create URL object
            URL url = new URL("https://postman-echo.com/post");
            
            // Open connection
            URLConnection connection = url.openConnection();
            
            // Set connection properties
            connection.setDoOutput(true);  // For POST requests
            connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
            connection.setRequestProperty("User-Agent", "Mozilla/5.0");
            
            // Send POST data
            OutputStreamWriter writer = new OutputStreamWriter(connection.getOutputStream());
            writer.write("name=John&age=30");
            writer.flush();
            writer.close();
            
            // Get response
            BufferedReader reader = new BufferedReader(
                new InputStreamReader(connection.getInputStream()));
            
            // Print response headers
            System.out.println("Response Headers:");
            connection.getHeaderFields().forEach((key, values) -> {
                if (key != null) {
                    System.out.println(key + ": " + values);
                }
            });
            
            // Print response body
            System.out.println("\nResponse Body:");
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
            
            // Close the reader
            reader.close();
            
        } catch (IOException e) {
            System.out.println("Error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

## HttpURLConnection

The `HttpURLConnection` class provides more HTTP-specific functionality:

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.net.HttpURLConnection;
import java.net.URL;

public class HttpURLConnectionExample {
    public static void main(String[] args) {
        try {
            // Create URL object
            URL url = new URL("https://jsonplaceholder.typicode.com/posts");
            
            // Open connection
            HttpURLConnection connection = (HttpURLConnection) url.openConnection();
            
            // Set request method
            connection.setRequestMethod("POST");
            
            // Set request headers
            connection.setRequestProperty("Content-Type", "application/json");
            connection.setRequestProperty("Accept", "application/json");
            
            // Enable input/output streams
            connection.setDoOutput(true);
            connection.setDoInput(true);
            
            // Create JSON request body
            String jsonInputString = "{\"title\":\"Test Post\",\"body\":\"This is a test\",\"userId\":1}";
            
            // Write request body
            try (OutputStream os = connection.getOutputStream()) {
                byte[] input = jsonInputString.getBytes("utf-8");
                os.write(input, 0, input.length);
            }
            
            // Get response code
            int responseCode = connection.getResponseCode();
            System.out.println("Response Code: " + responseCode);
            
            // Read response
            try (BufferedReader br = new BufferedReader(
                    new InputStreamReader(connection.getInputStream(), "utf-8"))) {
                
                StringBuilder response = new StringBuilder();
                String responseLine;
                while ((responseLine = br.readLine()) != null) {
                    response.append(responseLine.trim());
                }
                
                System.out.println("Response: " + response.toString());
            }
            
            // Disconnect
            connection.disconnect();
            
        } catch (IOException e) {
            System.out.println("Error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

## InetAddress

The `InetAddress` class represents an IP address:

```java
import java.net.InetAddress;
import java.net.UnknownHostException;

public class InetAddressExample {
    public static void main(String[] args) {
        try {
            // Get local host
            InetAddress localhost = InetAddress.getLocalHost();
            System.out.println("Local Host: " + localhost.getHostName() + 
                              " (" + localhost.getHostAddress() + ")");
            
            // Get address by name
            InetAddress googleAddress = InetAddress.getByName("www.google.com");
            System.out.println("Google Address: " + googleAddress.getHostAddress());
            
            // Get all addresses for a host
            InetAddress[] allAddresses = InetAddress.getAllByName("www.google.com");
            System.out.println("\nAll addresses for www.google.com:");
            for (InetAddress address : allAddresses) {
                System.out.println("  " + address.getHostAddress());
            }
            
            // Check if addresses are reachable
            System.out.println("\nReachability:");
            System.out.println("Local host reachable: " + localhost.isReachable(1000));
            System.out.println("Google reachable: " + googleAddress.isReachable(2000));
            
            // Get address properties
            System.out.println("\nAddress Properties:");
            System.out.println("Is loopback address: " + localhost.isLoopbackAddress());
            System.out.println("Is link local address: " + localhost.isLinkLocalAddress());
            System.out.println("Is multicast address: " + localhost.isMulticastAddress());
            
        } catch (UnknownHostException e) {
            System.out.println("Unknown host: " + e.getMessage());
        } catch (IOException e) {
            System.out.println("I/O error: " + e.getMessage());
        }
    }
}
```

## NetworkInterface

The `NetworkInterface` class provides information about network interfaces:

```java
import java.net.InetAddress;
import java.net.NetworkInterface;
import java.net.SocketException;
import java.util.Collections;
import java.util.Enumeration;

public class NetworkInterfaceExample {
    public static void main(String[] args) {
        try {
            // Get all network interfaces
            Enumeration<NetworkInterface> interfaces = NetworkInterface.getNetworkInterfaces();
            
            for (NetworkInterface iface : Collections.list(interfaces)) {
                System.out.println("\nInterface: " + iface.getName() + 
                                  " (" + iface.getDisplayName() + ")");
                
                // Check if interface is up
                System.out.println("  Up? " + iface.isUp());
                
                // Check if loopback interface
                System.out.println("  Loopback? " + iface.isLoopback());
                
                // Check if point-to-point interface
                System.out.println("  Point-to-point? " + iface.isPointToPoint());
                
                // Check if virtual interface
                System.out.println("  Virtual? " + iface.isVirtual());
                
                // Get MTU
                System.out.println("  MTU: " + iface.getMTU());
                
                // Get hardware address (MAC)
                byte[] mac = iface.getHardwareAddress();
                if (mac != null) {
                    System.out.print("  Hardware address: ");
                    for (int i = 0; i < mac.length; i++) {
                        System.out.format("%02X%s", mac[i], (i < mac.length - 1) ? ":" : "");
                    }
                    System.out.println();
                }
                
                // Get IP addresses
                System.out.println("  IP addresses:");
                Enumeration<InetAddress> addresses = iface.getInetAddresses();
                while (addresses.hasMoreElements()) {
                    InetAddress addr = addresses.nextElement();
                    System.out.println("    " + addr.getHostAddress());
                }
                
                // Get sub-interfaces
                Enumeration<NetworkInterface> subInterfaces = iface.getSubInterfaces();
                if (subInterfaces.hasMoreElements()) {
                    System.out.println("  Sub-interfaces:");
                    while (subInterfaces.hasMoreElements()) {
                        NetworkInterface subIface = subInterfaces.nextElement();
                        System.out.println("    " + subIface.getName());
                    }
                }
            }
            
        } catch (SocketException e) {
            System.out.println("Socket error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

## Non-Blocking I/O (NIO)

Java NIO provides non-blocking I/O operations for network programming.

### NIO Server

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;
import java.util.Iterator;
import java.util.Set;

public class NIOServer {
    public static void main(String[] args) {
        try {
            // Create selector
            Selector selector = Selector.open();
            
            // Create server socket channel
            ServerSocketChannel serverChannel = ServerSocketChannel.open();
            serverChannel.configureBlocking(false);
            serverChannel.socket().bind(new InetSocketAddress(8080));
            
            // Register the channel with the selector for accept operations
            serverChannel.register(selector, SelectionKey.OP_ACCEPT);
            
            System.out.println("NIO Server started on port 8080");
            
            while (true) {
                // Wait for events
                selector.select();
                
                // Get selected keys
                Set<SelectionKey> selectedKeys = selector.selectedKeys();
                Iterator<SelectionKey> keyIterator = selectedKeys.iterator();
                
                while (keyIterator.hasNext()) {
                    SelectionKey key = keyIterator.next();
                    
                    // Remove the key from the selected keys set
                    keyIterator.remove();
                    
                    // Handle the event
                    if (!key.isValid()) {
                        continue;
                    }
                    
                    if (key.isAcceptable()) {
                        // Accept new connection
                        handleAccept(key, selector);
                    } else if (key.isReadable()) {
                        // Read data from client
                        handleRead(key);
                    }
                }
            }
            
        } catch (IOException e) {
            System.out.println("Server exception: " + e.getMessage());
            e.printStackTrace();
        }
    }
    
    private static void handleAccept(SelectionKey key, Selector selector) throws IOException {
        // Get server channel from the key
        ServerSocketChannel serverChannel = (ServerSocketChannel) key.channel();
        
        // Accept the connection
        SocketChannel clientChannel = serverChannel.accept();
        clientChannel.configureBlocking(false);
        
        // Register the client channel for read operations
        clientChannel.register(selector, SelectionKey.OP_READ);
        
        System.out.println("Connection accepted from " + 
                          clientChannel.getRemoteAddress());
    }
    
    private static void handleRead(SelectionKey key) throws IOException {
        // Get client channel from the key
        SocketChannel clientChannel = (SocketChannel) key.channel();
        
        // Create buffer for reading data
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        
        try {
            // Read data from client
            int bytesRead = clientChannel.read(buffer);
            
            if (bytesRead == -1) {
                // Client closed the connection
                clientChannel.close();
                key.cancel();
                System.out.println("Connection closed by client");
                return;
            }
            
            // Process the data
            buffer.flip();
            byte[] data = new byte[buffer.limit()];
            buffer.get(data);
            String message = new String(data).trim();
            
            System.out.println("Received from client: " + message);
            
            // Echo the message back to client
            String response = "Server echo: " + message;
            ByteBuffer responseBuffer = ByteBuffer.wrap(response.getBytes());
            clientChannel.write(responseBuffer);
            
        } catch (IOException e) {
            // Handle connection error
            clientChannel.close();
            key.cancel();
            System.out.println("Connection error: " + e.getMessage());
        }
    }
}
```

### NIO Client

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SocketChannel;
import java.util.Scanner;

public class NIOClient {
    public static void main(String[] args) {
        try {
            // Create socket channel
            SocketChannel socketChannel = SocketChannel.open();
            
            // Connect to server
            socketChannel.connect(new InetSocketAddress("localhost", 8080));
            System.out.println("Connected to server");
            
            // Create scanner for user input
            Scanner scanner = new Scanner(System.in);
            
            while (true) {
                // Get user input
                System.out.print("Enter message (or 'exit' to quit): ");
                String message = scanner.nextLine();
                
                if ("exit".equalsIgnoreCase(message)) {
                    break;
                }
                
                // Send message to server
                ByteBuffer buffer = ByteBuffer.wrap(message.getBytes());
                socketChannel.write(buffer);
                
                // Receive response from server
                ByteBuffer responseBuffer = ByteBuffer.allocate(1024);
                int bytesRead = socketChannel.read(responseBuffer);
                
                if (bytesRead > 0) {
                    responseBuffer.flip();
                    byte[] data = new byte[responseBuffer.limit()];
                    responseBuffer.get(data);
                    String response = new String(data).trim();
                    System.out.println("Server response: " + response);
                }
            }
            
            // Close the connection
            socketChannel.close();
            scanner.close();
            System.out.println("Connection closed");
            
        } catch (IOException e) {
            System.out.println("Client exception: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

## Datagram Channel (NIO UDP)

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.DatagramChannel;

public class DatagramChannelExample {
    public static void main(String[] args) {
        try {
            // Create datagram channel
            DatagramChannel channel = DatagramChannel.open();
            
            // Bind to a local port
            channel.socket().bind(new InetSocketAddress(9090));
            
            // Configure non-blocking mode
            channel.configureBlocking(false);
            
            System.out.println("Datagram channel bound to port 9090");
            
            // Create buffer for receiving data
            ByteBuffer buffer = ByteBuffer.allocate(1024);
            
            while (true) {
                // Clear the buffer for new data
                buffer.clear();
                
                // Receive data
                InetSocketAddress senderAddress = (InetSocketAddress) channel.receive(buffer);
                
                if (senderAddress != null) {
                    // Process received data
                    buffer.flip();
                    byte[] data = new byte[buffer.limit()];
                    buffer.get(data);
                    String message = new String(data).trim();
                    
                    System.out.println("Received from " + senderAddress + ": " + message);
                    
                    // Send response
                    String response = "Echo: " + message;
                    ByteBuffer responseBuffer = ByteBuffer.wrap(response.getBytes());
                    channel.send(responseBuffer, senderAddress);
                }
                
                // Sleep a bit to avoid busy waiting
                Thread.sleep(100);
            }
            
        } catch (IOException | InterruptedException e) {
            System.out.println("Exception: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

## HTTP Client (Java 11+)

Java 11 introduced a new HTTP client API in the `java.net.http` package:

```java
import java.io.IOException;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.time.Duration;

public class HttpClientExample {
    public static void main(String[] args) {
        try {
            // Create HTTP client
            HttpClient client = HttpClient.newBuilder()
                .version(HttpClient.Version.HTTP_2)
                .followRedirects(HttpClient.Redirect.NORMAL)
                .connectTimeout(Duration.ofSeconds(10))
                .build();
            
            // Create HTTP request
            HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create("https://jsonplaceholder.typicode.com/posts/1"))
                .header("User-Agent", "Java HTTP Client")
                .GET()
                .build();
            
            // Send request and get response
            HttpResponse<String> response = client.send(request, 
                HttpResponse.BodyHandlers.ofString());
            
            // Print response information
            System.out.println("Status code: " + response.statusCode());
            System.out.println("Headers: " + response.headers());
            System.out.println("Body: " + response.body());
            
            // POST request example
            String jsonBody = "{\"title\":\"foo\",\"body\":\"bar\",\"userId\":1}";
            
            HttpRequest postRequest = HttpRequest.newBuilder()
                .uri(URI.create("https://jsonplaceholder.typicode.com/posts"))
                .header("Content-Type", "application/json")
                .POST(HttpRequest.BodyPublishers.ofString(jsonBody))
                .build();
            
            HttpResponse<String> postResponse = client.send(postRequest, 
                HttpResponse.BodyHandlers.ofString());
            
            System.out.println("\nPOST Response:");
            System.out.println("Status code: " + postResponse.statusCode());
            System.out.println("Body: " + postResponse.body());
            
            // Asynchronous request example
            client.sendAsync(request, HttpResponse.BodyHandlers.ofString())
                .thenApply(HttpResponse::body)
                .thenAccept(body -> System.out.println("\nAsync response: " + body))
                .join();  // Wait for completion
            
        } catch (IOException | InterruptedException e) {
            System.out.println("Exception: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

## WebSockets (Java EE)

Java EE provides support for WebSockets through the `javax.websocket` API:

```java
import javax.websocket.*;
import javax.websocket.server.ServerEndpoint;
import java.io.IOException;
import java.util.concurrent.CopyOnWriteArraySet;

@ServerEndpoint("/websocket")
public class WebSocketServer {
    private static final CopyOnWriteArraySet<Session> sessions = new CopyOnWriteArraySet<>();
    
    @OnOpen
    public void onOpen(Session session) {
        sessions.add(session);
        System.out.println("New connection: " + session.getId());
        
        try {
            session.getBasicRemote().sendText("Welcome to the WebSocket server!");
        } catch (IOException e) {
            System.out.println("Error sending welcome message: " + e.getMessage());
        }
    }
    
    @OnMessage
    public void onMessage(String message, Session session) {
        System.out.println("Received message from " + session.getId() + ": " + message);
        
        // Broadcast the message to all connected clients
        for (Session s : sessions) {
            try {
                s.getBasicRemote().sendText("User " + session.getId() + ": " + message);
            } catch (IOException e) {
                System.out.println("Error broadcasting message: " + e.getMessage());
            }
        }
    }
    
    @OnClose
    public void onClose(Session session, CloseReason reason) {
        sessions.remove(session);
        System.out.println("Connection closed: " + session.getId() + 
                          ", Reason: " + reason.getReasonPhrase());
    }
    
    @OnError
    public void onError(Session session, Throwable throwable) {
        System.out.println("Error for session " + session.getId() + ": " + throwable.getMessage());
        throwable.printStackTrace();
    }
}
```

## Multicast Sockets

Multicast allows sending data to multiple recipients simultaneously:

```java
import java.io.IOException;
import java.net.DatagramPacket;
import java.net.InetAddress;
import java.net.MulticastSocket;

public class MulticastSender {
    public static void main(String[] args) {
        try {
            // Create multicast socket
            MulticastSocket socket = new MulticastSocket();
            
            // Set time-to-live (TTL)
            socket.setTimeToLive(1);  // Restrict to local network
            
            // Multicast group address (must be in range 224.0.0.0 to 239.255.255.255)
            InetAddress group = InetAddress.getByName("230.0.0.1");
            
            // Port for multicast
            int port = 4446;
            
            // Message to send
            String message = "Hello, Multicast!";
            byte[] data = message.getBytes();
            
            // Create packet
            DatagramPacket packet = new DatagramPacket(data, data.length, group, port);
            
            // Send packet
            socket.send(packet);
            System.out.println("Sent multicast message: " + message);
            
            // Close socket
            socket.close();
            
        } catch (IOException e) {
            System.out.println("Sender exception: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

```java
import java.io.IOException;
import java.net.DatagramPacket;
import java.net.InetAddress;
import java.net.MulticastSocket;

public class MulticastReceiver {
    public static void main(String[] args) {
        try {
            // Multicast group address
            InetAddress group = InetAddress.getByName("230.0.0.1");
            
            // Port for multicast
            int port = 4446;
            
            // Create multicast socket and bind to port
            MulticastSocket socket = new MulticastSocket(port);
            
            // Join multicast group
            socket.joinGroup(group);
            
            System.out.println("Multicast receiver started");
            System.out.println("Listening for group " + group.getHostAddress() + " on port " + port);
            
            while (true) {
                // Buffer for receiving data
                byte[] buffer = new byte[1024];
                DatagramPacket packet = new DatagramPacket(buffer, buffer.length);
                
                // Receive packet
                socket.receive(packet);
                
                // Process received data
                String message = new String(packet.getData(), 0, packet.getLength());
                System.out.println("Received: " + message);
            }
            
        } catch (IOException e) {
            System.out.println("Receiver exception: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

## Security Considerations

### SSL/TLS Sockets

```java
import javax.net.ssl.SSLServerSocketFactory;
import javax.net.ssl.SSLServerSocket;
import javax.net.ssl.SSLSocket;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.PrintWriter;

public class SSLServer {
    public static void main(String[] args) {
        // Set system properties for keystore
        System.setProperty("javax.net.ssl.keyStore", "keystore.jks");
        System.setProperty("javax.net.ssl.keyStorePassword", "password");
        
        try {
            // Create SSL server socket factory
            SSLServerSocketFactory factory = (SSLServerSocketFactory) SSLServerSocketFactory.getDefault();
            
            // Create SSL server socket
            SSLServerSocket serverSocket = (SSLServerSocket) factory.createServerSocket(8443);
            
            System.out.println("SSL Server started on port 8443");
            
            while (true) {
                // Accept client connection
                SSLSocket clientSocket = (SSLSocket) serverSocket.accept();
                System.out.println("Client connected: " + 
                                  clientSocket.getInetAddress().getHostAddress());
                
                // Create input and output streams
                BufferedReader in = new BufferedReader(
                    new InputStreamReader(clientSocket.getInputStream()));
                PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true);
                
                // Read message from client
                String message = in.readLine();
                System.out.println("Received from client: " + message);
                
                // Send response to client
                out.println("Secure server received: " + message);
                
                // Close the client socket
                clientSocket.close();
                System.out.println("Client connection closed");
            }
            
        } catch (Exception e) {
            System.out.println("Server exception: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

```java
import javax.net.ssl.SSLSocketFactory;
import javax.net.ssl.SSLSocket;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.PrintWriter;

public class SSLClient {
    public static void main(String[] args) {
        // Set system properties for truststore
        System.setProperty("javax.net.ssl.trustStore", "truststore.jks");
        System.setProperty("javax.net.ssl.trustStorePassword", "password");
        
        try {
            // Create SSL socket factory
            SSLSocketFactory factory = (SSLSocketFactory) SSLSocketFactory.getDefault();
            
            // Create SSL socket
            SSLSocket socket = (SSLSocket) factory.createSocket("localhost", 8443);
            
            // Start SSL handshake
            socket.startHandshake();
            
            System.out.println("Connected to secure server");
            
            // Create input and output streams
            BufferedReader in = new BufferedReader(
                new InputStreamReader(socket.getInputStream()));
            PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
            
            // Send message to server
            String message = "Hello from secure client";
            out.println(message);
            System.out.println("Sent to server: " + message);
            
            // Read response from server
            String response = in.readLine();
            System.out.println("Received from server: " + response);
            
            // Close the socket
            socket.close();
            System.out.println("Connection closed");
            
        } catch (Exception e) {
            System.out.println("Client exception: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

### Network Security Best Practices

1. **Use secure protocols**: Prefer HTTPS over HTTP, and use TLS for secure communication.

2. **Validate input**: Always validate data received from the network to prevent injection attacks.

3. **Implement timeouts**: Set appropriate timeouts for network operations to prevent resource exhaustion.

4. **Use connection pooling**: Reuse connections when possible to improve performance.

5. **Implement proper error handling**: Handle network errors gracefully and provide appropriate feedback.

6. **Limit connection attempts**: Implement rate limiting to prevent denial-of-service attacks.

7. **Use secure random numbers**: For cryptographic operations, use `SecureRandom` instead of `Random`.

8. **Keep libraries updated**: Regularly update networking libraries to get security patches.

9. **Implement proper authentication**: Use strong authentication mechanisms for sensitive operations.

10. **Encrypt sensitive data**: Always encrypt sensitive data before transmitting it over the network.

## Conclusion

Java provides a comprehensive set of networking APIs that enable developers to create various types of networked applications. From low-level socket programming to high-level HTTP clients, Java offers tools for different networking needs.

When developing networked applications, consider factors such as performance, security, and error handling. Choose the appropriate API based on your requirements, and follow best practices to create robust and secure networked applications.