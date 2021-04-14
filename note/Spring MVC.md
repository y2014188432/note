

### Spring MVC

Spring MVC是目前最主流的实现 MVC 设计模式的企业级开发框架， Spring 框架的一个子模块，无需整合。

### 什么是MVC设计模式

将应用程序分为 Controller、Model、View三层，Controller 接受客户端请求，调用 Model 生成业务数据，传递给 View

Spring MVC  就是对这套流程的封装，屏蔽了很多底层代码，开放出接口，让开发者可以更加轻松、便捷的完成基于 MVC 模式的 Web 开发。

![image-20201125151918033](F:\.Data\Spring MVC-01)

### Spring MVC的核心组件

- DispatcherServlet：前置指挥器，是整个流程控制的核心，控制其他组件的执行，进行统一调度，降低组件之间的耦合性，相当于总指挥。`【调度员、小服务程序】`
- Handler：处理器，完成具体的业务逻辑，相当于Servlet或Action
- HandlerMapping：  处理器映射器，DispatcherServlet接受到请求之后，通过HandlerMapping将不同的请求映射到不同的Handler
- HandlerInterceptor：处理器拦截器，是一个接口，如果需要完成一些拦截处理，可以实现该接口。
- HandlerExecutionChain：处理器执行链，包括两部分内容：Handler和HandlerInterceptor（系统会有一个默认的HandlerInterceptor，如果需要额外设置拦截，可以添加拦截器）。
- HandlerAdapter：处理器适配器，Handler执行业务方法之前，需要进行一系列的操作，包括表单数据的验证，数据类型的转换，将表单数据封装到JavaBean等，这些操作都是由HandlerApater来完成，开发者只需将注意力集中在业务逻辑的处理上，DispatcherServlet通过HandlerAdapter执行不同的Handler。
- ModelAndView：装载了模型数据和视图信息，作为Handler的处理结果，返回给DispatchServlet。
- ViewResolver：视图解析器，DispatchServlet通过他将逻辑视图解析为物理视图，最终将渲染结果相应给客户端。

### Spring MVC的工作流程

- 客户端请求被 DisptacherServlet 接收。

- 根据 HandlerMapping 映射到 Handler。

- 生成 Handler 和 HandlerInterceptor 。

- Handler 和 HandlerInterceptor 以 HandlerExecutionChain 形式一并返回给 DisptacherServlet。

- DispatcherServlet 通过 HandlerAdapter 调用 Handler 的方法完成业务逻辑处理。

- Handler 返回一个 ModelAndView 给 DispatcherServlet。

- DispatcherServlet 将获取的 ModelAndView 对象传给 ViewResolver 视图解析器，将逻辑视图解析为物理视图 View。

- ViewResovler 返回一个 View 给 DispatcherServlet。

- DispatchSerclet 根据 View 进行视图渲染（将模型数据 Model 填充到视图 View 中）。

- DispatcherServlet 将渲染后的结果相应给客户端。

    ![image-20201125160930915](F:\.Data\Spring MVC-02)

### 如何使用

- 创建 Maven 工程，pom.xml

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.0.0.RELEASE</version>
  </dependency>
</dependencies>
```

- 在 web.xml 中配置 DispatcherServlet。

    ```xml
    <!DOCTYPE web-app PUBLIC
     "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
     "http://java.sun.com/dtd/web-app_2_3.dtd" >
    
    <web-app>
      <display-name>Archetype Created Web Application</display-name>
      
      <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
          <param-name>contextConfigLocation</param-name>
          <param-value>classpath:springMVC.xml</param-value>
        </init-param>
      </servlet>
    
      <!--映射-->
      <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
      </servlet-mapping>
    </web-app>
    ```

- springMVC.xml 配置

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
        <!--  自动扫描 -->
        <context:component-scan base-package="com.y"></context:component-scan>
    
        <!--  视图解析器 -->
        <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
            <property name="prefix" value="/"></property>
            <property name="suffix" value=".jsp"></property>
        </bean>
    </beans>
    ```

