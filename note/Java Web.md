### IDEA 集成 Tomcat

1、创建 java web 工程

![image-20201207162018764](F:\.Data\java web-01)

### Servlet

- 什么是 servlet？

     servlet 是 Java web 开发的基础，于平台无关的服务器组件，它是运行在 Servlet 容器、Web  应用服务器、Tomcat，负责于客户端进行通信。

- Servlet 的功能：

    1. 创建并返回基于客户端请求的动态 HTML 页面
    2. 与数据库进行通信

- 如何使用 Servlet？

Servlet 本身是一组接口（javax.servlet，java.lang、java.util、javax.sql），自定义一个类，并且实现 Servlet 接口，这个类就具备了接受客户端请求以及做出相应响应

```java
package com.y.servlet;

import javax.servlet.*;
import java.io.IOException;

/**
 * @author Y2014188432
 * */
public class MyServlet implements Servlet {
    @Override
    //构造函数（create 2021-03-11）
    public void init(ServletConfig servletConfig) throws ServletException {

    }

    @Override
    //servlet 配置 用于描述 Servlet 的基本信息（create 2021-03-11）
    public ServletConfig getServletConfig() {
        return null;
    }

    @Override
    /**
    * @param servletRequest 服务请求
    * @param servletResponse 服务返回
    * （create 2021-03-11）
    */
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        String id=servletRequest.getParameter("id");
        System.out.println("我已升空，感觉良好");
        servletResponse.setContentType("text/html;charset=UTF-8");
        servletResponse.getWriter().write("你传递的参数为id："+id);
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {

    }
}
```

- 客户端不能直接请求 servlet ，需要开发者手动配置映射，有两种配置方式。

    1、基于 XML 文件中配置

```xml
<servlet>
    <servlet-name>MyServlet</servlet-name>
    <servlet-class>com.y.servlet.MyServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>MyServlet</servlet-name>
    <url-pattern>/servlet</url-pattern>
</servlet-mapping>
```

​	2、基于注解的方式。

```java
@WebServlet("\servlet")
public class MyServlet implements Servlet{
    
}
```

上述两种配置方式结果完全一致。

### Servlet 的声明周期

#### Servlet 实例化过程

1. 当浏览器请求 Servlet 的时候，Tomcat 会查询当前 Servlet 的实例化对象是否存在，若不存在，则通过反射机制动态创建对象；若存在，直接进入第 3 步
2. 调用 init 方法完成初始化操作
3. 调用 service 方法完成业务逻辑操作
4. 关闭 Tomcat 时，会调用 destory 方法，释放当前对象所占用的资源

#### Servlet各方法运行次数

- 无参构造函数只调用一次，创建对象
- init 只调用一次，初始化对象
- service 调用 N 次，执行业务方法
- destory 只调用一次，卸载对象

=====================================（update 2021-03-11）

### ServletConfig

- 改接口是用来描述 Servlet 的基本信息的
- getServletName（） 返回类名（带包名）
- getInitialParameter（String key） 获取 init 参数的值（web.xml)
- getInitParameter（） 返回所有的 initParameter 的 name 值，一般用作遍历初始化参数
- getServletContext（） 返回 ServletContext 对象，它是 Servlet 的上下文，整个 Servlet 的管理者
- servletConfig 和 ServletContext 的区别
    - servletConfig 作用于单个 Servlet 实例，相当于局部的
    - servletContext 作用于整个 Web 应用，相当于全局的

=====================================（update 2021-03-11）

### Servlet 的层次结构

#### 层次结构（类的继承）

- Servlet （父）---> GenericServlet ---> HttpServlet（子）

#### HTTP 请求类型

- GET 读取
- POST 保存
- PUT 修改
- DELETE 删除

=====================================（update 2021-03-11）

### Servlet 的上手操作

#### 实现总述

- 直接继承 Servlet 的子类 HttpServlet
- 重写 doGET 和 doPOST 方法

#### 优点

- 屏蔽了不常用的方法，减轻了代码的开发量

#### 实例

```java
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/test")
public class Servlet2 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("GET");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("POST");
    }
}
```

===================================================================================（update 2021-03-11）

### JSP

- JSP 本质上就是一个 Servlet，JSP 主要负责与用户交互，将最终的界面呈现给用户，HTML+JS+CSS+Java 的混合文件。、

