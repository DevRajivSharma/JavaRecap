# Java Environment Setup

## Installing JDK (Java Development Kit)

1. Download the latest JDK from the [Oracle website](https://www.oracle.com/java/technologies/javase-downloads.html) or use OpenJDK.
2. Install the JDK by following the installation instructions for your operating system.
3. Set up the environment variables:
   - Set `JAVA_HOME` to the JDK installation directory.
   - Add `%JAVA_HOME%\bin` (Windows) or `$JAVA_HOME/bin` (Unix/Linux) to the `PATH` variable.

## Verifying Installation

```java
// Check Java version
java -version
javac -version
```

## Integrated Development Environments (IDEs)

While you can write Java code in any text editor, using an IDE can significantly improve productivity:

### Eclipse

1. Download Eclipse from [eclipse.org](https://www.eclipse.org/downloads/).
2. Install and launch Eclipse.
3. Select a workspace directory.
4. Create a new Java project: File > New > Java Project.

### IntelliJ IDEA

1. Download IntelliJ IDEA from [jetbrains.com](https://www.jetbrains.com/idea/download/) (Community Edition is free).
2. Install and launch IntelliJ IDEA.
3. Create a new Java project: File > New > Project.

### NetBeans

1. Download NetBeans from [netbeans.apache.org](https://netbeans.apache.org/download/index.html).
2. Install and launch NetBeans.
3. Create a new Java project: File > New Project > Java Application.

### Visual Studio Code

1. Download VS Code from [code.visualstudio.com](https://code.visualstudio.com/).
2. Install the Java Extension Pack from the Extensions marketplace.
3. Create a new Java project using the Java Project Explorer.

## Your First Java Program

1. Create a new file named `HelloWorld.java`.
2. Add the following code:

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

3. Compile the program:

```
javac HelloWorld.java
```

4. Run the program:

```
java HelloWorld
```

## Build Tools

### Maven

1. Download Maven from [maven.apache.org](https://maven.apache.org/download.cgi).
2. Set up environment variables:
   - Set `M2_HOME` to the Maven installation directory.
   - Add `%M2_HOME%\bin` (Windows) or `$M2_HOME/bin` (Unix/Linux) to the `PATH` variable.
3. Create a new Maven project:

```
mvn archetype:generate -DgroupId=com.example -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

### Gradle

1. Download Gradle from [gradle.org](https://gradle.org/releases/).
2. Set up environment variables:
   - Set `GRADLE_HOME` to the Gradle installation directory.
   - Add `%GRADLE_HOME%\bin` (Windows) or `$GRADLE_HOME/bin` (Unix/Linux) to the `PATH` variable.
3. Create a new Gradle project:

```
gradle init --type java-application
```

## Java Version Management

### SDKMAN (Unix/Linux/macOS)

1. Install SDKMAN:

```
curl -s "https://get.sdkman.io" | bash
```

2. Install a specific Java version:

```
sdk install java 17.0.2-open
```

### Jabba (Cross-platform)

1. Install Jabba:

```
curl -sL https://github.com/shyiko/jabba/raw/master/install.sh | bash
```

2. Install a specific Java version:

```
jabba install openjdk@17.0.2
```