- 创建Handler

    ```java
    @Controller
    public class FirstMvc {
        @RequestMapping("/index")
        public String index(){
            System.out.println("执行了index...");
            return "index";
        }
    }
    ```

### Spring MVC 注解

- @RequestMapping

    Spring MVC 通过 @RequestMapping 注解将 URL 请求与业务方法进行映射，在 Handler 的类定义处以及方法定义处都可以添加 @ResquestMapping ，在类定义处添加，相当于客户端多了一层访问路径。

- @Controler

    @Controler 在类定义处添加，将该类交于 IOC 容器来管理（结合 springmvc.xml 的自动扫描配置使用），同时使其称为一个控制器，可以接收客户端请求。

    ```java
    @Controller
    @RequestMapping("/hello")
    public class FirstMvc {
        @RequestMapping("/index")
        public String index(){
            System.out.println("执行了index...");
            return "index";
        }
    }
    ```

- @RequestMapping 相关参数

    - value ：指定 URL 请求的实际地址，是 @RequestMapping 的默认值。

    ```java
    @RequestMapping("/index")
    public String index(){
        System.out.println("执行了index...");
        return "index";
    }
    //上写同等
    @RequestMapping(value="/index")
    public String index(){
        System.out.println("执行了index...");
        return "index";
    }
    ```

    - method：指定请求的 method 类型，GET、POST、PUT、DELET。

    ```java
    @RequestMapping(value="/index",method = RequestMethod.GET)
    public String index(){
        System.out.println("执行了index...");
        return "index";
    }
    ```

    上述代码表示 index 方法只能接收 GET 请求

    - params：指定请求中必须包含某些参数，否则无法调用该方法。

    ```java
    @RequestMapping(value="/index",method = RequestMethod.GET,params = {"name","id = 10"})
    public String index(){
        System.out.println("执行了index...");
        return "index";
    }
    ```

    上述代码表示请求中必须包含 name 和 id 两个参数，同时 id 的值必须是 10。

    关于参数绑定，在形参列表中，通过添加 @RequestParam 注解完成 HTTP 请求参数与业务方法形参的映射，也可以设置形参与传递参数一样达到同样的效果。

    ```java
    @RequestMapping(value = "/index",params = {"name","id"})
    public String index(@RequestParam("name") String str,@RequestParam("id") int age){
        System.out.println(str+"  "+age);
        System.out.println("执行了index...");
        return "index";
    }
    //上下同等
    @RequestMapping(value = "/index",params = {"name","id"})id
    public String index(String name,int id){
        System.out.println(str+"  "+age);
        System.out.println("执行了index...");
        return "index";
    }
    ```

    上述代码表示将参数 name 和 id 分别付给形参 str 和 age ，同时自动完成了数据类型转换。

    Spring MVC 也支持 RESTful 风格的 URl。

    传统类型：http://localhost:8080/hello/index?name=zhangsan&id=10

    REST类型：http://localhost:8080/hello/index/zhangsan/10

    ```java
    @RequestMapping("/rest/{name}/{id}")
    public String rest(@PathVariable("name") String name,@PathVariable("id") int id){
        System.out.println(name+" "+id);
        return "index";
    }
    ```

    通过 @pathVariable 注解完成请求参数与形参的映射。

- 映射 Cookie

Spring MVC 通过映射可以直接在业务方法中获取 Cookic 的值。

```java
@RequestMapping("/cookie")
public String cookie(@CookieValue(value = "JSESSIONID") String cookieId){
    System.out.println("cookieId=  "+cookieId);
    return "index";
}
```

- 使用 JavaBean 绑定参数

Java MVC 会根据请求参数和 JavaBean 属性名进行自动匹配，自动为对象填充属性，同时支持级联属性。

//Java User 类

```java
import lombok.Data;
@Data
public class User {
    private long id;
    private String name;
    private AddRess addRess;
}
```

//Java AddRess 类

```java
import lombok.Data;
@Data
public class AddRess {
    private String value;
}
```

//Handle

```java
@RequestMapping(value = "/save",method = RequestMethod.POST)
public String save(User user){
    System.out.println(user);
    return "index";
}
```

//register.jsp

