##  My Batis 概述

- MyBatis 是 apache 的一个开源项目 iBatis ，2010 年这个项目由 apache software foundation 迁移到 google code ，并改名为 MyBatis，2013 年 11 月迁移到 Github。

- MyBatis 是一个实现了数据持久化的开源框架，简单理解就是对 JDBC 进行封装。
- ORMaping：Object Relationship Mapping 对象关系映射
    - 对象：面向对象
    - 关系：关系型数据库
    - 映射：java 到 MySQL 的映射
- MyBatis 的优点
    - 与 JDBC 相比，减少了 50% 以上的代码量
    - MyBatis 是最简单的持久化框架，小巧并且简单易学
    - MyBatis 相当灵活，不会对应用程序或者数据库的现有设计强加任何影响，SQL 写在 XML 里，从程序代码中彻底分离，降低耦合度，便于统一管理和优化，并可复用。
    - 提供 XML 标签，支持编写动态 SQL 语句。
    - 提供映射标签，支持对象与数据库的 ORM 字段关系映射
- MyBatis 的缺点
    - SQL 语句的编写工作量较大，尤其是字段多、关联表多时，更是如此，对开发人员编写 SQL 语句的功底有一定要求。
    - SQL 语句依赖于数据库，导致数据库移植性差，不能随意更换数据库。 

## 如何使用？

- 添加依赖

    ```xml
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.2</version>
    </dependency>
    
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.20</version>
    </dependency>
    ```

- 创建表

    ```mysql
    create table account(
        id int primary key auto_increment,
        name varchar(11),
        password varchar(11),
        age int
    )
    ```

- 创建数据表对应的实体类 Account

    ```java
    @Data
    @AllArgsConstructor
    public class Account {
        private long id;
        private String name;
        private String password;
        private int age;
    }
    ```

- 创建配置文件，文件名可自定义

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>
        <environments default="development">
            <environment id="development">
                <!-- 配置 JDBC 事务管理 -->
                <transactionManager type="JDBC"/>
                <!-- POOLED 配置 JDBC 数据源连接池 -->
                <dataSource type="POOLED">
                    <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                    <property name="url" value="jdbc:mysql://localhost:3306/mybatis001"/>
                    <property name="username" value="YSir"/>
                    <property name="password" value="001129"/>
                </dataSource>
            </environment>
        </environments>
        <mappers>
            <mapper resource="com/y/mapper/AccountMapper.xml"/>
        </mappers>
    </configuration>
    ```

> 使用原生接口

​		MyBatis 框架开发者自定义 SQL 语句,写在 Mapper.xml 文件中,实际开发中,会为每个实体类创建对应的 Mapper.xml ,定义管理改对象数据的 SQL.

- 创建 mapper.xml

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="com.y.mapper.accountMapper">
        <insert id="save" parameterType="com.y.entity.Account">
            insert into account(name, password, age) values (#{name},#{password},#{age})
        </insert>
    
    </mapper>
    ```
    - namespace 通常设置为文件所在包+文件名的形式.
    - insert 标签表示执行添加操作.
    - select 标签表示执行查询操作.
    - update 标签表示执行删除操作.
    - id 是实际调用 MyBatis 方法时需要用到的参数.
    - parameterType 是调用对应方法时参数的数据类型.

- 添加测试类

    ```java
    public class Test1 {
        public static void main(String[] args) {
            //加载 MyBatis 配置文件
            InputStream inputStream = Test1.class.getClassLoader().getResourceAsStream("config.xml");
            SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
            SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
            SqlSession sqlSession = sqlSessionFactory.openSession();
            String statement = "com.y.mapper.accountMapper.save";
            sqlSession.insert(statement,new Account(1L,"joker","253648",26));
            sqlSession.commit();
        }
    }
    ```

> 通过 Mapper 代理实现自定义接口

- 自定义接口,实现相关业务方法。
- 编写于方法对应的 Mapper.xml。

1. 自定义接口

    ```java
    public interface AccountRepository {
        /**
         * 增加一条数据
         * @param account 数据源
         * @return 受影响的行数
         */
        public int save(Account account);
    
        /**
         * 更新数据
         * @param account 数据源
         * @return 受影响的行数
         */
        public int update(Account account);
    
        /**
         * 通过 id 删除数据
         * @param id 数据源
         * @return 受影响的行数
         */
        public int deleteById(long id);
    
        /**
         * 返回所有数据
         * @return 所有数据
         */
        public List<Account> findAll();
    
        /**
         * 通过 id 查询目标
         * @param id 目标 id
         * @return 目标信息
         */
        public Account findById(long id);
    }
    ```