- 当服务器接收到一个后缀是 jsp 的请求时，将该请求交给 JSP 引擎去处理，每一个 JSP 页面第一次被访问的时候，JSP 引擎会自动的翻译成 Servlet 文件，再由 Web 容器调用 Servlet 完成相应。

- 单纯从开发的角度看，JSP 就是在 HTML 中嵌入 java 程序。

- 具体的嵌入的方式有三种 

    1、JSP 脚本：执行 Java 逻辑代码

    ```jsp
    <% Java 代码 %>
    ```

    2、JSP 声明：定义 Java 方法

    ```jsp
    <%!
    	声明 Java 方法
    %>
    ```

    3、JSP 表达式：把 Java 对象直接输出到页面

    ```java
    <%= Java 变量 %>
    ```

- 实例

    ```jsp
    <%@ page import="com.y.entity.User" %>
    <%@ page import="java.util.List" %>
    <%@ page import="java.util.ArrayList" %><%--
      Created by IntelliJ IDEA.
      User: Y2014188432
      Date: 2020/12/9
      Time: 14:08
      To change this template use File | Settings | File Templates.
    --%>
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <title>Title</title>
    </head>
    <body>
    <%
        List<User> users=new ArrayList<>();
        users.add(new User("张三",22));
        users.add(new User("李四",22));
        users.add(new User("王五",22));
    %>
    <table >
        <tr>
            <th>姓名</th>
            <th>年龄</th>
        </tr>
        <%
            for (User user : users) {
        %>
        <tr>
            <td><%= user.getName()%>
            </td>
            <td><%= user.getAge()%>
            </td>
        </tr>
        <%
            }
        %>
    </table>
    </body>
    </html>
    ```

### JSP 内置对象

1. request

    表示一次请求，HttpServletRequest。

2. response

    表示一次相应，HttpServletResponse。

3. pageContext

    页面上下文，获取页面信息，PageContext。

4. session

    表示一次会话，保存用户信息，HttpSession。

5. application

    表示当前 Web 应用，全局对象，保存所有用户共享信息，ServletContext。

6. config

    当前 JSP 对应的 Servlet 的 ServletConfig 对象，获取当前 Servlet 的信息。

7. out

    向浏览器输出数据，JspWrite。

8. page

    当前 JSP 对应的 Servlet 对象，Servlet。

9. exception

    表示 JSP 页面发生的异常，Exception。

常用的是 request、response、session、application、pageContext

#### Request 的常用方法

1. String getParameter(String key) 获取客户端传来的参数。
2. String getParameterValue(String key) 获取客户端传来的多个参数。
3. void setAttribute(String key，Object val) 通过键值对的形式保存数据。
4. Object getAttribute(String key) 通过 key 取出 value。
5. RequestDispatcher getRequestDispatcher(String path) 返回一个 RequestDispatcher 对象，该对象的 forward 方法用于请求转发。
6. void setCharacterEncoding(String charset) 指定每个请求的编码。

```jsp
<!-- Text.jsp -->
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<h1>Text1</h1>
<%
    String idStr=request.getParameter("id");
    Integer id=Integer.parseInt(idStr);
    id++;
    request.setAttribute("number",id);
    request.getRequestDispatcher("text2.jsp").forward(request,response);
%>
<%= id%>
</body>
</html>

<!-- Text2.jsp -->
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<h1>text2</h1>
<%
    Integer id=(Integer) request.getAttribute("number");
    id++;
%>
<%= id%>
</body>
</html>
```

#### Response 的常用方法

- sendRedirect(String path) 重定向，页面之间的跳转。

> 重定向与转发之间的区别
>
> 重定向是发送一次新的请求，地址栏改变，也叫客户端跳转
>
> 转发是同一个请求在服务器传递，地址栏不变，也叫服务器跳转

#### Seccess

- 用户会话

    服务器无法识别每一次 HTTP 请求的出处（不知道来自于那个终端），它只会接受一个信号请求，所以就存在一个问题：将用户的相应发送给其他人，必须有一种技术来让服务器知道请求来自哪，这就是会话技术。

- 会话

    客户端和服务器之间发生的一系列连续的请求和相应的过程，打开浏览器进行操作到关闭浏览器的过程。