```html
<body>
<form action="/hello/save" method="POST">
    用户id:<input type="text" name="id"/><br/>
    用户名:<input type="text" name="name"/><br/>
    地 址 :<input type="text" name="addRess.value"/>
    <input type="submit" value="注册"/>
</form>
</body>
```

//关于中文乱码的过滤器（web.xml）

```xml
<filter>
  <filter-name>encodingFilter</filter-name>
  <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
  <init-param>
    <param-name>encoding</param-name>
    <param-value>UTF-8</param-value>
  </init-param>
</filter>
<filter-mapping>
  <filter-name>encodingFilter</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```

//关于 @Data 的依赖（pom.xml）

```xml
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
  <version>1.18.6</version>
  <scope>provided</scope>
</dependency>
```

- JSP 页面的转发以及重新定向

Spring MVC 默认是以转发形式相应 jsp

1、转发（地址栏不变，相当于只请求服务器一遍）

```java
@RequestMapping("/forward")
public String forward(){
    return "forward:/index.jsp";
}
```

2、重定向（地址栏改变，相当于再次请求服务器）

```java
@RequestMapping("/redirect")
public String redirect(){
    return "redirect:/index.jsp";
}
```

### Spring MVC 数据绑定

- 当类中全部方法都需要加注解 @ResponseBody 时，将 @Controler 注解改为 @RestControler 即可

```java
@Controller
@RequestMapping("/hello")
public class FirstMvc {
    @RequestMapping("/packageType")
    @ResponseBody
    public String packageType(@RequestParam(value = "num",required = false,defaultValue = "0") Integer id){
        return id+"";
    }

    @RequestMapping("/array")
    @ResponseBody
    public String array(String[] name){
        return Arrays.toString(name);
    }
}
//上下同等
@RestController
@RequestMapping("/hello")
public class FirstMvc {
    @RequestMapping("/packageType")
    public String packageType(@RequestParam(value = "num",required = false,defaultValue = "0") Integer id){
        return id+"";
    }

    @RequestMapping("/array")
    public String array(String[] name){
        return Arrays.toString(name);
    }
}
```

- 基本数据绑定

```java
@RequestMapping("/baseType")
@ResponseBody
public String baseType(int id){
    return id+"";
}
```

@ResponseBody 表示 Spring MVC 会直接将业务方法返回给客户端，如果不加 @ResponseBody 注解，Spring MVC 会将业务方法的返回值传递给 DispatcherServlet  ， 在由 DispatcherServlet 调用 ViewResolver 对返回值进行解析，映射到一个 JSP 资源。

- 包装类

```java
@RequestMapping("/packageType")
@ResponseBody
public String packageType(@RequestParam(value = "num",required = false,defaultValue = "0")Integer id){
    return id+"";
}
```

包装类允许传递参数为空值，同时上述代码中默认值为0

value =“num” ：将 HTTP 请求中名为 num 的参数赋值给形参 id

required ：设置 num 是否为必填项，true 表示必填，false 表示可不填，可以省略该项。

defaultValue ：设置形参的默认值。

- 数组

```java
@RequestMapping("/array")
@ResponseBody
public String array(String[] name){
    return Arrays.toString(name);
}
```

- List

Spring MVC 不支持 List 类型的直接转换，需要对 List 集合进行封装。

集合封装类

```java
import lombok.Data;
import java.util.List;
@Data
public class UserList {
    private List<User> users;

}
```

JSP

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="/hello/list" method="post">
        <label>
            用户1、     id：<input type="text" name="users[0].id"/><br/>
            用户1、   name：<input type="text" name="users[0].name"/><br/>
            用户2、     id：<input type="text" name="users[1].id"/><br/>
            用户2、   name：<input type="text" name="users[1].name"/><br/>
            用户3、     id：<input type="text" name="users[2].id"/><br/>
            用户3、   name：<input type="text" name="users[2].name"/><br/>
        </label>
        <input  type="submit" value="提交"/>
    </form>
