### Spring 两大核心机制

#### IoC （控制反转） / DI （依赖注入）

#### AOP （面向切面编程）

### 概述

Spring 是一个企业级开发框架，是软件设计层面的框架，优势在于可以将应用程序进行分层，开发者可以自主选择组件。

### IoC 的实现过程

- 创建项目

    创建 Maven 项目 ，导入 Spring 的上下文依赖

    ```xml
    <!-- 添加 spring 上下文的依赖 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.0.0.RELEASE</version>
    </dependency>
    ```

    导入实体类管理工具 lombok ，方便管理实体类

    ```xml
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.6</version>
        <scope>provided</scope>
    </dependency>
    ```

- 创建实体类 Student 

    ```java
    import lombok.Data;
    @Data
    public class Student {
        public String name;
        public Integer age;
        public Double score;
    }
    ```

- 调用实体类

    - 常规的调用方法

        ```java
        Student student = new Student();
        student.setName("张三");
        student.setAge(19);
        student.setScore(89.6);
        System.out.println(student);
        ```

    - IoC 调用

        创建 spring.xml 文件

        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
        <bean id="student" class="com.y.entity.Student">
            <property name="name" value="张三"/>
            <property name="age" value="19"/>
            <property name="score" value="89.6"/>
        </bean>
        </beans>
        ```

        创建测试类

        ```java
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring.xml");
        Student student = (Student) applicationContext.getBean("student");
        System.out.println(student);
        ```



### 配置文件

- 通过配置 `bean` 标签来完成对象的管理
    - `id` :对象名
    - `class` :对象的模板类（对象类必须要有无参构造，因为是通过反射完成创建对象，调用的是无参构造）

- 对象的成员变量是通过 `property` 标签完成赋值

    - `name` 成员变量名

    - `value` 成员变量值（常规参数和 String 可以直接赋值，如果是其他引用类型，使用 ref ）

    - `ref` 将 IoC 容器中的其他 bean 赋值给当前的成员变量（DI）

        ```xml
        <bean id="student" class="com.y.entity.Student">
            <property name="name" value="张三"/>
            <property name="age" value="19"/>
            <property name="score" value="89.6"/>
            <property name="address" ref="address"/>
        </bean>
        <bean id="address" class="com.y.entity.Address">
            <property name="address" value="创新路"/>
        </bean>
        ```



### scope 作用域

Spring 管理的 bean 是根据 scope 来生成的，表示 bean 的作用域，共4种。默认为 singleton。

- singleton ：单例，表示通过 IoC 容器获取的 bean 是唯一的。
- prototype：原型，表示通过 IoC 容器获取的 bean 是不同的。
- request：请求，表示在一次 HTTP 请求内有效。
- session：会话，表示在一个用户会话内有效。

request 和 session 只适用 Web 项目，一般使用 singleton 和 prototype 较多

prototype 模式当业务代码获取 IoC 容器种的 bean 时，Spring 才去调用无参构造创建对应的 bean。

singleton 模式无论业务代码是否获取 IoC 容器中的 bean ，Spring 在加载 spring.xml 时就会创建 bean。



### Spring 的工厂方法

IoC 通过工厂模式创建 Bean 的方式有两种：

- 静态工厂方法

     Car.java

    ```java
    import lombok.AllArgsConstructor;
    import lombok.Data;
    @Data
    @AllArgsConstructor
    public class Car {
        public String name;
        public Double speed;
    }
    ```

    spring-factory.xml

    ```xml
    <bean id="car" class="com.y.factory.CarFactory" factory-method="getCar">
        <constructor-arg value="2"/>
    </bean>
    ```

    Text.java

    ```java
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-factory.xml");
    Car car = (Car) applicationContext.getBean("car");
    System.out.println(car);
    ```

- 实例工厂方法

    instanceCarFactory.java

    ```java
    public class InstanceCarFactory {
        Map<String,Car> carMap;
        public InstanceCarFactory(){
            carMap = new HashMap<>();
            carMap.put("1",new Car("五菱",120.0));
            carMap.put("2",new Car("手推车",40.0));
        }
        public Car getCar(String id){
            return carMap.get(id);
        }
    }
    ```

    spring-fatory.xml

    ```xml
    <!-- 实例化装载工厂 -->
    <bean id="carFactory" class="com.y.factory.InstanceCarFactory"/>
    <bean id="car2" factory-bean="carFactory" factory-method="getCar">
        <constructor-arg value="1"/>
    </bean>
    ```

    text.java

    ```java
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-factory.xml");
    Car car = (Car) applicationContext.getBean("car2");
    System.out.println(car);
    ```



### AOP

- 通过动态代理实现 AOP

    ```java
    public class MyInvocationHandler implements InvocationHandler {
        /**
         * 接受委托对象
         */
        public Object object = null;
    
        /**
         * 返回代理对象
         * @param object 所要代理的对象
         * @return 代理对象
         */
        public Object bind(Object object){
            this.object = object;
            return Proxy.newProxyInstance(object.getClass().getClassLoader(),object.getClass().getInterfaces(),this);
        }
        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            System.out.println(method.getName()+"方法的参数是 "+ Arrays.toString(args));
            Object result = method.invoke(this.object,args);
            System.out.println(method.getName()+"的运行结果是 "+result);
            return result;
        }
    }
    ```

    ```java
    public interface Cal {
        /**
         * 加
         * @param num1 参数1
         * @param num2 参数2
         * @return 结果
         */
        public int add(int num1,int num2);
        /**
         * 减
         * @param num1 参数1
         * @param num2 参数2
         * @return 结果
         */
        public int sub(int num1,int num2);
        /**
         * 乘
         * @param num1 参数1
         * @param num2 参数2
         * @return 结果
         */
        public int mul(int num1,int num2);
        /**
         * 除
         * @param num1 参数1
         * @param num2 参数2
         * @return 结果
         */
        public int div(int num1,int num2);
    }
    ```

    ```java
    public class CalImpl implements Cal {
    
        @Override
        public int add(int num1, int num2) {
            return num1+num2;
        }
    
        @Override
        public int sub(int num1, int num2) {
            return num1-num2;
        }
    
        @Override
        public int mul(int num1, int num2) {
            return num1*num2;
        }
    
        @Override
        public int div(int num1, int num2) {
            return num1/num2;
        }
    }
    ```

    ```java
    public class Test1 {
        public static void main(String[] args) {
            Cal cal = new CalImpl();
            MyInvocationHandler myInvocationHandler = new MyInvocationHandler();
            Cal cal1 = (Cal)myInvocationHandler.bind(cal);
            cal1.add(1,1);
            cal1.sub(5,4);
            cal1.mul(4,5);
            cal1.div(8,4);
        }
    }
    ```

    > 上述方法是通过动态代理实现 AOP 的过程，比较复杂，不好理解，Spring 框架对 AOP 进行了封装，使用 Spring 框架可以面向对象的思想来实现 AOP

- 通过 Spring 框架实现 AOP

    切面类

    ```java
    @Aspect
    @Component
    public class LoggerAspect {
        @Before("execution(public int com.y.entity.impl.CalImpl.*(..))")
        public void before(JoinPoint joinPoint){
            String name = joinPoint.getSignature().getName();
            String args = Arrays.toString(joinPoint.getArgs());
            System.out.println(name+"方法的参数是"+args);
        }
    
        @After(value = "execution(public int com.y.entity.impl.CalImpl.*(..))")
        public void after(JoinPoint joinPoint){
            //获取参数名
            String name = joinPoint.getSignature().getName();
            System.out.println(name+"方法运行完成");
        }
    
        @AfterReturning(value = "execution(public int com.y.entity.impl.CalImpl.*(..))",returning = "result")
        public void afterReturning(JoinPoint joinPoint ,Object result){
            //获取参数名
            String name = joinPoint.getSignature().getName();
            System.out.println(name+"方法的结果是"+result);
        }
    
        @AfterThrowing(value = "execution(public int com.y.entity.impl.CalImpl.*(..))",throwing = "exception")
        public void afterThrowing(JoinPoint joinPoint,Exception exception){
            //获取参数名
            String name = joinPoint.getSignature().getName();
            System.out.println(name+"方法运行异常,抛出错误:\n"+exception);
        }
    }
    ```

    > `@Aspect` ：表示该类是切面类
    >
    > `@Component`：将该类的对象注入到 IoC 容器
    >
    > `@Before` ：表示方法执行的具体位置和时机

    被代理类

    ```java
    @Component
    public class CalImpl implements Cal {
    
        @Override
        public int add(int num1, int num2) {
            return num1+num2;
        }
    
        @Override
        public int sub(int num1, int num2) {
            return num1-num2;
        }
    
        @Override
        public int mul(int num1, int num2) {
            return num1*num2;
        }
    
        @Override
        public int div(int num1, int num2) {
            return num1/num2;
        }
    }
    ```

    spring.xml

    ```xml
    <!-- 自动扫描 -->
    <context:component-scan base-package="com.y"/>
    <!-- 切面自动生成代理 -->
    <aop:aspectj-autoproxy/>
    ```

---------------------------------------------------------------------------------------------------- end --------------------------------------------------------------------------