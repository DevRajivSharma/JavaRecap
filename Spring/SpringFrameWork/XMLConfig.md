# XML Configuration — Demo & Reference

## Demo Classes (Plain Java)

```java
package com.demo;

public class Repository {
    public String findName() { return "world"; }
}

public class Service {
    private final Repository repository;
    public Service(Repository repository) { this.repository = repository; }
    public String greet(String prefix) { return prefix + ", " + repository.findName(); }
}

public class Client {
    private Service service;
    private String prefix;
    public void setService(Service service) { this.service = service; }
    public void setPrefix(String prefix) { this.prefix = prefix; }
    public String call() { return service.greet(prefix); }
}

public class Config {
    private java.util.List<String> servers;
    private java.util.Map<String, String> settings;
    public void setServers(java.util.List<String> servers) { this.servers = servers; }
    public void setSettings(java.util.Map<String, String> settings) { this.settings = settings; }
    public java.util.List<String> getServers() { return servers; }
    public java.util.Map<String, String> getSettings() { return settings; }
}

public class Factory {
    public static Service createService(Repository repo) { return new Service(repo); }
}

public class LifecycleBean {
    public void init() {}
    public void close() {}
}
```

## Root `<beans>` with Namespaces

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="
         http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd
         http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- examples below can be placed here -->

</beans>
```

## Wiring Examples

Constructor injection:

```xml
<bean id="repository" class="com.demo.Repository"/>
<bean id="service" class="com.demo.Service">
    <constructor-arg ref="repository"/>
    <!-- or c:repo-ref if constructor name params exist -->
</bean>
```

Setter injection:

```xml
<bean id="client" class="com.demo.Client">
    <property name="service" ref="service"/>
    <property name="prefix" value="Hello"/>
</bean>
```

`p:` and `c:` namespace shortcuts:

```xml
<bean id="clientP" class="com.demo.Client" p:service-ref="service" p:prefix="Hi"/>
<bean id="serviceC" class="com.demo.Service" c:0-ref="repository"/>
```

Autowiring (XML):

```xml
<bean id="autoService" class="com.demo.Service" autowire="constructor"/>
<bean id="autoClient" class="com.demo.Client" autowire="byType"/>
```

Nested beans:

```xml
<bean id="clientNested" class="com.demo.Client">
    <property name="service">
        <bean class="com.demo.Service">
            <constructor-arg>
                <bean class="com.demo.Repository"/>
            </constructor-arg>
        </bean>
    </property>
    <property name="prefix" value="Hey"/>
</bean>
```

Factory method:

```xml
<bean id="repository2" class="com.demo.Repository"/>
<bean id="serviceFromFactory" class="com.demo.Factory" factory-method="createService">
    <constructor-arg ref="repository2"/>
    <!-- when factory-method is static, args are for the method -->
</bean>
```

Lifecycle and lazy-init:

```xml
<bean id="life" class="com.demo.LifecycleBean" init-method="init" destroy-method="close" lazy-init="true"/>
```

Depends-on:

```xml
<bean id="initRepo" class="com.demo.Repository"/>
<bean id="serviceDepends" class="com.demo.Service" depends-on="initRepo">
    <constructor-arg ref="initRepo"/>
</bean>
```

Scopes:

```xml
<bean id="singletonService" class="com.demo.Service" scope="singleton">
    <constructor-arg ref="repository"/>
</bean>
<bean id="prototypeService" class="com.demo.Service" scope="prototype">
    <constructor-arg ref="repository"/>
</bean>
```

Collections:

```xml
<bean id="config" class="com.demo.Config">
    <property name="servers">
        <list>
            <value>srv-1</value>
            <value>srv-2</value>
        </list>
    </property>
    <property name="settings">
        <map>
            <entry key="region" value="eu"/>
            <entry key="retries" value="3"/>
        </map>
    </property>
</bean>
```

Property placeholders:

```xml
<context:property-placeholder location="classpath:app.properties"/>
<bean id="clientProps" class="com.demo.Client" p:prefix="${client.prefix}" p:service-ref="service"/>
```

Profiles:

```xml
<beans profile="dev">
    <bean id="repository" class="com.demo.Repository"/>
</beans>
<beans profile="prod">
    <bean id="repository" class="com.demo.Repository"/>