</body>
</html>
```

业务方法

```java
@RequestMapping("/list")
@ResponseBody
public String list(UserList userList){
    StringBuilder str= new StringBuilder(" ");
    for(User user:userList.getUsers()){
        str.append(user);
    }
    return str.toString();
}
```

处理 @ResponseBody 中文乱码问题，在 SpringMvc 中配置消息转换器。

```xml
<mvc:annotation-driven>
    <!--消息转换器-->
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <property name="supportedMediaTypes" value="text/html;charset=UTF-8" >
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```

- Map

自定义封装类

```java
import com.y.User;
import lombok.Data;
import java.util.Map;
@Data
public class UserMap {
    private Map<String, User> users;
}
```

业务方法

```java
@RequestMapping("/map")
@ResponseBody
public String map(UserMap userMap){
    StringBuilder str=new StringBuilder(" ");
    for(String key:userMap.getUsers().keySet()){
        User user=userMap.getUsers().get(key);
        str.append(user);
    }
    return str.toString();
}
```

JSP

```xml
<form action="/hello/map" method="post">
    <label>
        用户1、     id：<input type="text" name="users['a'].id"/><br/>
        用户1、   name：<input type="text" name="users['a'].name"/><br/>
        用户2、     id：<input type="text" name="users['b'].id"/><br/>
        用户2、   name：<input type="text" name="users['b'].name"/><br/>
        用户3、     id：<input type="text" name="users['c'].id"/><br/>
        用户3、   name：<input type="text" name="users['c'].name"/><br/>
    </label>
    <input  type="submit" value="提交"/>
</form>
```

- JSON

客户端发送 JSON 格式数据，直接通过 Spring MVC 绑定到业务方法的形参中去。

处理 Spring MVC 无法加载静态资源时，在 web.xml 中添加配置即可。

```xml
<servlet-mapping>
  <servlet-name>default</servlet-name>
  <url-pattern>*.js</url-pattern>
</servlet-mapping>
```

业务方法

```java
@RequestMapping("/json")
@ResponseBody
public User json(@RequestBody User user){
    System.out.println(user);
    user.setId("6");
    user.setName("李四");
    return user;
}
```

Spring MVC 中的 JSON  和JavaBean 的转换需要借助 fastjson ，pom.xml 引入相关依赖。

```xml
<dependency>
  <groupId>com.alibaba</groupId>
  <artifactId>fastjson</artifactId>
  <version>1.2.75</version>
</dependency>
```

springmvc.xml 中添加 fastjson 配置。

```xml
<bean class="com.alibaba.fastjson.support.spring.FastJsonContainer"/>
```

JSP

```xml
<%--
  Created by IntelliJ IDEA.
  User: Y2014188432
  Date: 2021/1/10
  Time: 13:13
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
    <script type="text/javascript" src="js/jquery-3.3.1.min.js"></script>
    <script type="text/javascript">
        $(function () {
            var user={
                "id":"1",
                "name":"张三"
            };
            $.ajax({
                url:"/test1/json",
                data:JSON.stringify(user),
                type:"POST",
                contentType:"application/json",
                dataType:"JSON",
                success:function (data) {
                    alert(data.name+"----"+data.id)
                }
            });
        });
    </script>
</head>
<body>

</body>
</html>

```

### Spring MVC 模型数据解析

JSP 四大作用域对应的内置对象：怕个Context、request、session、application。

模型数据的绑定是由 ViewResolver 来完成的，实际开发中，我们需要先添加模型数据，在交给 ViewResolver 来绑定。

Spring MVC 提供了以下几种方式添加模型数据：

- Map
- Model
- ModelAndView
- @SessionAttribute

> 将模型数据绑定到 request 对象

1、Map

```java
@RequestMapping("/map")
public String map(Map<String, User> map){
    User user=new User();
    user.setId(1);
    user.setName("张三");
    map.put("User",user);
    return "view";
}
```

JSP

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page isELIgnored="false" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
${requestScope.user}

</body>
</html>
```

2、Model

```java
@RequestMapping("/model")
public String model(Model model){
    User user=new User();
    user.setId(2L);
    user.setName("李四");
    model.addAttribute("user",user);
    return "view";
}
```

JSP

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page isELIgnored="false" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
${requestScope.user}

