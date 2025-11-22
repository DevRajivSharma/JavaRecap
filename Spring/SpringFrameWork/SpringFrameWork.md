# Spring Framework — Classic (XML) Notes

## What It Is

- Spring Framework manages your Java objects and wires them together using XML or annotations.
- These notes focus on classic XML configuration (no Spring Boot).

## Why Use XML Config

- Explicit bean definitions and wiring.
- Clear separation of concerns across layers.
- Easy to understand application startup and dependencies.

## Major Concepts (Docs)

- XML Configuration Reference: [XMLConfig.md](XMLConfig.md)
- Core Container: [CoreContainer.md](CoreContainer.md)
- AOP: [AOP.md](AOP.md)
- MVC: [MVC.md](MVC.md)
- Data Access: [DataAccess.md](DataAccess.md)
- Security: [Security.md](Security.md)
- Testing: [Testing.md](Testing.md)

## Simple Project Structure (XML)

- `src/main/resources/applicationContext.xml` — main bean definitions
- `src/main/java/...` — application classes

## Create `applicationContext.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
         http://www.springframework.org/schema/beans
         https://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/context
         https://www.springframework.org/schema/context/spring-context.xsd">

    <context:property-placeholder location="classpath:app.properties"/>

    <bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource">
        <property name="url" value="${db.url}"/>
        <property name="username" value="${db.user}"/>
        <property name="password" value="${db.pass}"/>
    </bean>

    <bean id="repository" class="com.example.Repository">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <bean id="service" class="com.example.Service">
        <constructor-arg ref="repository"/>
    </bean>

</beans>
```

## Use In Main Application

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
        com.example.Service service = ctx.getBean("service", com.example.Service.class);
        ((ClassPathXmlApplicationContext) ctx).close();
    }
}
```

## Quick Notes

- Prefer constructor injection for mandatory dependencies.
- Externalize settings in properties and load via `context:property-placeholder`.
- Use profiles in XML if needed with `<beans profile="dev">` blocks.
