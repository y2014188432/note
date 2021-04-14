## 简述

- Spring Boot 是一个快速开发框架，可以迅速搭建出一套基于 Spring 框架体系的应用，是 Spring Cloud 的基础。
- Spring Boot 开启了各种自动装配，从而简化代码的开发，不需要编写各种配置文件，只需要引入相关依赖就可以迅速搭建一个应用。
- 特点
    1. 不需要 web.xml
    2. 不需要 springmvc.xml
    3. 不需要 tomcat,Spring Boot 内嵌了 tomcat
    4. 不需要配置 JSON 配置，支持 REST 架构
    5. 个性化配置非常简单

## 如何使用

- 创建 Maven 工程

    ```xml
      <!-- 继承父包 -->
        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>2.0.7.RELEASE</version>
        </parent>
    
        <dependencies>
            <!-- web 启动 jar -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
            </dependency>
        </dependencies>
    ```

    == over  ==

    ## Spring Boot 整合 JSP

    - pom.xml

        ```xml
        <parent>
          <groupId>org.springframework.boot</groupId>
          <version>2.0.7.RELEASE</version>
          <artifactId>spring-boot-starter-parent</artifactId>
        </parent>
        <dependencies>
          <!-- web 组件 -->
          <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
          </dependency>
          <!-- 整合 jsp  -->
          <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
          </dependency>
          <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-jasper</artifactId>
          </dependency>
          <!-- jstl -->
          <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
          </dependency>
        </dependencies>
        ```

    - application.yml

        ```yaml
        server:
          port: 8181
        spring:
          mvc:
            view:
              prefix: /
              suffix: .jspdd
        ```

## Spring Boot 整合 HTML

Spring Boot 可以结合 Thymeleaf 模板来整合 HTML ，使用原生的 HTML 作为视图

Thyme leaf 模板是面向 Web 和独立环境的 Java 模板引擎，能够处理 HTML、XML、JavaScript、CSS 等。

- pom.xml

    ```xml
    <!-- thymeleaf -->
    <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

- application.yml

    ```yaml
    server:
      port: 9090
    spring:
      thymeleaf:
        prefix: classpath:/templates/
        suffix: .html
        mode: HTML5
        encoding: UTF-8
    ```

- index.html

    ```html
    <!DOCTYPE html>
    <html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
        <h1>Hello World</h1>
        <table>
            <tr>
                <th>ID</th>
                <th>用户名</th>
                <th>密码</th>
            </tr>
            <tr th:each="user:${users}">
                <td th:text="${user.id}"></td>
                <td th:text="${user.username}"></td>
                <td th:text="${user.password}"></td>
            </tr>
        </table>
    </body>
    </html>
    ```

- Handler

    ```java
    package com.y.controller;
    
    import com.y.entity.User;
    import com.y.service.UserService;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    import java.util.Collection;
    
    @Controller
    @RequestMapping("/index")
    public class TestHandler {
    
        @Autowired
        private UserService userService;
    
        @GetMapping("/index")
        public String index(Model model){
            Collection<User> users = userService.findAll();
            model.addAttribute("users",users);
            return "index";
        }
    
    
    }
    ```

- 启动类

    ```java
    package com.y;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    @SpringBootApplication
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class,args);
        }
    }
    ```

## Thymeleaf 常用语法

### 赋值，拼接

```html
<p th:text="${user}"></p>
<p th:text="'学生的姓名是'+${user}"></p>
<p th:text="|学生的姓名是${user}|"></p>
```

### 条件判断：if / unless

- if 表示条件成立时显示内容

- unless 表示条件不成立时显示内容

- handler

    ```java
    @RequestMapping("/test1")
    public String test1(Map<String,Integer> map){
        map.put("one",1);
        map.put("two",2);
        map.put("three",3);
        return "test1";
    }
    ```

- html

    ```html
    <p th:if="${one == 1}" th:text="if标签测试成功"></p>
    <p th:unless="${two == 1}" th:text="unless标签测试成功"></p>
    ```

### 循环

- handler

    ```java
    @GetMapping("/index")
    public String index(Model model){
        Collection<User> users = userService.findAll();
        model.addAttribute("users",users);
        return "index";
    }
    ```

- html

    ```html
    <tr th:each="user,stat:${users}">
        <td th:text="${user.id}"></td>
        <td th:text="${user.username}"></td>
        <td th:text="${user.password}"></td>
    </tr>
    ```

- index 集合中元素的index（从0开始）

- count 集合中元素的count（从1开始）

- size 集合的大小

- current 当前迭代变量

- even/odd 当前迭代是否为偶数/奇数（从0开始计算）

- first 当前迭代的元素是否是第一个

- last 当前迭代的元素是否是最后一个

### URL

- Thymeleaf 对于 URL 的处理是通过 `@{....}` 进行处理，结合 th:href 、th:src

- ```html
    <a th:href="@{http://www.baidu.com}">跳转百度</a>
    <a th:href="@{'http://localhost:9090/index/test3?name='+${name}}">跳转Test3</a>
    <div th:style="|background:url(${url})|"></div>
    ```

### 三元运算

- handler

    ```java
    @GetMapping("/eq")
    public String eq(Model model){
        model.addAttribute("age",30);
        return "test";
    }
    ```

- html

    ```html
    <input th:value="${age gt 30?'中年':'青年'}"/>
    ```

- gt great than 大于

- ge great equal 大于等于

- eq equal 等于

- lt less than 小于

- le less equal 小于等于

- ne not equal 不等于

### swith

- handler

    ```java
    @GetMapping("/switch")
    public String switchTest(Model model){
        model.addAttribute("gender","女");
        return "test";
    }
    ```

- html

    ```html
    <div th:switch="${gender}">
      <p th:case="女">女</p>
      <p th:case="男">男</p>
      <p th:case="*">未知</p>
    </div>
    ```

### 基本对象

- `#ctx` ：上下文对象
- `#vars`：上下文变量
- `#locale`：区域对象
- `#request`：HttpServletRequest 对象
- `#response`：HttpServletResponse 对象
- `#session`：HttpSession 对象
- `#servletContext`：ServletContext 对象