</body>
</html>
```

3、ModelAndView

```java
@RequestMapping("/modelAndView")
public ModelAndView modelAndView(){
    User user=new User();
    user.setName("ModelAndView_1");
    user.setId(1L);
    ModelAndView modelAndView=new ModelAndView();
    modelAndView.addObject("user",user);
    modelAndView.setViewName("view");
    return modelAndView;
}

@RequestMapping("/modelAndView2")
public ModelAndView modelAndView2(){
    User user=new User();
    user.setName("ModelAndView_2");
    user.setId(2L);
    ModelAndView modelAndView=new ModelAndView();
    modelAndView.addObject("user",user);
    View view =new InternalResourceView("/view.jsp");
    modelAndView.setView(view);
    return modelAndView;
}

@RequestMapping("/modelAndView3")
public ModelAndView modelAndView3(){
    User user=new User();
    user.setName("ModelAndView_3");
    user.setId(3L);
    ModelAndView modelAndView=new ModelAndView("view");
    modelAndView.addObject("user",user);
    return modelAndView;
}

@RequestMapping("/modelAndView4")
public ModelAndView modelAndView4(){
    User user=new User();
    user.setName("ModelAndView_4");
    user.setId(4L);
    View view=new InternalResourceView("/view.jsp");
    ModelAndView modelAndView=new ModelAndView(view);
    modelAndView.addObject("user",user);
    return modelAndView;
}

@RequestMapping("/modelAndView5")
public ModelAndView modelAndView5(){
    User user=new User();
    user.setName("ModelAndView_5");
    user.setId(5L);
    Map<String,User> map=new HashMap<>(20);
    map.put("user",user);
    return new ModelAndView("view",map);
}

@RequestMapping("/modelAndView6")
public ModelAndView modelAndView6(){
    User user=new User();
    user.setName("ModelAndView_6");
    user.setId(6L);
    Map<String,User> map=new HashMap<>(20);
    map.put("user",user);
    View view=new InternalResourceView("/view.jsp");
    return new ModelAndView(view,map);
}

@RequestMapping("/modelAndView7")
public ModelAndView modelAndView7(){
    User user=new User();
    user.setName("ModelAndView_7");
    user.setId(7L);
    return new ModelAndView("view","user",user);
}

@RequestMapping("/modelAndView8")
public ModelAndView modelAndView8(){
    User user=new User();
    user.setName("ModelAndView_8");
    user.setId(8L);
    View view=new InternalResourceView("/view.jsp");
    return new ModelAndView(view,"user",user);
}
```

JSP

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page isELIgnored="false" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
${requestScope.user}

</body>
</html>
```

4、HttpServletRequest

```java
@RequestMapping("/request")
public String request(HttpServletRequest httpServletRequest){
    User user=new User();
    user.setName("张三");
    user.setId(2L);
    httpServletRequest.setAttribute("user",user);
    return "view";
}
```

5、@ModelAttribute

- 定义一个方法，该方法专门用来返回要填充的模型数据中的对象。

```java
@ModelAttribute
public User getUser(){
    User user=new User();
    user.setName("张三");
    user.setId(10L);
    return user;
}
```

- 业务方法中无需在处理模型数据，只需返回模型即可。

```java
@RequestMapping("/modelAttribute")
public String modelAttribute(){
    return "view";
}
```

> 将模型数据绑定到 session

1、直接使用原生的 Servlet API

```java
@RequestMapping("/session")
public String session(HttpServletRequest request){
    HttpSession session=request.getSession();
    User user=new User();
    user.setId(2L);
    user.setName("李四");
    session.setAttribute("user",user);
    return "view";
}

@RequestMapping("/session2")
public String session2(HttpSession session){
    User user=new User();
    user.setId(2L);
    user.setName("李四");
    session.setAttribute("user",user);
    return "view";
}
```

2、@SessionAttributes

```java
@SessionAttributes(value = "user")
public class ViewHandler{
}
```

```java
@SessionAttributes(types = User.class)
public class ViewHandler{
}
```

