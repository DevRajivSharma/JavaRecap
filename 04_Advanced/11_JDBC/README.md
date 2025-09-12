# Java Database Connectivity (JDBC)

JDBC (Java Database Connectivity) is a Java API that enables Java applications to interact with databases. It provides methods for querying and updating data in databases, regardless of the database management system being used.

## Introduction to JDBC

JDBC serves as a bridge between Java applications and databases, allowing developers to:

- Connect to databases
- Execute SQL queries and updates
- Retrieve and process query results
- Handle database transactions
- Manage database metadata

JDBC follows a driver-based architecture, which allows Java applications to connect to any database for which a JDBC driver is available.

## JDBC Architecture

The JDBC architecture consists of four main components:

1. **JDBC API**: A set of Java interfaces and classes for database operations
2. **JDBC Driver Manager**: Manages database drivers and establishes connections
3. **JDBC Drivers**: Database-specific implementations that translate JDBC calls into database-specific protocols
4. **Database**: The actual database management system (DBMS)

## JDBC Driver Types

There are four types of JDBC drivers:

1. **Type 1 (JDBC-ODBC Bridge)**: Translates JDBC calls to ODBC calls
2. **Type 2 (Native-API/partly Java driver)**: Uses database-specific native client libraries
3. **Type 3 (Network-Protocol/pure Java driver)**: Uses a middleware server for database access
4. **Type 4 (Thin/pure Java driver)**: Directly translates JDBC calls into database-specific network protocols

Type 4 drivers are the most commonly used as they offer the best performance and don't require additional software installation on the client side.

## Setting Up JDBC

### Adding JDBC Driver

To use JDBC, you need to add the appropriate JDBC driver to your project. For Maven projects, add the driver dependency to your `pom.xml` file:

```xml
<!-- MySQL Driver Example -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.28</version>
</dependency>
```

For Gradle projects, add to your `build.gradle` file:

```groovy
// MySQL Driver Example
dependencies {
    implementation 'mysql:mysql-connector-java:8.0.28'
}
```

For non-build tool projects, download the JDBC driver JAR file and add it to your classpath.

## Basic JDBC Operations

### Establishing a Connection