2. 创建接口对应的 Mapper.xml ,定义接口对应的 SQL 语句

    > statement 标签可根据 SQL 执行的业务选择 insert 、delete、update、select
    >
    > MyBatis 框架会根据规则自动创建接口实现类的代理对象

    - 规则：
        1. Mapper.xml 中 namespace 为接口的全类名
        2. Mapper.xml 中 statement 的 id 为接口中对应的方法名
        3. Mapper.xml 中 statement 的 parameterType 和接口中对应方法的参数类型一致。
        4. Mapper.xml 中 statement 的 resultType 和接口中对应方法的返回值类型一致。

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="com.y.repository.AccountRepository">
        <!-- 添加一条数据 -->
        <insert id="save" parameterType="com.y.entity.Account">
            insert into account(name,password,age) values (#{name},#{password},#{age})
        </insert>
        <!-- 通过 id 更新一条数据 -->
        <update id="update" parameterType="com.y.entity.Account">
            update account set name = #{name},password = #{password},age = #{age} where id = #{id}
        </update>
        <!-- 通过 id 删除一条数据 -->
        <delete id="deleteById" parameterType="long">
            delete from account where id = #{id}
        </delete>
        <!-- 查询所有数据 -->
        <select id="findAll" resultType="com.y.entity.Account">
            select * from account
        </select>
        <!-- 通过 id 返回所要查询的数据 -->
        <select id="findById" parameterType="long" resultType="com.y.entity.Account">
            select * from account where id = #{id}
        </select>
    </mapper>
    ```

3. 在 config.xml 中完成 Mapper 的注册

    ```xml
    <mapper resource="com/y/repository/AccountRepository.xml"/>
    ```

4. 测试类

    ```java
    public class Test2 {
        public static void main(String[] args) {
            //MyBatis 基本配置
            InputStream inputStream = Test2.class.getClassLoader().getResourceAsStream("config.xml");
            SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
            SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
            SqlSession sqlSession = sqlSessionFactory.openSession();
            //获取实现接口的代理对象
            AccountRepository accountRepository = sqlSession.getMapper(AccountRepository.class);
            //测试: 增加一条数据
    //        Account account = new Account(7L,"方圆","001234",34);
    //        accountRepository.save(account);
    //        sqlSession.commit();
            //测试: 更新数据
    //        Account account = new Account(7L,"方圆","isGood",34);
    //        accountRepository.update(account);
    //        sqlSession.commit();
            //测试: 通过 id 删除数据
    //        accountRepository.deleteById(7L);
    //        sqlSession.commit();
            //测试: 返回所有数据
    //        List<Account> accounts = accountRepository.findAll();
    //        for(Account account:accounts){
    //            System.out.println(account);
    //        }
            //测试: 通过 id 查询信息
    //        Account account = accountRepository.findById(5L);
    //        System.out.println(account);
            //释放资源
            sqlSession.close();
        }
    }
    ```

## Mapper.xml

- statement 标签：select 、update 、delete 、insert 分别对应查询、修改、添加操作。

- parameterType：参数数据类型

    1. 基本数据类型，通过 id 查询 Account

        ```xml
        <!-- 通过 id 返回所要查询的数据 -->
        <select id="findById" parameterType="long" resultType="com.y.entity.Account">
            select * from account where id = #{id}
        </select>
        ```

    2. String 类型，通过 name 查询 Account

        ```xml
        <!-- 通过 name 返回所要查询的数据 -->
        <select id="findByName" parameterType="java.lang.String" resultType="com.y.entity.Account">
            select * from account where name = #{name}
        </select>
        ```

    3. 包装类，通过 id 查询 account

        ```xml
        <!-- 通过 id 返回所要查询的数据 -->
        <select id="findById" parameterType="java.lang.long" resultType="com.y.entity.Account">
            select * from account where id = #{id}
        </select>
        ```

    4. 多个参数，通过 name 和 age 查询 Account

        ```xml
        <!-- 通过 id 和 name 返回所要查询的数据 -->
        <select id="findById"resultType="com.y.entity.Account">
            select * from account where id = #{param1} and name = #{param2}
        </select>
        ```

    5. JavaBean

        ```xml
        <update id="update" parameterType="com.y.entity.Account">
            update account set name = #{name},password = #{password},age = #{age} where id = #{id}
        </update>
        ```

- resultType：结果类型

    1. 基本数据类型，统计 Account 总数

        ```xml
        <select id="count" resultType="int">
            select count(*) from account
        </select>
        ```

    2. 包装类，统计 Account 总数

        ```xml
        <select id="count" resultType="java.lang.Integer">
            select count(*) from account
        </select>
        ```

    3. String 类型，通过 id 查询 Account 的 name

        ```xml
        <select id="findNameById" parameterType="long" resultType="java.lang.String">
        	select name from account where id = #{id}
        </select>
        ```

    4. JavaBean 类型

        ```xml
        <select id="findAll" resultType="com.y.entity.Account">
        	select * from account
        </select>
        ```

-  it is 占位符

## 级联查询

当表中存在一对一和一对多的关系时，查询表时需要级联处理

- 学生表 Student

    ```java
    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    public class Student {
        private long id;
        private String name;
        private Classes classes;
    }
    ```

- 教室表 Classes

    ```java
    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    public class Classes {
        private long id;
        private String name;
        private List<Student> students;
    }
    ```

- 持久层接口类 StudentRepository

    ```java
    public interface StudentRepository {
        /**
         * 通过 id 查询学生
         * @param id 学生 id
         * @return 学生信息
         */
        public Student findById(long id);
    }
    ```

- 持久层中的 Mapper.xml

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="com.y.repository.StudentRepository">
        <resultMap id="studentMap" type="com.y.entity.Student">
            <id column="s_id" property="id"/>
            <result column="s_name" property="name"/>
            <association property="classes" javaType="com.y.entity.Classes">
                <id column="c_id" property="id"/>
                <result column="c_name" property="name"/>
            </association>
        </resultMap>
        <select id="findById" parameterType="long" resultMap="studentMap">
            select s.s_id ,s.s_name ,c.c_id,c.c_name from classes c,student s where c.c_id = s.s_no and s.s_id = #{id}
        </select>
    </mapper>
    ```

- config.xml 中注册 Mapper.xml

    ```xml
    <mapper resource="com/y/repository/StudentRepository.xml"/>
    ```

- 测试类

    ```java
    public class Test3 {
        public static void main(String[] args) {
            // MyBatis 基本配置
            InputStream inputStream = Test3.class.getClassLoader().getResourceAsStream("config.xml");
            SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
            SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
            SqlSession sqlSession = sqlSessionFactory.openSession();
            // 接口代理类
            StudentRepository studentRepository = sqlSession.getMapper(StudentRepository.class);
            //测试: 实体类的级联查询
            System.out.println(studentRepository.findById(1L));
            //关闭资源
            sqlSession.close();
        }
    }
    ```

> 通过班级 id 查询学生信息

- ClassesRepository 

    ```java
    public interface ClassesRepository {
        /**
         * 通过 id 查询班级里的学生信息
         * @param id 班级 id
         * @return 班级信息
         */
        public Classes findById(long id);
    }
    ```

- ClassesRepository.xml

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="com.y.repository.ClassesRepository">
        <resultMap id="classesMap" type="com.y.entity.Classes">
            <id column="c_id" property="id"/>
            <result column="c_name" property="name"/>
            <collection property="students" ofType="com.y.entity.Student">
                <id column="s_id" property="id"/>
                <result column="s_name" property="name"/>
            </collection>
        </resultMap>
        <select id="findById" parameterType="long" resultMap="classesMap">
            select s.s_id ,s.s_name ,c.c_id,c.c_name from classes c,student s where c.c_id = s.s_no and c.c_id = #{id}
        </select>
    </mapper>
    ```

- config.xml 中注册

    ```xml
    <mapper resource="com/y/repository/ClassesRepository.xml"/>
    ```

- 测试类

    ```java
    public class Test4 {
        public static void main(String[] args) {
            // MyBatis 基本配置
            InputStream inputStream = Test4.class.getClassLoader().getResourceAsStream("config.xml");
            SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
            SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
            SqlSession sqlSession = sqlSessionFactory.openSession();
            // 接口代理类
            ClassesRepository classesRepository = sqlSession.getMapper(ClassesRepository.class);
            //测试: 通过班级 id 查询学生并存放到实体类 Classes
            System.out.println(classesRepository.findById(1L));
            //关闭资源
            sqlSession.close();
        }
    }
    ```

> 多对多关系可以拆解为两个一对多关系

## 逆向工程

MyBatis 框架需要：实体类、自定义 Mapper 接口、Mapper.xml

传统开发中上述的三个组件需要开发者手动创建，逆向工程可以帮助开发者来自动创建三个组件，减轻开发者的工作量，提高工作效率。

### 如何使用

MyBatis Generator ，简称 MBG，是一个专门为 MyBatis 框架开发者定制的代码生成器，可自动生成 MyBatis 框架所需要的实体类、Mapper 接口、Mapper.xml，支持基本的 CRUD 操作，但是一些相对复杂的 SQL 需要开发者自己来完成

- 新建 Maven 工程，pom.xml

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.20</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-core</artifactId>
            <version>1.3.2</version>
        </dependency>
    </dependencies>
    ```

- 创建 MBG 配置文件 generatorConfig.xml

    1. jdbcConnection 配置数据库连接信息
    2. javaModleGenerator 配置 JavaBean 的生成策略
    3. sqlMapGenerator 配置 SQL 映射文件的生成策略
    4. javaClientGenerator 配置 Mapper 接口的生成策略
    5. table 配置目标数据表（tableName：表名，domainObjectName：JavaBean 类名）

- 创建 generator.xml

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE generatorConfiguration
            PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
            "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
    <generatorConfiguration>
        <context id="testTables" targetRuntime="MyBatis3">
            <!-- 连接数据库 -->
            <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                            connectionURL="jdbc:mysql://localhost:3306/mybatis001"
                            userId="YSir"
                            password="001129"
            />
            <!-- JavaBean 生成策略 -->
            <javaModelGenerator targetPackage="com.y.entity"
                                targetProject="./src/main/java"
            />
            <!-- SQL 映射文件生成策略 -->
            <sqlMapGenerator targetPackage="com.y.repository"
                             targetProject="./src/main/java"
            />
            <!-- Mapper 接口的生成策略 -->
            <javaClientGenerator type="XMLMAPPER"
                                 targetPackage="com.y.repository"
                                 targetProject="./src/main/java"
            />
            <!-- 配置目标数据表 -->
            <table tableName="account"
                   domainObjectName="Account"
            />
        </context>
    </generatorConfiguration>
    ```

- 创建启动类

    ```java
    package com.y.test;
    
    import org.mybatis.generator.api.MyBatisGenerator;
    import org.mybatis.generator.config.Configuration;
    import org.mybatis.generator.config.xml.ConfigurationParser;
    import org.mybatis.generator.exception.InvalidConfigurationException;
    import org.mybatis.generator.exception.XMLParserException;
    import org.mybatis.generator.internal.DefaultShellCallback;
    
    import java.io.File;
    import java.io.IOException;
    import java.sql.SQLException;
    import java.util.ArrayList;
    import java.util.List;
    
    /**
     * @author Y2014188432
     */
    public class Test1 {
        public static void main(String[] args) {
            List<String> warings = new ArrayList<>();
            boolean overwrite = true;
            String genCig = "/generatorConfig.xml";
            File configFile = new File(Test1.class.getResource(genCig).getFile());
            ConfigurationParser configurationParser = new ConfigurationParser(warings);
            Configuration configuration = null;
            try{
                configuration = configurationParser.parseConfiguration(configFile);
                DefaultShellCallback callback = new DefaultShellCallback(overwrite);
                MyBatisGenerator myBatisGenerator = new MyBatisGenerator(configuration,callback,warings);
                myBatisGenerator.generate(null);
            } catch (XMLParserException | IOException | InvalidConfigurationException | InterruptedException | SQLException e) {
                e.printStackTrace();
            }
        }
    }
    ```

## 延迟加载

- 什么是延迟加载

    延迟加载也叫懒加载、惰性加载，使用延迟加载可以提高程序的运行效率，针对于数据持久层的操作，在某些特定的情况下去访问特定的数据库，在其他情况下可以不访问某些表，从一定程度上减少了 Java 应用与数据库的交互次数。

    查询学生和班级时，学生和班级是两张不同的表，如果当前需求只需要获取学生的信息，那么查询学生单表即可，如果需要通过学生获得对应的班级信息，则必须查询两张表。

    不同的业务需求，需要查询不同的表，根据具体业务需求来动态减少数据表查询的工作就是延迟加载。

- StudentRepository 添加方法

    ```java
    /**
         * 延迟加载 通过 id 查询学生
         * @param id 学生 id
         * @return 学生信息
         */
    public Student findByIdLazy(long id);
    ```

- StudentRepository.xml 实现方法

    ```xml
    <resultMap id="studentMapLazy" type="com.y.entity.Student">
        <id column="s_id" property="id"/>
        <result column="s_name" property="name"/>
        <association property="classes"
                     javaType="com.y.entity.Classes"
                     select="com.y.repository.ClassesRepository.findByIdLazy"
                     column="s_no"
        />
    </resultMap>
    <select id="findByIdLazy" parameterType="long" resultMap="studentMapLazy">
        select * from student where s_id = #{id}
    </select>
    ```

- ClassesRepository 中添加方法

    ```java
    /**
     * 延迟加载 通过 id 查询班级信息
     * @param id 班级 id
     * @return 班级信息
     */
    public Classes findByIdLazy(long id);
    ```

- ClassesRepository.xml 中实现

    ```xml
    <!-- 延迟加载: 通过 id 查询班级信息 -->
    <select id="findByIdLazy" parameterType="long" resultType="com.y.entity.Classes">
        select c.c_id as id, c_name as name from classes c where c_id = #{id}
    </select>
    ```

- config.xml 中开启延迟加载

    ```xml
    <setting name="lazyLoadingEnabled" value="true"/>
    ```

- 测试

    ```java
    public class Test6 {
        public static void main(String[] args) {
            // MyBatis 的基本配置
            InputStream inputStream = Test6.class.getClassLoader().getResourceAsStream("config.xml");
            SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
            SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
            SqlSession sqlSession = sqlSessionFactory.openSession();
            //接口的实现类
            StudentRepository studentRepository = sqlSession.getMapper(StudentRepository.class);
            //测试: 延迟加载
            Student student = studentRepository.findByIdLazy(1L);
            System.out.println(student.getName());
            //关闭资源
            sqlSession.close();
        }
    }
    ```

## MyBatis 缓存

- 什么是 MyBatis 缓存

    使用缓存可以减少 Java 应用于数据库的交互次数，conger提升程序的运行效率。比如查询出 id = 1 的对象，第一次查询出之后会自动将该对象保存到缓存中，当下一次查询时，直接从缓存中取出对象即可，无需再次访问数据库。

- MyBatis 缓存分类

    1. 一级缓存：SqlSession 级别，默认开启，并且不能关闭。

        操作数据库时需要创建 SqlSession 对象，在对象中有一个 HashMap 用于存储缓存数据，不同的 SqlSession 之间缓存数据区域是互不影响的。

        一级缓存的作用域是 SqlSession 范围的，当在同一个 SqlSession 中执行两次相同的 SQL 语句时，第一次执行完毕后会将结果保存到缓存中，第二次查询时直接从缓存中获取。

        需要注意的是，如果 SqlSession 执行了 DML 操作（insert、update、delete），MyBatis 必须将缓存清空以保证数据的准确性。 

        > 默认开启，无需多言

    2. 二级缓存：Mapper 级别，默认关闭，可以开启

        使用二级缓存是，多个 SqlSession 使用同一个 Mapper 的 SQL 语句操作数据库，得到的数据会存在二级缓存区，同样是使用 HashMap 进行数据储存，相比较于一级缓存，二级缓存的范围更大，多个 SqlSession 可以共用二级缓存，二级缓存是跨 SqlSession 的。

        二级缓存是多个 SqlSession 共享的，其作用域是 Mapper 的同一个 namespace ，不同的 SqlSession 两次执行相同的 namespace 下的 SQL 语句，参数也相等，则第一次执行成功之后会将数据保存到二级缓存中，第二次可直接从二级缓存中取出数据。 

> 经典

- 开启二级缓存

    ```xml
    <setting name="cacheEnabled" value="true"/>
    ```

- Mapper.xml

    ```xml
    <cache></cache>
    ```

- 实体类实现序列化接口

    ```java
    public class Account implements Seralizable{
    }
    ```

    > ehcache 二级缓存

- pom.xml 添加相关依赖

    ```xml
    <dependency>
    	<groupId>org.mybatis</groupId>
    	<artifacId>mybatis-ehcache</artifactId>
    	<version>1.0.0</version>
    </dependency>
    <dependency>
    	<groupId>net.sf.ehcache</groupId>
    	<artifacId>mybatis-core</artifactId>
    	<version>2.4.3</version>
    </dependency>
    ```

- 添加 ehcache.xml

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
             updateCheck="false">
    	<diskStore/>
        <!--
           defaultCache：默认缓存策略，当ehcache找不到定义的缓存时，则使用这个缓存策略。只能定义一个。
         -->
        <!--
          name:缓存名称。
          maxElementsInMemory:缓存最大数目
          maxElementsOnDisk：硬盘最大缓存个数。
          eternal:对象是否永久有效，一但设置了，timeout将不起作用。
          overflowToDisk:是否保存到磁盘，当系统当机时
          timeToIdleSeconds:设置对象在失效前的允许闲置时间（单位：秒）。仅当eternal=false对象不是永久有效时使用，可选属性，默认值是0，也就是可闲置时间无穷大。
          timeToLiveSeconds:设置对象在失效前允许存活时间（单位：秒）。最大时间介于创建时间和失效时间之间。仅当eternal=false对象不是永久有效时使用，默认是0.，也就是对象存活时间无穷大。
          diskPersistent：是否缓存虚拟机重启期数据 Whether the disk store persists between restarts of the Virtual Machine. The default value is false.
          diskSpoolBufferSizeMB：这个参数设置DiskStore（磁盘缓存）的缓存区大小。默认是30MB。每个Cache都应该有自己的一个缓冲区。
          diskExpiryThreadIntervalSeconds：磁盘失效线程运行时间间隔，默认是120秒。
          memoryStoreEvictionPolicy：当达到maxElementsInMemory限制时，Ehcache将会根据指定的策略去清理内存。默认策略是LRU（最近最少使用）。你可以设置为FIFO（先进先出）或是LFU（较少使用）。
          clearOnFlush：内存数量最大时是否清除。
          memoryStoreEvictionPolicy:可选策略有：LRU（最近最少使用，默认策略）、FIFO（先进先出）、LFU（最少访问次数）。
          FIFO，first in first out，这个是大家最熟的，先进先出。
          LFU， Less Frequently Used，就是上面例子中使用的策略，直白一点就是讲一直以来最少被使用的。如上面所讲，缓存的元素有一个hit属性，hit值最小的将会被清出缓存。
          LRU，Least Recently Used，最近最少使用的，缓存的元素有一个时间戳，当缓存容量满了，而又需要腾出地方来缓存新的元素的时候，那么现有缓存元素中时间戳离当前时间最远的元素将被清出缓存。
       -->
        <defaultCache
                maxElementsInMemory="1000"
                maxElementsInMemory="10000"
                eternal="false"
                overflowToDisk="false"
                timeToIdleSeconds="120"
                timeToLiveSeconds="120"
                diskExpiryThreadIntervalSeconds="120"
                memoryStoreEvictionPolicy="LRU"/> 
    </ehcache>
    ```

- config.xml 配置开启二级缓存

    ```xml
    <setting name="cacheEnabled" value="true"/>
    ```

- Mapper.xml 中配置二级缓存

    ```xml
    <cache type="org.mybatis.caches.ehcache.EhcacheCache">
    	<!-- 缓存创建之后，最后一次访问缓存的时间至缓存失效的时间间隔 -->
        <propert name="timeToIdlesSeconds" value="3600"/>
        <!-- 缓存自创建时间起至失效的时间间隔 -->
        <propert name="timeToLiveSecnds" value="3600"/>
        <!-- 缓存回收策略，LRU 表示移除近期使用最少的对象 -->
        <propert name="memoryStoreEvictionPolicy" value="LRU"/>
    </cache>
    ```

    

## MyBatis 动态 SQL 