对于 ViewHandler 中的所有业务方法，只要向 request 中添加了  key = "user"、key ="address"，的对象时，Spring MVC 会自动的将该数据添加到 session 中，保存 key 不变。

> 将模型数据绑定到 application 对象

```java
@RequestMapping("/application")
public String application(HttpServletRequest request){
    ServletContext applicationContext=request.getServletContext();
    User user=new User();
    user.setName("王五");
    user.setId(5L);
    applicationContext.setAttribute("user",user);
    return "view";
}
```

### Spring MVC 自定义数据转换器

数据转换器是指客户端 HTTP 请求中的参数转换为业务方法中自定义的形参，自定义表示开发者可以自主设计转换的方式，HandlerApdter 已经提供了通用的转换，String 转 double，表单数据的封装等，但是在特殊的业务场景下，HandlerAdapter 无法进行转换，就需要开发者自定义转换器。

客户端输入 String 类型的数据 "2019-03-03"，自定义转换器将该数据转为 Date 类型的对象。

- 创建 DateConverter 转换器，实现 Converter 接口。

```java
package com.y.converter;

import org.springframework.core.convert.converter.Converter;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

/**
 * @author Y2014188432
 * */
public class DateConverter implements Converter<String, Date> {
    private String pattern;
    public DateConverter(String pattern){
        this.pattern=pattern;
    }
    @Override
    public Date convert(String s) {
        SimpleDateFormat simpleDateFormat=new SimpleDateFormat(this.pattern);
        Date date=null;
        try {
            date=simpleDateFormat.parse(s);
        } catch (ParseException e) {
            e.printStackTrace();
        }
        return date;
    }
}
```

- springmvc.xml 配置转换器 

```xml
<!-- 配置自定义转换器 -->
<bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
    <property name="converters">
        <list>
            <bean class="com.y.converter.DateConverter">
                <constructor-arg type="java.lang.String" value="yyyy-MM-dd"/>
            </bean>
        </list>
    </property>
</bean>
<mvc:annotation-driven conversion-service="conversionService">
```

- JSP

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<form action="/convert/date" method="post">
    <label>
        请输入日期：<input type="text" name="date"/><br/>
        <input type="submit" value="提交"/>
    </label>
</form>
</body>
</html>
```

- Handler

```java
@RequestMapping("/date")
public String date(Date date){
    return date.toString();
}
```

客服端输入"10086-张三-18" ，通过自定义转换器转换为 Student 类

- Handler

```java
@RequestMapping("/student")
public String student(Student student){
    return student.toString();
}
```

- Student

```java
@Data
public class Student {
    private Long id;
    private String name;
    private int age;
}
```

- JSP

```jsp
<html>
<head>
    <title>Title</title>
</head>
<body>
<form action="/convert/student" method="post">
    <label>
        请输入学生信息：<input type="text" name="student"/>(id-name-age)
        <br/>
        <input type="submit" value="提交"/>
    </label>
</form>
</body>
</html>
```

- converter

```java
public class StudentConverter implements Converter<String, Student> {
    @Override
    public Student convert(String s) {
        String[] str=s.split("-");
        Student student=new Student();
        student.setId(Long.parseLong(str[0]));
        student.setName(str[1]);
        student.setAge(Integer.parseInt(str[2]));
        return student;
    }
}
```

- springmvc.xml

```xml
<bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
    <property name="converters">
        <list>            
            <bean class="com.y.converter.StudentConverter"/>
        </list>
    </property>