</beans>
```

Imports and aliases:

```xml
<import resource="classpath:extra-context.xml"/>
<alias name="service" alias="mainService"/>
```

## Example `applicationContext.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="
         http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <context:property-placeholder location="classpath:app.properties"/>

    <bean id="repository" class="com.demo.Repository"/>

    <bean id="service" class="com.demo.Service">
        <constructor-arg ref="repository"/>
    </bean>

    <bean id="client" class="com.demo.Client" p:service-ref="service" p:prefix="Hello"/>

    <bean id="config" class="com.demo.Config">
        <property name="servers">
            <list><value>a</value><value>b</value></list>
        </property>
        <property name="settings">
            <map><entry key="mode" value="demo"/></map>
        </property>
    </bean>

    <bean id="life" class="com.demo.LifecycleBean" init-method="init" destroy-method="close"/>

</beans>
```

Sample `app.properties`:

```properties
client.prefix=HelloFromProps
```

## Use In Main Application

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
        ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
        com.demo.Client client = ctx.getBean("client", com.demo.Client.class);
        String result = client.call();
        ctx.close();
    }
}
```

## Quick Recipes

- Constructor injection: `<constructor-arg ref="..."/>`
- Setter injection: `<property name="..." ref|value="..."/>`
- Shortcuts: `p:prop="..."`, `p:bean-ref="..."`, `c:0-ref="..."`
- Collections: `<list>`, `<set>`, `<map>`, `<props>`
- Lifecycle: `init-method`, `destroy-method`, `lazy-init`
- Scope: `singleton`, `prototype`
- Profiles: `<beans profile="dev">...` and `<beans profile="prod">...`

## Root `<beans>` and Namespaces

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
         http://www.springframework.org/schema/beans
         https://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/context
         https://www.springframework.org/schema/context/spring-context.xsd
         http://www.springframework.org/schema/aop
         https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- beans go here -->

</beans>
```

## Define Beans

```xml
<bean id="myBean" class="com.example.MyBean" scope="singleton"
      init-method="init" destroy-method="close"/>
```

Attributes: `id`, `name`, `class`, `scope`, `lazy-init`, `init-method`, `destroy-method`, `factory-method`, `factory-bean`.

## Inject Dependencies

Constructor injection:

```xml
<bean id="service" class="com.example.Service">
    <constructor-arg ref="repository"/>
</bean>
<bean id="repository" class="com.example.Repository"/>
```

Setter/property injection:

```xml
<bean id="service" class="com.example.Service">
    <property name="repository" ref="repository"/>
</bean>
```

Nested beans and values:

```xml
<bean id="client" class="com.example.Client">
    <property name="endpoint" value="https://api"/>
    <property name="timeout" value="2000"/>
</bean>
```

## Collections

```xml
<bean id="config" class="com.example.Config">
    <property name="servers">
        <list>
            <value>server1</value>
            <value>server2</value>
        </list>
    </property>
    <property name="settings">
        <map>
            <entry key="retries" value="3"/>
            <entry key="region" value="eu"/>
        </map>
    </property>
    <property name="props">
        <props>
            <prop key="k1">v1</prop>
        </props>
    </property>
    <property name="unique">
        <set>
            <value>a</value>
            <value>b</value>
        </set>
    </property>
</bean>
```

## Property Placeholders

```xml
<context:property-placeholder location="classpath:app.properties"/>
<bean id="ds" class="org.apache.commons.dbcp2.BasicDataSource">
    <property name="url" value="${db.url}"/>
    <property name="username" value="${db.user}"/>
    <property name="password" value="${db.pass}"/>
</bean>
```

## Component Scan (optional)

```xml
<context:component-scan base-package="com.example"/>
```

## Profiles

```xml
<beans profile="dev">
    <bean id="cache" class="com.example.InMemoryCache"/>
</beans>
<beans profile="prod">
    <bean id="cache" class="com.example.RedisCache"/>
</beans>
```

## Imports and Aliases

```xml
<import resource="classpath:datasource.xml"/>
<alias name="service" alias="mainService"/>
```

## Full Example

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

    <bean id="repository" class="com.example.Repository"/>

    <bean id="service" class="com.example.Service">
        <constructor-arg ref="repository"/>
    </bean>

    <bean id="app" class="com.example.App">
        <property name="service" ref="service"/>
    </bean>

</beans>
```
