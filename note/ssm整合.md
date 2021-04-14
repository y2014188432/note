## 创建 ssm 整合

### 添加依赖

1. Spring MVC

    ```xml
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.0.0.RELEASE</version>
    </dependency>
    ```

2. Spring JDBC

    ```xml
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.3.2</version>
    </dependency>
    ```

3. Spring AOP

    ```xml
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aop</artifactId>
        <version>5.0.0.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aspects</artifactId>
        <version>5.0.0.RELEASE</version>
    </dependency>
    ```

4. MyBatis

    ```xml
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.4.6</version>
    </dependency>
    ```

5. MyBatis 整合 Spring

    ```xml
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>1.3.2</version>
    </dependency>
    ```

6. MySQL 驱动

    ```xml
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.20</version>
    </dependency>
    ```

7. C3P0

    ```xml
    <dependency>
        <groupId>com.mchange</groupId>
        <artifactId>c3p0</artifactId>
        <version>0.9.5.2</version>
    </dependency>
    ```

8. jstl

    ```xml
    <dependency>
        <groupId>jstl</groupId>
        <artifactId>jstl</artifactId>
        <version>1.2</version>
    </dependency>
    ```

9. Servlet API

    ```xml
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
    </dependency>
    ```

10. Lombok

    ```xml
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.6</version>
    </dependency>
    ```

### 配置文件

#### web.xml

> web.xml 中配置 SpringMVC、Spring、字符编码过滤器、加载静态资源。

- 启动 Spring

    ```xml
    <!-- 启动 Spring -->
    <context-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:spring.xml</param-value>
    </context-param>
    <listener>
      <listener-class>org.springframework.web.context.ContextCleanupListener</listener-class>
    </listener>
    ```

- Spring MVC

    ```xml
    <!-- Spring MVC -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:config/springmvc.xml</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    ```

- 字符编码过滤器

    ```xml
    <!-- 字符编码过滤器 -->
    <filter>
      <filter-name>characterEncodingFilter</filter-name>
      <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
      <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
      </init-param>
    </filter>
    <filter-mapping>
      <filter-name>characterEncodingFilter</filter-name>
      <url-pattern>/*</url-pattern>
    </filter-mapping>
    ```

- 加载静态资源

    ```xml
    <servlet-mapping>
      <servlet-name>default</servlet-name>
      <url-pattern>*.[js|css|png|jpg]</url-pattern>
    </servlet-mapping>  
    ```

#### spring.xml

- 配置 MyBatis数据源

    ```xml
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.cj.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/ssm?userUnicode=true&amp;characterEncoding=UTF-8"/>
        <property name="user" value="YSir"/>
        <property name="password" value="001129"/>
        <property name="initialPoolSize" value="5"/>
        <property name="maxPoolSize" value="10"/>
    </bean>
    ```
```
    
- 配置 MyBatis SqlSessionFactory

    ```xml
    <!-- 配置 MyBatis SqlSessionFactory -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="configLocation" value="classpath:config/mybatis.xml"/>
        <property name="mapperLocations" value="classpath:com/y/repository/*.xml"/>
    </bean>
```

- 扫描自定义的 Mapper 接口

    ```xml
    <!-- 扫描自定义的 Mapper 接口 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.y.repository"/>
    </bean>
    ```

#### mybatis.xml

- 可配置 MyBatis 的其他配置，如打印 SQL

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>
        <settings>
            <!-- 开启打印 SQL 语句 -->
            <setting name="LogImpl" value="STDOUT_LOGGING"/>
        </settings>
    
        <typeAliases>
            <!-- 指定一个包名，MyBatis 会在包名下搜索需要的 JavaBean -->
            <package name="com.y.entity"/>
        </typeAliases>
    </configuration>
    ```

#### springmvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
    <!-- 启动注解驱动 -->
    <mvc:annotation-driven/>
    <!-- 扫描业务代码 -->
    <context:component-scan base-package="com.y"/>
    <!-- 配置视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
```