- 会话状态

    指服务器和浏览器在会话过程中产生的状态信息，借助于会话状态，服务器能够把属于同一次会话的一系列请求和相应关联起来。

实现会话有两种方式：

- session
- cookie

属于同一次会话的请求都有一个相同的标识符，sessionID

Session 常用的方法：

1. String getId()			获取 sessionID
2. void setMaxInactiveInterval(int interval) 设置 session 的失效时间，单位为秒
3. int getMaxInactiveInterval()    获取 session 的失效时间，单位为秒
4. void invalidate()                   设置 session 立即失效
5. void setAttribute(String key,Object value)   通过键值对的形式来存储数据
6. Object getAttribute(String key)   通过键获取对应的数据
7. void removeAttribute(String key)  通过键删除对应的数据

### Cookie

Cookie 是服务端在 HTTP 相应中附带传送给浏览器的一个小文本文件，一旦浏览器保存了某个 Cookie，在之后的请求和响应过程中，会将此 Cookie 来回传递，这样就可以通过 Cookie 这个载体完成客户端和服务端的数据交互。

cookie

- 创建 cookie

    ```java
    Cookie cookie = new Cookie("name","tom");
    response.addCookie(cookie);
    ```

- 读取 cookie

    ```java
    Cookie[] cookies = request.getCookies();
    ```

Cookie 常用的方法

1. void setMaxAge(int age)   设置 Cookie 的有效时间，单位为秒
2. int getMaxAge()   获取 Cookie 的有效时间。
3. String getName() 获取 Cookie 的 name。
4. String getValue() 获取 Cookie 的 value。

### Session 和 Cookie 的区别

session：保存在服务器、保存的数据是 Object、会随着会话的结束而销毁、保存重要信息

cookie：保存在浏览器、保存的数据是 String、可以长期保存在浏览器中、保存不重要信息

储存用户信息：

- session：

    setAttribute("name","admin")  存

    getAttribute("name")      取

    生命周期：服务端，只要 Web 应用重启就销毁、客户端，只要关闭浏览器就销毁。

    退出登录：session.invalidate()

- cookie：

    responce.addCookie(new Cookie(name,"admin") ) 存

    ```java
    Cookie[] cookies=request.getCookies();
    for(Cookie cookie:cookies){
    }//取
    ```

    生命周期：不随服务端的重启而销毁，客户端：默认是只要关闭浏览器就销毁，我们通过 setMaxAge() 方法设置有效期，则不随浏览器的关闭而销毁，而是由设置的时间来决定。

    退出登录：setMaxAge(0)

### JSP 内置对象作用域

- 四个元素

    page、request、session、application

    范围大小：page < request < session < application

1. page

    对应的内置对象是 pageContext

    作用域：只在当前页面有效

2. request

    对应的内置对象是 request

    作用域：在一次请求内有效

3. session

    对应的内置对象是 session

    作用域：在一次会话内有效

4. application

    对应的内置对象是 application

    作用域：对应整个 Web 应用

- 网站登录统计

    ```jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <title>Title</title>
    </head>
    <body>
    <%
        if(application.getAttribute("sum")== null){
            application.setAttribute("sum",1);
        }
        int count = (int)application.getAttribute("sum");
    
    %>
    <p>你是第 <%= count%> 位访问的用户</p>
    <%
        count ++;
        application.setAttribute("sum",count);
    %>
    </body>
    </html>
    ```

### EL 表达式

- Expression Language 表达式语言，在 jsp 页面中用于取值。

- 仅限 JSP 页面中使用。

- 样式 ${ 变量名 }

- 对四种域对象的默认查找顺序：pageContext --> request --> responce --> application

- 可以通过指定数据域对数据进行查找

- 数据级联

    ```jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <title>Title</title>
    </head>
    <body>
    <%
        User user=new User("张三",23);
        session.setAttribute("User",user);
    %>
    <table>
        <tr>
            <th>姓名</th>
            <th>年龄</th>
        </tr>
        <tr>
            <td>${User.name}</td>
            <td>${User.age}</td>
        </tr>
    </table>
    </body>
    </html>
    ```

###  JSTL

JSP Standard Tag Libray：JSP 标准标签库，JSP 为开发者提供的一系列的标签，使用这些标签可以完成一些逻辑处理，比如循环遍历集合，让代码更加简洁，不会出现 JSP 脚本穿插的情况。