### 内嵌对象

> 可以直接通过 # 访问。

- dates：java.util.Date 的功能方法

- calendars：java.util.Calendar 的功能方法

- numbers：格式化数字

- strings：java.lang.String 的功能方法

- objects：Object 的功能方法

- bools：对布尔求值的方法

- arrays：操作数组的功能方法

- lists：操作集合的功能方法

- sets：操作集合的功能方法

- maps：操作集合的功能方法

- handler

    ```java
    @GetMapping("/util")
    public String util(Model model){
        model.addAttribute("name","zhangsan");
        model.addAttribute("users",new ArrayList<>());
        model.addAttribute("count",22);
        model.addAttribute("date",new Date());
        return "test";
    }
    ```

- html

    ```html
    <!-- 格式化时间 -->
    <p th:text="${#dates.format(date,'yyyy-MM-dd HH:mm:sss')}"></p>
    <!-- 创建当前时间，精确到天 -->
    <p th:text="${#dates.createToday()}"></p>
    <!-- 创建当前时间，精确到秒 -->
    <p th:text="${#dates.createNow()}"></p>
    <!-- 判断是否为空 -->
    <p th:text="${#strings.isEmpty(name)}"></p>
    <!-- 判断List是否为空 -->
    <p th:text="${#lists.isEmpty(users)}"></p>
    <!-- 输出字符串长度 -->
    <p th:text="${#strings.length(name)}"></p>
    <!-- 拼接字符串 -->
    <p th:text="${#strings.concat(name,name,name)}"></p>
    <!-- 创建自定义字符串 -->
    <p th:text="${#strings.randomAlphanumeric(count)}"></p>
    ```

## Spring Boot 数据校验

```java
package com.southwind.entity;

import lombok.Data;
import org.hibernate.validator.constraints.Length;

import javax.validation.constraints.Min;
import javax.validation.constraints.NotEmpty;
import javax.validation.constraints.NotNull;

@Data
public class User {
    @NotNull(message = "id不能为空")
    private Long id;
    @NotEmpty(message = "姓名不能为空")
    @Length(min = 2,message = "姓名长度不能小于2位")
    private String name;
    @Min(value = 16,message = "年龄必须大于16岁")
    private int age;
}
```

```java
@GetMapping("/validator")
public void validatorUser(@Valid User user,BindingResult bindingResult){
  System.out.println(user);
  if(bindingResult.hasErrors()){
    List<ObjectError> list = bindingResult.getAllErrors();
    for(ObjectError objectError:list){
      System.out.println(objectError.getCode()+"-"+objectError.getDefaultMessage());
    }
  }
}
```

## Spring Boot  整合 MyBatis

### 所需依赖

- mysql 连接 JDBC

    ```xml
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.20</version>
    </dependency>
    ```

- mybatis 整合 springboot 专用包（mybatis 公司出品）

    ```xml
    <!-- springboot 整合 MyBatis -->
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>1.3.2</version>
    </dependency>
    ```

### 配置数据库连接

```yaml
spring:
  datasource:
    username: YSir
    password: "001129"
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/mybatis001
    #注：密码为纯数字时用  ---《《《 双引号 》》》---  包围
```

### 配置 MyBatis 基本配置

```yaml
mybatis:
  # 扫描的 mapper 类目录
  mapper-locations: classpath:/mapper/*.xml
```

### Mapper 的相关要求

- mapper 的名字和 持久层接口的名字应当相同
- mapper 的 namespace 应该为对应的接口路径

### 启动类的要求

- 添加如下注解

    ```java
    @MapperScan("com.y.repository")
    ```

## Spring Boot 整合 Spring Data JPA

- 所需依赖

    ```xml
    <!-- Spring Data JPA -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <!-- JDBC -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.11</version>
    </dependency>
    ```

- 持久层

    ```java
    package com.y.repository;
    
    import com.y.entity.Goods;
    import org.springframework.data.jpa.repository.JpaRepository;
    
    public interface GoodsRepository extends JpaRepository<Goods,Integer> {
    }
    ```

- 实体类

    ```java
    package com.y.entity;
    
    import lombok.Data;
    import org.hibernate.annotations.Proxy;
    
    import javax.persistence.*;
    
    @Data
    @Entity(name = "goods")
    @Proxy(lazy = false)
    public class Goods {
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        @Column(name = "g_id")
        private long id;
        @Column(name = "g_name")
        private String name;
    }
    ```

- Handler

    ```java
    package com.y.controller;
    
    import com.y.entity.Goods;
    import com.y.repository.GoodsRepository;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    import java.util.List;
    
    @RestController
    @RequestMapping("/test")
    public class GoodsHandler {
    
        @Autowired
        private GoodsRepository goodsRepository;
    
        @RequestMapping("/findAll")
        public List<Goods> findAll(){
            return goodsRepository.findAll();
        }
    }
    ```

- 配置文件

    ```yaml
    server:
      port: 8080
    spring:
      datasource:
        username: YSir
        password: "001129"
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://localhost:3306/mybatis001
      jpa:
        show-sql: true
        properties:
          hiberties:
            format_sql: true
    ```

- 启动类

    ```java
    package com.y;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    @SpringBootApplication
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class,args);
        }
    }
    ```

## Spring Boot 整合 Mongo DB

## Spring Boot 整合 Spring Security

> 客户端权限控制



### ss