The first step in using JDBC is to establish a connection to the database:

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class JDBCConnectionExample {
    public static void main(String[] args) {
        // Database URL, username, and password
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";
        
        // Establish connection
        try (Connection connection = DriverManager.getConnection(url, username, password)) {
            System.out.println("Database connected successfully!");
            // Perform database operations here
        } catch (SQLException e) {
            System.out.println("Database connection error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

The JDBC URL format varies depending on the database:

- MySQL: `jdbc:mysql://hostname:port/database`
- PostgreSQL: `jdbc:postgresql://hostname:port/database`
- Oracle: `jdbc:oracle:thin:@hostname:port:database`
- SQL Server: `jdbc:sqlserver://hostname:port;databaseName=database`
- SQLite: `jdbc:sqlite:path/to/database.db`
- H2 (in-memory): `jdbc:h2:mem:database`
- H2 (file): `jdbc:h2:file:path/to/database`

### Executing SQL Statements

JDBC provides several ways to execute SQL statements:

#### Statement

Use `Statement` for simple SQL queries without parameters:

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class StatementExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";
        
        try (Connection connection = DriverManager.getConnection(url, username, password);
             Statement statement = connection.createStatement()) {
            
            // Execute a query
            ResultSet resultSet = statement.executeQuery("SELECT * FROM employees");
            
            // Process the result set
            while (resultSet.next()) {
                int id = resultSet.getInt("id");
                String name = resultSet.getString("name");
                String email = resultSet.getString("email");
                
                System.out.println("ID: " + id + ", Name: " + name + ", Email: " + email);
            }
            
            // Execute an update
            int rowsAffected = statement.executeUpdate(
                "UPDATE employees SET salary = salary * 1.1 WHERE department = 'IT'");
            System.out.println(rowsAffected + " rows updated");
            
        } catch (SQLException e) {
            System.out.println("Database error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

#### PreparedStatement

Use `PreparedStatement` for SQL queries with parameters or for queries that are executed multiple times:

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class PreparedStatementExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";
        
        try (Connection connection = DriverManager.getConnection(url, username, password)) {
            
            // Query with parameters
            String query = "SELECT * FROM employees WHERE department = ? AND salary > ?";
            
            try (PreparedStatement pstmt = connection.prepareStatement(query)) {
                // Set parameters
                pstmt.setString(1, "IT");
                pstmt.setDouble(2, 50000.0);
                
                // Execute query
                ResultSet resultSet = pstmt.executeQuery();
                
                // Process results
                while (resultSet.next()) {
                    int id = resultSet.getInt("id");
                    String name = resultSet.getString("name");
                    double salary = resultSet.getDouble("salary");
                    
                    System.out.println("ID: " + id + ", Name: " + name + ", Salary: " + salary);
                }
            }
            
            // Insert with parameters
            String insertQuery = "INSERT INTO employees (name, email, department, salary) VALUES (?, ?, ?, ?)";
            
            try (PreparedStatement pstmt = connection.prepareStatement(insertQuery)) {
                // Set parameters
                pstmt.setString(1, "John Doe");
                pstmt.setString(2, "john.doe@example.com");
                pstmt.setString(3, "HR");
                pstmt.setDouble(4, 60000.0);
                
                // Execute insert
                int rowsInserted = pstmt.executeUpdate();
                System.out.println(rowsInserted + " row(s) inserted");
            }
            
        } catch (SQLException e) {
            System.out.println("Database error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

#### CallableStatement

Use `CallableStatement` to call stored procedures and functions:

```java
import java.sql.CallableStatement;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Types;

public class CallableStatementExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";
        
        try (Connection connection = DriverManager.getConnection(url, username, password)) {
            
            // Call a stored procedure that returns a result set
            String procedureCall = "{CALL get_employees_by_department(?)}";
            
            try (CallableStatement cstmt = connection.prepareCall(procedureCall)) {
                // Set input parameter
                cstmt.setString(1, "IT");
                
                // Execute and process result set
                ResultSet resultSet = cstmt.executeQuery();
                
                while (resultSet.next()) {
                    int id = resultSet.getInt("id");
                    String name = resultSet.getString("name");
                    
                    System.out.println("ID: " + id + ", Name: " + name);
                }
            }
            
            // Call a stored procedure with IN and OUT parameters
            String countProcedure = "{CALL count_employees_by_department(?, ?)}";
            
            try (CallableStatement cstmt = connection.prepareCall(countProcedure)) {
                // Set input parameter
                cstmt.setString(1, "HR");
                
                // Register output parameter
                cstmt.registerOutParameter(2, Types.INTEGER);
                
                // Execute
                cstmt.execute();
                
                // Get output parameter value
                int count = cstmt.getInt(2);
                System.out.println("Number of employees in HR: " + count);
            }
            
            // Call a stored function
            String functionCall = "{? = CALL calculate_bonus(?, ?)}";
            
            try (CallableStatement cstmt = connection.prepareCall(functionCall)) {
                // Register return value
                cstmt.registerOutParameter(1, Types.DOUBLE);
                
                // Set input parameters
                cstmt.setInt(2, 101);  // Employee ID
                cstmt.setDouble(3, 0.1);  // Bonus rate
                
                // Execute
                cstmt.execute();
                
                // Get return value
                double bonus = cstmt.getDouble(1);
                System.out.println("Calculated bonus: " + bonus);
            }
            
        } catch (SQLException e) {
            System.out.println("Database error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

### Processing ResultSet

The `ResultSet` interface provides methods to access the data returned by a query:

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.sql.Date;

public class ResultSetExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";
        
        try (Connection connection = DriverManager.getConnection(url, username, password);
             Statement statement = connection.createStatement()) {
            
            ResultSet resultSet = statement.executeQuery(
                "SELECT id, name, email, hire_date, salary, is_manager FROM employees");
            
            // Process the result set
            while (resultSet.next()) {
                // Access by column index (1-based)
                int id = resultSet.getInt(1);
                
                // Access by column name
                String name = resultSet.getString("name");
                String email = resultSet.getString("email");
                
                // Handle different data types
                Date hireDate = resultSet.getDate("hire_date");
                double salary = resultSet.getDouble("salary");
                boolean isManager = resultSet.getBoolean("is_manager");
                
                // Check for NULL values
                String department = resultSet.getString("department");
                if (resultSet.wasNull()) {
                    department = "Not Assigned";
                }
                
                System.out.println("ID: " + id + 
                                 ", Name: " + name + 
                                 ", Email: " + email + 
                                 ", Hire Date: " + hireDate + 
                                 ", Salary: " + salary + 
                                 ", Manager: " + isManager + 
                                 ", Department: " + department);
            }
            
        } catch (SQLException e) {
            System.out.println("Database error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

### ResultSet Types and Concurrency

JDBC supports different types of result sets with varying scrollability and updatability:

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class ResultSetTypesExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";
        
        try (Connection connection = DriverManager.getConnection(url, username, password)) {
            
            // Create a scrollable and updatable result set
            Statement statement = connection.createStatement(
                ResultSet.TYPE_SCROLL_INSENSITIVE,  // Scrollable
                ResultSet.CONCUR_UPDATABLE);         // Updatable
            
            ResultSet resultSet = statement.executeQuery(
                "SELECT id, name, salary FROM employees");
            
            // Navigate through the result set
            System.out.println("Forward iteration:");
            while (resultSet.next()) {
                System.out.println("ID: " + resultSet.getInt("id") + 
                                 ", Name: " + resultSet.getString("name"));
            }
            
            // Move to the first row
            resultSet.first();
            System.out.println("\nFirst row: " + resultSet.getString("name"));
            
            // Move to the last row
            resultSet.last();
            System.out.println("Last row: " + resultSet.getString("name"));
            
            // Move to the previous row
            if (resultSet.previous()) {
                System.out.println("Previous row: " + resultSet.getString("name"));
            }
            
            // Move to a specific row (absolute position)
            resultSet.absolute(3);
            System.out.println("Row 3: " + resultSet.getString("name"));
            
            // Move relative to current position
            resultSet.relative(-1);
            System.out.println("Relative -1: " + resultSet.getString("name"));
            
            // Update a row
            resultSet.absolute(2);
            System.out.println("\nBefore update - Salary: " + resultSet.getDouble("salary"));
            
            resultSet.updateDouble("salary", resultSet.getDouble("salary") * 1.1);
            resultSet.updateRow();
            
            // Verify the update
            resultSet.refreshRow();
            System.out.println("After update - Salary: " + resultSet.getDouble("salary"));
            
            // Insert a new row
            resultSet.moveToInsertRow();
            resultSet.updateString("name", "New Employee");
            resultSet.updateDouble("salary", 50000.0);
            resultSet.insertRow();
            
            // Move back to the current row
            resultSet.moveToCurrentRow();
            
            // Close the statement
            statement.close();
            
        } catch (SQLException e) {
            System.out.println("Database error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

## Transaction Management

JDBC provides transaction management capabilities to ensure data integrity:

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class TransactionExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";
        
        Connection connection = null;
        
        try {
            // Establish connection
            connection = DriverManager.getConnection(url, username, password);
            
            // Disable auto-commit
            connection.setAutoCommit(false);
            
            // First operation: withdraw money from account 1
            String withdrawSql = "UPDATE accounts SET balance = balance - ? WHERE account_id = ?";
            try (PreparedStatement withdrawStmt = connection.prepareStatement(withdrawSql)) {
                withdrawStmt.setDouble(1, 1000.0);
                withdrawStmt.setInt(2, 101);
                withdrawStmt.executeUpdate();
            }
            
            // Second operation: deposit money to account 2
            String depositSql = "UPDATE accounts SET balance = balance + ? WHERE account_id = ?";
            try (PreparedStatement depositStmt = connection.prepareStatement(depositSql)) {
                depositStmt.setDouble(1, 1000.0);
                depositStmt.setInt(2, 102);
                depositStmt.executeUpdate();
            }
            
            // If both operations succeed, commit the transaction
            connection.commit();
            System.out.println("Transaction committed successfully");
            
        } catch (SQLException e) {
            // If any operation fails, roll back the transaction
            System.out.println("Transaction failed: " + e.getMessage());
            
            try {
                if (connection != null) {
                    connection.rollback();
                    System.out.println("Transaction rolled back");
                }
            } catch (SQLException ex) {
                System.out.println("Rollback failed: " + ex.getMessage());
            }
            
            e.printStackTrace();
            
        } finally {
            // Reset auto-commit to default and close connection
            try {
                if (connection != null) {
                    connection.setAutoCommit(true);
                    connection.close();
                }
            } catch (SQLException e) {
                System.out.println("Error closing connection: " + e.getMessage());
            }
        }
    }
}
```

### Transaction Isolation Levels

JDBC supports different transaction isolation levels to control the visibility of changes made by concurrent transactions:

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class TransactionIsolationExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";
        
        try (Connection connection = DriverManager.getConnection(url, username, password)) {
            
            // Get the default isolation level
            int defaultLevel = connection.getTransactionIsolation();
            System.out.println("Default isolation level: " + getIsolationLevelName(defaultLevel));
            
            // Set to READ_UNCOMMITTED
            connection.setTransactionIsolation(Connection.TRANSACTION_READ_UNCOMMITTED);
            System.out.println("Changed to: " + 
                             getIsolationLevelName(connection.getTransactionIsolation()));
            
            // Set to READ_COMMITTED
            connection.setTransactionIsolation(Connection.TRANSACTION_READ_COMMITTED);
            System.out.println("Changed to: " + 
                             getIsolationLevelName(connection.getTransactionIsolation()));
            
            // Set to REPEATABLE_READ
            connection.setTransactionIsolation(Connection.TRANSACTION_REPEATABLE_READ);
            System.out.println("Changed to: " + 
                             getIsolationLevelName(connection.getTransactionIsolation()));
            
            // Set to SERIALIZABLE
            connection.setTransactionIsolation(Connection.TRANSACTION_SERIALIZABLE);
            System.out.println("Changed to: " + 
                             getIsolationLevelName(connection.getTransactionIsolation()));
            
        } catch (SQLException e) {
            System.out.println("Database error: " + e.getMessage());
            e.printStackTrace();
        }
    }
    
    private static String getIsolationLevelName(int level) {
        switch (level) {
            case Connection.TRANSACTION_NONE:
                return "TRANSACTION_NONE";
            case Connection.TRANSACTION_READ_UNCOMMITTED:
                return "TRANSACTION_READ_UNCOMMITTED";
            case Connection.TRANSACTION_READ_COMMITTED:
                return "TRANSACTION_READ_COMMITTED";
            case Connection.TRANSACTION_REPEATABLE_READ:
                return "TRANSACTION_REPEATABLE_READ";
            case Connection.TRANSACTION_SERIALIZABLE:
                return "TRANSACTION_SERIALIZABLE";
            default:
                return "UNKNOWN";
        }
    }
}
```

## Batch Processing

Batch processing allows executing multiple SQL statements in a single database call, improving performance:

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.sql.Statement;

public class BatchProcessingExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";
        
        try (Connection connection = DriverManager.getConnection(url, username, password)) {
            
            // Disable auto-commit for batch operations
            connection.setAutoCommit(false);
            
            // Batch with Statement
            try (Statement statement = connection.createStatement()) {
                statement.addBatch("INSERT INTO departments VALUES (1, 'HR')");
                statement.addBatch("INSERT INTO departments VALUES (2, 'IT')");
                statement.addBatch("INSERT INTO departments VALUES (3, 'Finance')");
                
                // Execute batch
                int[] results = statement.executeBatch();
                
                System.out.println("Statement batch results:");
                for (int i = 0; i < results.length; i++) {
                    System.out.println("Statement " + (i + 1) + ": " + results[i] + " rows affected");
                }
            }
            
            // Batch with PreparedStatement
            String insertSql = "INSERT INTO employees (name, email, department_id) VALUES (?, ?, ?)";
            
            try (PreparedStatement pstmt = connection.prepareStatement(insertSql)) {
                // First batch item
                pstmt.setString(1, "John Doe");
                pstmt.setString(2, "john.doe@example.com");
                pstmt.setInt(3, 1);
                pstmt.addBatch();
                
                // Second batch item
                pstmt.setString(1, "Jane Smith");
                pstmt.setString(2, "jane.smith@example.com");
                pstmt.setInt(3, 2);
                pstmt.addBatch();
                
                // Third batch item
                pstmt.setString(1, "Bob Johnson");
                pstmt.setString(2, "bob.johnson@example.com");
                pstmt.setInt(3, 3);
                pstmt.addBatch();
                
                // Execute batch
                int[] results = pstmt.executeBatch();
                
                System.out.println("\nPreparedStatement batch results:");
                for (int i = 0; i < results.length; i++) {
                    System.out.println("Row " + (i + 1) + ": " + results[i] + " rows affected");
                }
            }
            
            // Commit the transaction
            connection.commit();
            System.out.println("\nBatch operations committed");
            
        } catch (SQLException e) {
            System.out.println("Database error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

## Handling Large Objects (LOBs)

JDBC provides support for handling large objects such as BLOBs (Binary Large Objects) and CLOBs (Character Large Objects):

```java
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.io.InputStream;
import java.io.Reader;
import java.io.Writer;
import java.sql.Blob;
import java.sql.Clob;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class LobExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";
        
        try (Connection connection = DriverManager.getConnection(url, username, password)) {
            
            // Insert BLOB (Binary Large Object)
            insertBlob(connection, 1, new File("image.jpg"));
            
            // Insert CLOB (Character Large Object)
            insertClob(connection, 1, new File("document.txt"));
            
            // Retrieve BLOB
            retrieveBlob(connection, 1, new File("retrieved_image.jpg"));
            
            // Retrieve CLOB
            retrieveClob(connection, 1, new File("retrieved_document.txt"));
            
        } catch (SQLException | IOException e) {
            System.out.println("Error: " + e.getMessage());
            e.printStackTrace();
        }
    }
    
    private static void insertBlob(Connection connection, int id, File file) 
            throws SQLException, IOException {
        
        String sql = "UPDATE employees SET profile_photo = ? WHERE id = ?";
        
        try (PreparedStatement pstmt = connection.prepareStatement(sql);
             FileInputStream fis = new FileInputStream(file)) {
            
            // Set BLOB parameter
            pstmt.setBinaryStream(1, fis, file.length());
            pstmt.setInt(2, id);
            
            int rowsAffected = pstmt.executeUpdate();
            System.out.println(rowsAffected + " row(s) updated with BLOB");
        }
    }
    
    private static void insertClob(Connection connection, int id, File file) 
            throws SQLException, IOException {
        
        String sql = "UPDATE employees SET resume = ? WHERE id = ?";
        
        try (PreparedStatement pstmt = connection.prepareStatement(sql);
             FileReader reader = new FileReader(file)) {
            
            // Set CLOB parameter
            pstmt.setCharacterStream(1, reader, file.length());
            pstmt.setInt(2, id);
            
            int rowsAffected = pstmt.executeUpdate();
            System.out.println(rowsAffected + " row(s) updated with CLOB");
        }
    }
    
    private static void retrieveBlob(Connection connection, int id, File file) 
            throws SQLException, IOException {
        
        String sql = "SELECT profile_photo FROM employees WHERE id = ?";
        
        try (PreparedStatement pstmt = connection.prepareStatement(sql)) {
            pstmt.setInt(1, id);
            
            try (ResultSet rs = pstmt.executeQuery()) {
                if (rs.next()) {
                    Blob blob = rs.getBlob("profile_photo");
                    
                    if (blob != null) {
                        try (InputStream is = blob.getBinaryStream();
                             FileOutputStream fos = new FileOutputStream(file)) {
                            
                            byte[] buffer = new byte[4096];
                            int bytesRead;
                            
                            while ((bytesRead = is.read(buffer)) != -1) {
                                fos.write(buffer, 0, bytesRead);
                            }
                            
                            System.out.println("BLOB retrieved and saved to " + file.getPath());
                        }
                    }
                }
            }
        }
    }
    
    private static void retrieveClob(Connection connection, int id, File file) 
            throws SQLException, IOException {
        
        String sql = "SELECT resume FROM employees WHERE id = ?";
        
        try (PreparedStatement pstmt = connection.prepareStatement(sql)) {
            pstmt.setInt(1, id);
            
            try (ResultSet rs = pstmt.executeQuery()) {
                if (rs.next()) {
                    Clob clob = rs.getClob("resume");
                    
                    if (clob != null) {
                        try (Reader reader = clob.getCharacterStream();
                             Writer writer = new FileWriter(file)) {
                            
                            char[] buffer = new char[4096];
                            int charsRead;
                            
                            while ((charsRead = reader.read(buffer)) != -1) {
                                writer.write(buffer, 0, charsRead);
                            }
                            
                            System.out.println("CLOB retrieved and saved to " + file.getPath());
                        }
                    }
                }
            }
        }
    }
}
```

## Metadata

JDBC provides access to database metadata, which can be useful for dynamically working with databases:

### DatabaseMetaData

```java
import java.sql.Connection;
import java.sql.DatabaseMetaData;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;

public class DatabaseMetaDataExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";
        
        try (Connection connection = DriverManager.getConnection(url, username, password)) {
            
            // Get database metadata
            DatabaseMetaData metaData = connection.getMetaData();
            
            // Database information
            System.out.println("Database Product Name: " + metaData.getDatabaseProductName());
            System.out.println("Database Product Version: " + metaData.getDatabaseProductVersion());
            System.out.println("Driver Name: " + metaData.getDriverName());
            System.out.println("Driver Version: " + metaData.getDriverVersion());
            
            // JDBC/Database features
            System.out.println("\nSupports Transactions? " + metaData.supportsTransactions());
            System.out.println("Supports Batch Updates? " + metaData.supportsBatchUpdates());
            System.out.println("Supports Stored Procedures? " + metaData.supportsStoredProcedures());
            
            // Get tables
            System.out.println("\nTables:");
            ResultSet tables = metaData.getTables(null, null, "%", new String[]{"TABLE"});
            
            while (tables.next()) {
                String tableName = tables.getString("TABLE_NAME");
                String tableType = tables.getString("TABLE_TYPE");
                String remarks = tables.getString("REMARKS");
                
                System.out.println("  " + tableName + " (" + tableType + ")" + 
                                 (remarks != null ? ": " + remarks : ""));
                
                // Get columns for this table
                System.out.println("  Columns:");
                ResultSet columns = metaData.getColumns(null, null, tableName, "%");
                
                while (columns.next()) {
                    String columnName = columns.getString("COLUMN_NAME");
                    String dataType = columns.getString("TYPE_NAME");
                    int columnSize = columns.getInt("COLUMN_SIZE");
                    boolean nullable = columns.getInt("NULLABLE") == DatabaseMetaData.columnNullable;
                    
                    System.out.println("    " + columnName + " - " + dataType + 
                                     "(" + columnSize + ")" + 
                                     (nullable ? " NULL" : " NOT NULL"));
                }
                
                // Get primary keys
                System.out.println("  Primary Keys:");
                ResultSet primaryKeys = metaData.getPrimaryKeys(null, null, tableName);
                
                while (primaryKeys.next()) {
                    String pkName = primaryKeys.getString("PK_NAME");
                    String columnName = primaryKeys.getString("COLUMN_NAME");
                    int keySeq = primaryKeys.getInt("KEY_SEQ");
                    
                    System.out.println("    " + columnName + 
                                     " (Sequence: " + keySeq + ")" + 
                                     (pkName != null ? ", Constraint: " + pkName : ""));
                }
                
                // Get foreign keys
                System.out.println("  Foreign Keys:");
                ResultSet foreignKeys = metaData.getImportedKeys(null, null, tableName);
                
                while (foreignKeys.next()) {
                    String fkName = foreignKeys.getString("FK_NAME");
                    String fkColumnName = foreignKeys.getString("FKCOLUMN_NAME");
                    String pkTableName = foreignKeys.getString("PKTABLE_NAME");
                    String pkColumnName = foreignKeys.getString("PKCOLUMN_NAME");
                    
                    System.out.println("    " + fkColumnName + 
                                     " -> " + pkTableName + "." + pkColumnName + 
                                     (fkName != null ? ", Constraint: " + fkName : ""));
                }
                
                System.out.println();
            }
            
        } catch (SQLException e) {
            System.out.println("Database error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

### ResultSetMetaData

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.sql.SQLException;

public class ResultSetMetaDataExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";
        
        try (Connection connection = DriverManager.getConnection(url, username, password)) {
            
            String query = "SELECT * FROM employees";
            
            try (PreparedStatement pstmt = connection.prepareStatement(query);
                 ResultSet rs = pstmt.executeQuery()) {
                
                // Get metadata from the result set
                ResultSetMetaData metaData = rs.getMetaData();
                
                // Get column count
                int columnCount = metaData.getColumnCount();
                System.out.println("Number of columns: " + columnCount);
                
                // Print column information
                System.out.println("\nColumn Information:");
                for (int i = 1; i <= columnCount; i++) {
                    System.out.println("Column " + i + ":");
                    System.out.println("  Name: " + metaData.getColumnName(i));
                    System.out.println("  Label: " + metaData.getColumnLabel(i));
                    System.out.println("  Type: " + metaData.getColumnTypeName(i));
                    System.out.println("  Type Code: " + metaData.getColumnType(i));
                    System.out.println("  Display Size: " + metaData.getColumnDisplaySize(i));
                    System.out.println("  Precision: " + metaData.getPrecision(i));
                    System.out.println("  Scale: " + metaData.getScale(i));
                    System.out.println("  Schema: " + metaData.getSchemaName(i));
                    System.out.println("  Table: " + metaData.getTableName(i));
                    System.out.println("  Nullable: " + 
                                     (metaData.isNullable(i) == ResultSetMetaData.columnNullable ? 
                                      "Yes" : "No"));
                    System.out.println("  Auto Increment: " + metaData.isAutoIncrement(i));
                    System.out.println("  Case Sensitive: " + metaData.isCaseSensitive(i));
                    System.out.println("  Currency: " + metaData.isCurrency(i));
                    System.out.println("  Read Only: " + metaData.isReadOnly(i));
                    System.out.println("  Searchable: " + metaData.isSearchable(i));
                    System.out.println("  Signed: " + metaData.isSigned(i));
                    System.out.println("  Writable: " + metaData.isWritable(i));
                    System.out.println();
                }
                
                // Print data with dynamic column handling
                System.out.println("Data:");
                
                // Print header
                for (int i = 1; i <= columnCount; i++) {
                    System.out.print(metaData.getColumnLabel(i));
                    if (i < columnCount) {
                        System.out.print(" | ");
                    }
                }
                System.out.println("\n" + "-".repeat(80));
                
                // Print rows
                while (rs.next()) {
                    for (int i = 1; i <= columnCount; i++) {
                        System.out.print(rs.getString(i));
                        if (i < columnCount) {
                            System.out.print(" | ");
                        }
                    }
                    System.out.println();
                }
            }
            
        } catch (SQLException e) {
            System.out.println("Database error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

## Connection Pooling

Connection pooling improves performance by reusing database connections instead of creating new ones for each request. Here's an example using HikariCP, a popular connection pool library:

```java
import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class ConnectionPoolExample {
    public static void main(String[] args) {
        // Configure HikariCP
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl("jdbc:mysql://localhost:3306/mydatabase");
        config.setUsername("root");
        config.setPassword("password");
        config.setMaximumPoolSize(10);
        config.setMinimumIdle(5);
        config.setIdleTimeout(30000);
        config.setConnectionTimeout(10000);
        config.setPoolName("MyHikariPool");
        
        // Create the connection pool
        try (HikariDataSource dataSource = new HikariDataSource(config)) {
            
            System.out.println("Connection pool created");
            
            // Perform database operations using connections from the pool
            for (int i = 0; i < 5; i++) {
                performDatabaseOperation(dataSource, i);
            }
            
            System.out.println("\nConnection pool statistics:");
            System.out.println("Active connections: " + dataSource.getHikariPoolMXBean().getActiveConnections());
            System.out.println("Idle connections: " + dataSource.getHikariPoolMXBean().getIdleConnections());
            System.out.println("Total connections: " + dataSource.getHikariPoolMXBean().getTotalConnections());
            
        } catch (Exception e) {
            System.out.println("Error: " + e.getMessage());
            e.printStackTrace();
        }
    }
    
    private static void performDatabaseOperation(HikariDataSource dataSource, int iteration) {
        String query = "SELECT COUNT(*) FROM employees";
        
        try (Connection connection = dataSource.getConnection();
             PreparedStatement pstmt = connection.prepareStatement(query);
             ResultSet rs = pstmt.executeQuery()) {
            
            if (rs.next()) {
                int count = rs.getInt(1);
                System.out.println("Operation " + iteration + ": Employee count = " + count);
            }
            
        } catch (SQLException e) {
            System.out.println("Database operation error: " + e.getMessage());
        }
    }
}
```

## Best Practices

### 1. Use Connection Pooling

Creating database connections is expensive. Use connection pooling to reuse connections and improve performance.

### 2. Always Close Resources

Always close JDBC resources (Connection, Statement, ResultSet) to prevent resource leaks. Use try-with-resources for automatic resource management.

### 3. Use PreparedStatement

Prefer `PreparedStatement` over `Statement` for:
- Better performance (prepared statements are precompiled)
- Protection against SQL injection attacks
- Easier parameter handling

### 4. Batch Operations

Use batch operations for executing multiple similar SQL statements to reduce network overhead.

### 5. Handle Exceptions Properly

Implement proper exception handling and provide meaningful error messages. Use specific exception types when possible.

### 6. Use Transactions

Use transactions for operations that must be executed as a unit to maintain data integrity.

### 7. Avoid Large Result Sets

Limit the size of result sets to avoid memory issues. Use pagination or filtering to retrieve only necessary data.

### 8. Use Appropriate Data Types

Use the appropriate JDBC data types that match the database column types to avoid conversion issues.

### 9. Avoid String Concatenation for SQL

Don't build SQL queries using string concatenation. Use parameterized queries with `PreparedStatement` instead.

### 10. Consider Using an ORM

For complex applications, consider using an Object-Relational Mapping (ORM) framework like Hibernate or JPA to simplify database operations.

## Common JDBC Patterns

### Data Access Object (DAO) Pattern

The DAO pattern separates the data access logic from the business logic:

```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

// Model class
class Employee {
    private int id;
    private String name;
    private String email;
    private String department;
    private double salary;
    
    // Constructors
    public Employee() {}
    
    public Employee(int id, String name, String email, String department, double salary) {
        this.id = id;
        this.name = name;
        this.email = email;
        this.department = department;
        this.salary = salary;
    }
    
    // Getters and setters
    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    
    public String getDepartment() { return department; }
    public void setDepartment(String department) { this.department = department; }
    
    public double getSalary() { return salary; }
    public void setSalary(double salary) { this.salary = salary; }
    
    @Override
    public String toString() {
        return "Employee{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", email='" + email + '\'' +
                ", department='" + department + '\'' +
                ", salary=" + salary +
                '}';
    }
}

// DAO interface
interface EmployeeDAO {
    Employee findById(int id) throws SQLException;
    List<Employee> findAll() throws SQLException;
    List<Employee> findByDepartment(String department) throws SQLException;
    void save(Employee employee) throws SQLException;
    void update(Employee employee) throws SQLException;
    void delete(int id) throws SQLException;
}

// DAO implementation
class EmployeeDAOImpl implements EmployeeDAO {
    private Connection connection;
    
    public EmployeeDAOImpl(Connection connection) {
        this.connection = connection;
    }
    
    @Override
    public Employee findById(int id) throws SQLException {
        String sql = "SELECT * FROM employees WHERE id = ?";
        
        try (PreparedStatement pstmt = connection.prepareStatement(sql)) {
            pstmt.setInt(1, id);
            
            try (ResultSet rs = pstmt.executeQuery()) {
                if (rs.next()) {
                    return extractEmployeeFromResultSet(rs);
                }
            }
        }
        
        return null;
    }
    
    @Override
    public List<Employee> findAll() throws SQLException {
        List<Employee> employees = new ArrayList<>();
        String sql = "SELECT * FROM employees";
        
        try (PreparedStatement pstmt = connection.prepareStatement(sql);
             ResultSet rs = pstmt.executeQuery()) {
            
            while (rs.next()) {
                employees.add(extractEmployeeFromResultSet(rs));
            }
        }
        
        return employees;
    }
    
    @Override
    public List<Employee> findByDepartment(String department) throws SQLException {
        List<Employee> employees = new ArrayList<>();
        String sql = "SELECT * FROM employees WHERE department = ?";
        
        try (PreparedStatement pstmt = connection.prepareStatement(sql)) {
            pstmt.setString(1, department);
            
            try (ResultSet rs = pstmt.executeQuery()) {
                while (rs.next()) {
                    employees.add(extractEmployeeFromResultSet(rs));
                }
            }
        }
        
        return employees;
    }
    
    @Override
    public void save(Employee employee) throws SQLException {
        String sql = "INSERT INTO employees (name, email, department, salary) VALUES (?, ?, ?, ?)";
        
        try (PreparedStatement pstmt = connection.prepareStatement(sql)) {
            pstmt.setString(1, employee.getName());
            pstmt.setString(2, employee.getEmail());
            pstmt.setString(3, employee.getDepartment());
            pstmt.setDouble(4, employee.getSalary());
            
            pstmt.executeUpdate();
        }
    }
    
    @Override
    public void update(Employee employee) throws SQLException {
        String sql = "UPDATE employees SET name = ?, email = ?, department = ?, salary = ? WHERE id = ?";
        
        try (PreparedStatement pstmt = connection.prepareStatement(sql)) {
            pstmt.setString(1, employee.getName());
            pstmt.setString(2, employee.getEmail());
            pstmt.setString(3, employee.getDepartment());
            pstmt.setDouble(4, employee.getSalary());
            pstmt.setInt(5, employee.getId());
            
            pstmt.executeUpdate();
        }
    }
    
    @Override
    public void delete(int id) throws SQLException {
        String sql = "DELETE FROM employees WHERE id = ?";
        
        try (PreparedStatement pstmt = connection.prepareStatement(sql)) {
            pstmt.setInt(1, id);
            pstmt.executeUpdate();
        }
    }
    
    private Employee extractEmployeeFromResultSet(ResultSet rs) throws SQLException {
        Employee employee = new Employee();
        employee.setId(rs.getInt("id"));
        employee.setName(rs.getString("name"));
        employee.setEmail(rs.getString("email"));
        employee.setDepartment(rs.getString("department"));
        employee.setSalary(rs.getDouble("salary"));
        return employee;
    }
}

// Usage example
public class DAOPatternExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";
        
        try (Connection connection = DriverManager.getConnection(url, username, password)) {
            // Create DAO
            EmployeeDAO employeeDAO = new EmployeeDAOImpl(connection);
            
            // Create a new employee
            Employee newEmployee = new Employee(0, "Alice Brown", "alice.brown@example.com", "Marketing", 65000.0);
            employeeDAO.save(newEmployee);
            System.out.println("Employee saved");
            
            // Find all employees
            List<Employee> allEmployees = employeeDAO.findAll();
            System.out.println("\nAll employees:");
            for (Employee emp : allEmployees) {
                System.out.println(emp);
            }
            
            // Find employees by department
            List<Employee> itEmployees = employeeDAO.findByDepartment("IT");
            System.out.println("\nIT department employees:");
            for (Employee emp : itEmployees) {
                System.out.println(emp);
            }
            
            // Update an employee
            if (!allEmployees.isEmpty()) {
                Employee empToUpdate = allEmployees.get(0);
                empToUpdate.setSalary(empToUpdate.getSalary() * 1.1);  // 10% raise
                employeeDAO.update(empToUpdate);
                System.out.println("\nEmployee updated: " + empToUpdate);
            }
            
            // Find employee by ID
            Employee emp = employeeDAO.findById(1);
            if (emp != null) {
                System.out.println("\nFound employee with ID 1: " + emp);
            }
            
        } catch (SQLException e) {
            System.out.println("Database error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

## Conclusion

JDBC is a powerful API for database connectivity in Java applications. It provides a standard interface for connecting to relational databases, executing SQL statements, and processing results. By following best practices and using appropriate patterns, you can create efficient and maintainable database applications with JDBC.

While JDBC provides low-level control over database operations, for more complex applications, consider using higher-level frameworks like JPA, Hibernate, or Spring Data, which build upon JDBC to provide additional features and simplify database interactions.