- JSTL 的使用

    1. 需要导入 jar 包（两个 jstl.jar standard.jar）

        存放的位置必须在 WEB-INF 目录之下

    2. 在 JSP 页面开始的地方导入 JSTL 标签库

        ```jsp
        <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
        ```

    3. 在需要的地方使用
    
        ```jsp
        <table>
          <tr>
            <th>姓名</th>
            <th>年龄</th>
          </tr>
          <c:forEach items="${users}" var="user">
              <tr>
                <td>${user.name}</td>
                <td>${user.age}</td>
              </tr>
          </c:forEach>
        </table>
        ```

- JSTL 优点
    1. 提供了统一的标签
    2. 可以用于编写各种动态功能
- 

### 过滤器

Filter

熔断器 Hystrxis

- 功能
    1. 用来拦截传入的请求和传出的相应。
    2. 修改或以某种方式处理正在服务端和客户端之间交换的数据流。

- 如何使用

    1. 与使用 Servlet 类似，Filter 是 Java Web 提供的一个接口，开发者只需要自定义一个类并且实现该接口即可。

        ```java
        public class Encoding implements Filter {
            @Override
            public void init(FilterConfig filterConfig) throws ServletException {
        
            }
        
            @Override
            public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
                servletRequest.setCharacterEncoding("UTF-8");
                filterChain.doFilter(servletRequest, servletResponse);
            }
        
            @Override
            public void destroy() {
        
            }
        }
        ```

    2. 在 web.xml 中配置

        ```xml
        <filter>
            <filter-name>encoding</filter-name>
            <filter-class>com.y.filter.Encoding</filter-class>
        </filter>
        <filter-mapping>
            <filter-name>encoding</filter-name>
            <url-pattern>/</url-pattern>
        </filter-mapping>
        ```

        注意：diFilter 方法中处理完业务逻辑之后，必须添加filterChain.doFilter(servletRequest, servletResponse);否则无法向后传递，一直停留在过滤器中。

    ### Filter 的生命周期

    当 Tomcat 启动时，通过反射机制调用 Filter 的无参构造函数创建实例化对象，同时调用 init 方法实现初始化，doFilter 方法调用多次，当 Tomcat 服务关闭的时候，调用 destory 来销毁 Filter 对象。

    - 无参构造函数

        只调用一次，当 Tomcat 启动时调用（Filter 一定要配置）

    - init 方法

        只调用一次，当 Filter 的实例化对象创建完成之后调用

    - doFilter

        调用多次，访问 Filter 的业务逻辑都写在 Filter 中

    - destory

        只调用一次，Tomcat 关闭时调用

    实际开发中 Filter 的使用场景：

    1. 统一处理中文乱码。

    2. 屏蔽敏感词。

        ```java
        @WebFilter("/word")
        public class Word implements Filter {
            @Override
            public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
                servletRequest.setCharacterEncoding("UTF-8");
                String word = servletRequest.getParameter("word");
                word = word.replaceAll("敏感词","***");
                servletRequest.setAttribute("word",word);
                filterChain.doFilter(servletRequest, servletResponse);
            }
        }
        ```

    3. 控制资源的访问权限。

        ```java
        package com.y.filter;
        
        import javax.servlet.*;
        import javax.servlet.annotation.WebFilter;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        import javax.servlet.http.HttpSession;
        import java.io.IOException;
        import java.net.http.HttpRequest;
        
        /**
         * @author Y2014188432
         * */
        @WebFilter("/download.jsp")
        public class Download implements Filter {
            @Override
            public void init(FilterConfig filterConfig) throws ServletException {
        
            }
        
            @Override
            public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
                HttpServletResponse response = (HttpServletResponse) servletResponse;
                HttpServletRequest request = (HttpServletRequest) servletRequest;
                HttpSession session = request.getSession();
                String user = (String)session.getAttribute("user");
                if(user == null){
                    response.sendRedirect("login.jsp");
                }else{
                    filterChain.doFilter(servletRequest, servletResponse);
                }
            }
        
            @Override
            public void destroy() {
        
            }
        }
        ```

### 文件的上传下载

#### 上传