</bean>
```

### Spring MVC REST

REST：Representational State Transfer，资源表现层状态转换，是目前比较主流的一种互联网软件架构，它结构清晰、标准规范、便于拓展。

- 资源（Resource）

是网络上的一个实体，或者说网络中存在的一个具体信息，一段文本、一张图片、一首歌曲、一段视频等等，总之就是一个具体的存在。可以用一个 URL（统一资源定位符）指向它，每个资源都有一个特定的 URL ，要获取该资源时，只需要访问对应的 URL 即可。

- 表现层（Representation）

资源具体呈现出来的形式，比如文本可以用 txt 格式表示，也可以用 HTML、XML、JSON等格式来表示。

- 状态转换（State Transfer）

客服端如果希望操作服务器中的某个资源，就需要通过某种方式让服务端发生状态转换，而这种转换是建立在表现层之上的，所以叫做"表现层状态转换"。

#### 特点

- URL 更加简洁。
- 有利于不同系统之间的资源共享，只需要遵守一定的规范，不需要进行其他配置。

#### 如何使用

REST 具体操作就是 HTTP 协议中四个表示操作的动词分别对应 CRUD 基本操作。

GET 用来表示获取资源。

POST 用来表示新建资源。

PUT 用来表示修改资源。

DELETE 用来表示删除资源。

测试源码

- Student 实体类

    ```java
    @Data
    @AllArgsConstructor
    public class Student {
        private String name;
        private String id;
        private Integer age;
    }
    ```

- StudentRepository

    ```java
    public interface StudentRepository {
        /**
         * 返回所有的信息
         * @return 学生信息
         */
        public Collection<Student> findAll();
    
        /**
         * 通过 id 查询信息
         * @param id 查询 id
         * @return 学生信息
         */
        public Student findById(String id);
    
        /**
         * 增加或更新学生信息
         * @param student 学生信息
         */
        public void saveOrUpdate(Student student);
    
        /**
         * 通过学生 id 删除学生信息
         * @param id 学生 id
         */
        public void deleteById(String id);
    }
    ```

- StudentRepositoryImpl

    ```java
    @Repository
    public class StudentRepositoryImpl implements StudentRepository {
        private static Map<String,Student> studentMap;
        static {
            studentMap = new HashMap<>();
            studentMap.put("1",new Student("张三","1",21));
            studentMap.put("2",new Student("李四","2",23));
            studentMap.put("3",new Student("王五","3",22));
        }
        @Override
        public Collection<Student> findAll() {
            return studentMap.values();
        }
    
        @Override
        public Student findById(String id) {
            return studentMap.get(id);
        }
    
        @Override
        public void saveOrUpdate(Student student) {
            studentMap.put(student.getId(),student);
        }
    
        @Override
        public void deleteById(String id) {
            studentMap.remove(id);
        }
    }
    ```

- Handler

    ```java
    @RestController
    @RequestMapping("/test2")
    public class Test2 {
        @Autowired
        private StudentRepository studentRepository;
        @GetMapping("/findAll")
        public Collection findAll(){
            return studentRepository.findAll();
        }
        @GetMapping("/findById")
        public Student findById(String id){
            return studentRepository.findById(id);
        }
        @PostMapping("/save")
        public void save(@RequestBody Student student){
            studentRepository.saveOrUpdate(student);
        }
        @PutMapping("/update")
        public void update(@RequestBody Student student){
            studentRepository.saveOrUpdate(student);
        }
        @DeleteMapping("/delete")
        public void delete(String id){
            studentRepository.deleteById(id);
        }
    }
    
    ```

### Spring MVC 文件上传下载

> 单文件上传

​		底层是使用 Apache fileupload 组件完成上传，Spring MVC 对这种方式进行封装。

- pom.xml

    ```xml
    <dependency>
      <groupId>commons-io</groupId>
      <artifactId>commons-io</artifactId>
      <version>2.5</version>
    </dependency>
    <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.3.3</version>
    </dependency>
    ```

- web.xml

    ```xml
    <!-- 放行图片格式 -->
    <servlet-mapping>
      <servlet-name>default</servlet-name>
      <url-pattern>*.jpg</url-pattern>
    </servlet-mapping>
    <servlet-mapping>
      <servlet-name>default</servlet-name>
      <url-pattern>*.png</url-pattern>
    </servlet-mapping>
    ```

- JSP

    ```jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <%@ page isELIgnored="false" %>
    <html>
    <head>
        <title>Title</title>
    </head>
    <body>
        <form action="/file/upload" method="post" enctype="multipart/form-data">
            <input type="file" name="img"/><br/>
            <input type="submit" value="上传"/>
        </form>
        <img src="${path}"/>
    </body>
    </html>
    ```

- Handler

    ```java
    @RequestMapping("/upload")
    public String upload(MultipartFile img, HttpServletRequest request){
        //获取存储路径
        String path = request.getServletContext().getRealPath("file");
        //获取文件名
        String name = img.getOriginalFilename();
        File file = new File(path,name);
        try{
            img.transferTo(file);
        } catch (IOException e) {
            e.printStackTrace();
        }
        request.setAttribute("path","/file/"+name);
        return "upload";
    }
    ```

> 多文件上传

- JSP

    ```jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <%@ page isELIgnored="false" %>
    <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
    <html>
    <head>
        <title>Title</title>
    </head>
    <body>
    <form action="/file/uploads" method="post" enctype="multipart/form-data">
        1.<input type="file" name="imgs"/><br/>
        2.<input type="file" name="imgs"/><br/>
        3.<input type="file" name="imgs"/><br/>
        <input type="submit" value="上传"/><br/>
    </form>
    <c:forEach items="${paths}" var="path">
        <img src="${path}"/>
    </c:forEach>
    </body>
    </html>
    ```

- pom.xml

    ```xml
    <dependency>
      <groupId>jstl</groupId>
      <artifactId>jstl</artifactId>
      <version>1.2</version>
    </dependency>
    <dependency>
      <groupId>taglibs</groupId>
      <artifactId>standard</artifactId>
      <version>1.1.2</version>
    </dependency>
    ```

- Handler

    ```java
    @PostMapping("/uploads")
    public String uploads(MultipartFile[] imgs,HttpServletRequest request){
        List<String> paths = new ArrayList<>();
        for(MultipartFile img:imgs){
            if(img.getSize()>0){
                String path = request.getServletContext().getRealPath("file");
                //获取文件名
                String name = img.getOriginalFilename();
                File file = new File(path,name);
                try{
                    img.transferTo(file);
                    paths.add("/file/"+name);
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
        request.setAttribute("paths",paths);
        return "uploads";
    }
    ```

### 文件下载

- JSP

    ```jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <title>Title</title>
    </head>
    <body>
        <a href="/file/download?name=1.png">1.png</a>
        <a href="/file/download?name=2.png">2.png</a>
        <a href="/file/download?name=3.png">3.png</a>
    </body>
    </html>
    ```

- Handler

    ```java
    @GetMapping("/download")
    public void download(String name, HttpServletResponse response,HttpServletRequest request)  {
        if(name != null){
            String path = request.getServletContext().getRealPath("file");
            File file = new File(path,name);
            OutputStream outputStream = null;
            if(file.exists()){
                response.setContentType("application/fore-download");
                response.setHeader("Content-Disposition","attachment;filename="+name);
                try {
                    outputStream = response.getOutputStream();
                    outputStream.write(FileUtils.readFileToByteArray(file));
                    outputStream.flush();
                } catch (IOException e) {
                    e.printStackTrace();
                } finally {
                    if (outputStream != null){
                        try {
                            outputStream.close();
                        } catch (IOException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        }
    ```

### Spring MVC 表单标签库

![image-20210111165440644](C:\Users\Y2014188432\AppData\Roaming\Typora\typora-user-images\image-20210111165440644.png)

![image-20210111165903515](C:\Users\Y2014188432\AppData\Roaming\Typora\typora-user-images\image-20210111165903515.png)

![image-20210111170113655](C:\Users\Y2014188432\AppData\Roaming\Typora\typora-user-images\image-20210111170113655.png)

### Spring 数据校验

Spring MVC 提供了两种数据校验的方式：

​	1、基于 Validator 接口。

​	2、使用 Annotation JSR -303 标准进行校验

基于 Validator 接口的方式需要自定义 Validator 验证器，每一条数据的验证规则需要开发者手动完成，使用 Annotation JSR -303 标准则不需要自定义验证器，通过注解的方式可以直接在实体类中添加每个属性的验证规则，这种方式更加方便，实际开发中推荐使用。

> 基于 Validator
>
> Annotation JSR -303 标准

使用 Annotation JSR - 303 标准进行验证，需要导入支持这种标准的依赖 jar 文件，这里我们使用 HbernateValidator 