- JSP
    1. 设置一个 input 标签，将 type 设置为 file。
    2. 设置 form 的 method 为 post。
    3. form 表单的 enctype 设置 multipart/form-data ,(以二进制流传数据)

- Servlet
    1. fileUpload 组件可以将所有的请求信息都解析成 FileItem对象，可以通过对 FiletItem 对象的操作完成上传，面向对象的思想。（引入包 commons-fileupload.jar、commons-io.jar）

### 文件下载

```java
package com.y.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;

/**
 * @author Y2014188432
 * */
@WebServlet("/download")
public class Download extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //设置响应方式
        resp.setContentType("application/x-msdownload");
        //设置下载后的名字
        String fileName = "1.jpg";
        resp.setHeader("Content-Disposition","attachment;filename="+fileName);
        //获取输出流
        OutputStream outputStream = resp.getOutputStream();
        String path = req.getServletContext().getRealPath("file/302568.jpg");
        InputStream inputStream = new FileInputStream(path);
        int temp = 0;
        while ((temp = inputStream.read()) != -1){
            outputStream.write(temp);
        }
        //关闭流
        outputStream.close();
        inputStream.close();
    }
}
```

### Ajax

> Asynchronous JavaScript And XML 异步的 JavaScript 和 XML
>
> AJAX 不是新的编程语言，指的是一种交互方式，异步加载，客户端和服务器的数据交互更新在局部页面的技术，不需要刷新整个页面。

- 优点
    1. 局部刷新，刷新效率高
    2. 用户体验更好

- 基于 JQuery 的 AJAX

-  jsp

    ```jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    
    <html>
    <head>
        <title>Title</title>
        <script type="text/javascript" src="js/jquery-3.3.1.min.js"></script>
        <script>
            $(function () {
                var button = $("#btn");
                button.click(function () {
                    $.ajax({
                        url:'/text',
                        type:'post',
                        data:'id=1',
                        dataType:'text',
                        success:function (data) {
                            button.before(data+"<br/>");
                        }
                    })
                });
            })
        </script>
    </head>
    <body>
    
    <input id="btn" type="button" value="提交"/>
    </body>
    </html>
    
    ```

- servlet

    ```java
    @WebServlet("/text")
    public class Text extends HttpServlet {
        @Override
        protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            String id = req.getParameter("id");
            String str = "Hello World!";
            resp.getWriter().write(str);
        }
    }
    ```

    ### 传统的 Web 数据交互与 AJAX 数据交互

    - 客户端的请求方式不同

        传统：浏览器发送同步请求

        AJAX：异步引擎发送异步请求

    - 服务器响应的方式不同

        传统：响应一个完整的 JSP 页面（视图）

        AJAX：响应需要的数据

    - 客户端处理方式不同

        传统：需要等待服务器完成响应并且重新加载整个页面之后，用户才能继续后续操作

        AJAX：动态更新页面中的局部内容，不影响用户的其他操作

### AJAX  原理

 ![](F:\.Data\Java Web-02)

### 基于 JQuery  的  AJAX 语法

$.ajax({属性})

常用的属性参数

1. url ：请求的后端服务地址
2. type：请求方式，默认 get
3. data：请求参数
4. dataType：服务器返回的数据类型，Text/json
5. success：请求成功的回调函数
6. error：请求失败的回调函数
7. complete：请求完成的回调函数（无论成功或失败都会执行）

### JSON

JavaScript Object Notation ，一中轻量级数据交互格式，完成 js 与 java 等后端开发语言对象数据之间的转换。

客户端与服务器之间传递对象数据，需要用 JSON 格式

JSP

```jsp
<%--
  Created by IntelliJ IDEA.
  User: Y2014188432
  Date: 2020/12/15
  Time: 21:08
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>

<html>
<head>
    <title>Title</title>
    <script type="text/javascript" src="js/jquery-3.3.1.min.js"></script>
    <script>
        $(function () {
            var button = $("#btn");
            button.click(function () {
                $.ajax({
                    url:'/text',
                    type:'post',
                    data:'id=1',
                    dataType:'json',
                    success:function (data) {
                        $("#name").val(data.name);
                        $("#age").val(data.age);
                    }
                })
            });
        })
    </script>
</head>
<body>
姓名：<input id="name" type="text"/><br/>
年龄：<input id="age" type="text"/><br/>
<input id="btn" type="button" value="提交"/>
</body>
</html>

```

servlet

```java
package com.y.servlet;

import com.y.entity.User;
import net.sf.json.JSONObject;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * @author Y2014188432
 * */
@WebServlet("/text")
public class Text extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        User user = new User("张三",23);
        resp.setCharacterEncoding("UTF-8");
        JSONObject jsonObject = JSONObject.fromObject(user);
        resp.getWriter().write(jsonObject.toString());
    }
}

```

### JDBC

Java DataBase Connectiviy：Java 数据库连接系统

是一个独立于特定数据库的管理系统，通用的 SQL 数据库存取和操作的公共接口。

定义了一组标准，为访问不同的数据库提供了统一路径。 

![image-20201218174930757](F:\.Data\Java web-03)

### JDBC 体系结构

JDBC 接口包括两个层面

1. 面向应用的 API ，供程序员调用
2. 面向数据库的 API ，供厂商开发数据库的驱动程序 

![image-20201219132525085](F:\.Data\Java web-04)

- JDBC API

    提供者：Java 官方

    内容：供开发者调用的接口

    jar：java.sql 和 javax.dql

    - DriverManager 类
    - Connection 接口
    - Statement 接口
    - ResultSet 接口

- DriverManager

    提供者：Java 官方

    作用：管理不同的 JDBC 驱动

- JDBC 驱动

    提供者：数据库厂商

    作用：负责连接不同的数据库

### JDBC 的使用

1. 加载数据库驱动，Java 程序和数据库之间的桥梁
2. 获取 Connection ，Java 程序与数据库的一次连接
3. 创建 Statement 对象，由 Connection 产生，执行 SQL 语句
4. 如果需要接受返回值，创建 ResultSet 对象，保存 Statement 执行之后所查询到的结果。

```java
package com.y.entity;

import java.sql.*;

/**
 * @author Y2014188432
 * */
public class Text {
    public static void main(String[] args)  {
        try {
            //加载驱动
            Class.forName("com.mysql.cj.jdbc.Driver");
            //获取连接
            String url ="jdbc:mysql://localhost:3306/first";
            String user = "root";
            String password = "123456";
            Connection connection = DriverManager.getConnection(url,user,password);
            //执行
            //增
//            String sql = "insert into sc value ('2018001001','c009',99)";
            //删
//            String sql = "delete from sc where sno='2018001001' and cno='c009'";
            //改
//            String sql = "update sc set grade=99 where sno='2018001001' and cno='c007'";
            //查
            String sql = "select * from sc";
            Statement statement = connection.createStatement();
            ResultSet resultSet = statement.executeQuery(sql);
            while(resultSet.next()){
                System.out.print(resultSet.getString(1)+"\t");
                System.out.print(resultSet.getString(2)+"\t");
                System.out.println(resultSet.getString(3)+"\t");
                System.out.println("***************************");
            }
        } catch (ClassNotFoundException | SQLException e) {
            e.printStackTrace();
        }
    }
}
```

### PreparedStatement

Statement 的子类，提供了 SQL 占位符的功能

使用 Statement 进行开发有两个问题：

1. 需要频繁拼接 String 字符串，出错率比较高
2. 存在 SQL 注入风险

SQL注入：利用某些系统没有对用户输入信息进行充分检测，在用户输入的数据中注入非法的 SQL 语句，从而利用系统的 SQL 引擎完成恶意行为的做法。

```java
package com.y.entity;

import java.sql.*;

/**
 * @author Y2014188432
 * */
public class DemoLogin {
    public static void main(String[] args) {
        String user = "李元芳";
        String id = "0247053642";
        //连接数据库
        try {
            //添加驱动
            Class.forName("com.mysql.cj.jdbc.Driver");
            //获取连接
            String myUrl = "jdbc:mysql://localhost:3306/text";
            String myUser = "root";
            String myPassword = "123456";
            Connection connection = DriverManager.getConnection(myUrl,myUser,myPassword);
            //验证登录
            //sql 语句
            String sql = "select * from student where s_name = ? and s_no = ?";
            //实例化 执行对象
            PreparedStatement preparedStatement = connection.prepareStatement(sql);
            //标注 ？
            preparedStatement.setString(1,user);
            preparedStatement.setString(2,id);
            //返回结果
            ResultSet resultSet = preparedStatement.executeQuery();
            //返回登录结果
            if(resultSet.next()){
                System.out.println("登录成功!");
            }else{
                System.out.println("登录失败!");
            }
        } catch (ClassNotFoundException | SQLException e) {
            e.printStackTrace();
        }
    }
}
```

### 数据库连接池

JDBC 开发流程

1. 加载驱动（只需要加载一次）
2. 建立数据库连接（Connection）
3. 执行 SQL 语句（Statement）
4. ResultSet 接受结果集（查询）
5. 断开连接，释放资源

数据库连接对象是通过 DriverManager 来获取的，每次获取都需要向数据库申请获取连接，验证用户名和密码，执行完 SQL 语句后断开连接，这样的方式会造成资源的浪费，数据库连接没有得到很好的重复利用。

可以使用数据库连接池解决这一问题

数据库连接池的基本思想就是为数据库建立一个缓冲池，预先向缓冲池中放入一定数量的连接对象，当需要获取数据库连接的时候，只需要从缓冲池中取出一个对象，用完之后再放回到缓冲池中，供下一次请求使用，做到了资源的重复利用。

当数据库连接池中没有空闲的连接时，新的请求就会进入等待队列，等待其他线程释放连接。

### 数据库连接池实现

JDBC 的数据库连接池使用 javax.sql. DataSource 接口来完成的。DataSource 是 Java 官方提供的接口，使用的时候开发者并不需要自己来实现接口，可以使用第三方的工具，C3P0 是一个常用的第三方实现，实际开发中直接使用 C3P0 以可完成数据库连接池的操作。导入 c3p0.jar

相当于对数据库连接阶段进行优化

实现代码

```java
public class DataSourceText {
    public static void main(String[] args) {
        try{
            //创建 c3p0
            ComboPooledDataSource dataSource = new ComboPooledDataSource();
            dataSource.setDriverClass("com.mysql.cj.jdbc.Driver");
            dataSource.setJdbcUrl("jdbc:mysql://localhost:3306");
            dataSource.setUser("root");
            dataSource.setPassword("123456");
            Connection connection = dataSource.getConnection();
            System.out.println(connection);
            connection.close();
        } catch (PropertyVetoException | SQLException e) {
            e.printStackTrace();
        }
    }
}
//其他限制条件
//设置初始化连接个数
dataSource.setInitialPoolSize(20);
//设置最大链接数
dataSource.setMaxPoolSize(40);
//当连接对象不够时，再次申请的连接对象个数
dataSource.setAcquireIncrement(10);
//设置最小链接数（即：当可用连接剩余 x 时，开始申请连接）
dataSource.setMinPoolSize(2);
```

在实际开发的过程中，往往通过配置 c3po-config.xml 文件的方法来配置连接池的各个属性。

- c3p0-config.xml

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <c3p0-config>
        <named-config name="c3p0">
            <!-- 基本元素 -->
            <property name="user">root</property>
            <property name="password">123456</property>
            <property name="driverClass">com.mysql.cj.jdbc.Driver</property>
            <property name="jdbcUrl">jdbc:mysql://localhost:3306</property>
            <!-- 设置初始化个数 -->
            <property name="initialPoolSize">20</property>
            <!-- 设置最大连接数 -->
            <property name="maxPoolSize">40</property>
            <!--  设置最小连接数 -->
            <property name="minPoolSize">2</property>
            <!--  连接个数不足时，再次请求的单次连接个数 -->
            <property name="acquireIncrement">5</property>
        </named-config>
    </c3p0-config>
    ```

- DataSourceText.java

    ```java
    public class DataSourceText {
        public static void main(String[] args) {
            try{
                //创建 c3p0
                ComboPooledDataSource dataSource = new ComboPooledDataSource("c3p0");
                Connection connection = dataSource.getConnection();
                System.out.println(connection);
                connection.close();
            } catch ( SQLException e) {
                e.printStackTrace();
            }
        }
    } 
    ```

### DBUtils

数据库工具

### library

 

### 总述 && 经验 && 技巧

-  使用接口

    LoingService：

- 实体类

    1. 用来封装结果

    ![image-20210104131329689](C:\Users\Y2014188432\AppData\Roaming\Typora\typora-user-images\image-20210104131329689.png)

    