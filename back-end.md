# [MyBatis](https://mybatis.net.cn/index.html)

> 一套简化 JDBC 开发的持久层框架

## JDBC

> 由Java提供操作数据库的规范（接口），数据库公司实现该类接口，供 Java 开发者使用。

```java
// 加载驱动类入内存
Class.forName("com.mysql.jdbc.Driver");
// 获取连接对象
String url = "jdbc:mysql://127.0.0.1:3306/db1?useSSL=false";
String username = "root";
String password = "root";
Connection conn = DriverManager.getConnection(url, username, password);
// 定义SQL
String sql = "update account set money = 2000 where id = 1";
// 获取执行SQL对象
Statement stmt = conn.createStatement();
// 执行SQL
int count = stmt.executeUdate(sql);
// 处理结果
System.out.println(count);
// 释放资源
stmt.close();
conn.close();
```

- DriverManger 的作用

  - 注册驱动：`static registerDriver(Driver driver)`
  - 获得连接：`static getConnection(String url, String username, String password)`

- 问题1：`Class.forName("com.mysql.jdbc.Driver")` 只将类加载入内存，DriverManger是如何注册该驱动的对象的呢？

  ```java
  package com.mysql.cj.jdbc;
  
  public class Driver enxtends NonRegisteringDriver implements java.sql.Driver {
      public Driver() throws SQLException;
      
      // 只要Driver类被加载，就会执行下面静态代码块的代码，从而完成注册。
      static {
          try {
              DriverManger.registerDriver(new Driver());
          }catch(SQLException var1) {
              throw new RuntimeException("Can't register driver!");
          }
      }
  }
  ```

- 问题2：使用MySQL5之后的驱动jar包，为什么不用添加 `Class.forName("com.mysql.jdbc.Driver")` 这一行代码

  - MySQL5 之后的驱动jar包使用了 <font color=red>SPI</font> 机制，暴露了驱动类的位置，并根据位置自动加载驱动类入内存，不需要手动指出位置并加载驱动类

  - 暴露实现类
    - 在ClassPath路径下创建 META-INF/services 的文件夹
    - 该目录下创建文件，以实现类作为文件内容，实现类实现的接口作为文件名（如文件名：java.sql.Driver  文件内容：com.mysql.cj.jdbc.Driver）

- Connection 的作用

  - 获取执行SQL的对象

    - 获得普通执行SQL的对象：`Statement createStatement()`

    - 预编译SQL的执行SQL对象：防止SQL注入：`PreparedStatement prepareStatement(含占位符的sql)`

      ```java
      String sql = "select * from tb_user where username = ? and password = ?";
      PreparedStatement pstmt = conn.prepareStatement(sql);
      pstmt.setString(1, name);
      pstmt.setString(2, pwd);
      ResultSet rs = pstmt.executeQuery();
      ```

      > 开启预编译：jdbc:mysql://127.0.0.1:3306/db1?useSSL=false&<font color=red>useServerPrepStmts=true</font>

      > java 程序在执行到 prepareStatement(sql) 方法后，会将含占位符的SQL发送给数据库，数据库收到后，进行语法检查、<font color=red>编译SQL</font>等操作，但不执行（因为此时内容是占位符）。
      >
      > 1. 后续等java程序传过来值后，再执行SQL，此时传入的值如果存在SQL注入的敏感字符，也不会重新编译
      > 2. 多条sql使用同一个含占位符sql，不会重复编译

    - 执行存储过程的对象：`CallableStatement prepareCall(sql)`

  - 管理事务

    - 开启事务：`setAutoCommit(boolean autoCommit)`
      - true：自动提交事务
      - false：手动提交事务，即开启事务
    - 提交事务：`commit()`
    - 回滚事务：`rollback()`

- Statement 的作用：执行SQL语句

  - 执行DML、DDL语句：`int executeUpdate(sql)`
    - 执行DML语句返回值：DML语句影响的行数
    - 执行DDL语句返回值：不能根据该返回值是否大于0判断是否执行成功，因为DDL执行成功，返回的也可能为0
  - 执行DQL语句：`ResultSet executeQuery(sql)` ，返回值为结果集

- 数据库连接池：分配管理数据库连接（Connection）

  - 标准接口：DataSource
    - 官方（SUN）提供的数据库连接池标准接口，由第三方组织实现
    - 功能：获得连接 `Connection getConnection`
  - 常见数据库连接池：Druid，Hikali，DBCP，C3P0





## 快速入门

- <span id="mybatis-config.xml">mybatis-config.xml</span>（MyBatis核心配置文件）

  > 用于替换连接信息，解决硬编码问题

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
    PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuration>
      <properties resource="配置文件如：jdbc.properties"/>
      <!-- 用于简化SQL映射文件中resultType属性的全路径类名的书写
      <typeAliases>
          <package name="包扫描路径如：cn.langh.domain"/>
      </typeAliases>
      -->
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
                  <property name="driver" value="${driver}"/>
                  <property name="url" value="${url}"/>
                  <property name="username" value="${username}"/>
                  <property name="password" value="${password}"/>
              </dataSource>
          </environment>
      </environments>
      <mappers>
          <mapper resource="SQL映射文件1位置"/>
          <mapper resource="SQL映射文件2位置"/>
          <!-- 满足一定条件可直接用包扫描 
          <package name="包扫描路径如：cn.langh.mappper"/> 
          -->
      </mappers>
  </configuration>
  ```

- XxxMapper.xml（SQL映射文件）

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <mapper namespace="命名空间">
      <select id="sql的id" resultType="返回的值的类型(pojo类的全路径限定名)">
          select * from Blog where id = #{id}
      </select>
  </mapper>
  ```

- 使用

  ```java
  public static void main(String[] args) {
      // 构建SqlSessionFactory
      String resource = "mybatis-config.xml在项目目录的位置";
      InputStream inputStream = Resources.getResourceAsStream(resource);
      SqlSessionFactory sqlSessionFactory = 
          new SqlSessionFactoryBuilder().build(inputStream);
  
      // 获取SqlSession对象，以此来执行sql（参数表明是否自动提交事务，不写默认false）
      SqlSession sqlSession = sqlSessionFactory.openSession(布尔值);
  
      // 执行SQL，返回值类型与SQL映射文件的resultType对应
      List<POJO> pojo = sqlSession.selectList("命名空间.sql的id");
  
      // 释放资源
      sqlsession.close();
  }
  ```





## Mapper代理

- 创建SQL映射文件，添加SQL语句

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <mapper namespace="命名空间">
      <select id="selectAll" resultType="cn.langh.POJO.User">
          select * from mybatis_01
      </select>
      <select id="selectById" resultType="cn.langh.POJO.User">
          select * from mybatis_01 where id = #{id}
      </select>
  </mapper>
  ```

- 根据SQL映射文件，创建对应的映射接口

  ```java
  public interface UserMapper {
      // 方法名和返回参数 与 sql语句的id和resultType保持一致
      List<User> selectAll();
      User selectById(@Param("id") int id);
  }
  ```

- 将xml映射文件与映射接口放在同一目录中

- 设置SQL映射文件的namespace属性为Mapper接口的全限定名

  ```xml
  <mapper namespace="cn.langh.mapper.UserMapper">
      <select id="selectAll" resultType="cn.langh.POJO.User">
          select * from mybatis_01
      </select>
      <select id="selectById" resultType="cn.langh.POJO.User">
          select * from mybatis_01 where id = #{id}
      </select>
  </mapper>
  ```

- 构建SqlSessionFactory

- 获取SqlSession

- 执行sql

  - 获取UserMapper接口的代理对象

  ```java
  UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
  ```

  - 执行方法

  ```java
  List<User> users = userMapper.selectAll();
  ```

- 释放资源

- 如果Mapper接口的名称和sql映射文件的名称相同，并在同一目录下，则可以使用包扫描的方式**简化sql映射文件的加载**

  ```xml
  <!-- mybatis-config.xml 文件内  -->
  <mappers>
      <!--  <mapper resource="cn/langh/mapper/UserMapper.xml"/>  -->
      <package name="cn.langh.mappper"/>
  </mappers>
  ```

  



## mybatis核心配置文件

- configuration（配置）
  - [properties（属性）](https://mybatis.net.cn/configuration.html#properties)
  - [settings（设置）](https://mybatis.net.cn/configuration.html#settings)
  - [typeAliases（类型别名）](https://mybatis.net.cn/configuration.html#typeAliases)
  - [typeHandlers（类型处理器）](https://mybatis.net.cn/configuration.html#typeHandlers)
  - [objectFactory（对象工厂）](https://mybatis.net.cn/configuration.html#objectFactory)
  - [plugins（插件）](https://mybatis.net.cn/configuration.html#plugins)
  - environments（环境配置）
    - environment（环境变量）
      - transactionManager（事务管理器）
      - dataSource（数据源）
  - [databaseIdProvider（数据库厂商标识）](https://mybatis.net.cn/configuration.html#databaseIdProvider)
  - [mappers（映射器）](https://mybatis.net.cn/configuration.html#mappers)

- **environments**：配置数据库连接环境信息。可以配置多个environment，通过default属性切换不同的environment
- **typeAliases**：可指定pojo包，会为包中的实体类自动取类型别名，**别名不区分大小写**，在mapper配置中的resultType中可使用该别名，**用于简化resultType中全路径类名的书写**
- **<font color=blue>配置标签是需要遵循一定的顺序</font>**



## 其他问题

- 数据库的字段名称 和 实体类的属性名称 不一样时，不能自动封装。三种解决方法：

  - 方法1：查询时取别名：对不一样的列名取别名，让别名与实体类属性名一致

    ```xml
    <select id="selectAll" resultTyep="User">
    	select id, user_name(数据库字段名称) as userName(别名) from user(表);
    </select>
    ```

  - 方法2：将sql片段抽取出来

    ```xml
    <sql id="sql_01">id, user_name as userName</sql>
    
    <select id="selectAll" resultTyep="User">
    	select
        	<include regid="sql_01"/>
        from user(表);
    </select>
    ```

  - 方法3：映射
    - id：唯一标识
    - type：放回的值的类型，也支持别名
    - 子标签
      - id：完成主键字段的映射
      - column：完成一般字段的映射
    
    ```xml
    <resultMap id="resultMap_01" type="user">
    	<result column="列名称" property="实体类的属性名"/>
        <result id="列名称" property="实体类的属性名"/>
    </resultMap>
    
    <select id="selectAll" resultType="reusltMap_01">
    	select 字段 from user(表)
    </select>
    ```

- 参数占位符

  - `#{参数}`：会将其替换为" **?** "。为了防止sql注入
  - `${参数}`：拼接sql，会存在sql注入问题

- 参数类型：select标签中的 **parameterType** 可以设置所传递的参数类型，可以省略

- 特殊字符的处理：

  - 转义字符：<：`&lt;`
  - cData区：将字符写在`<![CDATA[ 内容 ]]>`中
  
- 添加语句中的主键返回

  \<insert>标签中useGeneratedKeys属性属性设置为true，keyProperty设置为对象的主键



## 参数传递

> MyBatis 提供了 ParamNameResolver 类来进行参数封装

- 多个参数：当形参为多个参数时，会封装为Map集合

  ```java
  User login(@Param("username") String username,
             @Param("password") String password);
  
  /*  
  如果不添加 @Param 注解
    map.put("arg0", 参数值1); map.put("param1", 参数值1);
    map.put("arg1", 参数值2); map.put("param2", 参数值2);
    ......
  如果添加 @Param 注解
    map.put("param1", 参数值1); map.put("注解1value", 参数值1);
    map.put("param2", 参数值2); map.put("注解2value", 参数值2);
    
  @Param 注解中的value替换的是键名arg
  */
  ```

  ```xml
  <select id="login" resultType="cn.langh.POJO.User">
      select * from users 
      where username = #{}
      and password = #{}
  </select>
  <!--
  如果形参前不添加 @Param 注解
    #{} 中可填 arg0/param1; arg1/param2; ......
  如果形参前添加 @Param 注解
    #{} 中可填 param1/注解1value; param2/注解2value; ......
  -->
  ```

- 单个参数

  ```java
  User selectById(int id);
  User login(Map<String, String> map);
  ```

  - pojo类型：直接使用，方法的形参名 和 参数占位符名 一致（#{}中直接填映射接口中方法中的对应形参名）

  - map集合：直接使用，map中的键名 和 参数占位符名 一致

  - Collection：封装为Map集合，可以使用@Param注解，替换集合中默认的arg键名

    ```java
    map.put("arg0",collection集合坐标);
    map.put("collection",collection集合坐标);
    ```

  - List：封装为Map集合，可以使用@Param注解，替换集合中默认的arg键名

    ```java
    map.put("arg0",collection集合坐标);
    map.put("collection",collection集合坐标);
    map.put("list",collection集合坐标);
    ```

  - Array：封装为Map集合，可以使用@Param注解，替换集合中默认的arg键名

    ```java
    map.put("arg0",数组);
    map.put("array",数组);
    ```

  - 其他类型：如int，double等，直接使用



## 动态sql

- \<if>标签

```xml
<if test="\布尔表达式\"> 内容 </if>
```

```xml
<select id="..." resultMap="...">
    select * form tb_brand
    <where>
        <if test="companyName != null and companyName != ''">
            and company_name like #{companyName}
        </if>
        <if test="status != null">and status = #{status}</if>
    </where>
</select>
```

- \<choose>标签

```xml
<choose>
	<when test=""></when>
    <when test=""></when>
    <otherwise></otherwise>
</choose>
```

- \<where>标签：可自动检测动态sql是否存在由于拼接导致的一部分问题




## 注解开发

> 用注解开发更加方便，但是只适合一些简单的语句，对于稍微复杂的语句，Java注解不仅力不从心，还会让本就复杂的语句更加混乱不堪。

- @Select

```java
@Select("select * from user where id = #{id}")
User selectById(int id);
```

- @Insert
- @Update
- @Delete









# JavaWeb

> JavaWeb三大组件：Servlet、Filter、Listener

- tomcat 依赖导入

  ```xml
  <!-- 打包方式是war包，并通过maven插件中的tomcat7:run启动web项目 -->
  <plugin>
      <groupId>org.apache.tomcat.maven</groupId>
      <artifactId>tomcat7-maven-plugin</artifactId>
      <version>2.2</version>
      <configuration>
          <port>80</port>
          <path>/</path>
      </configuration>
  </plugin>
  ```

- Servlet、Filter、Listener依赖导入

  ```xml
  <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <!-- 编译、测试环境有效，运行环境无效 -->
      <!-- 项目部署在Tomcat上时，Tomcat中已有该jar包，所以运行时排除jar包，防止冲突 -->
      <scope>provided</scope> 
  </dependency>
  ```



## Tomcat

- 项目结构

  - 开发中的项目结构

    ```tree
    ├─项目包
    │  └─src
    │     ├─main
    │     │  ├─java源代码包
    │     │  ├─resources
    │     │  └─webapp
    │     │     ├─html
    │     │     ├─js
    │     │     ├─css
    │     │     └─WEB-INF
    │     │        └─web.xml
    │     └─test
    ```

  - 部署中的项目结构

    ```tree
    ├─项目包
    │  ├─html
    │  ├─js
    │  ├─css
    │  └─WEB-INF
    │     ├─classes（程序字节码文件）
    │     ├─lib
    │     └─web.xml
    ```



## Servlet

### 快速入门

- 使用Tomcat

  - 方法1：idea设置Tomcat位置

  - 方法2：maven的Tomcat内嵌插件

- 创建web项目，导入Servlet依赖坐标

  ```xml
  <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <scope>provided</scope> 
  </dependency>
  ```

- 创建：

  - 定义一个类，实现Servlet接口，重写接口中的方法。
  - 并加上`@WebServlet("uri")`指定uri路径的访问路径
  - 注意：不要写成`"/demo/"`
  
  ```java
  @WebServlet("/demo")
  public class ServletDemo implements Servlet {
      @Override
      public void service(ServletRequest servletRequest, ServletResponse servletResponse) 
          throws ServletException, IOException {
          System.out.println("service方法执行了");
      }
      
      // ... ...
  }
  ```



### 生命周期

- Servlet运行在**Servlet容器**（web服务器，可以是 Tomcat、Jetty 或 Undertow 等。这个 Servlet 容器负责管理 Servlet 的生命周期，包括创建 Servlet 对象、调用 Servlet 方法等。）中，其生命周期由容器来管理，分为4个阶段

  1. **加载和实例化**：**默认情况下**，当Servlet第一次被访问时，由容器创建Servlet对象
  2. **初始化**：当Servlet实例化之后，容器将调用Servlet的 `init()` 方法初始化这个对象，完成一些如加载配置文件、创建连接等初始化工作。该方法只**调用一次**
  3. **请求处理**：**每次**请求Servlet时，Servlet容器都会调用Servlet的 `service()` 方法对请求进行处理
  4. **服务终止**：当需要释放内存或者容器关闭时，容器就会调用Servlet实例的 `destory()` 方法完成资源的释放。当 `destory()` 方法调用之后，容器会释放这个Servlet实例，该实例随后会被Java垃圾回收器所回收

- 改变创建Servlet对象的时机 `@WebServlet(urlPatterns="/demo", loadOnStartup=1)`

  - 负整数：第一次被访问时创建Servlet对象
  - 0或正整数：服务器启动时创建Servlet对象，**数字越小，优先级越高**

- Servlet 接口

  ```java
  public interface Servlet {
      void init(ServletConfig var1) throws ServletException;
  
      void service(ServletRequest var1, ServletResponse var2) 
          throws ServletException, IOException;
      
      void destroy();
  
      // 可用于返回一些信息，如作者、版本等
      String getServletInfo();
      
      // init方法被调用时，会传入ServletConfig对象
      // 将该对象作用于提升（提升为成员变量），并在init调用时赋值，即可在此方法中返回
      ServletConfig getServletConfig();
  }
  ```

- Servlet 体系结构

  - GenericServlet 抽象类实现了Servlet接口，剩下一个 `service(...)` 方法待实现

    ```java
    public abstract class GenericServlet 
        implements Servlet, ServletConfig, Serializable
    ```

    ```java
    @WebServlet("/demo")
    public class ServletDemo extends GenericServlet {
    
        @Override
        public void service(ServletRequest servletRequest, ServletResponse servletResponse) 
            throws ServletException, IOException {
    
        }
    }
    ```

  - HttpServlet 类继承了 GenericServlet 抽象类，实现了 `service(...)` 方法。并将该方法按请求类型分配给 `doGet(...)`、`doPost(...)`、`doPut(...)`、`doDelete(...)`等方法执行，可通过重写这些方法得到相应的功能



### 其他配置

- urlPattern 配置

  - 一个Servlet，可以配置多个访问路径：`@WebServlet(urlPatterns = {"/demo01", "/demo02"})`

  - urlPattern的配置规则（若满足多个servlet路径，只会匹配一个，精确匹配的优先级高）
    - 精确匹配：`@WebServlet("/user/select")`
    - 目录匹配：`@WebServlet("/user/*")`
    - 扩展名匹配：`@WebServlet("*.abc")`（不能以 **/** 开头）
    - 任意匹配：`@WebServlet("/")`

  - 存在一个名叫 `DefaultServlet` 的默认Servlet，当其他所有Servlet都无法匹配上时，会将请求交给 `DefaultServlet`。该Servlet的作用是在静态资源中（html、png等）找同路径的资源
- XML配置方式编写Servlet

  - Servlet从3.0版本开始支持使用注解配置，3.0之前只支持xml配置文件的配置方式
  - 方法：略



### 请求与响应

- Request

  - Request继承体系

    - ServletRequest：java提供的请求接口
    - HttpServletRequest：java提供，继承自ServeltRequest，对Http协议封装的请求对象接口
    - RequestFacade：Tomcat定义的实现类

  - tomcat7 中请求中文乱码

    > tomcat7 中实现的 Request 接口
    >
    > > 在解析Post之类的请求时，会通过字符流读取参数，可通过设置字符流字符集解决
    > >
    > > 在解析Get之类的请求时，直接用ISO-8859-1字符集解码，从而导致乱码

    - Post请求乱码：`request.setCharacterEncoding("UTF-8")`

    - Get请求乱码（以请求路径：`/localhost/request-demo?firstName=张` 为例）

      - 流程：

        `张` --utf-8编码--> `1110 0101   1011 1100   1010 0000` 

        --URL编码--> `%E5%BC%A0` 

        --传输到服务器--> `%E5%BC%A0` 

        --URL解码--> `1110 0101   1011 1100   1010 0000`

        --ISO-8859-1解码--> `???`

      - 解决方法流程：

        `???` --ISO-8859-1编码-->  `1110 0101   1011 1100   1010 0000`

        --utf-8解码--> `张三`

        ```java
        String username = request.getParamter("username"); // 乱码
        
        byte[] bytes = username.getBytes(StandardCharsets.ISO_8859_1);
        username = new String(bytes, StandardCharsets.UTF-8);
        ```

  - Request请求转发（多servlet共同处理请求，**同一服务器内部的跳转**）

    - 实现方式：`req.getRequestDispatcher("转发资源路径").forward(req, resp);`

    - 请求转发资源间数据共享，使用Request对象

      | 返回值 | 方法名          | 参数                  | 作用                  |
      | ------ | --------------- | --------------------- | --------------------- |
      | void   | setAttribute    | String name, Object o | 存储数据到request域中 |
      | Object | getAttribute    | String name           | 根据key，获得值       |
      | void   | removeAttribute | String name           | 根据key，删除对应键值 |

- Response

  - Response继承体系

    - ServletResponse
    - HttpServletResponse
    - ResponseFacade

  - Response请求重定向（非本servlet处理内容，重定向的正确的处理位置，**服务器内、外部均可跳转**）

    ```java
    // 设置状态码
    response.setStatus(302);
    // 重定向，设置响应头 Location
    response.setHeader("Location", "/虚拟目录（一般是项目名）/重定向资源路径");
    ```

    ```java
    // 简化重定向
    response.sendRedirect("/虚拟目录/资源路径");
    ```

  - 设置返回类型和字符集

    ```java
    // 如果返回的内容包含需要解析的html标签，需要设置解析文件类型为html
    response.setHeader("content-type", "text/html");
    // 简化
    response.setContentType("text/html;charset=utf-8");
    
    // 流不需要关闭，它会随着响应的结束，response对象的销毁而关闭
    PrintWriter writer = resp.getWriter();
    writer.write("<h1>你好，世界！</h1>");
    ```

- 什么时候需要加虚拟目录，什么时候不需要加虚拟目录？
  - 明确路径供谁使用
    - 浏览器使用：需要加虚拟目录（项目访问路径）
    - 服务端使用：不需要加虚拟目录
  - 举例：
    - `<a href="路径">`：需要加
    - `<form action="路径">`：需要加
    - `req.getRequestDispatcher("路径")`：不用加
    - `resp.sendRedirect("路径")`：需要加
  - 动态获取虚拟目录：`request.getContextPath()`





## Filter

> 过滤器一般完成一些**通用**的操作，如：权限控制、统一编码处理、敏感字符处理等

- 快速入门

  - 定义类，实现Filter接口，并重写其所有方法
  - 配置Filter拦截资源的路径：在类上定义`@WebFilter("拦截资源路径")`注解

  ```scala
  @WebFilter(Array("/*"))
  class FilterDemo01 extends Filter{
      override def init(filterConfig: FilterConfig): Unit = {}
  
      override def doFilter(servletRequest: ServletRequest, servletResponse: ServletResponse, filterChain: FilterChain): Unit = {
          val map = servletRequest.getParameterMap
          if (map.containsKey("username")) {
              // 放行
              filterChain.doFilter(servletRequest, servletResponse)
              // 此处代码在servlet执行完后执行
              System.out.println("即将返回资源");
              return
          }
          println("拦截请求")
          val response = servletResponse.asInstanceOf[HttpServletResponse]
          response.setStatus(HttpServletResponse.SC_FORBIDDEN)
          response.getWriter.println("The request has been intercepted")
      }
  
      override def destroy(): Unit = {}
  }
  ```

- 过滤器链：略





## Listener

> 用来监听Application、Session、Request这三个对象的创建、销毁和属性变化情况

- 快速入门

  ```scala
  @WebListener
  class MyListener extends ServletContextListener {
    override def contextInitialized(servletContextEvent: ServletContextEvent): Unit = ???
  
    override def contextDestroyed(servletContextEvent: ServletContextEvent): Unit = ???
  }
  ```

- 监听器接口

  | 监听器分类         | 监听器接口名                    | 监听内容                  |
  | ------------------ | ------------------------------- | ------------------------- |
  | ServletContext监听 | ServletContextListener          | 创建、销毁                |
  |                    | ServletContextAttributeListener | 属性变化                  |
  | Session监听        | HttpSessionListener             | 创建、销毁                |
  |                    | HttpSessionAttributeListener    | 属性变化                  |
  |                    | HttpSessionBindingListener      | 对象与Session的绑定、解除 |
  |                    | HttpSessionActivationListener   | Session数据的钝化和活动   |
  | Request监听        | ServletRequestListener          | 创建、销毁                |
  |                    | ServletRequestAttributeListener | 属性变化                  |









# Spring



## 核心容器

### bean实例化

- **方式一：构造方法实例化bean**

```xml
<bean id="userDao" class="cn.langh.dao.impl.UserDaoImpl"/>
```

```xml
<bean id="userService" class="cn.langh.service.impl.UserServiceImpl">
	<property name="userDao" ref="userDao"/>
</bean>
```



- **方式二：使用工厂实例化bean**

  1. 使用静态工厂实例化bean

     ```xml
     <bean id="userDao" class="cn.langh.factory.UserDaoFactory" factory-method="getUserDao"/>
     ```

  2. 使用实例工厂实例化bean

     - 方式一

     ```xml
     <bean id="userServiceFactory" class="cn.langh.factory.UserServiceFactory"/>
     <bean id="userService" factory-bean="userServiceFactory" factory-method="getUserService"/>
     ```
     
     - 方式二
     
     ```java
     public class UserDaoFactoryBean implements FactoryBean<UserDao> {
        
        @Override
        public UserDao getObject() throws Exception {
            return new UserDaoImpl();
        }
            
        @Override
        public Class<?> getObjectType() {
            return UserDao.class;
        }
        
        @Override
        public boolean isSingleton() {
            return true;
        }
     }
     ```
     
     ```xml
     <bean id="userDao3" class="cn.langh.factory.UserDaoFactoryBean"/>
     ```



### bean的生命周期

- 生命周期
  
  0. BeanDefinition的加载流程
  
  1. bean对象的创建（调用无参构造）
  2. 给bean对象设置相关属性
  3. bean后置处理器（初始化之前）
  4. bean对象初始化（调用指定初始化方法）
  5. bean后置处理器（初始化之后）
  6. bean对象创建完成，可以使用
  7. bean对象销毁（调用指定销毁方法）
  8. IOC容器关闭
  
- 初始化、销毁方法的设置

  - 方法一：bean 中指定 **init** 方法和 **destroy** 方法

  ```xml
  <bean id="userDao" class="cn.langh.dao.impl.UserDaoImpl" init-method="init" destroy-method="destroy"/>
  ```

  - 方法二：实现 **InitializingBean** 和 **DisposableBean** 接口,重写方法
  
  ```java
  @Override
  public void destroy() throws Exception {
      System.out.println("销毁方法");
  }
  
  @Override
  //配置完成后执行
  public void afterPropertiesSet() throws Exception {
      System.out.println("初始化方法");
  }
  ```

- 后置处理器

  - 创建后置处理器，实现后置处理器接口，重写里面两个默认方法

  ```java
  public class MyBeanPostProcessor implements BeanPostProcessor {
      @Override
      public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
          System.out.printf("后置处理器 %s初始化前执行\n", beanName);
          return bean;
      }
  
      @Override
      public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
          System.out.printf("后置处理器 %s初始化后执行\n", beanName);
          return bean;
      }
  }
  ```

  - 配置后置处理器这个bean

  ```xml
  <bean id="..." class="cn.eli.bean.MyBeanPostProcessor"/>
  ```

  

  

### 依赖注入

- setter注入

  - 简单注入

  ```java
  @Setter
  public class UserDao implements UserDaoImpl{
      private int connectionNum;
      private String databaseName;
  }
  ```

  ```xml
  <bean id="userDao" class="cn.langh.dao.impl.UserDaoImpl">
      <property name="connectionNum" value="10"/>
      <property name="databaseName" value="mysql"/>
  </bean>
  ```

  - 引用注入

  ```java
  @Setter
  public class BookServiceImpl implements BookService{
      private UserDao userDao;
      private BookDao bookDao;
  }
  ```

  ```xml
  <bean id="userDao" class="cn.langh.dao.impl.UserDaoImpl"/>
  
  <bean id="userService" class="cn.langh.service.impl.UserServiceImpl">
      <!-- 外部bean -->
      <property name="userDao" ref="userDao"/>
      <!-- 内部bean -->
      <property name="bookDao">
          <bean id="bookDao" class="......"/>
      </property>
  </bean>
  ```

- 构造器注入

  - 简单类型

  ```java
  public class BookDaoImpl implements BookDao{
      private int connectionNum;
      private String databaseName;
      
      public BookDaoImpl(int connectionNum,String databaseName){
          this.connectionNum = connectionNum;
          this.databaseName = databaseName;
      }
  }
  ```

  ```xml
  <bean id="bookDao" class="cn.langh.dao.impl.BookDaoImpl">
      <!-- 标准注入方式 -->
      <constructor-arg name="databaseName" value="mysql"/>
      <constructor-arg name="connectionNum" value="10"/>
      
      <!-- 解耦合注入方式 -->
      <!-- 依靠参数类型注入 -->
      <constructor-arg type="int" value="10"/>
      <!-- 依靠参数位置注入 -->
      <constructor-arg index="0" value="10"/>
  </bean>
  
  
  ```
  
- 引用类型

```java
public class BookServiceImpl implements BookService {
    private BookDao bookDao;
    private UserDao userDao;

    public BookServiceImpl(BookDao bookDao,UserDao userDao){
        this.bookDao = bookDao;
        this.userDao = userDao;
    }
}
```

```xml
<bean id="bookService" class="cn.langh.service.impl.BookServiceImpl">
    <constructor-arg name="bookDao" ref="bookDao"/>
    <constructor-arg name="userDao" ref="userDao"/>
</bean>
```

- 自动装配（优先级低于setter注入 与 构造器注入）

```xml
<bean id="userService" class="cn.langh.service.impl.UserServiceImpl" autowire="byType"/>
```

```xml
<bean id="userService" class="cn.langh.service.impl.UserServiceImpl" autowire="byName"/>
```

- 集合注入（setter注入）

  - array

  ```xml
  <property name="hobbies">
      <array>
          <value>抽烟</value>
          <value>喝酒</value>
          <value>烫头</value>
      </array>
  </property>
  ```

  - List

  ```xml
  <property name="list">
      <list>
          <value>zhangsan</value>
          <value>lisi</value>
          <value>wangwu</value>
      </list>
  </property>
  ```

  - Set

  ```xml
  <property name="set">
      <set>
          <value>zhangsan</value>
          <value>lisi</value>
          <value>wangwu</value>
          <value>wangwu</value>
      </set>
  </property>
  ```

  - Map

  ```xml
  <property name="map">
      <map>
          <entry key="zhangsan" value="18"/>
          <entry key="lisi" value="19"/>
          <entry key="wangwu" value="20"/>
      </map>
  </property>
  ```

  - Properties

  ```xml
  <property name="properties">
      <props>
          <prop key="zhangsan">18</prop>
          <prop key="lisi">19</prop>
          <prop key="wangwu">20</prop>
      </props>
  </property>
  ```

- 特殊值的注入

  ```xml
  <bean id="..." class="......">
      <!-- null值的注入 -->
      <property name="..."><null/></property>
      <!-- 特殊字符的注入，如“ <> ”（xml实体） -->
      <property name="..." value="&lt;&gt;"></property>
      <!-- CDATA区 -->
      <property name="...">
          <value><![CDATA[ 特殊符号 ]]></value>
      </property>
  </bean>
  ```

- 级联赋值

  ```java
  @Setter
  @Getter // 级联赋值的getter是必要的
  public class A {
      public String name;
  }
  ```

  ```java
  @Setter
  public class B {
      public A a;
  }
  ```

  ```xml
  <bean id="a" class=".....">
      <property name="name" value="aaa"/>
  </bean>
  
  <bean id="b" class="......">
      <property name="a" ref="a"/>
      <!-- 级联赋值 -->
      <property name="a.name" value="bbb"/>
  </bean>
  ```

- [p命名空间注入](https://www.bilibili.com/video/BV1kR4y1b7Qc?p=30&vd_source=25ad2de4838bd28372a4956bac63c618)：略
- [引用集合类型的bean](https://www.bilibili.com/video/BV1kR4y1b7Qc?p=29&vd_source=25ad2de4838bd28372a4956bac63c618)：略

### 数据源对象管理

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/test"/>
    <property name="username" value="root"/>
    <property name="password" value="root"/>
</bean>
```

- 加载外部properties

  - 引入context命名空间（添加约束）

    ```xml
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd">
    ```

  - 使用context空间加载properties文件

    ```xml
    <context:property-placeholder location="jdbc.properties"/>
    
    <bean id="dataSource2" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
    ```

- 注：

  1. 命名空间默认为优先加载系统属性，可在context标签中设置 `system-properties-mode="NEVER"`

  2. context标签中 location 属性设置为 `*.properties` 表示加载所有后缀为properties的文件

  3. 加载properties的标准格式为 `location="classpath:*.properties"`

  4. 从类路径或jar包中搜索并加载properties文件格式为 `location="classpath*:*.properties"`



### 容器

- 获取容器 

  ```java
  new ClassPathXmlApplicationContext("applicationContext.xml");
  ```

  ```java
  new FileSystemXmlApplicationContext("绝对路径");
  ```

- 获取bean

  ```java
  UserDao userDao = (UserDao) app.getBean("userDao");
  UserDao userDao = app.getBean(UserDao.class);
  UserDao userDao = app.getBean("userDao",userDao.class);
  ```
  
- 注：

  - BeanFactory 是 IoC 容器的顶层接口，初始化BeanFactory对象时，加载的bean延迟加载
  - ApplicationContext 接口是 spring 容器的核心接口，初始化时bean立即加载，可用 `lazy-init` 属性控制

  - ApplicationContext 接口提供的 bean 操作相关方法，通过替他接口拓展其功能

  - ApplicationContext 接口常用的初始化类
    - ClassPathXmlApplicationContext
    - FileSystemXmlApplicationContext




### 总结

```xml
<bean
	id="bookDao"
	name="dao bookDaoImpl daoImpl"
	class="cn.langh.dao.impl.bookDaoImpl"
	scope="singleton/prototype"
	init-method="init"
	destroy-method="destroy"
	autowire="byType/byName"
	factory-method="getInstance"
	factory-bean="cn.langh.factory.BookDaoFactory"
	lazy-init="true"
/>
```





## 注解开发

> Spring从2.5开始提供了对注解技术的全面支持

### 定义bean

- 定义bean

  - `@Component("名称")`（注解在类上）
    - 业务层：`@Service`
    - 数据层：`@Repository`
    - 表现层：`@Controller`

- bean的管理

  - `@Scope("singleton/prototype")`（注解在类上）

  - 生命周期（注解在方法上）
    - 初始化方法：`@PostConstruct`（构造后运行）
    - 销毁方法：`@PreDestory`（销毁前运行）

- 组件扫描

  - xml

    ```xml
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd">
        
    <context:component-scan base-package="cn.langh"/>
    ```

  - 注解
    - `@ComponentScan("cn.langh")`
    - `@ComponentScan({"cn.langh.dao","cn.langh.service})`

- 配置类
  - 注解：`@Configuration`
  - 添加外部配置：`@ImportResource("applicationContext.xml")`
  - 组件扫描：`@ComponentScan`
  - 导入其他配置类：`@Import({jdbcConfig.class,...})`（其他配置类上不用加`@Configuration`）
  - 获取容器：`new AnnotationConfigApplicationContext(SpringConfig.class)`



### 依赖注入

- 配置文件
  - 加载：
    - `@propertySource({"文件名1", "文件名2", ...})`
    - `@PropertySources({@PropertySource("文件1"), @PropertySource("文件2"), ...})`
  - 使用：`${键}`
  
- 简单类型注入
  - `@Value("值")`

- 自动装配

  - `@Autowired`（可注解在构造方法上、方法上、成员变量上、形参上、注解上）

    - **先按类型注入，若容器中含相同类型的bean，则使用属性名作为名称查找**
    - 该注解加在属性上，并不依赖于setter，而是通过反射直接对属性赋值
  
  - `@Qualifier("名称")`：依赖于@Autowired，只注入指定名称的bean
  
  - `@Resource("名称")`（可注解在属性上、setter方法上）
  
    - **先根据名称装配**，未指定名称时，使用属性名作为名称查找。通过名称无法找到bean时，会自动**启用类型装配**
  
    - 是JakartaEE的规范之一，在JDK拓展包中【仅jdk8不需要引入依赖】
  
      ```xml
      <dependency>
          <groupId>jakarta.annotation</groupId>
          <artifactId>jakarta.annotation-api</artifactId>
          <version>2.1.1</version>
      </dependency>
      ```
  





## Spring整合Mybaits

- 位置坐标

  ```xml
  <dependencies>
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-context</artifactId>
          <version>5.2.10.RELEASE</version>
      </dependency>
      <dependency>
          <groupId>org.mybatis</groupId>
          <artifactId>mybatis</artifactId>
          <version>3.5.10</version>
      </dependency>
      <dependency>
          <groupId>com.alibaba</groupId>
          <artifactId>druid</artifactId>
          <version>1.0.9</version>
      </dependency>
      <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
          <version>5.1.46</version>
      </dependency>
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-jdbc</artifactId>
          <version>5.2.10.RELEASE</version>
      </dependency>
      <!--spring整合mybatis的包，由spring提供接口，mybatis实现-->
      <dependency>
          <groupId>org.mybatis</groupId>
          <artifactId>mybatis-spring</artifactId>
          <version>1.3.1</version>
      </dependency>
  </dependencies>
  ```

- 需要加载哪些bean？[可对应查看mybatis的核心配置文件](#mybatis-config.xml)

- 配置druidbean

  - xml配置

    ```xml
    <!-- 添加约束 -->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    
    <bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="url" value="${jdbc.url}"/>
        <property name="driverClassName" value="${jdbc.driverClassName}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
    ```

  - 注解配置

    ```java
    //@PropertySource("jdbc.properties")
    @PropertySource("classpath:jdbc.properties")
    public class JdbcConfig {
    
        @Value("${jdbc.driver}")
        private String driver;
        @Value("${jdbc.url}")
        private String url;
        @Value("${jdbc.username}")
        private String username;
        @Value("${jdbc.password}")
        private String password;
    
        @Bean
        public DataSource dataSource(){
            DruidDataSource ds = new DruidDataSource();
            ds.setDriverClassName(driver);
            ds.setUrl(url);
            ds.setUsername(username);
            ds.setPassword(url);
            return ds;
        }
    }
    ```


- MybaitsConfig

  ```java
  public class MybatisConfig {
      @Bean
      SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource){
          SqlSessionFactoryBean ssfb = new SqlSessionFactoryBean();
          // 设置类型别名的包，用于简化SQL映射文件中resultType属性的全路径类名的书写
          ssfb.setTypeAliasesPackage("cn.langh.domain");
          ssfb.setDataSource(dataSource);
          return ssfb;
      }
  
      @Bean
      // 设置SQL映射文件包扫描的位置
      MapperScannerConfigurer mapperScannerConfigurer(){
          MapperScannerConfigurer msc = new MapperScannerConfigurer();
          msc.setBasePackage("cn.langh.dao");
          return msc;
      }
  }
  ```





## Spring整合Junit

- spring整合junit5

  - 坐标

    ```xml
    <!-- 此处以spring6为例 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>6.0.11</version>
    </dependency>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-api</artifactId>
        <version>5.9.0</version>
        <scope>test</scope>
    </dependency>
    ```

  - 使用

    ```java
    import org.junit.jupiter.api.Test;
    
    // @SpringJUnitConfig(locations="classpath:配置文件.xml")
    @SpringJUnitConfig(classes = 配置类.class)
    public class SpringTestJunit4 {
        @Autowired
        private UserService userService;
    
        @Test
        public void testGetById(){
            System.out.println(userService.getById(1));
        }
    }
    ```

- spring整合junit4

  - 坐标

    ```xml
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13.2</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>5.2.10.RELEASE</version>
    </dependency>
    ```

  - 使用

    ```java
    import org.junit.Test;
    
    @RunWith(SpringJUnit4ClassRunner.class)//设置类运行器
    @ContextConfiguration(classes = 配置类.class)//指定spring容器
    // @ContextConfiguration("classpath:配置文件.xml")
    public class SpringTestJunit4 {
        @Autowired
        private UserService userService;
    
        @Test
        public void testGetById(){
            System.out.println(userService.getById(1));
        }
    }
    ```





## AOP面向切面编程

> AOP可以通过注解和[XML](https://www.bilibili.com/video/BV1kR4y1b7Qc?p=60&vd_source=25ad2de4838bd28372a4956bac63c618)配置，本篇主要记录基于注解配置AOP的方式

### 快速入门

- 引入aop相关依赖

  ```xml
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aop</artifactId>
      <version>5.2.10.RELEASE</version>
  </dependency>
  <!--
  AspectJ：是AOP思想的一种实现。本质是静态代理，将代理逻辑“织入”被代理的目标类编译得到的字节码文件，所以最终效果是动态的。weaver就是织入器。
  Spring 只是借用了AspectJ中的注解
  -->
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aspects</artifactId>
      <version>5.2.10.RELEASE</version>
  </dependency>
  ```

- 开启自动代理

  - 配置文件

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:aop="http://www.springframework.org/schema/aop"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/aop
           http://www.springframework.org/schema/aop/spring-aop.xsd">
    
    <aop:aspectj-autoproxy/>
    ```

  - 配置类：`@EnableAspectJAutoProxy`

- 创建目标资源（接口和实现类，jdk代理依赖于接口）

- 创建切面类（切入点和通知类型）

  - 定义切入点：`@Pointcut("execution(切入点表达式)")`

  - 绑定切入点与通知关系（切面）


- 举例：

  ```java
  @Configuration
  @ComponentScan
  @EnableAspectJAutoProxy
  public class SpringConfig{
      
  }
  ```

  ```java
  @Component
  @Aspect
  public class LogAspect {
      @Pointcut("execution(void cn.langh.dao.UserDao.save())")
      private void pt(){}
  
      // @After("execution(void cn.langh.dao.UserDao.save())")
      // @After("cn.eli.LogAspect.pt()") // 这样写，一个切入点表达式可用供多个方法使用
      @After("pt()") // 同一个类中可以直接写方法
      public void method(){
          System.out.println("日志...");
      }
  }
  ```

  



### 切入点表达式

- 横切关注点

  - 分散在各个模块中同样的问题，如日志记录、用户验证、事务处理、数据缓存等，都属于**横切关注点**
  - 可以使用多个横切关注点，对相关方法进行多个方面不同的增强

- 语法

  ```java
  @Pointcut("execution(权限修饰符 方法返回值类型 方法所在接口全路径.方法名(方法参数))")
  ```

  - 动作关键字：描述切入点的行为动作，例如`execution`表示执行到指定切入点


  - 访问修饰符：`public`，`private`等，可以省略
  - **必须是接口的全路径**，因为jdk动态代理依赖于接口


- 可以使用通配符描述切入点，快速描述

  - `*` ：**单个独立**的任意符号，可以独立出现，也可以作为前缀或后缀的匹配符出现（参数中填一个*表示只有一个参数）

  ```java
  execution(public * cn.langh.*.*Service.find*(*))
  ```

  - `..`  ：**多个连续**的任意符号，可以独立出现，常用于简化包名与参数的书写

  ```java
  execution(public User com..UserService.findById(..))
  ```

  - `+` ：专用于匹配子类类型

  ```java
  execution(* *..*Service+.*(..))
  ```



### 通知类型

- `@Before`：前置通知，在被代理的目标方法**前**执行

- `@After`：后置通知，在被代理的目标方法**最终结束后**执行（盖棺定论）

- `@Around`：环绕通知
  
  - 需要依赖形参
  - 方法的返回值为Object，方法执行完后要有返回值
  
  ```java
  @Around("切入点方法")
  public Object around(ProceedingJoinPoint pjp) throws Throwable{
      System.out.println("原始方法前");
      // 对原始方法进行调用
      Object o = pjp.proceed();
      // pjp.getSignature() = int cn.eli.bean.Calculator.div(int,int)
      System.out.println("原始方法后");
      return o;
  }
  ```

- `@AfterReturning`：返回通知，原始方法**成功执行完后**执行此方法
- `@AfterThrowing`：异常通知，原始方法**异常结束后**执行此方法



### 通知获取数据

- 获取参数
  - 通知方法中加形参 JoinPoint，调用 JoinPoint 中的方法Object[ ] getArgs()，获得参数
  - proceedingJoinPoint 是 JoinPoint 的实现类
  - JoinPoint 必须是形参的第一个

- 获取返回值

  - `@Around` 获取返回值

  ```java
  Object o = pjp.proceed();
  ```

  - `@AfterReturning` 获取返回值

  ```java
  // 将原始方法的返回值注入到形参ret中
  @AfterReturning(value="切入点方法", returning="ret")
  public void afterReturning(Object ret){
      方法体;
  }
  ```

- 获取异常

  - `@Around` 获取异常

  ```java
  try{
  	pjp.proceed();
  }catch(Throwable e){}
  ```

  - `@AfterThrowing` 获取异常

  ```java
  @AfterReturning(value="切入点方法", throwing="thr")//将原始方法抛出的异常注入到形参thr中
  public void afterThrowing(Throwable thr){
      方法体;
  }
  ```





## Spring事务

> 本篇主要记录基于**注解**实现**声明式**事务

### 快速入门

- 依赖

  ```xml
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>略</version>
  </dependency>
  ```

- 一般在业务层接口（Service接口）的方法上添加标注 `@Transactional`
  - `@Transactional` 可标注在类或接口上，表示该类或接口中所有方法都添加事务

- 配置DataSource（以xml为例）

  ```xml
  <context:property-placeholder location="classpath:jdbc.properties"/>
  
  <bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">
      <property name="url" value="${jdbc.url}"/>
      <property name="driverClassName" value="${jdbc.driverClassName}"/>
      <property name="username" value="${jdbc.username}"/>
      <property name="password" value="${jdbc.password}"/>
  </bean>
  ```

- 配置JdbcTemplate（以注解为例）

  ```java
  @Bean
  public JdbcTemplate jdbcTemplate(DataSource datasource){
      JdbcTemplate jdbcTemplate = new JdbcTemplate();
      jdbcTemplate.setDataSource(dataSource);
      return jdbcTemplate;
  }
  ```

- 添加事务管理器

  - XML配置事务管理器

    ```xml
    <!-- 添加 tx 约束 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="druidDataSource"/>
    </bean>
    ```

  - 注解配置事务管理器

    ```java
    @Bean
    public DataSourceTransactionManager transactionManager(DataSource dataSource){
        DataSourceTransactionManager transactionManager = 
            new DataSourceTransactionManager();
        transactionManager.setDataSource(dataSource);
        return transactionManager;
    }
    ```

- 开启事务管理器

  - 方法一：SpringConfig 上添加标注 `@EnableTransactionManagement`

  - 方法二：xml配置

    ```xml
    <tx:annotation-driven transaction-manager="transactionManager"/>
    ```




### 事务传播行为

- 事务角色

  - 事务管理者：发起事务方，在Spring中通常指代业务层开启事务的方法
  - 事务协调者：加入事务方，在Spring中通常指代数据层方法，也可以是业务层方法

  ```java
  // 事务管理者
  @Transactional
  public void 转账(变动金额数, 转账方, 收账方) {
      扣钱(变动金额数, 转账方);
      加钱(变动金额数, 收账方);
  }
  
  // 事务协调者
  @Transactional(态度)
  public void 扣钱(变动金额数, 变动金额方) {
      调用SQL语句扣除指定对象指定金额;
  }
  
  // 事务协调者
  @Transactional(态度)
  public void 加钱(变动金额数, 变动金额方) {
      调用SQL语句增加指定对象指定金额;
  }
  ```

- 事务传播行为

  - **事务协调员对管理员的态度**：是加入管理员的事务，还是新开个事务，...？
  - 通过 `@Transactional` 中的 `propagation` 属性设置（`Propagation.属性`）

  <table>
      <tr>
          <th><font color="red">协调者</font>的传播属性</th>
          <th>管理者<font color="red">状态</font></th>
          <th>协调者<font color="red">态度</font></th>
      </tr>
      <tr>
          <td rowspan="2">REQUIRED(默认)</td>
          <td>已开启事务</td>
          <td>加入该事务</td>
      </tr>
      <tr>
          <td>无</td>
          <td>新建事务</td>
      </tr>
      <tr>
          <td rowspan="2">REQUIRES_NEW</td>
          <td>已开启事务</td>
          <td>新建事务</td>
      </tr>
      <tr>
          <td>无</td>
          <td>新建事务</td>
      </tr>
      <tr>
          <td rowspan="2">SUPPORTS</td>
          <td>已开启事务</td>
          <td>加入该事务</td>
      </tr>
      <tr>
          <td>无</td>
          <td>无</td>
      </tr>
      <tr>
          <td rowspan="2">NOT_SUPPORTED</td>
          <td>已开启事务</td>
          <td>无</td>
      </tr>
      <tr>
          <td>无</td>
          <td>无</td>
      </tr>
      <tr>
          <td rowspan="2">MANDATORY</td>
          <td>已开启事务</td>
          <td>加入该事务</td>
      </tr>
      <tr>
          <td>无</td>
          <td>error</td>
      </tr>
      <tr>
          <td rowspan="2">NEVER</td>
          <td>已开启事务</td>
          <td>error</td>
      </tr>
      <tr>
          <td>无</td>
          <td>无</td>
      </tr>
      <tr>
          <td>NESTED</td>
          <td colspan="2">设置savePoint，一旦事务回滚，事务将回滚到savePoint处，交由客户响应提交/回滚</td>
      </tr>
  </table>



### 事务隔离级别

- 各种隔离级别解决并发问题的能力

  | 隔离级别         | 脏读 | 不可重复读 | 幻读 |
  | ---------------- | ---- | ---------- | ---- |
  | READ UNCOMMITTED | ×    | ×          | ×    |
  | READ COMMITTED   | √    | ×          | ×    |
  | REPEATABLE READ  | √    | √          | ×    |
  | SERIALIZABLE     | √    | √          | √    |

- 各种数据库对事务隔离级别的支持程度

  | 隔离级别         | Oracle  | MySQL   |
  | ---------------- | ------- | ------- |
  | READ UNCOMMITTED | ×       | √       |
  | READ COMMITTED   | √(默认) | √       |
  | REPEATABLE READ  | ×       | √(默认) |
  | SERIALIZABLE     | √       | √       |

- 设置隔离级别`@Transactional(isolation = Isolation.隔离级别)`
  - `DEFAULT`：使用数据库各自的默认隔离级别
  - `READ_UNCOMMITTED`：读未提交
  - `READ COMMITTED`：读已提交
  - `REPEATABLE READ`：可重复读
  - `SERIALIZABLE`：串行化



### Spring事务的其他配置

- 一些属性
  - `readOnly`：默认false，true表示只能做查询操作
  - `timeout`：设置一个事务的超时时间，在该时间内未完成，抛出异常并回滚。默认-1表示永不超时
  - `rollbackFor`：设置哪些异常回滚（默认Error和RuntimeException回滚）
  - `noRollbackFor`：设置哪些异常不回滚（默认Error和RuntimeException回滚）
  - `propagation`：事务传播行为
  - `isolation`：事务隔离级别

- `@Transactional`注解中的源码

  ```java
  public @interface Transactional {
      @AliasFor("transactionManager")
      String value() default "";
  
      @AliasFor("value")
      String transactionManager() default "";
  
      String[] label() default {};
  
      Propagation propagation() default Propagation.REQUIRED;
  
      Isolation isolation() default Isolation.DEFAULT;
  
      int timeout() default -1;
  
      String timeoutString() default "";
  
      boolean readOnly() default false;
  
      Class<? extends Throwable>[] rollbackFor() default {};
  
      String[] rollbackForClassName() default {};
  
      Class<? extends Throwable>[] noRollbackFor() default {};
  
      String[] noRollbackForClassName() default {};
  }
  ```










# SpringMVC



## 入门案例

### maven坐标

```xml
<dependencies>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.2.10.RELEASE</version>
    </dependency>
</dependencies>

<!--插件-->
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.tomcat.maven</groupId>
            <artifactId>tomcat7-maven-plugin</artifactId>
            <version>2.2</version>
            <configuration>
                <port>80</port>
                <path>/</path>
            </configuration>
        </plugin>
    </plugins>
</build>
```



### Config

- 创建springmvc的配置文件，加载controller对应的bean

  ```java
  @Configuration
  @ComponentScan("cn.langh.controller")
  public class SpringmvcConfig {
  }
  ```

- 初始化Servlet容器，加载SpringMVC环境，并设置SpringMVC技术处理的请求

  ```java
  public class ServletContainersInitConfig extends AbstractDispatcherServletInitializer{
      //用来加载SpringMVC容器配置
      protected WebApplicationContext createServletApplicationContext(){
          AnnotationConfigWebApplication ctx = new AnnotationConfigWebApplicationContext();
          ctx.register(SpringmvcConfig.class);
          return ctx;
      }
      
      // 设置哪些请求归属springMVC处理，
      // 如果设置为 "/" ，表示tomcat将无法划分静态资源（html、js...）所有请求交由springmvc划分
      protected String[] getServletMappings(){
          return new String[]{"/"};
      }
      
      //加载spring容器配置
      protected WebApplicationContext createRootApplicationContext(){
          return null;
      }
  }
  ```
  
  ```java
  public class ServletContainersInitConfig extends AbstractAnnotationConfigDispatcherServletInitializer {
      @Override
      protected Class<?>[] getRootConfigClasses() {
          return new Class[]{SpringConfig.class};
      }
  
      @Override
      protected Class<?>[] getServletConfigClasses() {
          return new Class[]{SpringmvcConfig.class};
      }
  
      @Override
      protected String[] getServletMappings() {
          return new String[]{"/"};
      }
  }
  ```
  
  



### Controller

- 使用 @Controller 定义Bean
- @RequestMapping 设置当前操作的访问路径
- @ResponseBody 设置当前操作的返回值类型

```java
@Controller
public class UserController {
    @RequestMapping("/save")
    @ResponseBody
    public String save(){
        System.out.println("user save...");
        return "{'module':'springmvc'}";
    }
}
```



### 入门案例的工作流程

- 启动服务器初始化过程
  - 服务器启动，执行ServletContainersInitConfig类，初始化web容器
  - 执行createServletApplicationContext方法，创建WebApplicationContext对象
  - 加载SpringmvcConfig
  - 执行@ComponentScan加载对应的bean
  - 加载UserController，每个@RequestMapping的名称对应一个具体的方法
  - 执行getServletMappings方法，定义所有的请求都通过SpringMVC
- 单次请求过程
  - 发送请求localhost/save
  - web容器发现所有请求都经过SpringMVC，将请求交给SpringMVC处理
  - 解析请求路径/save
  - 执行save()
  - 检测到有@ResponseBody直接将save()方法的返回值作为响应体返回给请求放



### 说明

- Root ApplicationContext 和 Servlet ApplicationContext 的区别

  - Root ApplicationContext：这是一个全局的上下文，它会被所有的 Servlet 共享。通常，我们会在这个上下文中加载整个应用的配置，例如服务层（Service）、数据访问层（DAO）等等。这个上下文的生命周期从应用启动开始，到应用关闭结束。
  - Servlet ApplicationContext：这是**每个 Servlet 独有的上下文，它只对应一个 Servlet**。通常，我们会在这个上下文中加载和这个 Servlet 相关的配置，例如控制器（Controller）、视图解析器（ViewResolver）等等。这个上下文的生命周期从 Servlet 创建开始，到 Servlet 销毁结束。

- 为什么要区分这两个 Context

  - 配置隔离：Root ApplicationContext 和 Servlet ApplicationContext 的区分使得我们可以将全局的配置（如服务层、数据访问层等）和 Servlet 相关的配置（如控制器、视图解析器等）分开，使得配置更加清晰，更易于管理。
  - 资源共享：所有的 Servlet ApplicationContext 都可以访问 Root ApplicationContext 中的 Bean，这意味着我们可以在 Root ApplicationContext 中定义全局的 Bean，然后在所有的 Servlet 中共享这些 Bean。
  - 生命周期管理：Root ApplicationContext 的生命周期从应用启动开始，到应用关闭结束，而 Servlet ApplicationContext 的生命周期从 Servlet 创建开始，到 Servlet 销毁结束。这样的设计使得我们可以更好地管理 Bean 的生命周期，例如，我们可以在 Root ApplicationContext 中定义长生命周期的 Bean（如服务层的 Bean），在 Servlet ApplicationContext 中定义短生命周期的 Bean（如请求作用域的 Bean）。

- 流程

  - 在 Spring MVC 中，当一个 Servlet 初始化时，Spring 会为这个 Servlet 创建一个 Servlet ApplicationContext。这个 Servlet ApplicationContext 包含了和这个 Servlet 相关的配置，例如控制器（Controller）、视图解析器（ViewResolver）等等。

  - 然后，Spring 会将这个 Servlet ApplicationContext 注册为 ServletContext 的一个属性（其实不一定是属性，可能只是Spring存储的一份对应关系，毕竟ServletContext是javax的）。这样，我们就可以在 Servlet 中通过 ServletContext 获取到 Servlet ApplicationContext，并使用其中的 Bean。

  - 这种设计使得我们可以在 Servlet 中使用 Spring 的功能，例如依赖注入、事务管理等。同时，由于 Servlet ApplicationContext 可以访问 Root ApplicationContext 中的 Bean，我们还可以在所有的 Servlet 中共享全局的 Bean。

    ```java
    import ...;
    import org.springframework.web.context.WebApplicationContext;
    import org.springframework.web.context.support.WebApplicationContextUtils;
    
    public class MyServlet extends HttpServlet {
        public void doGet(HttpServletRequest request, HttpServletResponse response) {
            // 获取 ServletContext
            ServletContext servletContext = this.getServletContext();
            ServletContext servletContext = request.getServletContext();
    
            // 通过 ServletContext 获取 WebApplicationContext （即 Servlet ApplicationContext）
            WebApplicationContext webApplicationContext = WebApplicationContextUtils.getWebApplicationContext(servletContext);
    
            // 从 WebApplicationContext 中获取 Bean
            MyBean myBean = (MyBean) webApplicationContext.getBean("myBean");
    
            // 使用 Bean
            myBean.doSomething();
        }
    }
    ```





## 参数接收

### 普通参数接收

- 通过设置形参来接收相同名称的请求参数

- 可通过在形参前加上@RequestParam("请求参数")来匹配形参和请求参数

```java
@RequestMapping("/getParam1")
@ResponseBody
public String getParam1(@RequestParam("name") String username,Integer age){
    System.out.println(username);
    return username;
}
```



### 实体类接收

- 请求参数与实体类属性名相同则可以直接接收
- 如果实体类中包含引用属性，则在传递该属性参数时应，请求参数应遵循格式：引用类名.引用类属性

```java
@RequestMapping("/getParam2")
@ResponseBody
public String getParam2(User user){
    System.out.println(user);
    return user.toString();
}
```

```java
public class User{
	private String name;
	private Integer age;
	private Address address;
}
```

```java
public class Address{
    private String province;
    private String city;
}
```

- json:

| Key              | VALUE    |
| ---------------- | -------- |
| name             | zhangsan |
| age              | 20       |
| address.province | Anhui    |
| address.city     | Hefei    |



### 数组与集合接收

- 名称相同，可以用数组直接接收
- 名称相同，且需要加@RequestParam，才能用集合接收



### json数据的接收

- 位置坐标

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.0</version>
</dependency>
```

- springmvcConfig上加注解@EnableWebMvc

- 形参前添加@RequestBody来接收参数


注：json格式

```json
//List<User> users
//[User{name='zhangsan', age=15, address=Address{province='beijing', city='beijing'}}, User{name='lisi', age=15, address=Address{province='beijing', city='beijing'}}]
[
    {
        "name":"zhangsan",
        "age":15,
        "address":{
        	"province":"beijing",
    	    "city":"beijing"
        }
    },
    {
        "name":"lisi",
        "age":15,
        "address":{
        	"province":"beijing",
    	    "city":"beijing"
        }
    }
]
```



### 中文乱码问题

在ServletContainersInitConfig中覆盖重写getServletFilters()

```java
@Override
protected Filter[] getServletFilters(){
    CharacterEncodingFilter filter = new CharacterEncodingFilter();
    filter.setEncoding("utf-8");
    return new Filter[]{filter};
}
```



### 日期时间型接收

```java
@RequestMapping("/date2")
@ResponseBody
public String date2(@DateTimeFormat(pattern = "yyyy-MM-dd") Date date){
    System.out.println(date);
    return date.toString();
}
```



### 文件接收

- 前端

  - 表单提交

    ```html
    <form action="http://localhost/file/upload" method="post" enctype="multipart/form-data">
        <input id="file" name="oneFile" type="file"/><br/>
        <input id="btn" value="submit" type="submit"/>
    </form>
    ```

  - ajax + FormData

    ```html
    <script>
    let btn = document.querySelector("#btn")
    btn.addEventListener("click", function(){
        // 1. 检验是否选择了文件
        let files = document.querySelector("#file").files
        if(files.length <= 0){
            return alert("请选择要上传的文件")
        }
    
        // 2. 向FormData中追加文件
        let fd = new FormData()
        fd.append("oneFile", files[0])
    
        // 3. 发送ajax请求
        const xhr = new XMLHtttpRequest();
        xhr.open("POST","http://www.test.com");
        xhr.send(fd)
        
        // 细节：用formdata提交文件时不需要设置请求头
    })
    </script>
    ```

- 后端

  - 接收

    ```java
    @PostMapping("/upload")
    public String upload(@RequestParam("oneFile") MultipartFile file) {
        if (file == null) return "null";
        return "success";
    }
    ```

  - 大小设置

    - 修改 yml 文件

      ```yaml
      spring:
        servlet:
          multipart:
            max-file-size: 100MB
            max-request-size: 300MB
      ```

    - 添加文件配置类

      ```java
      @Configuration
      public class FileConfig {
          @Bean
          public MultipartConfigElement multipartConfigElement() {
              MultipartConfigFactory factory = new MultipartConfigFactory();
              //允许上传的文件最大值
              factory.setMaxFileSize(DataSize.parse("50MB")); //KB,MB
              /// 设置总上传数据总大小
              factory.setMaxRequestSize(DataSize.parse("50MB"));
              return factory.createMultipartConfig();
          }
      }
      ```






## 会话技术

### Cookie

- 客户端会话技术，将数据保存到客户端

- 使用步骤

  ```java
  // 发送Cookie
  Cookie cookie = new Cookie(String name, String value);
  response.addCookie(cookie);
  ```

  ```java
  // 获取Cookie
  Cookie[] cookies = request.getCookies();
  ```

- 补充

  - cookie的保存时间

    - 默认情况下，cookie数据存在浏览器的内存中，浏览器关闭后，数据销毁
    - 持久化存储
      - setMaxAge(int seconds)
        - 正数：Cookie数据写到硬盘的文件中，持久化存储。cookie的存活时间
        - 负数：默认值（不会被持久化，浏览器关闭，cookie消失）
        - 零：删除cookie信息

  - cookie存储中文

    - 在tomcat 8之前 cookie不能直接存储中文数据，需要转码，一般用URL编码

    - 在tomcat 8及之后，cookie可以直接存储中文，但仍不支持特殊字符

      ```java
      // 对应的类在java.net包下
      // url编码
      URLEncoder.encode(String str, String charset);
      // url解码
      URLDecoder.decode(String str, String charset);
      ```

  - cookie的获取范围

    - 在一个服务器中，部署了多个web项目，cookie信息默认不能共享
    - setPath(String path)：设置cookie的获取范围，默认情况下，为当前web项目目录，将path设置为“/”，当前域名（ip）下的所有项目



### Session

- 服务器端会话技术，将数据保存到服务器中

- 原理

  - 调用request.getSession()后，服务器会检查请求中有没有 JSESSIONID 的cookie
  - 若没有，则创建Session对象，并为其定义一个id，再在cookie中存储这个id，并发送
  - 若有，则通过 JSESSIONID 这个id找到服务器中的Session对象
  - 因此，在一次会话范围内，Session对象是同一个

- 使用步骤

  - HttpSession session = request.getSession();
  - session.getAttribute(string name, Object value)
  - session.removeAttribute(String name)

- session什么时候被销毁

  - 服务器关闭
  - session对象调用invalidate()
  - session默认失效时间为30分钟

  

  



## REST风格

### 简介

- REST（Repretational State Transfer），表现形式状态转换
  - 传统风格资源描述形式
    - http://localhost/user/getById?id=1
    - http://localhost/user/saveUser
  - REST风格描述形式
    - http://localhost/user/1
    - http://localhost/user
- 优点
  - 隐藏资源的访问行为，无法通过地址得知对资源的何种操作
  - 书写简化
- 按照REST风格访问资源时使用行为动作区分对资源进行了何种操作

| http://localhost/users   | 查询全部用户信息 | GET（查询）       |
| ------------------------ | ---------------- | ----------------- |
| http://localhost/users/1 | 查询指定用户信息 | GET（查询）       |
| http://localhost/users   | 添加用户信息     | POST（新增/保存） |
| http://localhost/users   | 修改用户信息     | PUT（修改/更新）  |
| http://localhost/users/1 | 删除用户信息     | DELETE（删除）    |



### 快速入门

```java
@Controller
@RequestMapping("/rest")
public class TestController {

    @RequestMapping(value = "/users",method = RequestMethod.POST)
    @ResponseBody
    public String save(){
        System.out.println("user save...");
        return "user save";
    }

    //路径上的{id}表示占位，与形参名称一致
    //形参前的 @PathVariable 表示从路径中获取变量
    @RequestMapping(value = "/users/{id}",method = RequestMethod.PUT)
    @ResponseBody
    public String delete(@PathVariable Integer id){
        System.out.println("user NO."+id+" delete ...");
        return "user NO."+id+" delete ...";
    }
    
    // ......
}
```



### 快速开发

```java
//@Controller
//@ResponseBody
@RestController
@RequestMapping("/rest")
public class TestController {

    //@RequestMapping(value = "/users",method = RequestMethod.POST)
    @PostMapping
    public String save(){
        System.out.println("user save...");
        return "user save";
    }

    //@RequestMapping(value = "/users/{id}",method = RequestMethod.DELETE)
    @DelMapping("/{id}")
    public String delete(@PathVariable Integer id){
        System.out.println("user NO."+id+" delete ...");
        return "user NO."+id+" delete ...";
    }
    
    // ......
}
```





## 响应

- 基础

  - 响应页面：方法返回值设定位要跳转的页面
  
  
    - 响应json：方法返回值设置为pojo类型，并返回pojo对象
  


- 响应的完善

  - 构造响应结果Result，统一响应内容

  ```java
  public class Result{
      private Object data;
      private Code code;
      private String msg;
  }
  ```

  ```java
  public class Code {
      public static final Integer SAVE_OK = 20011;
      public static final Integer DELETE_OK = 20021;
      public static final Integer UPDATE_OK = 20031;
      public static final Integer GET_OK = 20041;
  
      public static final Integer SAVE_ERR = 20010;
      public static final Integer DELETE_ERR = 20020;
      public static final Integer UPDATE_ERR = 20030;
      public static final Integer GET_ERR = 20040;
  }
  ```

  ```java
  @GetMapping("/{id}")
  public Result getUserById(@PathVariable int id){
      User user = userService.findUserById(id);
      Integer code = user==null ? Code.GET_ERR : Code.GET_OK;
      String msg = user==null ? "数据查询失败，请重试":"";
      return new Result(code,user,msg);
  }
  ```

  



## 异常处理器

### 异常的处理方案

- 异常的分类
  - 业务异常（BusinessException）
    - 规范的用户行为产生的异常
    - 不规范的用户行为产生的异常
  - 系统异常（SystemException）
    - 项目运行过程中可预计且无法避免的异常
  - 其他异常
    - 编程人员未预期到的异常
- 异常处理方案
  - 业务异常
    - 发送对应的消息传递给用户，提醒规范操作
  - 系统异常
    - 发送固定消息传递给用户，安抚用户
    - 发送特定消息给运维人员，提醒维护
    - 记录日志
  - 其他异常
    - 发送固定消息传递给用户，安抚用户
    - 发送特定消息给编程人员，提醒维护（纳入预期范围内）
    - 记录日志



### 异常处理器

- 原理
  - 出现异常后，**将异常全部抛到表现层进行处理**
    - 先根据项目中异常的分类，自定义异常
    - 在持久层、业务层将异常捕获，并转成自定义异常抛到表现层
  - 表现层处理异常，每个方法单独书写捕获异常逻辑，代码书写量巨大、不便阅读且维护繁杂。可以使用**AOP**解决

- 使用

  - 表现层中定义异常（如：BusinessException、SystemException等）

    ```java
    public class SystemException extends RuntimeException{
        Integer code; // 自定义异常类型对应的code
        
        public SystemException(Integer code, String msg){
            super(msg);
            this.code = code;
        }
        
        public System.Exception(Integer code, String msg, Throwable cause){
            super(msg, cause);
            this.code = code;
        }
        
        public Integer getCode(){return code;}
    }
    ```

  - 业务层的异常抛出

    ```java
    public User getById(int id){
        try{
            int i = 1/0; // 模拟异常
        }catch(Exception e){
            // 运行时异常不需要trycatch，本身就会往表现层抛，这里是将异常变成自定义的异常
            throw new SystemException(Code.SYSTEM_TIMEOUT_ERR, "出异常啦");
        }
    }
    ```

  - 在表现层定义异常处理器，异常处理器会自动拦截异常（一般放在表现层包中，一方面异常是在表现层处理的，另一方面mvcConfig扫描controller包即可扫描到异常处理器。如果单独建一个包，须保证可以被扫描到）

    ```java
    // 定义异常处理器
    // @ControllerAdvice
    @RestControllerAdvice
    public class ProjectExceptionAdvice {
        // 定义拦截哪一种异常
        @ExceptionHandler(Exception.class)
        // 形参是异常对象
        public Result doException(Exception e){
            System.out.println(e.getMessage());
            return new Result(500,null,"出异常了");
        }
    }
    ```

    







## 拦截器

### 快速入门

- 拦截器与过滤器的区别

  - 归属不同：Filter属于Servlet技术，是Servlet的三大组件之一；Interceptor属于SpringMVC技术

  - 拦截内容不同：Filter对所有访问进行增强，Interceptor仅针对SpringMVC的访问进行增强

    一次请求的过程：

    - 请求进入tomcat，被分为静态资源请求、动态资源请求（如果 MVCConfig 中配置路径为 **"/"** ，表示所有请求路径都由springmvc分配，即不划分静态资源请求，所有请求都是动态资源请求，都进入servlet，由mvc划分）
    - 动态资源请求被Filter1、Filter2...过滤器链拦截
    - 通过过滤器链，动态资源请求到达servlet（springmvc）
    - 请求先进入springmvc中央控制器
      1. 如果没有配置拦截器，由根据请求路径分配到不同的controller
      2. 如果配置了拦截器，会根据规则先通过拦截器，在分配到controller
    - 响应从controller出来后，会**反向**回到浏览器

- 拦截器的定义

```java
@Component
public class ProjectInterceptor implements HandlerInterceptor {
    // 此方法会在进入controller之前执行，返回Boolean值决定是否执行后续操作。
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle...");
        // 是否放行
        return true;
    }

    // 在controller执行之后执行，但是视图还没有解析，可向ModelAndView中添加数据(前后端不分离)。
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle...");
    }
    
	// 该方法会在整个请求结束（请求结束，但是并未返回结果给客户端）之后执行， 可获取响应数据及异常信息。
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion...");
    }
}
```

- 拦截器的加载（SpringmvcSupport 也要被SpringmvcConfig扫描到）

```java
@Configuration
public class SpringmvcSupport extends WebMvcConfigurationSupport {
    // 过滤访问的静态资源：
    //     之前配置mvc时，让mvc拦截所有请求（即配置拦截目录为 "/" )
    //     此时若不配置静态资源，所有请求都会被拦截并与controller匹配，并不会区查找资源
    @Override
    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
        // 访问 /page/** 时让他定位到 /page/ 目录
        registry.addResourceHandler("/page/**").addResourceLocations("/page/");
        registry.addResourceHandler("/css/**").addResourceLocations("/css/");
        // ... ...
    }

    @Autowired
    private ProjectInterceptor projectInterceptor;

    // 添加拦截的url
    @Override
    protected void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(projectInterceptor).addPathPatterns("/user","/user/*");
    }
}
```

- 简化配置：即实现WebMvcConfigurer接口，并在mvc配置类中配置拦截器、资源路径等，最后加上**@EnableWebMvc**注解

```java
@Configuration
@ComponentScan("cn.langh.controller")
@EnableWebMvc
public class SpringMvcConfig implements WebMvcConfigurer{    
    @Autowired
    private ProjectInterceptor projectInterceptor;
    
    @Override
    public void addInterceptors (InterceptorRegistry registry){
       registry.addInterceptor(projectInterceptor).addPathPatterns("/user","/user/*");
    }
}
```



### @EnableWebMvc

- 在不使用 Spring Boot，只使用 Spring MVC 的项目中，@EnableWebMvc 注解用于启用 Spring MVC 的默认配置。这个注解会导入 DelegatingWebMvcConfiguration 类，这是一个 Spring MVC 的配置类，提供了很多默认的配置。

  ```java
  @Retention(RetentionPolicy.RUNTIME)
  @Target({ElementType.TYPE})
  @Documented
  @Import({DelegatingWebMvcConfiguration.class})
  public @interface EnableWebMvc {
  }
  ```

- 提供的常用默认配置

  - 自动配置了静态资源的处理，例如 CSS、JavaScript 和图片等。
  - 自动注册了 DispatcherServlet，这是 Spring MVC 的核心组件，负责处理并分发 HTTP 请求。  
  - 自动配置了 Spring MVC 的视图解析器（View Resolver），用于将控制器返回的模型数据渲染为视图。
  - 自动配置了消息转换器（Message Converter），用于在 HTTP 请求和响应中转换数据。
  - 自动配置了 @Controller 和 @RestController 的处理，这些注解用于标记控制器类。
  - 自动配置了数据绑定支持，例如将请求参数绑定到控制器方法的参数。
  - 自动配置了验证和格式化的支持。
  - 自动配置了国际化和本地化的支持。





### 拦截器参数

- Request
  - 原始request
  - request.getHeader(String name);

- Response
  - 原始response

- handler
  - 封装了 controller 中对应的方法
  - 可将 handler（Object）强转为HandMethod，便可调用反射中的方法

- ModeAndView
  - 封装了页面跳转的相关信息

- Exception
  - 原始程序执行过程中的异常




### 拦截器链

- 配置

```java
@Configuration
public class SpringmvcSupport extends WebMvcConfigurationSupport {
    
    @Autowired
    private ProjectInterceptor projectInterceptor;
    @Autowired
    private ProjectInterceptor2 projectInterceptor2;

    @Override
    protected void addInterceptors(InterceptorRegistry registry) {
      registry.addInterceptor(projectInterceptor).addPathPatterns("/user","/user/*");
      registry.addInterceptor(projectInterceptor2).addPathPatterns("/user","/user/*");
    }
}
```

- 执行顺序

```
preHandle...
preHandle222...
[INFO] {dataSource-1} inited
postHandle222...
postHandle...
afterCompletion222...
afterCompletion...
```









# Maven



## 分模块开发

- 独立模块

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>cn.langh</groupId>
    <artifactId>模块1XXX</artifactId>
    <version>1.0-SNAPSHOT</version>
</project>
```

- 原始模块

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>cn.langh</groupId>
    <artifactId>模块2XXX</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>cn.langh</groupId>
            <artifactId>模块1XXX</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
    </dependencies>
</project>
```

- 将独立模块打包进仓库
  - 使用独立模块的生命周期中的install命令



## 依赖传递

- 依赖具有传递性
  - 直接依赖
  - 间接依赖
- **依赖冲突所执行的顺序**
  - 路径优先：当依赖中出现相同的资源时，层级越深，优先级越低
  - 声明优先：当资源在相同层级被依赖时，配置顺序靠前的覆盖配置顺序靠后的
  - 特殊优先：当同级配置了相同资源的不同版本，后配置的覆盖先配置的

- 可选依赖：隐藏当前工程所依赖的资源，隐藏后，对应资源将不具备依赖的传递性（即引用当前工程的工程，无法使用当前工程隐藏的依赖，**是被引用者使用的**）

```xml
<dependency>
    <groupId>XXX</groupId>
    <artifactId>XXX</artifactId>
    <version>XXX</version>
    <optional>true</optional>
</dependency>
```

- 排除依赖：隐藏当前依赖所对应的部分依赖关系，**是引用者使用的**

```xml
<dependency>
    <groupId>XXX</groupId>
    <artifactId>XXX</artifactId>
    <version>XXX</version>
    <exclusions>
        <exclusion>
            <groupId>地址</groupId>
            <artifactId>需要排除的项目</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```



## 聚合

- 聚合：将多个模块组成一个整体，同时进行项目构建的过程

- 聚合工程：通常是一个不具有业务功能的“空”工程（有且仅有一个pom文件）

- 作用：使用聚合工程可以将多个工程编组，通过对聚合工程进行构建，实现对包含的模块进行同步构建

  - 当工程中某个模块发生更新（变更）时，必须保证工程中与已更新模块关联的模块同步更新，此时可以使用聚合工程来解决批量模块同步构建的问题。

- 步骤

  - 创建新的模块，并设置打包方式为pom
  - 设置管理的模块名称

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
  
      <groupId>cn.langh</groupId>
      <artifactId>springmvc_10_interceptor</artifactId>
      <version>1.0-SNAPSHOT</version>
      <packaging>pom</packaging>
  
      <modules>
          <module>../模块1</module>
          <module>../模块2</module>
      </modules>
  </project>
  ```

  

## 继承

- 创建新的模块，并设置打包方式为pom
- 配置工程的parent

```xml
<parent>
    <grouId></grouId>
    <argifactId></argifactId>
    <version></version>
    <relativePath>相对路径：定位到pom.xml文件，使用它可准确定位父类的位置</relativePath>
</parent>
```

- 依赖的继承

  - 定义在父工程的坐标，在子工程中自动继承

- 依赖管理

  - 在父工程中定义依赖管理，子工程添加依赖坐标时，不用加版本，自动与管理中的依赖版本一致

  ```xml
  <dependencyManagement>
      <dependencies>
          <dependency>
              <groupId>cn.langh</groupId>
              <artifactId>模块XXX</artifactId>
              <version>指定版本</version>
          </dependency>
      </dependencies>
  </dependencyManagement>
  ```

  

## 属性

- 定义与使用

```xml
<properties>
    <spring.version>5.2.10.RELEASE</spring.version>
    <junit.version>4.12</junit.version>
</properties>
```

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>${spring.version}</version>
</dependency>
```

- 配置文件加载maven的属性

  - 扩大maven的作用范围到配置文件

  ```xml
  <build>
      <resources>
          <resource>
              <!--指定加载属性的配置文件的位置-->
              <!--<directory>../maven_01/src/main/resources</directory>-->
              <directory>${project.basedir}/src/main/resources</directory>
              <!--表示识别配置文件中的${}符号-->
              <filtering>true</filtering>
          </resource>
      </resources>
  </build>
  ```

  - directory可设置为 `<directory>${project.basedir}/src/main/resources\</directory>`
    - ${project.basedir}maven内置系统属性，表示当前项目
    - 如果该配置设置在父类，子类配置继承该设置，子类不用再设置

- maven在打war包时，默认必须存在web.xml文件，但注解开发时，不需要该文件。可以创建该文件，也可以添加插件设置打包方式


```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.2.3</version>
            <configuration>
                <!--表示未找到web.xml时不声明-->
                <failOnMissingWebXml>false</failOnMissingWebXml>
            </configuration>
        </plugin>
    </plugins>
</build>
```



## 多环境开发

- 配置多环境

  ```xml
  <profiles>
      <profile>
          <id>env_dep</id>
          <properties>
              <!-- 在此处配置属性信息，
                   一般 与配置文件加载maven的属性 一起使用，即配置文件读取pom文件中的配置 -->
              <jdbc.url>jdbc:mysql://127.0.0.1:3306/ssm（开发环境地址）</jdbc.url>
          </properties>
          <!-- 通过activeByDefault配置当前使用哪套环境 -->
          <activation>
              <activeByDefault>true（是否为默认环境）</activeByDefault>
          </activation>
      </profile>
      <profile>
          <id>env_pro</id>
          <properties>
              <jdbc.url>生产环境地址</jdbc.url>
          </properties>
      </profile>
      <profile>
          <id>env_test</id>
          <properties>
              <jdbc.url>测试环境地址</jdbc.url>
          </properties>
      </profile>
  </profiles>
  ```

- 选择环境的两种方法

  1. 使用上述实例中的 `<activeByDefault>true</activeByDefault>`
  2. 使用maven指令 `mvn install -P 环境id`



## 跳过测试

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>2.12.4</version>
            <configuration>
                <!--全部跳过-->
                <skipTests>true</skipTests>
                <!--挑过部分-->
                <skipTests>false</skipTests>
                <exclude>类名（如 **/UserServiceTest.java）</exclude>
            </configuration>
        </plugin>
    </plugins>
</build>
```



## [私服](https://www.bilibili.com/video/BV1Fi4y1S7ix?p=86&vd_source=25ad2de4838bd28372a4956bac63c618)

- 私服的下载与安装
- maven文件配置私服
- 工程pom配置私服

```xml
<distributionManagement>
    <repository>
        <id></id>
        <url></url>
    </repository>
    <snapshotRepository>
        <id></id>
        <url></url>
    </snapshotRepository>
</distributionManagement>
```



## 常用插件

- scala 编译打包插件

  ```xml
  <!-- This plugin compiles Scala files，需要 scala-library 依赖 -->
  <plugin>
      <groupId>net.alchim31.maven</groupId>
      <artifactId>scala-maven-plugin</artifactId>
      <version>3.2.2</version>
      <executions>
          <execution>
              <id>scala-compile-first</id>
              <phase>process-resources</phase>
              <goals>
                  <goal>add-source</goal>
                  <goal>compile</goal>
              </goals>
          </execution>
          <execution>
              <id>scala-test-compile</id>
              <phase>process-test-resources</phase>
              <goals>
                  <goal>testCompile</goal>
              </goals>
          </execution>
      </executions>
  </plugin>
  ```

  - scala 依赖

    ```xml
    <!-- scala依赖 -->
    <dependency>
        <groupId>org.scala-lang</groupId>
        <artifactId>scala-library</artifactId>
        <version>2.13.10</version>
    </dependency>
    ```

- jdk版本插件

  ```xml
  <!-- 设置jdk版本 -->
  <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.10.1</version>
      <configuration>
          <source>17</source>
          <target>11</target>
      </configuration>
  </plugin>
  ```

- springboot依赖打包插件

  ```xml
  <!-- springboot项目的常用插件，在项目打包时，把需要的各种依赖包都打到jar包中 -->
  <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
      <configuration>
          <excludes>
              <exclude>
                  <groupId>org.projectlombok</groupId>
                  <artifactId>lombok</artifactId>
              </exclude>
          </excludes>
      </configuration>
  </plugin>
  ```

- tomcat内嵌插件

  ```xml
  <!-- 将tomcat服务器嵌入项目中 -->
  <!-- 打包方式是war包，并通过maven插件中的tomcat7:run启动web项目 -->
  <plugin>
      <groupId>org.apache.tomcat.maven</groupId>
      <artifactId>tomcat7-maven-plugin</artifactId>
      <version>2.2</version>
      <configuration>
          <port>80</port>
          <path>/</path>
      </configuration>
  </plugin>
  ```
  
  









# [MyBatisPlus](https://baomidou.com/)

## 快速入门

- 坐标

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.16</version>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>

<!--MybatisPlus的起步坐标-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.1</version>
</dependency>
```

- dao接口继承BaseMapper<?>,泛型为domian实体类
- service接口继承IService<?>
- domain类上用@TableName("表名")来标识实体类对应的表





## 标准数据层开发

### 主要功能

| 主要功能   | 自定义接口                             | MybatisPlus接口                                |
| ---------- | -------------------------------------- | ---------------------------------------------- |
| 新增       | boolean save(T t)                      | int insert(T t)                                |
| 删除       | boolean delete(int id)                 | int deleteById(Serializable id)                |
| 修改       | boolean update(T t)                    | int updateById(T t)                            |
| 根据id查询 | T getById(int id)                      | T selectById(Serilizable id)                   |
| 查询全部   | List\<T> getAll()                      | List\<T> selectList()                          |
| 分页查询   | PageInfo\<T> getAll(int page,int size) | IPage\<T> selectPage(IPage\<T> page)           |
| 按条件查询 | List\<T> getAll(Condition condition)   | IPage\<T> selectPage(Wrapper\<T> queryWrapper) |

```java
@Test
void testUpdate(){
    User user = new User();
    user.setId(1L);
    user.setPassword(333);
    userdao.updateById(user);
}
```



### lombok

- 一个java类库，提供了一组注解，简化POJO实体类开发

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <scope>provided</scope>
</dependency>
```

- @Setter

- @Getter

- @ToString

- @NoArgsConstructor：无参构造

- @AllArgsConstructor：全参构造

- @EqualsAndHashCode

- @Data：

  

### 分页查询

```java
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor(){
    //1.定义MybatisPlus的拦截器
    MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
    //2.添加分页的拦截器
    mybatisPlusInterceptor.addInnerInterceptor(new PaginationInnerInterceptor());
    return mybatisPlusInterceptor;
}
```

```java
@Test
void testGetByPage(){
    //current 表示所查的页码，size 表示每页的条数
	IPage page = new Page(int current,int size);
    userdao.selectPage(page,null);
    System.out.println("当前页码值："+page.getCurrent());
    System.out.println("每页条数值："+page.getSize());
    System.out.println("总共的页数："+page.getPages());
    System.out.println("总共的条数："+page.getTotal());
    System.out.println("数据："+page.getRecords());
}
```





## 条件查询

### 快速入门

```java
@Test
public void testQueryWrapper(){
    //方式一：按条件查询
    QueryWrapper queryWrapper1 = new QueryWrapper();
    queryWrapper1.lt("money",12000);

    //方式二：lambda格式按条件查询
    QueryWrapper<User> queryWrapper2 = new QueryWrapper<>();
    queryWrapper2.lambda().lt(User::getMoney,12000);

    //方式三：lambda格式按条件查询
    LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper();
    lqw.lt(User::getMoney,12000);
    
    List list = userDao.selectList(lqw);
    for (Object o : list) {
        System.out.println(o);
    }
}
```

- 常用条件
  - eq：等于
  - lt：小于
  - le：小于等于
  - gt：大于
  - ge：大于等于
  - between：介于
  - 并且关系：`lqw.lt(User::getMoney,20000).gt(User::getMoney,12000);`
  - 或者关系：`lqw.lt(User::getMoney,12000).or().gt(User::getMoney,20000);`

- null值判定
  - `lqw.lt(null != user.getMoney(),User::getMoney,user.getMoney());`



### 查询投影

- 查询指定列的数据

```java
//lambda
@Test
public void testSelect(){
    LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper();
    lqw.select(User::getUsername,User::getMoney);
    List<User> users = userDao.selectList(lqw);
}

//普通格式
@Test
public void testSelect(){
    QueryWrapper qw = new QueryWrapper();
    lqw.select("username","money");
    List<User> users = userDao.selectList(lqw);
}
```

- 查询总条数

```java
@Test
public void testSelectCount(){
    QueryWrapper qw = new QueryWrapper();
    qw.select("count(*) as count");
    List<Map<String,Object>> list = userDao.selectMaps(qw);
    System.out.println(list);
}
```

- 分组统计

```java
@Test
public void testSelectCount(){
    QueryWrapper qw = new QueryWrapper();
    //1. 将 总数 和 性别 字段查出来
    qw.select("count(*) as count , gender");
    //2. 按性别分组
    qw.groupBy("gender");
    List<Map<String,Object>> list = userDao.selectMaps(qw);
    System.out.println(list);
}
```



### 其他查询

- 等匹配

```java
@Test
public void testEqual(){
    LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper();
    lqw.eq(User::getUsername,"张三").eq(User::getPassword,"123");
    User user = userDao.selectOne(lqw);
    System.out.println(user);
}
```

- 近似查询

  - lqw.like(User::getName,"J")

  - lqw.likeLeft(User::getName,"J") 相当于 %J

  - lqw.likeRight(User::getName,"J") 相当于 J%

- 其他
  - https://baomidou.com/





## 注解

https://baomidou.com/pages/223848/

- @TableName：实体类上，标识实体类对应的表
  - 属性
    - value：String，实体类对应的表
- @TableField：非主键
  - 属性
    - value：String，数据库字段名
    - exist：boolean，是否为数据库表字段，默认为true
    - select：boolean，是否进行select查询，默认为true
- @TableId：主键注解
  - 属性
    - value：String，主键字段名
    - type：Enum，指定主键类型（IdType.枚举)
      - AUTO：数据库ID自增
      - NONE：无状态，该类型为未设置主键类型（注解里等于跟随全局，全局里约等于 INPUT）
      - INPUT：insert 前自行 set 主键值
      - ASSIGN_ID：雪花算法
      - ASSIGN_UUID：以UUID生成算法作为id生成策略
- @TableLogic：逻辑删除
  - 属性
    - value：String，逻辑未删除值
    - delval：String，逻辑删除值
- @Version





## 全局配置

```yaml
mybatis-plus:
 global-config:
  db-config:
   # 配置全局id设置类型
   id-type: auto
   # 配置全局表前缀
   table-prefix: tb_
   # 表中的哪个字段需要作为通用的逻辑删除字段
   logic-delete-field: deleted
   # 逻辑未删除值
   logic-not-delete-value: 0
   # 逻辑删除值
   logic-delete-value: 1
```





## 乐观锁

### 定义

- 乐观锁：在操作数据时十分乐观，认为别的线程不会同时修改数据，所以不会上锁，但是在更新数据时会判断在此期间别的线程是否更新过这个数据
- 悲观锁：在操作数据时比较悲观，认为每次拿数据时都可能有别的线程同时修改数据，所以每次拿数据时都会上锁，执行完操作后才会释放锁，这样别的线程想拿这个数据会阻塞直到他拿到锁



### 实现

- 一般是在数据表中加上一个数据版本号version字段，表示数据被修改的次数。当数据被修改是，version值会 +1 。当线程A要更新数据时，在读取数据的同时也会读取version值，在提交更新时，若刚才读取到的version值于数据库中的version值相等时才会更新
- `update user set name = "zhangsan",version = oldVersion +1 where id = id version = oldVersion`



### 应用

```java
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor(){
    //1.定义MybatisPlus的拦截器
    MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
    //2.添加分页的拦截器
    mybatisPlusInterceptor.addInnerInterceptor(new PaginationInnerInterceptor());
    //2.添加乐观锁的拦截器
    mybatisPlusInterceptor.addInnerInterceptor(new OptimisticLockInnerInterceptor());
    return mybatisPlusInterceptor;
}
```

```java
@Test
public void testUpdate(){
    //1. 通过id获取要修改的完整数据，所以这条数据中包含原始的 version 值
    User user = userDao.selectById(3L);
    //2. 设置值到需要更改的地方
    user.setName("zhangsan");
    //3. 更改
    userdao.updateById(user);
}
```





## 代码生成器

### 坐标

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>

<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.16</version>
</dependency>

<!--lombok-->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <scope>provided</scope>
</dependency>

<!--mybatisPlus的起步坐标-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.1</version>
</dependency>

<!--代码生成器-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.4.1</version>
</dependency>

<!--velocity模板引擎-->
<dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity-engine-core</artifactId>
    <version>2.3</version>
</dependency>
```



### 模板

```java
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;

public class CodeGenerator {
    public static void main(String[] args) {
        //1.获取代码生成器的对象
        AutoGenerator autoGenerator = new AutoGenerator();

        //设置数据库相关配置
        DataSourceConfig dataSource = new DataSourceConfig();
        dataSource.setDriverName("com.mysql.cj.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/"+这里"数据库名?serverTimezone=UTC");
        dataSource.setUsername("root");
        dataSource.setPassword("root");
        autoGenerator.setDataSource(dataSource);

        //设置全局配置
        GlobalConfig globalConfig = new GlobalConfig();
        globalConfig.setOutputDir(System.getProperty("user.dir")/*项目根位置*/+这里"/模块名/src/main/java");    //设置代码生成位置
        globalConfig.setOpen(false);    //设置生成完毕后是否打开生成代码所在的目录
        globalConfig.setAuthor("Eli");    //设置作者
        globalConfig.setFileOverride(true);     //设置是否覆盖原始生成的文件
        globalConfig.setMapperName("%sDao");    //设置数据层接口名，%s为占位符，指代模块名称
        globalConfig.setIdType(IdType.ASSIGN_ID这里);   //设置Id生成策略
        autoGenerator.setGlobalConfig(globalConfig);

        //设置包名相关配置
        PackageConfig packageInfo = new PackageConfig();
        packageInfo.setParent("com.aaa"这里);   //设置生成的包名，与代码所在位置不冲突，二者叠加组成完整路径
        packageInfo.setEntity("domain");    //设置实体类包名
        packageInfo.setMapper("dao");   //设置数据层包名
        autoGenerator.setPackageInfo(packageInfo);

        //策略设置
        StrategyConfig strategyConfig = new StrategyConfig();
        strategyConfig.setInclude("表名"这里);  //设置当前参与生成的表名，参数为可变参数，可添加多个表名
        strategyConfig.setTablePrefix("tb1_");  //设置数据库表的前缀名称，模块名 = 数据库表名 - 前缀名  例如： User = tbl_user - tbl_
        strategyConfig.setRestControllerStyle(true);    //设置是否启用Rest风格
        strategyConfig.setVersionFieldName("version");  //设置乐观锁字段名
        strategyConfig.setLogicDeleteFieldName("deleted");  //设置逻辑删除字段名
        strategyConfig.setEntityLombokModel(true);  //设置是否启用lombok
        autoGenerator.setStrategy(strategyConfig);
        //2.执行生成操作
        autoGenerator.execute();
    }
}
```

- 注意事项：
  - UserDao接口上要加@Mapper注解
  - 在配置文件中配置数据库连接池，端口号等信息









# 日志



## JUL

![屏幕截图 2022-09-02 145446](D:\picture\typora\java\屏幕截图 2022-09-02 145446.jpg)

### 快速入门

```java
Logger logger = Logger.getLogger("cn.Eli.test.JulTest");
logger.info("info");
logger.log(Level.INFO, "info");

String name = "zhangsan";
int age = 18;
logger.log(Level.INFO, "姓名：{0} 年龄：{1}", new Object[]{name, age});
```



### 日志级别

- 日志级别

  - SEVERE：错误
  - WARNING：警告
  - INFO：消息（默认）
  - CONFIG：配置
  - FINE：详细信息
  - FINER：详细信息
  - FINEST：详细信息

- 特殊日志级别

  - OFF：可用来关闭所有日志记录
  - ALL：可用来开启所有日志记录

- 设置日志级别

  ```java
  // 是否使用父处理器
  logger.setUseParentHandlers(false);
  // 定义处理器
  ConsoleHandler handler = new ConsoleHandler();
  //FileHandler handler = new FileHandler("C:\\Users\\123\\Desktop\\test.log");
  // 创建日志格式化对象
  SimpleFormatter formatter = new SimpleFormatter();
  // 在处理器中设置输出格式
  handler.setFormatter(formatter);
  // 在记录器中添加处理器
  logger.addHandler(handler);
  // 设置日志的打印级别
  // 日志记录器和处理器的级别进行设置
  logger.setLevel(Level.ALL);
  handler.setLevel(Level.ALL);
  ```



### 父子关系

- 父子关系

  ```java
  Logger logger1 = Logger.getLogger("cn.Eli.test");
  Logger logger2 = Logger.getLogger("cn.Eli.test.JulTest");
  
  logger2.getParent() == logger1; // true
  ```

- logger2的父logger是logger1

- logger1的父logger是 LogManager$RootLogger（内部类）的对象



### 配置文件

- 默认配置文件

  ```properties
  # 文件命名为 logging.properties
  # 配置RootLogger使用的处理器
  handlers = java.util.logging.ConsoleHandler
  # RootLogger的日志记录级别
  .level = INFO
  
  # 文件处理器属性的设置
  # 输出日志文件的路径
  # %h：用户文件夹，%u：文件索引值
  java.util.logging.FileHandler.pattern = %h/java%u.log
  # 输出日志文件的限制（50000字节）
  java.util.logging.FileHandler.limit = 50000
  # 输出日志文件的最大数量
  java.util.logging.FileHandler.count = 1
  # 输出日志的格式
  java.util.logging.FileHandler.formatter = java.util.logging.XMLFormatter
  # 处理器的级别
  java.util.logging.FileHandler.level = SEVERE
  
  # 控制台处理器属性的设置
  # 输出日志的级别
  java.util.logging.ConsoleHandler.level = INFO
  # 输出日志的格式
  java.util.logging.ConsoleHandler.formatter = java.util.logging.SimpleFormatter
  
  # 可以在指定包下设置特定的日志级别
  com.xyz.foo.level = SEVERE
  ```

- 自定义配置文件

  - 读取配置文件

    ```java
    InputStream is = JulTest.class.getClassLoader().getResourceAsStream("logging.properties");
    // 获取日志管理器对象
    LogManager logManager = LogManager.getLogManager();
    // 读取自定义配置文件
    logManager.readConfiguration(is);
    ```

  - 相关配置

    ```properties
    # 自定义记录qi
    cn.Eli.handlers = java.util.logging.FileHandler, java.util.logging.ConsoleHandler
    cn.Eli.level = CONFIG
    cn.Eli.userParentHandlers = false
    # 输出日志文件是否追加
    java.util.logging.FileHandler.append = true
    # 指定handler的字符集
    java.util.logging.ConsoleHandler.encoding = UTF-8
    ```





## Log4j

### 组件介绍

#### Loggers

- 具有继承机制
- 日志级别
  -  OFF > FATAL > ERROR > WARN > INFO > DEBUG（默认） > TRACE > ALL

#### Appendars

- 类别
  - ConsoleAppender
  - FileAppender
  - DailyRollingAppender
  - RollingFileAppender
  - JDBCAppender

#### Layouts

- 类别
  - HTMLLayout
  - SimpleLayout
  - PattenLayout
- PattenLayout日志输出格式
  - %m：输出代码中指定的日志信息
  - %p：输出优先级，及 DEBUG、INFO等
  - %n：换行符（Windows平台的换行符为“\n”，Unix平台为“\n”
  - %r：输出自应用启动到输出该 log 信息耗费的毫秒值
  - %c：输出打印语句所属的类的全名
  - %t：输出产生该日志的线程全名
  - %d：输出服务器当前时间，默认为ISO8601，也可以指定格式，如：%d{yyyy年MM月dd日 HH:mm:ss:SSS}
  - %l：输出日志时间发生的位置，包括类名、线程、及在代码中的行数。如：Test.main(Test.java:10)
  - %F：输出日志消息产生时所在的文件名称
  - %L：输出代码中的行号
  - %%：输出一个“%”字符 
  - 可以在 % 与字符之间加入修饰符来控制最小宽度、最大宽度和文本的对其方式。如：
    - %5c：输出category名称，最小宽度是5，category<5，默认的情况下右对齐
    - %-5c：输出category名称，最小宽度是5，category<5，”-“号指定左对齐，会有空格
    - %.5c：输出category名称，最大宽度是5，category>5，就会将左边多出的字符截掉，<5不会有空格
    - %20.30c：输出category名称<20补空格，并且右对齐，>30字符，就会左边交多出的字符截掉



### 快速入门

- 依赖导入

  ```xml
  <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.12</version>
  </dependency>
  ```

- 记录日志

  ```java
  // 加载初始化信息
  BasicConfigurator.configure();
  /*
  	源码
  	public static void configure(){
  		Logger root = Logger.getRootLogger();
  		root.addAppender(new ConsoleAppender(new PatternLayout("%r [%t] %p %c %x - %m%n")));
  	}
  */
  Logger logger = Logger.getLogger(Log4jTest.class);
  logger.info("info信息")
  ```

  



### 配置文件

```properties
# 文件命名为 log4j.properties
# 以下为根节点rootLogger的配置

#log4j.rootLogger = 日志级别,appenderName1,appenderName2,appenderName3...
log4j.rootLogger = trace,console


#log4j.appender.自定义appender的名字
# 输出到控制台中
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.conversionPattern = [%-5p] %d{yyyy-MM-dd HH:mm:ss:SSS} %m%n

# 输出到文件中
log4j.appender.file = org.apache.log4j.FileAppender
log4j.appender.file.layout = org.apache.log4j.PatternLayout
log4j.appender.file.layout.conversionPattern = [%-5p] %d{yyyy-MM-dd HH:mm:ss:SSS} %m%n
log4j.appender.file.file = C://Users//123//Desktop//log4j.log
log4j.appender.file.encoding = utf-8
# FileAppender中的其他属性
	# boolean fileAppend：是否追加，默认true开启追加
	# int bufferSize：缓冲字节数，默认8192

# 按文件大小进行拆分
log4j.appender.rollingFile = org.apache.log4j.RollingFileAppender
log4j.appender.rollingFile.layout = org.apache.log4j.PatternLayout
log4j.appender.rollingFile.layout.conversionPattern = [%-5p] %d{yyyy-MM-dd HH:mm:ss:SSS} %m%n
log4j.appender.rollingFile.file = C://Users//123//Desktop//log4j.log
log4j.appender.rollingFile.encoding = utf-8
# 指定单个文件的最大字节
log4j.appender.rollingFile.maxFileSize = 1MB
# 指定文件的最大索引值
log4j.appender.rollingFile.maxBackupIndex = 5

# 按时间对文件进行拆分
log4j.appender.dailyRollingFile = org.apache.log4j.DailyRollingFileAppender
log4j.appender.dailyRollingFile.layout = org.apache.log4j.PatternLayout
log4j.appender.dailyRollingFile.layout.conversionPattern = [%-5p] %d{yyyy-MM-dd HH:mm:ss:SSS} %m%n
log4j.appender.dailyRollingFile.file = C://Users//123//Desktop//log4j.log
log4j.appender.dailyRollingFile.encoding = utf-8
# '.'yyyy-MM-dd：默认，按天划分；'.'yyyy-MM-dd HH:mm:ss：按秒划分
log4j.appender.dailyRollingFile.datePattern = '.'yyyy-MM-dd

# 持久化日志
log4j.appender.logDB = org.apache.log4j.jdbc.JDBCAppender
log4j.appender.logDB.layout = org.apache.log4j.PatternLayout
log4j.appender.logDB.Driver = com.mysql.jdbc.Driver
log4j.appender.logDB.URL = jdbc.mysql://localhost:3306/数据库
log4j.appender.logDB.username = root
log4j.appender.logDB.password = root
log4j.appender.logDB.sql = INSERT INTO tbl_log(id,name,createTime,level,category,message) values('project_log','%d{yyyy-MM-dd HH:mm:ss}','%p','%c','%F','%m')
```



### 补充

- LogLog：记录log4j日志输出的日志

  - 控制日志开关的代码 if(debugEnabled && !quietMode){}
  - !quietMode 已经处于 true
  - LogLog.setInternalDebugging(true); 开启日志

- 持久化日志的表

  ```sql
  # 持久化日志的表
  CREATE TABLE tbl_log{
  	id int(11) NOT NULL AUTO_INCREMENT,
  	name varchar(255) DEFAULT NULL COMMENT '项目名称',
  	createTIme varchar(255) DEFAULT NULL COMMENT '创建时间',
  	level varchar(255) DEFAULT NULL COMMENT '日志级别',
  	category varchar(255) DEFAULT NULL COMMENT '所在类的全路径',
  	fileName varchar(255) DEFAULT NULL COMMENT '文件名称',
  	message varchar(255) DEFAULT NULL COMMENT '日志消息',
  	PRIMARY KEY(id)
  }
  ```

- log4j的自定义logger

  ```properties
  # 格式：log4j.logger.目录
  log4j.logger.cn.Eli = info,console
  ```

  - 自定义logger和根节点logger
    - 配置中输出位置不同的，取两者的并集
    - 配置中输出等级不同的，取自定义logger的等级
    - 配置中输出位置相同的，删去自定义logger中的相同位置，以免多次打印





## JCL

### 快速入门

- 依赖导入

  ```xml
  <dependency>
      <groupId>commons-logging</groupId>
      <artifactId>commons-logging</artifactId>
      <version>1.2</version>
  </dependency>
  ```

- 日志记录

  ```java
  // 未导入其他日志框架，默认使用的是jul
  Log log = LogFactory.getLog(LogTest.class);
  log.fatal("fatal信息");
  ```





## SLF4J

### 快速入门

- 依赖导入

  ```xml
  <!--slf4j 核心依赖-->
  <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>1.7.36</version>
  </dependency>
  
  <!--slf4j 自带的简单日志实现-->
  <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-simple</artifactId>
      <version>1.7.36</version>
  </dependency>
  ```

- 使用自带的日志记录框架记录日志

  ```java
  Logger logger = LoggerFactory.getLogger(LogTest.class);
  // 五个日志记录等级
  logger.error("error信息");
  logger.warn("warn信息");
  logger.info("info信息"); // 默认
  logger.debug("debug信息");
  logger.trace("trace信息");
  ```

- 使用占位符记录日志

  ```java
  Logger logger = LoggerFactory.getLogger(LogTest.class);
  String name = "zhangsan";
  int age = 18;
  logger.info("学生信息-姓名：{}，年龄：{}",name,age);
  ```



### 绑定模式

![slf4j-concrete-bindings](D:\picture\typora\java\slf4j-concrete-bindings.png)

- 三种绑定模式

  - 在没有绑定任何日志实现的基础上，日志是不能绑定实现任何功能的
  - logback和simple（包括nop），都是门面时间线后面提供的日志实现，所以API完全遵循slf4j进行设计的，只需要导入想要是日志框架，即可使用。但是nop是不实现日志记录
  - log4j和JUL是slf4j前面的日志框架，所以API不遵循slf4j进行设计，
    - 对于需要使用该类日志实现技术的项目，可用适配器绑定该日志实现技术
    - 对于已经使用该类日志实现技术的项目，可使用桥接技术，与日志门面进行衔接

- 补充

  - 先导入哪个日志实现依赖就用哪个依赖记录日志

  - 若想用nop关闭所有日志记录功能，则需要首先导入nop的依赖

    ```xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.36</version>
    </dependency>
    ```

  - SLF4J: Class path contains multiple SLF4J bindings.：表示存在多个日志记录实现



### 绑定

- 绑定log4j

  ```xml
  <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>1.7.36</version>
  </dependency>
  <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.12</version>
  </dependency>
  
  <!--导入 log4j 适配器的依赖-->
  <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-log4j12</artifactId>
      <version>1.7.36</version>
  </dependency>
  ```

- 绑定JUL

  ```xml
  <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>1.7.36</version>
  </dependency>
  
  <!--导入 jdk14（JUL） 的日志适配器-->
  <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-jdk14</artifactId>
      <version>1.7.36</version>
  </dependency>
  ```

  

### 桥接

![slf4j-legacy](D:\picture\typora\java\slf4j-legacy.png)

- 删除原先的日志实现技术的依赖

- 导入相应的桥接器依赖

  ```xml
  <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>log4j-over-slf4j</artifactId>
      <version>1.7.36</version>
  </dependency>
  ```

- 使用的是 log4j 包下的日志组件资源，但真正实现的是 slf4j + logback

- 导入依赖时

  - 如果 桥接器 在 适配器  的上方，则报错
  - 如果 桥接器 在 适配器 的下方，则不会施行桥接器





## logback

### 快速入门

- 依赖

  ```xml
  <!--slf4j 日志门面技术-->
  <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>1.7.36</version>
  </dependency>
  <!--logback 基础模块（包含logback-core核心依赖）-->
  <dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-classic</artifactId>
      <version>1.2.11</version>
  </dependency>
  ```

- 记录日志

  ```java
  // 使用的是日志门面技术
  //import org.slf4j.Logger;
  //import org.slf4j.LoggerFactory;
  
  Logger logger = LoggerFactory.getLogger(LogbackTest.class);
  logger.info("hello");
  ```



### 配置文件

- 日志输出格式

  - %-5level：级别，案例为设置5个字节，左对齐
  - %d{yyyy-MM-dd HH:mm:ss:SSS}：日期
  - %c：当前类的全限定名
  - %M：当前执行日志的方法
  - %L：行号
  - %thread：线程名称
  - %m或者%msg：信息
  - %n：换行

- 配置文件logback.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <configuration>
      <property name="pattern" value="[%-5level] %d{yyyy-MM-dd HH:mm:ss:SSS}   %-10thread --->    %msg    %c %M%n"></property>
      <property name="logDir" value="C://Users//123//Desktop"></property>
  
      <appender name="consoleAppender" class="ch.qos.logback.core.ConsoleAppender">
          <!--
              对于日志输出目标的配置
                  System.out：表示以黑色字体输出日志（默认）
                  System.err：表示以红色字体输出日志
          -->
          <target>System.err</target>
          <!--
              配置日志输出格式（引入上面的property）
          -->
          <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
              <pattern>${pattern}</pattern>
          </encoder>
      </appender>
  
      <!-- 默认以追加形式记录日志 -->
      <appender name="fileAppender" class="ch.qos.logback.core.FileAppender">
          <!-- 引入文件位置 -->
          <file>${logDir}/logback.log</file>
  
          <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
              <pattern>${pattern}</pattern>
          </encoder>
      </appender>
      
      <!--<logger name="cn.eli.nio.aaa" level="INFO">
          <appender-ref ref="consoleAppender"/>
      </logger>-->
  
      <!-- 配置rootLogger -->
      <root level="ALL">
          <appender-ref ref="consoleAppender"/>
          <appender-ref ref="fileAppender"/>
      </root>
  </configuration>
  ```

- 配置html格式的日志文件

  ```xml
  <property name="htmlPattern" value="%level%d{yyyy-MM-dd HH:mm:ss:SSS}%msg%c%M%thread"></property>
  <property name="logDir" value="C://Users//123//Desktop"></property>
  
  <appender name="fileAppender" class="ch.qos.logback.core.FileAppender">
      <file>${logDir}/logback.html</file>
      <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
          <layout class="ch.qos.logback.classic.html.HTMLLayout">
              <pattern>${htmlPattern}</pattern>
          </layout>
      </encoder>
  </appender>
  ```

- 拆分文件

  ```xml
  <appender name="roll" class="ch.qos.logback.core.rolling.RollingFileAppender">
      <file>${logDir}/roll_logback.log</file>
      <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">${pattern}</encoder>
      <!-- 指定拆分规则 -->
      <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
          <!-- 按时间和压缩格式命名文件 压缩格式gz -->
          <fileNamePattern>${logDir}/roll.%d{yyyy-MM-dd}.log%i.gz</fileNamePattern>
          <!-- 按文件大小进行拆分 -->
          <maxFileSize>1MB</maxFileSize>
      </rollingPolicy>
  </appender>
  ```

- 使用过滤器

  ```xml
  <appender name="consoleFilterAppender" class="ch.qos.logback.core.ConsoleAppender">
      <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
          <pattern>${pattern}</pattern>
      </encoder>
      <!-- 配置过滤器，匹配等级 -->
      <filter class="ch.qos.logback.classic.filter.LevelFilter">
          <level>ERROR</level>
          <!-- 高于level中设置的级别，则打印日志 -->
          <onMatch>ACCEPT</onMatch>
          <!-- 低于level中设置的级别，则屏蔽日志 -->
          <onMismatch>DENY</onMismatch>
      </filter>
  </appender>
  ```

- 自定义logger

  ```xml
  <!-- additivity="false"表示不继承rootLogger -->
  <logger name="cn.Eli" level="info" additivity="false">
  	<appender-ref ref="consoleAppender"/>
  </logger>
  ```



### 异步日志

- 同步日志：打印日志时，会暂停程序的执行（单线程）

- 异步日志：多线程

  ```xml
  <appender name="consoleAppender" class="ch.qos.logback.core.ConsoleAppender"></appender>
  
  <appender name="asyncAppender" class="ch.qos.logback.classic.AsyncAppender">
      <appender-ref ref="consoleAppender"/>
  </appender>
  
  <root level="ALL">
      <appender-ref ref="asyncAppender"/>
  </root>
  ```





## log4j2

### 快速入门

- 依赖

  ```xml
  <!-- slf4j 日志门面 -->
  <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>1.7.36</version>
  </dependency>
  <!-- 适配器 -->
  <dependency>
      <groupId>org.apache.logging.log4j</groupId>
      <artifactId>log4j-slf4j-impl</artifactId>
      <version>2.17.2</version>
  </dependency>
  <!-- log4j2 日志门面 -->
  <dependency>
      <groupId>org.apache.logging.log4j</groupId>
      <artifactId>log4j-api</artifactId>
      <version>2.17.2</version>
  </dependency>
  <!-- log4j2 日志实现 -->
  <dependency>
      <groupId>org.apache.logging.log4j</groupId>
      <artifactId>log4j-core</artifactId>
      <version>2.17.2</version>
  </dependency>
  ```

- 日志记录

  ```java
  // log4j2 日志记录
  //import org.apache.logging.log4j.LogManager;
  //import org.apache.logging.log4j.Logger;
  
  // 六种记录等级
  Logger logger = LogManager.getLogger(Log4j2Test.class);
  logger.fatal("fatal");
  logger.error("error"); // 默认
  logger.warn("warn");
  logger.info("info");
  logger.debug("debug");
  logger.trace("trace");
  ```

  ```java
  // slf4j+log4j2 日志记录
  //import org.slf4j.Logger;
  //import org.slf4j.LoggerFactory;
  
  // 执行原理：slf4j门面调用的是log4j2的门面，再由log4j2门面调用log4j2的日志实现
  Logger logger = LoggerFactory.getLogger(Log4j2Test.class);
  logger.error("error");
  logger.warn("warn");
  logger.info("info");
  logger.debug("debug");
  logger.trace("trace");
  ```

- 配置文件

  ```xml
  <!-- 配置文件名 log4j2.xml -->
  
  <?xml version="1.0" encoding="UTF-8" ?>
  <!-- 此处的error是配置记录日记记录器的日志级别（LogLog） -->
  <Configuration status="error">
      <Appenders>
          <Console name="consoleAppender" target="SYSTEM_ERR"></Console>
      </Appenders>
  
      <Loggers>
          <Root level="trace">
              <AppenderRef ref="consoleAppender"/>
          </Root>
      </Loggers>
  </Configuration>
  ```



### 其他配置

- 输出到文件中

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <Configuration>
      <properties>
          <property name="logDir" value="C://Users//123//Desktop"></property>
          <property name="pattern" value="[%-5level] %d{yyyy-MM-dd HH:mm:ss:SSS} %msg -> %c %M %thread%n"></property>
      </properties>
  
      <Appenders>
          <File name="fileAppender" fileName="${logDir}/log4j2.log">
              <PatternLayout pattern="${pattern}"/>
          </File>
      </Appenders>
  
      <Loggers>
          <Root level="trace">
              <AppenderRef ref="fileAppender"/>
          </Root>
      </Loggers>
  </Configuration>
  ```

- 拆分日志文件

  ```xml
  <!-- 日志文件的名字，filePattern表示日志文件拆分后的命名规则，可分文件夹-->
  <!-- 文件的命名不支持特殊符号 -->
  <RollingFile name="rollingFileAppender" fileName="${logDir}/rollog.log"
               filePattern="${logDir}/%d{yyyy-MM-dd}/rollog-%d{HH}hours-%i.log">
      <PatternLayout pattern="${pattern}"/>
  
      <Policies>
          <!-- 在系统启动时，触发拆分规则，产生一个日志文件 -->
          <OnStartupTriggeringPolicy/>
          <!-- 按文件的大小进行拆分-->
          <SizeBasedTriggeringPolicy size="1MB"/>
          <!-- 按时间节点进行拆分 拆分规则是filePattern-->
          <TimeBasedTriggeringPolicy/>
      </Policies>
  
      <!-- 在同一目录下，文件的个数限制，如果超出，则根据时间进行覆盖 -->
      <DefaultRolloverStrategy max="30"></DefaultRolloverStrategy>
  </RollingFile>
  ```




### 异步日志

- AsyncAppender

  - 异步日志依赖

    ```xml
    <dependency>
        <groupId>com.lmax</groupId>
        <artifactId>disruptor</artifactId>
        <version>3.3.7</version>
    </dependency>
    ```

  - 引入异步日志

    ```xml
    <Appenders>
        <Console name="consoleAppender" target="SYSTEM_ERR"></Console>
        
        <Async name="myAsync">
            <AppenderRef ref="consoleAppender"/>
        </Async>
    </Appenders>
    
    <Loggers>
        <Root level="trace">
            <AppenderRef ref="myAsync"/>
        </Root>
    </Loggers>
    ```

- AsyncLogger

  - 全局异步

    - 创建 log4j2.component.properties 文件
    - 配置：Log4jContextSelector=org.apache.logging.log4j.core.async.AsyncLoggerContextSelector

  - 混合异步

    ```xml
    <Loggers>
        <Root level="trace">
            <AppenderRef ref="myAsync"/>
        </Root>
        
        <!--
    	includeLocation="false"：去除日志中行号信息，行号信息非常影响效率
    	addactivity="false"：不继承rootLogger
    	-->
        <AsyncLogger name="cn.Eli" level="trace" includeLocation="false" additivity=
    "false">
        	<AppenderRef ref="consoleAppender"/>
        </AsyncLogger>
    </Loggers>
    ```









# SpringBoot



## 基础篇

### 配置文件

- 类型
  - application.properties
  - application.yml
  - application.yaml
  - 优先级：application.properties > application.yml > application.yaml

- yaml数据的读取

  - yaml数据

  ```yaml
  server:
   prot: 80
   servlet:
    context-path: /
   
  logging:
   level:
    root: info
    
  OnePerson:
   basketball: true
   music: true
   likes:
    - sing
    - dance
    - rap
  ```

  - 三种读取方式
  
    ```java
    @Value("${server.port}")
    private Integer port;
    ```
  
    ```java
    @Autowired
    private Environment environment;
    
    private String port = environment.getProperty("server.prot");
    private String like = environment.getProperty("OnePerson.likes[0]");
    ```
  
    ```java
    // 2.2以上版本只用确保能加入容器
    @Component
    @ConfigurationProperties(prefix = "OnePerson")
    @Date // setter是必要的
    public class OnePerson{
        // prefix与属性名的拼接，即为yaml文件中的
        private boolean basketball;
        private boolean music;
        private String[] likes;
    }
    
    public class Test{
        @Autowired
        private OnePerson onePerson;
    }
    ```
    
  - yaml文件的变量
  
  ```yaml
  location:
   baseDir: C:\windows
   
  tempDir: ${location.baseDir}\temp
  # \t是转义字符，使用引号包裹转义可生效
  tempDir: "${location.baseDir}\temp" # 此处的 \t hui
  ```
  
  - 自定义对象封装的警告解决方案
  
  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-configuration-processor</artifactId>
      <optional>true</optional>
  </dependency>
  ```
  





### 拦截器配置

> 与springmvc类似

- 定义拦截器

  ```java
  @Component
  public class IpCounterInterceptor implements HandlerInterceptor {
      @Override
      public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
          return true;
      }
  }
  ```

- 配置拦截器

  ```java
  @Configuration
  public class InterceptorConfiguration implements WebMvcConfigurer{
      @Autowired
      private IpCounterInterceptor ipCounterInterceptor;
  
      @Override
      public void addInterceptors(InterceptorRegistry registry) {
          registry.addInterceptor(ipCounterInterceptor)
                  .addPathPatterns("/**")
                  .excludePathPatterns("/error");
      }
  }
  ```

- 为什么使用`implements WebMvcConfigurer`而不使用`extends WebMvcConfigurationSupport`

  - 当你继承WebMvcConfigurationSupport类时，Spring Boot的自动配置将被禁用。这是因为Spring Boot的自动配置依赖于WebMvcAutoConfiguration类，而这个类在其条件注解@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)中指定了，只有当应用程序上下文中不存在WebMvcConfigurationSupport类型的bean时，才会启用自动配置。
  - 为什么这样设计：WebMvcConfigurationSupport类是Spring MVC的主配置类，它包含了很多默认的配置。当你继承这个类并添加@Configuration注解时，你的类将成为Spring MVC的主配置类，这将覆盖Spring Boot的自动配置。



### 整合junit

- 引入spring-boot-starter-test依赖，注意：scope 是否设置为test

- 测试 @SpringBootTest

  ```java
  @SpringBootTest
  class Springboot03JunitApplicationTests {
      @Autowired
      private UserService userService;
      
      public void test(){
          userService.save();
      }
  
      @Test
      void contextLoads() {
      }
  }
  ```

- 说明
  - spring在整合junit时，需要加注解@RunWith（设置类运行器）和@SpringContext（指定spring容器）。
  - springbootTest在测试运行时会找SpringBootConfiguration(即引导类，@SpringBootApplication中包含@SpringBootConfiguration注解)
  - 如果测试类在引导类包中或其子包下，则不需要指定spring容器位置。
  - 如果不在，则需用@SpringBootTest(classes = 引导类的class) 或 @ContextConfiguration(classes = 引导类的class)



### 整合mybatis/MP

- 配置依赖

  - mybatis-spring-boot-starter 或 mybatis-plus-boot-starter
  - mysql-connector-java

- 配置连接

  ```yaml
  spring:
    datasource:
      driver-class-name: com.mysql.jdbc.Driver
      url: jdbc:mysql://localhost:3306/ssm
      username: root
      password: root
      # 如果使用druid，可在此处配置datasource
      type: com.alibaba.druid.pool.DruidDataSource
  ```

  ```yaml
  spring:
    datasource:
      # 也可以这样配置数据库连接池
      druid: 
        driver-class-name: com.mysql.jdbc.Driver
        url: jdbc:mysql://localhost:3306/ssm
        username: root
        password: root
  ```

- 数据库SQL映射需要添加@Mapper被容器识别到


- 常见问题
  - MySQL-8驱动强制要求设置时区
    - 配置时区：jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC
    - 修改mysql数据库配置
  - 驱动类过时：driver-class-name: com.mysql.cj.jdbc.Driver
  
- mybatis plus 开启日志

  ```yaml
  mybatis-plus:
    configuration:
      log-impl: org.apache.ibatis.logging.stdout.StdOutImpl # 日志打印到控制台上
  ```

  





## 运维篇

### 服务器启动

#### windows

- 对springboot项目打包

- 运行项目

```shell
java -jar springboot.jar
```

- 需要添加下面依赖：该依赖可以将应用及其**所有依赖**打包成一个可执行的jar文件

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

- 常用指令

```shell
# 查询端口
netstat -ano
# 查询指定端口
netstat -ano | findstr "端口"
# 根据进程PID查询进程名称
tasklist | findstr "进程PID号"
# 根据PID杀死任务
taskkill /F /PID "进程PID号"
# 根据进程名称杀死任务
taskkill -f -t -im "进程名称"
```

#### linux

- 创建目录 /usr/local/java_project
- 将jar包移到该目录下
- 运行项目





### 配置高级

#### 配置

- 携带临时属性启动springboot

```cmd
java -jar springboot.jar --server.port=8080
```

- 携带多个属性启动springboot，属性间使用空格分隔

- 引导类设置

```java
public class Application{
    public static void main(String[] args){
        //args 中的数据是临时参数，不传递到run方法中可断开联系
        SpringApplication.run(Application.class,args);
    }
}
```

- springboot中的4级配置文件（复制叠加，1级最高，4级最低）

  - 1级：file：config/application.yml（config目录与打包完的jar包同一目录）
  - 2级：file：application.yml（配置文件与打包完的jar包同一目录）
  - 3级：classpath：config/application.yml
  - 4级：classpath：application.yml
  - 注：同级文件优先级：properties > yml > yaml（如果使用多级且不同类型配置文件，先手动测试优先级，不过一般也不那么做）

- spring配置文件的设置

  - 添加临时属性

  ```shell
  --spring.config.name=newApplication
  --spring.config.location=classpath:/newApplication1.yml/newApplication2.yml
  ```


#### 属性优先级

1. 默认属性（由设置指定`SpringApplication.setDefaultProperties`）。
2. [`@PropertySource`](https://docs.spring.io/spring-framework/docs/5.3.22/javadoc-api/org/springframework/context/annotation/PropertySource.html)`@Configuration`您的课程上的注释。请注意，`Environment`在刷新应用程序上下文之前，不会将此类属性源添加到 中。配置某些属性（例如在刷新开始之前读取的`logging.*`和）为时已晚。`spring.main.*`
3. 配置数据（例如`application.properties`文件）。
4. 仅在中`RandomValuePropertySource`具有属性的 A。`random.*`
5. 操作系统环境变量。
6. Java 系统属性 ( `System.getProperties()`)。
7. JNDI 属性来自`java:comp/env`.
8. `ServletContext`初始化参数。
9. `ServletConfig`初始化参数。
10. 来自`SPRING_APPLICATION_JSON`（嵌入在环境变量或系统属性中的内联 JSON）的属性。
11. 命令行参数。
12. `properties`属性在你的测试。可[用于测试应用程序的特定部分](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.testing.spring-boot-applications.autoconfigured-tests)[`@SpringBootTest`](https://docs.spring.io/spring-boot/docs/2.7.2/api/org/springframework/boot/test/context/SpringBootTest.html)的测试注释。
13. [`@TestPropertySource`](https://docs.spring.io/spring-framework/docs/5.3.22/javadoc-api/org/springframework/test/context/TestPropertySource.html)测试上的注释。
14. `$HOME/.config/spring-boot`当 devtools 处于活动状态时，目录中的[Devtools 全局设置属性。](https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.devtools.globalsettings)



### 多环境开发

#### 使用

- yaml，yml

  - 格式

  ```yaml
  # 应用环境
  # 启动指定环境
  spring:
   profiles:
    active: pro
  # 在此环境中添加公共配置
  ---
  # 环境设置
  # 生产环境
  spring:
   profiles: pro
  server:
   port: 80
  ---
  # 开发环境
  spring:
   profiles: dev
  server:
   port: 81
  ---
  # 测试环境
  spring:
   config:
    activate:
     on-profile: test
  server:
   port: 82
  ```

  - 拆分：

    - application-dev.yml
    - application-pro.yml：

    ```yaml
    server:
     port: 80
    ```

    - 主配置：

    ```yaml
    spring:
     profiles:
      active: pro
    ```

- properties：只能拆分成多文件

#### 独立配置文件

- 命名规则：
  - application-devDB.yml
  - application-devRedis.yml
  - application-devMVC.yml
- 使用include属性加载

```yaml
# 相同属性，后加载的覆盖先加载的，dev的优先级在最后
spring:
 profiles:
  active: dev
   include: devDB,devRedis,devMVC
```

- group（Spring2.4）

```yaml
# 选择dev后，会加载dev配置文件和dev组中的group，dev的优先级在最前
spring:
 profiles:
  active: dev
  group:
   "dev": devDB,devMVC
   "pro": proDB,proMVC
```

- 读取maven中的配置

```xml
<profiles>
    <profile>
        <id>env_dep</id>
        <properties>
            <profile.active>dev</profile.active>
        </properties>
        <activation>
            <activeByDefault>true（是否为默认环境）</activeByDefault>
        </activation>
    </profile>
    <profile>
        <id>env_pro</id>
        <properties>
            <profile.active>pro</profile.active>
        </properties>
    </profile>
    <profile>
        <id>env_test</id>
        <properties>
            <profile.active>test</profile.active>
        </properties>
    </profile>
</profiles>
```

```yaml
spring:
 profiles:
  active: @profile.active@
  group:
   "dev": devDB,devMVC
   "pro": devDB,devMVC
```

注：基于springboot读取maven配置的前提下，如果在idea下测试工程时pom.xml每次更新需要手动compile方可生效



### 日志

#### 基础操作

- 日志的作用
  - 编程期调试代码
  - 运营期记录信息
    - 记录日常运营的重要信息（峰值流量、平均响应时长……）
    - 记录应用报错信息（错误堆栈）
    - 记录运维过程数据（扩容、宕机、报警……）

- 日志文件：

  - 配置springboot日志

    ```properties
    # springboot默认使用的是logback日志
    
    logging.level.cn.Eli = trace
    logging.pattern.console = %d{yyyy-MM-dd HH:mm:ss} [%-5level] %m%n
    logging.file.path = C:/Users/123/Desktop
    ```

  - 配置logback日志文件

- 使用log4j2日志

  ```xml
  <dependency>
      <groutId>org.springframework.boot</groutId>
      <artifactId>spring-boot-stater-web</artifactId>
      <!-- 排除掉原始依赖（包括logback） -->
      <exclusions>
          <exclusion>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-stater-logging</artifactId>
          </exclusion>
      </exclusions>
  </dependency>
  
  <!-- 添加log4j2的依赖 -->
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-stater-logging</artifactId>
  </dependency>
  ```

  


### 热部署

- 坐标

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
</dependency>
```

- 激活热部署：Build Project

- 补充：

  - 重启：自定义开发代码，包含类、页面、配置文件等，加载位置restart类加载器（热部署）

  - 重载：jar包，加载位置base类加载器

- 热部署范围设置

  - 默认不触发重启的目录列表

    - /META-INF/maven、/META-INF/resouces、/resources、/static、/public、/templates

    - 设置

      ```yml
      devtools:
       restart:
        exclude: static/**,public/**
        enabled: true(默认)/flase
      ```

      



## 开发篇

### MongoDB

- docker部署

  ```shell
  # docker run --name mongo -v /mongo_local_data:/data/db -d -p 27017:27017 mongo
  docker run --name mongo -itd --auth -d -p 27017:27017 mongo
  ```

  - -itd 交互后台运行容器
  - --auth 需要密码才能访问容器服务

- 创建MongoDB账号

  ```shell
  docker exec -it mongo bash
  ```

  ```shell
  mongo admin
  db.createUser({ user:'admin',pwd:'mongo',roles:[ { role:'userAdminAnyDatabase', db: 'admin'},"readWriteAnyDatabase"]});
  ```

  ```shell
  db.auth('admin', 'mongo')
  ```

- 常用指令

  - 数据库操作

    - use DB_NAME：如果数据库不存在，则创建数据库，否则切换到指定数据库。
    - db：查看当前数据库
    - show dbs：查看所有数据库
    - db.DB_NAME.insert({"name":"张三"})：插入数据
    - db.DB_NAME.drop()：删除当前库

    - 注意: 在 MongoDB 中，集合只有在内容插入后才会创建! 就是说，创建集合(数据表)后要再插入一个文档(记录)，集合才会真正创建。

  - 集合操作

    - db.createCollection("name",  {options: val, ...})
      - name：集合名
      - options：可选参数
        - capped：布尔，如果为 true，则创建固定集合。固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档。当该值为 true 时，必须指定 size 参数。
        - size：数值，集合最大字节数
        - max：数值，指定固定集合中包含文档的最大数量。
    - db.COL_NAME.drop()
    - show collections

  - 文档操作

    - db.COL_NAME.insert(document)：插入文档
    - db.COL_NAME.insertOne(document)：_id存在则更新数据，不存在则插入文档

- 客户端

  - 依赖

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
    </dependency>
    ```

  - 配置

    ```yaml
    spring:
      data:
        mongodb:
          uri: mongodb://192.168.36.132/test
    ```

  - java客户端

    ```java
    @Autowired
    MongoTemplate mongoTemplate;
    ```

    



### task

- 坐标

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
  </dependency>
  ```

- 启动类添加@EnableScheduling注解

- 编写组件

  ```java
  @Component
  public class MyTask {
      @Scheduled(cron = "0/1 * * * * ?")
      public void print() {
          System.out.println("hello, world!");
      }
  }
  ```

  - cron生成器：https://www.bejson.com/othertools/cron/

  可选配置

  ```yaml
  spring:
    task:
      scheduling:
        pool:
          # 任务调度线程池大小，默认 1
          size: 1
        # 调度线程名的前缀，默认 scheduling-
        thread-name-prefix: timer_
        shutdown:
          # 线程关闭时等待所有任务完成
          await-termination: false
          # 设置调度线程关闭前最大等待时间，确保最后一次一定关闭
          await-termination-period: 10s
  ```






### Vilidation

> 本篇主要记录在**spring-boot**环境下的数据校验

- 依赖

  ```xml
  <!-- 含 hibernate-validator 依赖 -->
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-validation</artifactId>
  </dependency>
  ```

- 使用（以spring-boot-web应用为例）

  - 定义pojo类

    ```java
    @Data
    public class User {
        @NotNull(message="username为空")
        private String username;
        @NotNull
        private String password;
    }
    ```

  - controller

    ```java
    @RestController
    @Validated
    public class UserController {
        public String volidate(@Valid User user) {
            return "ok"；
        }
    }
    ```

- 常用注解

  | 注解                       | 说明                                                 |
  | -------------------------- | ---------------------------------------------------- |
  | @Null                      | 只能为null                                           |
  | @NotNull                   | 不能为null                                           |
  | @AssertFalse               | 必须为false                                          |
  | @AssertTrue                | 必须为true                                           |
  | @DecimalMax(value)         | 不大于指定值                                         |
  | @DecimalMin(value)         | 不小于指定值                                         |
  | @Max(value)                | 不大于指定值                                         |
  | @Min(value)                | 不小于指定值                                         |
  | @Digits(integer, fraction) | 必须为一个小数，整数位数不超过i，小数位数不超过f     |
  | @Past                      | 必须为过去的日期                                     |
  | @Future                    | 必须为将来的日期                                     |
  | @PastOrPresent             | 日期不超过现在                                       |
  | @FutureOrPresent           | 日期不早于现在                                       |
  | @Pattern(regexp)           | 必须符合指定的正则表达式                             |
  | @Size(min, max)            | 大小在min到max之间（Array、Collection、Map、String） |
  | @Length(max, min)          | 字符串长度在min到max之间（String）                   |
  | @NotEmpty                  | 不为空（Array、Collection、Map、String）             |
  | @NotBlank                  | 字符串不为空（String）                               |
  | @Email                     | 格式必须为email格式                                  |





### Mail

- 坐标

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
  </dependency>
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-mail</artifactId>
  </dependency>
  ```

- 配置

  ```yaml
  spring:
    mail:
      host: smtp.qq.com
      username: 2837995218@qq.com
      password: ratppigfwfugdgcj
  ```

- 发送业务

  ```java
  @Service("mail")
  public class MailServiceImpl implements MailService {
      @Autowired
      private JavaMailSender javaMailSender;
  
      @Override
      public void sendMail() {
          /* 发送简单邮件
          SimpleMailMessage message = new SimpleMailMessage();
          message.setFrom("2837995218@qq.com"+"(eli)");
          message.setTo("axdceep31@163.com");
          message.setSubject("respect");
          message.setText("hello, long time no see.");
          //message.setSentDate(); // 设置邮件发送的时间
          javaMailSender.send(message);*/
          
          try {
              MimeMessage message = javaMailSender.createMimeMessage();
              // 是否添加附件
              MimeMessageHelper helper = new MimeMessageHelper(message, true);
              helper.setFrom("2837995218@qq.com"+"(eli)");
              helper.setTo("axdceep31@163.com");
              helper.setSubject("respect");
              // 是否解析文本中的标签
              helper.setText("hello, long time no see. welcome to "+
                             "<a href='https://www.baidu.com'>百度</a>", true);
              // 添加附件
              File file1 = new File("file1");
              File file2 = new File("file2");
              helper.addAttachment(file1.getName(), file1);
              helper.addAttachment(file2.getName(), file2);
              javaMailSender.send(message);
          }catch (Exception e){
              e.printStackTrace();
          }
      }
  }
  ```

  



### admin

> 基于spring-boot-starter-actuator监控模块的图形化管理界面

#### actuator

- 依赖

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-actuator</artifactId>
  </dependency>
  ```

- 配置

  ```yaml
  management:
    endpoint:
      health:
        # 显示 health 的详细信息，默认never
        show-details: always
      info:
        # 是否提供该数据
        enable: true
    endpoints:
      enabled-by-default: true # 默认true，表示能暴露所有数据，jconsole可以查看
      web:
        exposure:
          # 配置在web端可以暴露的数据，* 可以查看所有信息，默认health
          include: "*"
  ```

- 使用：访问 http://localhost/actuator



#### monitor

- 坐标

  ```xml
  <!-- spring未集成admin，可使用第三方依赖管理 -->
  <dependencyManagement>
      <dependencies>
          <dependency>
              <groupId>de.codecentric</groupId>
              <artifactId>spring-boot-admin-dependencies</artifactId>
              <version>2.5.4</version> <!-- 与springboot版本一致 -->
              <type>pom</type>
              <scope>import</scope>
          </dependency>
      </dependencies>
  </dependencyManagement>
  
  <dependencies>
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
      </dependency>
      <dependency>
          <groupId>de.codecentric</groupId>
          <artifactId>spring-boot-admin-starter-server</artifactId>
      </dependency>
  </dependencies>
  ```

- 启动类加注解**@EnableAdminServer**

#### server

- 依赖

  ```xml
  <dependency>
      <groupId>de.codecentric</groupId>
      <artifactId>spring-boot-admin-starter-client</artifactId>
  </dependency>
  ```

- 配置

  ```yaml
  spring:
    boot:
      admin:
        client:
          url: http://localhost:80 # 监控服务地址
  
  # actuator的配置
  ```

- 信息展示

  - yaml配置

    ```yaml
    info:
      author: eli
      appName: @project.artifactId@
    ```

  - bean

    ```java
    @Component
    public class InfoConfig implements InfoContributor {
        @Override
        public void contribute(Info.Builder builder) {
            builder.withDetail("now：", new Date());
        }
    }
    ```

- health展示：health下有一个组件down，该服务状态为down

  ```java
  @Component
  public class HealthConfig extends AbstractHealthIndicator {
      @Override
      protected void doHealthCheck(Health.Builder builder) throws Exception {
          builder.withDetail("myHealth", "value");
          builder.withException(new RuntimeException("hello"));
          builder.status(Status.UP); // builder.up();
      }
  }
  ```

- metrics展示：可在业务层执行注入MeterRegistry，来统计该业务调用的次数

  ```java
  @Service
  public class UserServiceImpl implements UserService {
      private Counter counter;
      public BookServiceImpl(MeterRegistry registry){
          counter = registry.counter("该业务执行次数");
          // counter.increment(); // 业务中调用该方法
      }
  }
  ```

  

#### 自定义端点

```java
@Component
@Endpoint(id = "pay", enableByDefault = true)
public class PayEndpoint {
    @ReadOperation // 读取端点时，调用该方法
    public String getPay() {
        return "你支付，我快乐";
    }
}
```









## 原理篇



### spring补充

#### bean 的加载方式

1. xml + \<bean/>

2. xml:context（组件扫描） + 注解（@Configuration、@Component、@Service、@Controller、@Repository、+@Bean）

3. 第三种

   - 配置类 + 扫描（@ComponentScan） + 注解（@Component...）

   - 工厂生产bean

     ```java
     public class OneClassFactoryBean implements FactoryBean<OneClass> {
         @Override
         public OneClass getObject() throws Exception { return new OneClass(); }
         
         // 指定bean类型
         @Override
         public Class<?> getObjectType() { return OneClass.class; }
         
         @Override
         public boolean isSingleton() { return false; }
     }
     ```

     ```java
     @Configuration
     public class SpringConfig {
         @Bean
         public OneClassFactoryBean oneClass() {
             return new OneClassFactoryBean();
         }
     }
     ```

   - **@Configuration 注解的 proxyBeanMethod 属性**（@Configuration+@Bean 和 @Component+@Bean 的区别）
     - ProxyBeanMethods = true（默认），表示使用配置类的代理对象
     - 使用代理对象时，通过配置类**调用方法时**获得对象时，先检查容器中有没有相同实体的对象，若有，则使用容器内的对象

4. @Import

   - @ImportResource(String[] files)：可导入外部配置
   
   - @Import(Class<?>[] classes)
   
     - 说明
          - 用@Import导入的bean，名字为该bean的类全路径限定名
          - 可以降低源代码与Spring技术的耦合度
     - 使用目标：classes中对象可以是哪些？
       - 可导入**实体类**从而创建bean
       - 可导入**配置类**
     - 如果导入的配置文件加载了一个和本配置文件id重复的bean，则后加载的bean覆盖前加载的bean
   
     ```java
     // 所有配置文件 都配置了名为dog的bean
     // 顺序：	2.xml -> 1.xml -> SpringConfig1 -> SpringConfig3 -> SpringConfig2
     
     @Configuration
     @ImportResource({"applicationContext1.xml","applicationContext2.xml"})
     @Import({SpringConfig2.class,SpringConfig3.class})
     public class SpringConfig1 {
         @Bean
         public DogFactroyBean dog(){
             return new DogFactroyBean();
         }
     }
     ```


5. 可以在上下文对象初始化完毕后，手动添加bean

   ```java
   public static void main(String[] args) {
       AnnotationConfigApplication ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
       
       // registerBean(String beanName,Class<?> class,construtorArgs:......)
       // 后添加的覆盖先添加的
       ctx.registerBean("cat", Cat.class);
       ctx.registerBean("cat", Cat.class);
       
       // register(Class<?> class)
       ctx.register(Dog.class);
   }
   ```

6. @Import导入ImportSelector接口实现：用于条件控制选择性加载bean

   ```java
   @Configuration
   @Import(MyImportSelector.class) // SpringConfig导入了MyImportSelector类
   public class SpringConfig {}
   ```

   ```java
   public class MyImportSelector implements ImportSelector {
       @Override
       public String[] selectImports(AnnotationMetadata metadata) {
           // 获取导入类是否有该注解
           boolean flag = metadata.hasAnnotation("注解的全类名");
   
           // 需要加载的bean，可以通过以上条件控制选择加载bean
           if (flag) return new String[]{"cn.langh.bean.Dog"};
           else return new String[0];
       }
   }
   ```

   - 参数：AnnotationMetadata metadata
     - 作用：存储了类的元数据（可用 metadata.xxx 获得信息）
       - 获得类名：`String getClassName()`
       - 是否有该注解：`boolean hasAnnotation(String str)`（str是注解的全路径限定名）
       - 获取导入类注解中的属性值：`Map<String, Object> getAnnotationAttributes(String str)`（str是注解的全路径限定名）
     - 目标（储存了哪个类的元数据？）：导入类（即谁导入MyImportSelector，就是谁，上面案例是SpirngConfig）
   - 返回值：需要加载的bean的全路径限定名的数组
   - **ImportSelector接口的selectImports方法导入的bean，和@Import导入的bean处理过程是一样的**

7. @Import导入ImportBeanDefinitionRegistrar实现：也是用于条件控制选择性加载bean（相比于方法6更精细，可以设置bean名称)

   ```java
   @Import(MyRegister.class)
   @Configuration
   public class SpringConfig {}
   ```

   ```java
   public class MyRegister implements ImportBeanDefinitionRegistrar {
       @Override
       public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
           
       }
       
       @Override
       public void registerBeanDefinitions(
           AnnotationMetadata importingClassMetadata, 
           BeanDefinitionRegistry registry, 
           BeanNameGenerator importBeanNameGenerator) {
           
       }
   }
   ```

   - 方法参数

     - AnnotationMetadata：与方法6中的参数一样，通过条件选择可用来控制加载bean

     - BeanDefinitionRegistry：保存了bean的信息，用来构建bean，也可用来获取bean的一些信息

       ```java
       BeanDefinition beanDefinition = BeanDefinitionBuilder
                                                 .rootBeanDefinition(Dog.class)
                                                 .getBeanDefinition();
       beanDefinition.setScope(boolean);
       beanDefinition.LazyInit(boolean);
       // ...
       
       registry.registerBeanDefinition("name", beanDefinition); // 构建bean
       ```

     - BeanNameGenerator：可以设置bean名称的生成器

8. @Import导入BeanDefinitionRegistryPostProcessor实现：

   - BeanFactoryPostProcessor：这个接口定义了一个方法，postProcessBeanFactory，这个方法会**在所有的BeanDefinition被加载到容器之后、但在Bean实例被创建之前**被调用。
   - BeanDefinitionRegistryPostProcessor：这个接口**继承自BeanFactoryPostProcessor接口**，并添加了一个新的方法，postProcessBeanDefinitionRegistry。这个方法会在**所有的BeanDefinition被加载到容器之后、bean实例创建之前**就被调用，这使得我们可以在容器的启动过程中修改或添加Bean的定义。
   
   ```java
   @Configuration
   @Import(MyPostProcessor.class)
   public class SpringConfig {}
   ```
   
   ```java
   public class MyPostProcessor implements BeanDefinitionRegistryPostProcessor {
       @Override
       public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry beanDefinitionRegistry) throws BeansException {
   
       }
   
       @Override
       public void postProcessBeanFactory(ConfigurableListableBeanFactory configurableListableBeanFactory) throws BeansException {
           // 获取名为"myBean"的Bean定义
           BeanDefinition beanDefinition = beanFactory.getBeanDefinition("myBean");
   
           // 修改"myBean"的属性值
           beanDefinition.getPropertyValues().add("myProperty", "newValue");
       }
   }
   ```
   





#### BeanDefinition 加载流程

- 读取配置：首先，Spring容器会读取配置信息，这些配置信息可以来自于XML文件、注解、Java配置类等。配置信息中包含了Bean的定义，包括Bean的类名、作用域、构造函数参数、属性值等。
- 解析配置：Spring容器会解析读取到的配置信息，创建对应的BeanDefinition对象。每个BeanDefinition对象包含了创建一个Bean所需的全部信息，其中包括Bean的属性和依赖关系。 
- 注册BeanDefinition：解析完成后，Spring容器会将BeanDefinition对象注册到BeanDefinitionRegistry中。BeanDefinitionRegistry是一个接口，它定义了管理BeanDefinition的各种方法。在默认的情况下，Spring容器使用DefaultListableBeanFactory作为BeanDefinitionRegistry的实现。
- 后处理BeanDefinition：注册完成后，Spring容器会调用BeanDefinitionRegistryPostProcessor接口的方法，对BeanDefinition进行后处理。这是一个扩展点，你可以实现这个接口，对BeanDefinition进行自定义的修改。



#### 后三种导入方式流程

- @Import导入ImportSelector接口实现：（解析bean定义阶段）

  ImportSelector接口的selectImports方法返回的是需要导入的配置类的全类名，这些类会被Spring容器处理，**就像它们被@Import注解直接导入一样**（不同的是比@Import更精细，因为它可以通过AnnotationMetadata对象，控制导入bean）。如果返回值对应的这些类是配置类（即，它们有@Configuration注解），那么它们中的@Bean方法会被调用，以创建并初始化Bean。



- @Import导入ImportBeanDefinitionRegister实现：（解析bean定义阶段）

  相比于上一种方针，ImportBeanDefinitionRegistrar接口的registerBeanDefinitions方法**允许你直接操作BeanDefinitionRegistry，以编程的方式注册BeanDefinition**。你可以创建一个BeanDefinition对象，设置它的属性，然后将它注册到BeanDefinitionRegistry中。这种方式可以处理更复杂的场景，比如需要动态决定注册哪些Bean，或者需要根据某些条件来配置Bean的属性。

  这个接口通常与@Import注解一起使用，允许我们在处理@Configuration类时，手动注册额外的bean定义。这个接口定义了一个方法：registerBeanDefinitions，**在所有@Configuration类被处理之后执行**。



- @Import导入BeanDefinitionRegistryPostProcessor（以下简称BDRPP）实现：（bean定义后处理阶段）

  postProcessBeanDefinitionRegistry（以下简称registry方法） 和 postProcessBeanFactory（以下简称factory）方法 方法区别

  - registry方法在该接口中定义，factory方法是该接口继承BeanFactoryPostProcessor（以下简称BFPP）接口获得的。
  - registry侧重点是添加自定义BeanDefinition（也可以修改），factory方法是修改bean定义
  - registry方法先于factory方法执行，这样就可以在registry方法中做**最后的添加**，在factory方法中，做**最后的调整**（包括修改registry中新增的beanDefinition）

  ```java
  public static void invokeBeanFactoryPostProcessors(ConfigurableListableBeanFactory beanFactory, List<BeanFactoryPostProcessor> beanFactoryPostProcessors) {
      
          if (beanFactory instanceof BeanDefinitionRegistry) {
              // register方法 和 factory方法中的参数是同一个对象
              BeanDefinitionRegistry registry = (BeanDefinitionRegistry) beanFactory;
              List<BeanFactoryPostProcessor> regularPostProcessors = new ArrayList<>();
              List<BeanDefinitionRegistryPostProcessor> registryProcessors = new ArrayList<>();
  
              for (BeanFactoryPostProcessor postProcessor : beanFactoryPostProcessors) {
                  if (postProcessor instanceof BeanDefinitionRegistryPostProcessor) {
                      BeanDefinitionRegistryPostProcessor registryProcessor =
                              (BeanDefinitionRegistryPostProcessor) postProcessor;
                      //1、 此处执行的是伪装成 BFPP 的 BDRPP  的  registry 方法
                      registryProcessor.postProcessBeanDefinitionRegistry(registry);
                      registryProcessors.add(registryProcessor);
                  }
                  else {
                      //2、如果只是 BFPP 但不是 BDRPP 保存起来
                      regularPostProcessors.add(postProcessor);
                  }
              }
  
              // 开始执行没有伪装成 BFPP 的 BDRPP register方法
              // 原理：调用 beanFactory.getBeanNamesForType(BDRPP.class, true, false) 获得对象
              // 在依次判断是否实现了 PriorityOrdered、Ordered 接口
  
              // 首先执行实现了 PriorityOrderd 接口，优先执行
              ...;
              registryProcessors.addAll(实现该接口的对象们);
              invokeBeanDefinitionRegistryPostProcessors(实现该接口的对象们, registry);
  
              // 再获得实现了 Ordered 接口，执行
              ...;
              registryProcessors.addAll(实现该接口的对象们);
              invokeBeanDefinitionRegistryPostProcessors(实现该接口的对象们, registry);
  
              // 最后执行其他 BDRPP 对象
              ...;
              registryProcessors.addAll(实现该接口的对象们);
              invokeBeanDefinitionRegistryPostProcessors(实现该接口的对象们, registry);
  
              // 执行所有 BDRPP 对象的 factory方法
              invokeBeanFactoryPostProcessors(registryProcessors, beanFactory);
              // 执行所有 BFPP 但不是 BDRPP 对象的 factory方法
              invokeBeanFactoryPostProcessors(regularPostProcessors, beanFactory);
          }else{
              // 仅执行 factory方法
              invokeBeanFactoryPostProcessors(beanFactoryPostProcessors, beanFactory);
          }
      }
  ```

  





#### bean 的加载控制

- 编程式：bean加载方式中的方式6、7、8均可用于编程式控制bean加载，此处以方式6为例

  ```java
  public class MyImportSelector implements ImportSelector{
      @Override
      public String[] selectImport(AnnotationMetadata metadata){
          try{
              Class<?> class = Class.forname("cn.langh.bean.Mouse");
              if(class != null){
                  return new String[]{"cn.langh.bean.Cat"};
              }
          }catch(ClassNotFoundException e){
              return new String[0];
          }
          return null;
      }
  }
  ```

- 注解式

  - springboot提供的注解，定义在bean上或bean对应的类上

    - @ConditionalOnClass(name = "全类名")：存在这个类时
    - @ConditionalMissingClass(“全类名”)：不存在这个类时
    - @ConditionalOnBean(......)：存在这个bean时
    - @ConditionalMissingBean(......)：不存在这个bean时
    - @ConditionalOnNotWebApplication：不是web项目时
    - ......
  
  ```JAVA
  package org.springframework.boot.autoconfigure.data.redis;
  
  import ......
  import org.springframework.data.redis.core.RedisOperations;
  
  @Configuration(proxyBeanMethods=false) 
  // 是否加载该配置类取决于是否存在该类，是否存在该类取决于是否导入redis依赖
  @ConditionalOnClass({RedisOperations.class})
  @EnableConfigurationProperties({RedisProperties.class})
  @Import({LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class})
  public class RedisAutoConfiguration {
      
      @Bean
      // // 如果没有名叫redisTemplate的bean，且有且只有一个连接工厂对象，才会新注入一个redisTemplate
      @ConditionalOnMissingBean(name = {"redisTemplate"})
      @ConditionalOnSingleCandidate(RedisConnectionFactory.class)
      public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
          RedisTemplate<Object, Object> template = new RedisTemplate();
          template.setConnectionFactory(redisConnectionFactory);
          return template;
      }
  
      @Bean
      @ConditionalOnMissingBean
      @ConditionalOnSingleCandidate(RedisConnectionFactory.class)
      public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {
          return new StringRedisTemplate(redisConnectionFactory);
      }
  }
  ```
  
  






#### bean 依赖的属性配置

- 要求：从yml配置文件中读取配置。如果yml配置了，使用yml中的属性；如果yml没有配置，使用默认的属性

- 实例：省略了Cat、Mouse类的创建，它们中都定义了name、age属性，且都添加了Getter、Setter、构造方法等

  - yml配置文件

    ```yaml
    cartoon: 
      cat: 
        name: tom
        age: 4
      mouse: 
        name: jerry
        age: 3
    ```

  - 配置属性类：读取配置，**引用属性最好不要是内部类**

    ```java
    @Data // setter是必要的
    @Component
    @ConfigurationProperties(prefix = "cartoon")
    public class CartoonProperties {
        private Mouse mouse;
        private Cat cat;
    }
    ```

  - 校验与使用

    ```java
    @Component
    public class Cartoon {
        private Mouse mouse;
        private Cat cat;
    
        public Cartoon(CartoonProperties cartoonProperties) {
            // 此处进行校验，看cartoonPrope是否配置了值
            // 若配置，使用配置值，若为null，使用默认值
        }
    
        public void show() {
            System.out.println("cat: " + this.cat);
            System.out.println("mouse: " + this.mouse);
        }
    }
    ```
  
  - Spring Boot 2.2 以下版本需要在启动类上添加@EnableConfigurationProperties(value = CartoonProperties.class)注解，来标注配置类。2.2以上版本，如果配置类上有@Component，并能被扫描到，则不需要该注解




### 自动配置原理

- @SpringBootApplication

  - @SpringBootConfiguration

    - @Indexed
    - @Configuration
      - @Component

  - @EnableAutoConfiguration

    - **@Import({AutoConfigurationImportSelector.class})**
    - @AutoConfigurationPackage
      - **@Import({AutoConfigurationPackages.Registrar.class})**

  - @ComponentScan

    ```java
    @ComponentScan(excludeFilters = { // 排除掉一些扫描到，但不需要加载的bean
        @Filter(
            type = FilterType.CUSTOM,
            classes = {TypeExcludeFilter.class}), 
        @Filter(
            type = FilterType.CUSTOM,
            classes = {AutoConfigurationExcludeFilter.class})
        }
    )
    ```

- AutoConfigurationPackages.Registrar：**实现了ImportBeanDefinitionRegistrar**（查看bean的加载方式7）

  ```java
  static class Registrar implements ImportBeanDefinitionRegistrar, DeterminableImports {
      public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
          // println(arr) = ["cn.eli"] 即获得的启动类所在的包，用于扫描bean
          String[] arr = 
              (String[])(new PackageImports(metadata)).getPackageNames().toArray(new String[0]);
          
          AutoConfigurationPackages.register(registry, arr);
      }
      
      // ......
  }
  ```

  ```java
  public abstract class AutoConfigurationPackages {
      private static final String BEAN = AutoConfigurationPackages.class.getName();
      
      public static void register(BeanDefinitionRegistry registry, String... packageNames) {
          if (registry.containsBeanDefinition(BEAN)) {
              addBasePackages(registry.getBeanDefinition(BEAN), packageNames);
          } else {
              RootBeanDefinition beanDefinition = new RootBeanDefinition(BasePackages.class);
              beanDefinition.setRole(2);
              addBasePackages(beanDefinition, packageNames);
              registry.registerBeanDefinition(BEAN, beanDefinition);
          }
      }
      
      // BasePackages 类的作用是存储和管理通过 @EnableAutoConfiguration 注解指定的包名，并在需要时提供这些包名。
      static final class BasePackages { ... }
      
      // 为 BasePackages 这个类对应的 beanDefination 添加 基础包名（如：["cn.eli"]）
      private static void addBasePackages(BeanDefinition defination, String[] packages) { ... }
      
      static class Registrar implements ImportBeanDefinitionRegistrar, DeterminableImports { ... }
  }
  ```

  - 作用：设置当前所在的包为扫描包，后续要针对当前的包进行扫描

- AutoConfigurationImportSelector

  ```java
  public class AutoConfigurationImportSelector implements DeferredImportSelector, BeanClassLoaderAware, ResourceLoaderAware, BeanFactoryAware, EnvironmentAware, Ordered {
      // ...
  }
  ```

  - `XxxAware`：这是一种回调模式，当一个 Bean 实现了 Aware 接口后，Spring 在创建 Bean 的过程中，会检测到这个 Bean 是否实现了 Aware 接口，如果实现了，Spring 就会调用set方法，将相应的依赖注入到 Bean 中。bean对应的类实现该接口**（即实现XxxAware，就可获得对应的Xxx）**

    ```java
    @component
    public class OneClass implements ApplicationContextAware {
        private ApplicationContext appContext;
        
        @Override
        public void setApplicationContext(ApplicationContext context) throws BeansException {
            this.appContext = context;
        }
    }
    ```

  - `Ordered`

    - Ordered 是 Spring 框架中的一个接口，它用于表示一个对象可以被排序。这个接口只有一个方法 getOrder()，返回一个整数值，这个值就是对象的排序顺序。
    - 在 Spring 中，许多组件（如 BeanPostProcessor，HandlerInterceptor，OrderedTypeProvider 等）都可以实现 Ordered 接口，以便在需要排序这些组件时，Spring 可以根据 getOrder() 方法返回的值来确定它们的顺序。
    - 例如，如果你有多个 BeanPostProcessor，你可以通过实现 Ordered 接口并返回不同的排序值，来控制它们的执行顺序。返回的数值越小，优先级越高。 
    - 之所以添加这个功能，是应为
      - 一方面：一个bean可能依赖于另一个bean
      - 另一方面：控制加载bean时，可以为控制加载bean的处理器进行加载排序，让一个bean可以有序被多个处理器合理控制加载

  - `DeferredImportSelector`：继承自ImportSelector（查看bean的加载方式6）

    ```java
    public interface DeferredImportSelector extends ImportSelector {
        @Nullable
        default Class<? extends Group> getImportGroup() {
            return null;
        }
        
    
        public interface Group {
            void process(AnnotationMetadata metadata, DeferredImportSelector selector);
    
            Iterable<Entry> selectImports();
    
            public static class Entry { ... }
        }
    }
    ```

    ```java
    public class AutoConfigurationImportSelector implements DeferredImportSelector, BeanClassLoaderAware, ResourceLoaderAware, BeanFactoryAware, EnvironmentAware, Ordered {
        
        protected AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
            if (!this.isEnabled(annotationMetadata)) {
                return EMPTY_ENTRY;
            } else {
                // 获取 @EnableAutoConfiguration(exclude={clazz}, excludeName={str}) 注解中的排除项
                AnnotationAttributes attributes = this.getAttributes(annotationMetadata); // attributes: size = 2
                
                // 重点：获取所有的自动配置项，即读取 spring.factories 中的 autoconfigure
                List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);
                
                configurations = this.removeDuplicates(configurations);
                Set<String> exclusions = this.getExclusions(annotationMetadata, attributes);
                this.checkExcludedClasses(configurations, exclusions);
                configurations.removeAll(exclusions);
                configurations = this.getConfigurationClassFilter().filter(configurations);
                this.fireAutoConfigurationImportEvents(configurations, exclusions);
                return new AutoConfigurationEntry(configurations, exclusions);
            }
        }
        
        
        private static class AutoConfigurationGroup implements DeferredImportSelector.Group, BeanClassLoaderAware, BeanFactoryAware, ResourceLoaderAware {
            
            public void process(AnnotationMetadata annotationMetadata, DeferredImportSelector deferredImportSelector) {
                // 调用到 getAutoConfigurationEntry
            }
            
            public Iterable<DeferredImportSelector.Group.Entry> selectImports() { ... }
        }
    }
    ```

    - 说明
      - AutoConfigurationGroup 是 AutoConfigurationImportSelector 的一个私有静态内部类，它实现了 DeferredImportSelector.Group 接口
      - 这个类的主要职责是收集和处理自动配置类的导入。它维护了一个 entries 映射，用于存储自动配置类的元数据，以及一个 autoConfigurationEntries 列表，用于存储自动配置条目。
      - 在 process 方法中，它会获取给定注解元数据的自动配置条目，并将其添加到 autoConfigurationEntries 列表中。同时，它还会将自动配置条目中的配置类添加到 entries 映射中。
      - 在 selectImports 方法中，它会从 autoConfigurationEntries 列表中获取所有的自动配置类，并排除那些被排除的配置类。然后，它会对剩余的配置类进行排序，并返回一个包含这些配置类的DeferredImportSelector.Group.Entry 的列表。
      - 总的来说，AutoConfigurationGroup 类的作用是收集、处理和选择需要导入的自动配置类。

  - 作用：自动导入并加载其他用到的设置集





### 自动配置

- spring.fatories

  ```properties
  # Auto Configure 自动加载项
  org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  org.springframework.boot.autoconfigure.data.r2dbc.R2dbcDataAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.r2dbc.R2dbcRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.r2dbc.R2dbcTransactionManagerAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.redis.RedisReactiveAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.redis.RedisRepositoriesAutoConfiguration,\
  ......
  ```

- 以RedisAutoConfiguration为例

  - 如果导入依赖，依赖中就存在`org.springframework.data.redis.core.RedisOperations`这个类

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis</artifactId>
    </dependency>
    ```

  - 自动配置类

    ```java
    // 这个类在springboot基础包中，并不再redis包中
    package org.springframework.boot.autoconfigure.data.redis;
    
    import ......
    import org.springframework.data.redis.core.RedisOperations;
    
    // 反编译时，可能产生 @AutoConfiguration 这个注解，实际上springboot没有这个注解。
    @Configuration(proxyBeanMethods=false) 
    // 是否加载该配置类取决于是否存在该类，是否存在该类取决于是否导入redis依赖
    @ConditionalOnClass({RedisOperations.class})
    @EnableConfigurationProperties({RedisProperties.class})
    // 此处导入两种实现 的配置类，配置类中有对应的bean
    @Import({LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class})
    public class RedisAutoConfiguration {
        
        @Bean
        // 如果没有名叫redisTemplate的bean，且有且只有一个连接工厂对象，才会新注入一个redisTemplate
        @ConditionalOnMissingBean(name = {"redisTemplate"})
        @ConditionalOnSingleCandidate(RedisConnectionFactory.class)
        public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
            RedisTemplate<Object, Object> template = new RedisTemplate();
            template.setConnectionFactory(redisConnectionFactory);
            return template;
        }
    
        @Bean
        @ConditionalOnMissingBean
        @ConditionalOnSingleCandidate(RedisConnectionFactory.class)
        public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {
            return new StringRedisTemplate(redisConnectionFactory);
        }
    }
    ```

  - 配置类：@EnableConfigurationProperties({RedisProperties.class})

  




### 自定义自动配置

- 项目根目录下创建文件结构：META-INF/spring.factories

- spring.factories 内容

  ```properties
  org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  cn.eli.config.MyConfiguration,\
  ......
  ```

- 排除部分自动配置项

  - yml配置文件中排除

    - 排除一项

      ```yml
      spring:
        autoconfigure:
          exclude: org.springframework.boot.autoconfigure.task.TaskExecutionAutoConfiguration
      ```

    - 排除多项

      ```yml
      spring:
        autoconfigure:
          exclude: 
            - org....
            - com....
      ```

  - 注解配置：
  
    - @EnableAutoConfiguration 中的 exclude() 和 excludeName() 可以配置排除依赖项
    - @SpringBootApplication 中的 exclude() 和 excludeName() 继承自 @EnableAutoConfiguration，也可以配置
  
- 变更自动配置：去掉tomcat的自动配置，使用jetty的自动配置

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
      <exclusions>
          <exclusion>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-tomcat</artifactId>
          </exclusion>
      </exclusions>
  </dependency>
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-jetty</artifactId>
  </dependency>
  ```

  - 补充：jetty 和 tomcat 的 starter 依赖都注入了哪些相同的bean，对应实现了什么接口
    - **ServletWebServerFactory**：这是一个工厂接口，用于创建 ServletWebServer 实例。对应的 Jakarta Servlet API 接口是 javax.servlet.ServletContext。
    - **ServletWebServerApplicationContext**：这是 Spring 的 ApplicationContext，用于管理 Spring 应用中的 Bean。它包含了一个内嵌的 Servlet 容器，可以是 Tomcat 或 Jetty。对应的 Jakarta Servlet API 接口是 javax.servlet.ServletContext。
    - **DispatcherServlet**：这是 Spring MVC 的核心 Servlet，用于处理所有的 HTTP 请求。对应的 Jakarta Servlet API 接口是 javax.servlet.http.HttpServlet。
    - **ServletRegistrationBean、FilterRegistrationBean、ServletListenerRegistrationBean**：这些 Bean 用于在 Servlet 容器中注册 Servlet、Filter 和 Listener。对应的 Jakarta Servlet API 接口分别是 javax.servlet.Servlet、javax.servlet.Filter 和 javax.servlet.ServletContextListener。
    - **ErrorPageRegistrarBeanPostProcessor**：这个 Bean 用于处理错误页面的注册。对应的 Jakarta Servlet API 接口是 javax.servlet.ServletContext。
    - **ServletWebServerFactoryConfiguration**：这个 Bean 用于配置 Servlet 容器。对应的 Jakarta Servlet API 接口是 javax.servlet.ServletContext。





### 自定义starter

- 项目练习
  - 数据记录位置：Map/Redis
  - 功能触发位置：每次web请求（拦截器）
  - 业务参数（配置项）
    - 输出频率：默认10秒一次
    - 数据特征：累计数据 / 阶段数据，默认累计数据
    - 输出格式：详细输出 / 极简输出
  
- 配置属性类的使用

  - 定义配置属性类

    ```java
    @Data
    @Component("ipCounterProperties")
    @ConfigurationProperties(prefix = "counter.ip")
    @ToString
    public class IpCounterProperties {
        public enum IpCounterScope {
            TOTAL, STAGE
        }
        public enum ExecutorStyle {
            SIMPLE, DETAIL;
        }
    
        /**
         * 配置计数器
         */
        private RecorderProperties ipCounterProperties = new RecorderProperties();
        /**
         * 配置执行器
         */
        private ExecutorProperties[] executorProperties = null;
    }
    ```

    ```java
    @Data
    @ToString
    // 这个类不需要注册成bean，因此并不需要加上下面注销，但加上该注解，可以在编译时将该类字段添加到json文件中，从而在写yml配置时，ide可以提供提示
    // 如不需要注册成bean，只需要不使用 @EnableConfigurationProperties、@Import 导入即可
    @ConfigurationProperties(prefix = "counter.ip.executor-properties")
    public class ExecutorProperties {
        /**
         * 输出特征：详细输出/简单输出
         */
        private IpCounterProperties.ExecutorStyle style = IpCounterProperties.ExecutorStyle.DETAIL;
        /**
         * 执行器的全限定名
         */
        private String executorClassName = "space.relax.executor.StandedOutIpCounterExecutor";
    }
    ```
    
  - 配置类
  
    ```java
    // @EnableConfigurationProperties(IpCounterProperties.class)
    @Import({IpCounterProperties.class, InterceptorConfiguration.class, IpCounterInterceptor.class,
            IpCounterPostProcessor.class, ExecutorTask.class})
    @Slf4j
    @Configuration
    public class IpCounterConfiguration {
        @Bean
        ...
    }
    ```
  
    - `@EnableConfigurationProperties(IpCounterProperties.class)`：这样导入配置属性类的bean，有自己的一套命名规则
    - 用`@Import`导入IpCounterProperties，可以保证在后导入的bean之前加载
    - `@Import`导入IpCounterProperties这个bean，并在该类上使用`@Component("ipCounterProperties")`，这样就可以使用自定义的命名规则
  
  - ide 对配置文件的提示
  
    - 导入依赖
  
      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-configuration-processor</artifactId>
          <version>${spring-boot.version}</version>
          <optional>true</optional>
      </dependency>
      ```
  
    - maven install：会根据加了`@ConfigurationProperties(prefix="xxx")`注解的类，生成一个名叫spring-configuration-metadata.json的文件，idea的提示来源于这个文件
  
    - 添加文档注释，Idea可以在写配置，进行提示
  
  - spring-configuration-metadata.json（补充hints中的内容，进一步完善提示，最后将最终的文件放在starter中与spring.factories文件相同的目录即可）
  
    ```json
    {
      "groups": [
        {
          "name": "counter.ip",
          "type": "space.relax.properties.IpCounterProperties",
          "sourceType": "space.relax.properties.IpCounterProperties"
        },
        {
          "name": "counter.ip.executor-properties",
          "type": "space.relax.properties.ExecutorProperties",
          "sourceType": "space.relax.properties.ExecutorProperties"
        }, ......
      ],
      "properties": [
        {
          "name": "counter.ip.executor-properties",
          "type": "space.relax.properties.ExecutorProperties[]",
          "description": "配置执行器",
          "sourceType": "space.relax.properties.IpCounterProperties"
        },
        {
          "name": "counter.ip.executor-properties.style",
          "type": "space.relax.properties.IpCounterProperties$ExecutorStyle",
          "description": "输出特征：详细输出\/简单输出",
          "sourceType": "space.relax.properties.ExecutorProperties"
        }, ......
      ],
      "hints": [
        {
          "name": "counter.ip.executor-properties.style",
          "values": [
            {
              "value": "detail",
              "description": "详细输出"
            },
            {
              "value": "simple",
              "description": "简单输出"
            }
          ]
        }, ......
      ]
    }
    ```







### 其他介绍

- SpringBoot的启动流程
  - 初始化各种属性，加载成对象
    - 读取环境属性（Environment）
    - 系统配置（spring.factories）
    - 参数（Aruguments、application.properties）
  - 创建spring容器对象ApplicationContext，加载各种配置
  - 在容器创建前，通过监听器机制，应对不同阶段加载数据、更新数据的需求
  - 容器初始化过程中追加各种功能，例如统计时间、输出日志
  
- BeanPostProcessor接口

  ```java
  public interface BeanPostProcessor {
      @Nullable
      default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
          return bean;
      }
  
      @Nullable
      default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
          return bean;
      }
  }
  ```

  - postProcessBeforeInitialization()：这个方法在Bean的初始化方法（如@PostConstruct注解的方法，或者afterPropertiesSet方法，或者自定义的初始化方法）执行之前被调用。你可以在这个方法中对Bean进行一些处理。
  - postProcessAfterInitialization()：这个方法在Bean的初始化方法执行之后被调用。你可以在这个方法中对Bean进行一些后处理。









# SpringCloud

![springcloud](D:\picture\typora\java\springcloud.png)



## 实用篇

微服务的三大属性：可用性（Availability）、一致性（Consistency）和分区容错性（Partition tolerance）是分布式系统中的三个关键属性，通常被称为CAP定理。

- 可用性（Availability）：系统在正常和失败状态下都能对用户的每个请求给出响应。响应可以是正确的，也可以是错误的，但不能没有响应。在分布式系统中，可用性通常通过冗余和负载均衡等技术来实现。
- 一致性（Consistency）：所有节点在同一时间点看到的数据是一致的。在分布式系统中，一致性通常通过复制和一致性协议（如两阶段提交、Paxos、Raft等）来实现。
- 分区容错性（Partition tolerance）：系统在网络分区（即，系统的一部分节点无法与其他节点通信）的情况下仍能继续运行。在分布式系统中，容错性通常通过复制和冗余等技术来实现。

CAP定理指出，对于任何分布式系统，这三个属性不能同时满足。也就是说，你只能选择满足其中的两个。例如，你可以选择满足一致性和可用性，但这样的系统在网络分区的情况下可能无法正常工作。或者，你可以选择满足一致性和容错性，但这样的系统在某些情况下可能无法对用户的请求给出响应。

### 注册中心

#### 引入

- 注册 RestTemplate

  ```java
  // RestTemplete可以发送http请求
  @Bean
  public RestTemplate restTemplate(){
      return new RestTemplate();
  }
  ```

- 微服务的服务器远程调用其他服务器的接口

  ```java
  // 这是一个订单的业务接口
  @Service
  public class OrderService {
  	@Autowired
      private OrderMapper orderMapper;
      // 基于RestTemplete发送http请求
      @Autowired
      private RestTemplete restTemplete;
      
      public Order queryOrderById(Long orderId){
          Order order = orderMapper.selectById(orderId);
          String url = "http://localhost:8081/user/"+order.getUserId();
          // 发送http的GET请求，实现远程调用
          User user = restTemplate.getForObject(url, User.class);
          
          order.setUser(user);
          return order;
      }
  }
  ```

  

#### Eureka

- 在Eureka架构中，微服务角色有两类：

  - EurekaServer：服务端、注册中心
    - 记录服务信息
    - 心跳监控
  - EurekaClient：客户端
    - provider：服务提供者
      - 注册自己的信息到EurekaService
      - 每隔30秒向EurekaService发送心跳
    - consumer：服务消费者
      - 根据服务名称从EurekaService中拉去服务列表
      - 基于服务列表做负载均衡，选中一个微服务发起远程调用

- Eureka工作流程

  ![eureka工作流程](D:\picture\typora\后端\springcloud\eureka工作流程.png)

- 搭建Eureka服务

  - 父工程设置依赖管理

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR10</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    ```
    
  - 子Eureka服务引依赖
  
    ```xml
    <dependency>
    	<groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    </dependency>
    ```
  
  - 编写启动类，添加**@EnableEurekaServer**注解
  
  - 添加application.yml文件，编写注册配置
  
    ```yml
    server:
      port: 10086
    # 微服务的名称
    spring:
     application:
      name: eurekaserver
    # 服务地址
    eureka:
     client:
      service-url:
       defaultZone: http://127.0.0.1:10086/eureka/
    ```
  


- 服务注册

  - 依赖

    ```xml
    <dependency>
    	<groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    ```

  - 配置地址

    ```yml
    spring:
     application:
      name: userservice
    eureka:
     client:
      service-url:
       defaultZone: http://127.0.0.1:10086/eureka/
    ```

- 服务发现

  - 修改OrderService的代码，修改访问的url路径，用服务器代替ip、端口

    ```java
    String url = "http://userservice/user/"+order.getUserId();
    ```

  - 在order-service项目的RestTemplate的注册bean方法上添加负载均衡注解**@LoadBalanced**

    ```java
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
    ```

- 自我保护机制

  - 自我保护模式中，eureka会保存服务注册表中的信息，不再注销任何服务实例（当服务不再向eureka发送心跳时，eureka注册中心默认会保留信息90秒）

  - 禁止自我保护、设置保留时间

    ```yml
    eureka:
     server:
      enable-self-preservation: false
      eviction-interval-timer-in-ms: 2000
    ```

  - 心跳

    ```yaml
    eureka:
     instance:
      instance-id: customer
      # 访问路径时可以显示ip
      prefer-ip-address: true
      # Eureka客户端向服务端发送心跳的时间间隔，单位为秒（默认30秒）
      lease-renewal-interval-in-seconds: 1
      # Eureka服务端在收到最后一次心跳后等待时间上线，单位为秒（默认90秒），超时将提出
      lease-expiration-duration-in-seconds: 2
    ```



#### zookeeper



#### Nacos

##### 服务治理

- 使用步骤

  - 启动nacos的注册中心

    ```cmd
    startup.cmd -m standalone
    ```

  - 在父工程中添加spring-cloud-alibaba的管理依赖

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.2.5.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    ```

  - 添加nacos的客户端依赖

    ```xml
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        <version>2.2.5.RELEASE</version>
    </dependency>
    ```

  - 配置application.yml中的nacos地址

    ```yml
    spring:
     application:
      name: customerService
     cloud:
      nacos:
       server-addr: localhost:8848
    ```

  - 注：eureka对注册的服务的名称大小写不敏感，nacos对大小写敏感

- 服务分级存储模型

  - 一级是服务，例如customerService

  - 二级是集群，例如合肥或上海

    ```yml
    spring:
     cloud:
      nacos:
       server-addr: localhost:8848
       discovery:
        cluster-name: HF # 集群名称
    ```

  - 三级是实例

- 根据集群负载均衡（在本地集群中采用随机策略，即服务消费者**优先**访问和自己同一集群的提供者，没有则会访问其他集群）

  - 服务的提供者和消费者都设置集群

  - 在服务消费者的application.yml中配置

    ```yml
    userService: # 服务提供者的服务名
     ribbon:
      NFLoadBalancerRuleClassName: com.alibaba.cloud.nacos.ribbon.NacosRule
    ```

- 权重设置：在nacos的网页控制台配置，一般配置值在区间[0, 1]。在更新维护项目时，可以先将权重设置为0，在停机升级

- 环境隔离（dev、test、public等）

  - 不同环境中的服务无法相互访问

  - 先在网页控制台左侧导航栏的【命名空间】中创建命名空间，默认都在public中

  - 添加服务到该命名空间

    ```yml
    spring:
     cloud:
      nacos:
       server-addr: localhost:8848
       discovery:
        cluster-name: HF # 集群名称
        namespace: 123bc2f8-b2f1..... # 此处填写不是名称，而是id名
    ```

- 配置非临时实例

  - 临时实例每个一段时间会向nacos发送一次心跳，心跳停止时，nacos会直接移出该服务

  - 非临时实例是由nacos主动询问，服务停止时，会标记健康状态为false，不会移出该服务（一般不使用，对服务器压力大）

  - application.yml

    ```yml
    spring:
     cloud:
      nacos:
       server-addr: localhost:8848
       discovery:
        cluster-name: HF # 集群名称
        namespace: namespace的id
        ephemeral: false # 是否是临时实例
    ```


- 与Eureka的区别
  - 共同点
    - 都支持服务注册和服务拉取
    - 都支持服务提供者心跳方式做健康检测
  - 区别
    - Nacos支持服务端主动检测提供者状态：临时实例采用心跳模式，非临时实例采用主动检测模式
    - 临时实例心跳不正常会被剔除，非临时实例则不会被剔除
    - Nacos支持服务列表变更的消息推送模式，服务列表更新更及时
    - Nacos集群默认采用AP方式（可用性、分区容错），当集群中存在非临时实例时，采用CP模式（一致性、分区容错）；Eureka采用AP方式



##### 配置中心

- 统一配置管理

  - 在nacos的网页控制台左侧导航栏中的【配置管理】新建配置

    - Data Id：【服务器名】-【环境(如dev/test)】.【后缀名(如yml)】
    - Group：分组
    - 配置格式及内容

  - 读取nacos配置文件

    - 项目启动后会先读取nacos中的配置文件，再读取application.yml中的配置，因此需要在bootstrap.yml中配置nacos的地址信息，才能拿到nacos的配置文件信息

    - 引入Nacos的配置管理客户端依赖

      ```xml
      <dependency>
          <groupId>com.alibaba.cloud</groupId>
          <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
      </dependency>
      ```

    - 在customerService中的resource目录添加一个bootstrap.yml文件，这个文件是引导文件，优先级高于application.yml，微服务启动时，会读取[spring.application.name]-[spring.profiles.active].yaml，作为文件名，向Nacos查找文件

      ```yml
      spring:
       application:
        name: customerService # 服务名称
       profiles:
        active: dev # 开发环境
       cloud:
        nacos:
         server-addr: localhost:8848 # Nacos地址
         config:
          file-extension: yaml # 文件后缀名
      ```
  
- 配置热更新：你写的应用应该支持对热更新的配置做出响应（如在while(true)的循环体开始前赋值还是在循环体中赋值）

  - 方式一：在用@Value读取nacos配置文件的类上加上@RefreshScope

    ```java
    @RefreshScope
    public class Test {
        @Value("${pattern.dateformat}")
        private String dateformat;
    }
    ```

  - 方式二：使用ConfigurationProperties自动刷新

    ```java
    @Component
    @Data
    @ConfigurationProperties(prefix="pattern")
    public class PatternProperties{
        private String dateformat;
    }
    ```

- 多环境的配置共享

  - 原理：微服务启动时，会读取[spring.application.name]-[spring.profiles.active].yaml，作为文件名

  - 步骤：

    - 新建配置，命名为[spring.application.name].yaml如customeService.yaml

    - 读取nacos文件

      ```yml
      spring:
       application:
        name: customerService # 服务名称
       cloud:
        nacos:
         server-addr: localhost:8848 # Nacos地址
         config:
          file-extension: yaml # 文件后缀名
      ```

  - 配置文件优先级：服务名-环境.yaml > 服务名.yaml > 本地配置

- Nacos集群





### 负载均衡

#### Ribbon

- 负载均衡原理

  ![Ribbon](D:\picture\typora\java\Ribbon.png)

  - 服务器发送 http://customerService/customer/1
  - Ribbon负载均衡拦截此次请求
  - Ribbon负载均衡向eureka-server拉去customerService服务列表
  - 获得服务列表后，进行负载均衡，并取得服务器

- 负载均衡策略

  - 原理：负载均衡的规则是一个叫做IRule的接口来定义的，每个子接口都是一种规则

    ![IRule](D:\picture\typora\java\IRule.png)

  - 负载均衡策略

    - RandomRule：随机选择一个可用的服务器
    - RoundRobinRule：简单轮询服务列表来选择服务器。它是Ribbon默认的负载均衡规则
    - WeightedResponseTimeRule：为每一个服务器赋予一个权重值。服务器响应时间越长，这个服务器的权重就越小。这个规则会随机选择服务器，权重值会影响服务器的选择
    - ZoneAvoidanceRule：在指定区域中以可用的服务器为基础进行服务器的选择，而后再对Zone内的多个服务做轮询。

  - 选择策略

    - 方式一：注册一个IRule接口的bean，放在@SpringBootApplication扫描的的地方，返回对应的实现类

      ```java
      @Bean
      public IRule randomRule(){
          return new RandomRule();
      }
      ```

    - 方式二：bean放在@SpringBootApplication扫描不到的地方，用@RibbonClient注解

      ```java
      @SpringBootApplication
      @RibbonClient(name="customerService", configuration=MyselfRule.class)
      public class App {
          public static void main(String[] args) {
              SpringApplication.run(App.class, args);
          }
      }
      ```
      
    - 方式二：在order-service（服务消费者）的application.yml文件中，添加新的配置
    
      ```yml
      userservice:
       ribbon:
        NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule
      ```

- 加载策略

  - Ribbon默认是采用懒加载，即第一次访问时才去创建LoadBalanceClient，第一次请求时间很长

  - 而饥饿加载则会在项目启动时创建，降低第一次访问的耗时

    ```yml
    ribbon:
     eager-load: # 饥饿加载
      enabled: true # 开启
      clients: customerService # 指定对customerService这个服务器饥饿加载
    ```

    

#### openFeign

- 基本使用

  - 引入依赖

    ```xml
    <!-- openfeign依赖携带ribbon依赖 -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
        <version>2.2.7.RELEASE</version>
    </dependency>
    ```
    
  - 启动类加`@EnableFeignClients(clients = CustomerClient.class)`
  
  - 定义和使用Feign客户端
  
    ```java
    @FeignClient("customerService(服务器名)")
    public interface CustomerClient {
        @GetMapping("/customer/{id}")
        Customer findById(@PathVariable("id") Long id);
    }
    ```
  
    ```java
    Customer customer = customerClient.findById({id});
    ```
  
- 配置

  | 类型                | 作用             | 说明                                             |
  | ------------------- | ---------------- | ------------------------------------------------ |
  | feign.Logger.Level  | 修改日志级别     | 四种级别：NONE、BASIC、HEADERS、FULL             |
  | feign.codec.Decoder | 响应结果的解析器 | 解析http远程调用结果，如解析json字符串为java对象 |
  | feign.coder.Encoder | 请求参数编码     | 将请求参数编码，便于通过http发送请求             |
  | feign.Contract      | 支出的注解格式   | 默认是SpringMVC的注解                            |
  | feign.Retryer       | 失败重试机制     | 请求失败的重试机制，默认无，但会使用Ribbon的重试 |

  - 日志配置
  
    - 配置文件，feign.client.config.xxx.loggerLevel: FULL
  
      - xxx是default，表示全局配置
      - xxx是某个服务，表示某个服务上
  
    - java代码（配置bean）
  
      ```java
      public class FeignClientConfiguration{
          @Bean
          public Logger.Level FeignLogLevel(){
              return Logger.Level.BASIC;
          }
      }
      ```
  
      - 全局：`@EnableFeignClients(defaultConfiguration = FeignClientConfiguration.class)`
      - 局部：`@FeignClient(value="customerService",configuration = FeignClientConfiguration.class)`

- 性能优化

  - 原理：feign默认使用URLConnection发起请求，URLConnection不支持连接池。可改用支持链接池的HttpClient或OKHttp

  - 步骤

    - 引入HttpClient依赖

      ```xml
      <dependency>
      	<groupId>io.github.openfeign</groupId>
          <artifactId>feign-httpclient</artifactId>
      </dependency>
      ```

    - 配置连接池

      ```yaml
      feign:
       client:
        config:
         default:
          loggerLevel: BASIC # 关闭日志或使用BASIC级别可优化性能
       httpclient:
        enabled: true # 开启feign对HttpClient的支持
        max-connections: 200 # 最大连接数
        max-connection-per-route: 50 # 每个路径的最大连接数
      ```

- 超时控制

  ```yaml
  ribbon:
   # 建立连接所用的时间，使用与网络状况正常的情况下，两端连接所用的时间
   ConnectTimeout: 5000
   # 建立连接后从服务器读取到可用资源所用的时间
   ReadTimeout: 5000
  ```
  
- 最佳实践

  - 方法一

    - 原理：FeignClient("customerService")接口与CustomerController接口中的方法重复，可将其抽出成一个接口，并由上述两个接口继承这个接口
    - 问题：耦合；CustomerController接口需要覆盖重写接口中的方法

  - 方法二

    - 将Feign抽出成一个模块，供所需要的模块使用

    - 步骤

      - 首先创建一个module，命名为feign-api，然后引入feign所需的依赖
      - 将order-service中编写的CustomerClient、Customer、DefaultFeignConfiguration都复制到feign-api中
      - 在order-service中引入feign-api的依赖
      - 修改order-service中各类的import导入部分

    - 问题：当FeignClient不在SpringBootApplication的扫描范围内时，这些FeignClient无法使用。有两种解决方法

      - 指定FeignClient所在包

        ```java
        @EnableFeignClients(BasePackages="cn.Eli.feign.clients")
        ```

      - 指定FeignClient字节码

        ```java
        @EnableFeignClients(clients={CustomerClient.class})
        ```



### 服务监控与保护

#### Hystrix

##### 使用步骤

- 依赖

  ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
      <version>2.2.10.RELEASE</version>
  </dependency>
  ```

- 开启Hystrix：启动类加`@EnableCircuitBreaker/@EnableHystrix`

- 设置超时时间



##### 服务降级

- 服务超时或出错时的回馈

- @HystrixCommand

  ```java
  @Override
  @HystrixCommand(fallbackMethod = "customer_timeoutHandler", commandProperties = {
      @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "3000")
  })
  public String customer_timeout() {
      //int a = 10/0;
      int millis = 5000;
      try {
          Thread.sleep(millis);
      } catch (InterruptedException e) {
          throw new RuntimeException(e);
      }
      return "成功，延时："+millis;
  }
  
  public String customer_timeoutHandler(){
      return "失败";
  }
  ```

- orderService（消费端）也可以使用服务降级，以免请求超时

  - 打开feign的hystrix设置：在application.yml中配置

    ```yaml
    # 若在feignClient中配置服务降级，须开启以下配置
    feign:
     hystrix:
      enabled: true
      
    # 上面配置开启后，hystrix也会有一个默认超时时间，类似于ribbon
    # 区别于之前配置的ribbon超时时间，两者都要配置
    hystrix:
      command:
        default:
          execution:
            isolation:
              thread:
                timeoutInMilliseconds: 5000
    # ribbon 超时报错：Read timed out
    # hystrix 超时报错：java.util.concurrent.TimeoutException: null
    ```

  - 服务降级：orderController中使用服务降级

    - @HystrixCommand

      ```java
      @HystrixCommand(fallbackMethod = "timeoutHandler", commandProperties = {
          @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "3000")
      })
      @GetMapping("/timeout")
      public String timeout(){
          // 调用feignclient方法
          String str = customerClient.customer_timeout();
          return str;
      }
      
      public String timeoutHandler(){
          return "失败";
      }
      ```

  - 消费端统一配置

    - FeignClient端

      ```java
      @FeignClient("customerService", fallback = CustomerFallbackClient.class)
      public interface CustomerClient {
          @GetMapping("/customer/{id}")
          Customer findById(@PathVariable int id);
      
          @GetMapping("/customer/timeout")
          String customer_timeout();
      }
      ```

    - 统一配置降级类

      ```java
      @Component
      public class CustomerFallbackClient implements CustomerClient{
          @Override
          public Customer findById(int id){
              return null;
          }
          
          @Override
          public String customer_timeout(){
              return "失败";
          }
      }
      ```

- 另外，统一配置降级还可以使用：@DefaultProperties(defaultFallback = "defaultMethod")，加在类上，用于向该类中加了@HystrixCommand但未指定处理方法的方法指定默认处理方法



##### 服务熔断

- 熔断

  ```java
  @Override
  @HystrixCommand(fallbackMethod = "customer_breakHandler", commandProperties = {
      @HystrixProperty(name = "circuitBreaker.enabled", value = "true"), // 是否开启断路器
      @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "10"), // 请求次数默认20
      @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds", value = "5000"), // 拒绝请求时间，默认5000
      @HystrixProperty(name = "circuitBreaker.timeInMilliseconds", value = "10000"), // 时间窗口（内部时钟），默认10000
      @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "60") // 失败率达到60%后跳闸，默认50
  })
  public String customer_break(int id) {
      if (id < 0){
          throw new RuntimeException("id不能为负数");
      }
      String uuid = UUID.randomUUID().toString();
      return "成功，流水号："+uuid;
  }
  
  public String customer_breakHandler(int id){
      return "失败";
  }
  ```

- 熔断打开：请求不再进行调用当前服务，内部设置时钟一般为MTTR（平均故障处理时间），当打开时长达到所设置时钟则进入半熔断状态
- 熔断关闭：
- 熔断半开：部分请求根据规则调用当前服务，如果请求成功且复合规则默认认为当前服务恢复正常，关闭熔断



##### 服务监控

- 新建一个springboot模块

- 引入依赖

  ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
      <version>2.2.10.RELEASE</version>
  </dependency>
  <!-- 监控依赖 -->
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-actuator</artifactId>
  </dependency>
  ```

- 配置端口

- 主启动类添加`@EnableHystrixDashboard`

- 所有微服务提供类需要添加监控依赖配置






#### Sentinel

##### 使用步骤

- 下载启动 sentinel-dashboard-1.8.6.jar

  - `docker pull bladex/sentinel-dashboard`
  - `docker run --name sentinel -p 8858:8858 -td bladex/sentinel-dashboard`

- 导入依赖

  ```xml
  <dependency>
      <groupId>com.alibaba.cloud</groupId>
      <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
  </dependency>
  <dependency>
      <groupId>com.alibaba.cloud</groupId>
      <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
  </dependency>
  ```
  
- application.yml

  ```yaml
  server:
    port: 8401
  
  spring:
    application:
      name: sentinel-customerService
    cloud:
      nacos:
        discovery:
          server-addr: 192.168.36.131:8848
      sentinel:
        transport:
          dashboard: 192.168.336.131:8858
          # 默认端口8719，假如被占用会自动从8719开始依次+1扫描未被占用端口
          port: 8719
  
  management:
    endpoints:
      web:
        exposure:
          include: '*'
  
  # 若使用openFeign的fallback类须开启以下配置
  feign:
    sentinel:
      enabled: true
  ```
  
  

##### 流控规则

- 资源名：唯一名称，默认请求路径
- 针对来源：Sentinel可以针对调用者进行限流
- 阈值类型/单机阈值
  - QPS（每秒的请求数量）：当调用该api的QPS达到阈值时进行限流
  - 线程数：当调用该api的线程数达到阈值时，进行限流
- 是否集群
- 流控模式：
  - 直接：api达到限流条件时，直接限流
  - 关联：当关联的资源达到阈值时，限流自己
  - 链路：只记录指定链路上的流量（指定资源从入口资源进来的流量，如果达到阈值，就进行限流）
- 流控效果：
  - 快速失败：直接失败，抛异常
  - Warm Up：根据codeFactory（冷加载因子，默认3）的值，经过预热时长，才达到QPS阈值
  - 排队等待：



##### 流控降级

- 资源名
- 熔断策略
  - RT（慢调用比例）
  - 异常比例
  - 异常数
- 比例阈值
- 熔断时间
- 最小请求数



##### 热点规则

- java

  - ```java
    @GetMapping("/hotKey")
    @SentinelResource(value = "hotKey", blockHandler = "deal_testHotKey")
    public String getForHotKey(
        @RequestParam(value = "p1", required = false) String p1,
        @RequestParam(value = "p2", required = false) String p2)
    {
        return "------testHotKey:   p1->"+p1+"  p2->"+p2;
    }
    public String deal_testHotKey(String p1, String p2, BlockException blockException) {
        return "-------deal_hotkey";
    }
    ```
  
- 图形化配置

  - 资源名
  - 参数索引：java代码中的索引号参数
  - 单机阈值
  - 统计窗口期
  - 参数例外项：指定参数值为某个特定值时，可单独指定该值的限流阈值



##### 系统规则

- 阈值类型
  - Load
  - RT
  - 线程数
  - 入口QPS
  - cpu使用率



##### 授权规则

规则：若请求从gateway转发来的，则可以访问，否则，不能访问

- 请求通过网关时，添加origin的请求头，可设置其值为gateway

  ```yml
  spring:
    cloud:
      gateway:
        default-filters:
          - AddRequestHeader=origin,gateway
  ```

- 重写解析方法，判断是否带有origin的请求头

  - Sentinel是通过RequestOriginParser这个接口的parseOrigin来获取请求的来源的

    ```java
    public interface RequestOriginParser {
        String parseOrigin(HttpServletRequest request);
    }
    ```

  - 例如，尝试从request中获取一个名为origin的请求头，并返回授权规则

    ```java
    @Component
    public class HeaderOriginParser implements RequestOriginParser {
        @Override
        public String parseOrigin(HttpServletRequest request) {
            String origin = request.getHeader("origin");
            if(StringUtils.isEmpty(origin)) {
                return "blank";
            }
            return origin;
        }
    }
    ```

- 填写白名单：流控应用填写 gateway



##### @SentinelResource

- value：资源名

- blockHandlerClass：指定规则处理类

- blockHandler：指定规则处理方法，该方法只会 在控制台上配置的问题产生时 调用

- fallbackClass：指定异常处理类

- fallback：指定异常处理方法，该方法会 在java代码出现异常时 调用

  ```java
  @GetMapping("/fallback/{number1}/{number2}")
  @SentinelResource(value = "fallback", fallback = "deal_fallback")
  public String getForFallback(@PathVariable("number2") int number2,
                               @PathVariable("number1") int number1) {
      if (number2 == -1) {
          throw new RuntimeException("请求非法");
      }
      return "number 值为："+number1;
  }
  public String deal_fallback(int number2, int number1, Throwable throwable) {
      return "number2的值为 "+number2+"  ->  "+throwable.getMessage();
  }
  ```

- fallback和blockHandler两者都配置：规则归blockHandler处理，异常归fallback处理

- exceptionToIgnore：忽略指定异常的处理



##### 配置持久化

- 依赖

  - ```xml
    <dependency>
        <groupId>com.alibaba.csp</groupId>
        <artifactId>sentinel-datasource-nacos</artifactId>
    </dependency>
    ```

- yaml配置

  - ```yaml
    spring:
      application:
        name: customerServer-sentinel
      cloud:
        nacos:
          server-addr: 192.168.36.132:8848
        sentinel:
          transport:
            port: 8720
            dashboard: 192.168.36.132:8858
          # 用于配置持久化到nacos上
          datasource:
            ds1:
              nacos:
                server-addr: 192.168.36.132:8848
                data-id: ${spring.application.name}
                group-id: DEFAULT_GROUP
                data-type: json
                rule-type: flow
    ```

- nacos新建配置

  - Data ID：customerServer-sentinel

  - 格式配置：json

  - 配置内容

    - 配置

      - resource：资源名
      - limitApp：来源应用
      - grade：阈值类型，0表示线程数，1表示QPS
      - count：单机阈值
      - strategy：流控模式，0表示直接，1表示关联，2表示链路
      - controlBehavior：流控效果，0表示快速失败，1表示Warm up，2表示排队等待
      - clusterMode：是否集群

    - ```json
      [
          {
              "resource": "/customer/{id}",
              "limitApp": "default",
              "grade": 1,
              "count": 1,
              "strategy": 0,
              "controlBehavior": 0,
              "clusterMode": false
          }
      ]
      ```

      





### 服务网关

#### Gateway

- 基础使用

  - 引入依赖

    ```xml
    <!-- springboot版本用2.3.9.RELEASE -->
    <!-- jdk用8 -->
    
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-gateway</artifactId>
    </dependency>
    <!-- gateway也是一项微服务，需要nacos服务发现依赖 -->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
    ```
  
    注：spring-boot-starter-web依赖与gateway中的spring-boot-starter-webflux依赖冲突
  
  - 启动类
  
  - 配置
  
    ```yaml
    server:
      port: 80
    spring:
      application:
        name: gateway
      cloud:
        nacos:
          server-addr: 192.168.36.131:8848
        gateway:
          discovery:
            locator:
            # 开启注册中心动态创建路由的功能，利用微服务名进行路由
            enabled: true
            # eureka 自动大写服务名时，可开启
            #lowerCaseServiceId: true
          routes:
            - id: customer-service # 路由id，自定义，唯一
              # uri: http://127.0.0.1:8081 # 路由的目标地址 http就是固定地址
              uri: lb://customerService # lb是loadBalance，后面跟服务名
              predicates: # 路由断言，是否符合下面规则
                - Path=/customer/** # 按路径匹配
            - id: order-service
              uri: lb://orderService
              predicates:
                - Path=/order/**
              #filters:
                #- StripPrefix=1
    ```
  
- 断言工厂

  - Path

    - ```yml
      predicates:
        - Path=/order/**
      ```

  - 其他断言

    - ```yml
      predicates:
        - Path=/order/**
        # 时间格式：2022-01-01T00:00:00.000+08:00[Asia/Shanghai]
        - After=时间
        - Before=时间
        - Between=时间A,时间B
        
        # 前一个为键名，后一个为正则表达式
        # 携带键值对为 username=zzyy 的cookie
        - Cookie=username, zzyy
        # 携带一个键为 X-Request-Id, 值为整数的Header
        - Header=X-Request-Id, \d+
        - Host=**.baidu.com
        - Method=Get
        # 携带userId=整数的参数 http://localhost/order/userId=3
        - Query=userId, \d+
      ```

- 过滤器

  - 全局过滤器

  - 自定义过滤器

    - ```java
      @Component
      public class MyGatewayFilter implements GlobalFilter, Ordered {
          @Override
          public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
              System.out.println("filter执行了。。。");
              if (!exchange.getRequest().getPath().toString().contains("order")) {
                  exchange.getResponse().setStatusCode(HttpStatus.NOT_FOUND);
                  return exchange.getResponse().setComplete();
              }
              return chain.filter(exchange);
          }
      
          /**
           * 加载过滤器的顺序
           * @return 数字越小优先级越高
           */
          @Override
          public int getOrder() {
              return 0;
          }
      }
      ```

      








### Docker

#### 安装

- 卸载docker

  ```shell
  yum remove docker \
                    docker-client \
                    docker-client-latest \
                    docker-common \
                    docker-latest \
                    docker-latest-logrotate \
                    docker-logrotate \
                    docker-selinux \
                    docker-engine-selinux \
                    docker-engine \
                    docker-ce
  ```

- 安装

  - 安装yum工具

    ```shell
    yum install -y yum-utils \
               device-mapper-persistent-data \
               lvm2 --skip-broken
    ```

  - 配置docker的yum源：告诉linux去哪下载

    ```shell
    yum-config-manager \
        --add-repo \
        https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
        
    sed -i 's/download.docker.com/mirrors.aliyun.com\/docker-ce/g' /etc/yum.repos.d/docker-ce.repo
    
    yum makecache fast
    ```

  - 安装docker-ce（社区版）

    ```shell
    yum install -y docker-ce
    ```

- 启动docker

  - 关闭防火墙

    ```shell
    # 关闭
    systemctl stop firewalld
    # 禁止开机启动防火墙
    systemctl disable firewalld
    ```

  - 启动docker

    ```shell
    systemctl start docker  # 启动docker服务
    
    systemctl stop docker  # 停止docker服务
    
    systemctl restart docker  # 重启docker服务
    ```

- 镜像加速

  - 参考阿里云的镜像加速文档：https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors

    ```shell
    sudo mkdir -p /etc/docker
    sudo tee /etc/docker/daemon.json <<-'EOF'
    {
      "registry-mirrors": ["https://18madm2u.mirror.aliyuncs.com"]
    }
    EOF
    sudo systemctl daemon-reload
    sudo systemctl restart docker
    ```

    

#### 使用

- 镜像操作

  - 镜像拉取：docker pull
  - 查看镜像：docker images
  - 删除镜像：docker rmi
  - 构建镜像：docker build
  - 保存镜像：docker save
  - 加载镜像：docker load

- 容器状态

  - 运行镜像：docker run
    - -d：后台运行
    - -p：端口映射
    - -e：添加配置信息
    - --name：容器名
    - docker run --name mysql_5.7 -e MYSQL_ROOT_PASSWORD=root -d mysql:5.7
  - 暂停容器：docker pause
  - 恢复容器：docker unpause
  - 停止容器：docker stop
  - 开启容器：docker start

- 容器操作

  - 进入容器：docker exec
    - docker exec -it mysql_5.7 bash
  - 查看容器运行日志：docker [container] logs 容器名
  - 默认查看所有运行容器及状态：docker ps
  - 删除容器：docker rm

- 数据卷 docker volume [COMMAND]

  - create：创建一个volume
  - inspect：显示一个或多个volume的信息
  - ls：列出所有的volume
  - prune：删除未使用的volume

- 挂载

  - 数据卷挂载

    ```shell
    docker run --name myNginx \
    	-v html:/root/html \ # 把html数据卷挂载到容器内的/root/html这个目录中
    	-p 8080:80 \ # 把宿主机的8080端口映射到容器内的80端口上
        nginx 
    ```

  - 目录挂载（宿主机目录、文件未提前创建会自动创建目录，不会创建文件）

    - -v [宿主机目录]:[容器内目录]
    - -v [宿主机文件]:[容器内文件]

    ```shell
    docker run --name mysql_5.7 \
    	-e MYSQL_ROOT_PASSWORD=root \
    	-p 3306:3306 \
    	-v /var/docker-volume/mysql/conf/hmy.cnf:/etc/mysql/conf.d/hmy.cnf \
    	-v /var/docker-volume/mysql/data:/var/lib/mysql \
    	-d mysql:5.7
    ```

    

#### 自定义镜像

- 指令

  - FROM：指定基础镜像
  - ENV：设置环境变量，可在后面指令使用
  - COPY：拷贝本地文件到镜像的指定目录
  - RUN：执行Linux的shell命令，一般是安装过程的命令
  - EXPOSE：指定容器运行时监听的端口，供镜像使用者查看
  - ENTRYPOINT：镜像中应用的启动命令，容器运行时调用

- 基于Ubuntu构建Dockerfile文件

  ```dockerfile
  # 指定基础镜像
  FROM ubuntu:16.04
  
  # 配置环境变量，JDK的安装目录
  ENV JAVA_DIR=/usr/local
  
  # 拷贝jdk和java的项目包
  COPY ./jdk-17.0.4.1_linux-x64_bin.tar.gz $JAVA_DIR/
  COPY ./java-demo-1.0-SNAPSHOT.jar /tmp/app.jar
  
  # 安装JDK
  RUN cd $JAVA_DIR && tar -xf ./jdk-17.0.4.1_linux-x64_bin.tar.gz && mv ./jdk-17.0.4.1 ./java17
  
  # 配置环境变量
  ENV JAVA_HOME=$JAVA_DIR/java17
  ENV PATH=$PATH:$JAVA_HOME/bin
  
  # 暴露端口
  EXPOSE 80
  
  # 入口，java项目的启动命令
  ENTRYPOINT java -jar /tmp/app.jar
  ```

- 基于java:8-alpine镜像

  ```dockerfile
  FROM java:8-alpine
  COPY ./docker-demo.jar /tmp/app.jar
  EXPOSE 8080
  ENTRYPOINT java -jar /tmp/app.jar
  ```

- 构建镜像

  ```shell
  docker build -t javaweb:1.0 .
  # -t t是tag（javaweb:1.0是一个tag）
  # . 表示dockerfile所在的目录
  ```



#### 运行镜像

- ubuntu：docker run -itd --name ubuntu ubuntu

- redis
  
  - 创建文件夹
  
    ```shell
    mkdir -p /var/docker-volume/redis/conf
    touch /var/docker-volume/redis/conf/redis.conf
    mkdir -p /var/docker-volume/redis/data
    ```
  
  - 运行镜像
  
    ```shell
    docker run -p 6379:6379 --name redis \
    -v /var/docker-volume/redis/data:/data \
    -v /var/docker-volume/redis/conf/redis.conf:/etc/redis/redis.conf \
    -d redis \
    redis-server /etc/redis/redis.conf \
    appendonly yes
    ```
  
  - redis-server /etc/redis/redis.conf  以配置文件启动redis，加载容器内的conf文件，最终找到的是挂载的目录/usr/local/docker/redis.conf
  
  - appendonly yes 开启redis 持久化
  
- mysql:5.7

  - 创建文件夹

    ```shell
    mkdir -p /var/docker-volume/mysql/log
    mkdir -p /var/docker-volume/mysql/data
    mkdir -p /var/docker-volume/mysql/conf
    ```

  - 运行镜像

    ```shell
    docker run -p 3306:3306 --name mysql \
    	-v /var/docker-volume/mysql/log:/var/log/mysql \
    	-v /var/docker-volume/mysql/data:/var/lib/mysql \
    	-v /var/docker-volume/mysql/conf:/etc/mysql \
    	-e MYSQL_ROOT_PASSWORD=root \
    	-d mysql:5.7
    ```
    
  - 添加配置文件conf目录下添加my.cnf文件

    ```ini
    [mysqld]
    # 跳过域名解析
    skip-name-resolve
    character_set_server=utf8
    datadir=/var/lib/mysql
    server-id=1000
    ```

- nginx

  - 创建挂载目录

    ```shell
    mkdir -p /var/docker-volume/nginx/conf
    mkdir -p /var/docker-volume/nginx/log
    mkdir -p /var/docker-volume/nginx/html
    ```

  - 复制文件到挂载目录

    ```shell
    # 先生成容器
    docker run --name nginx -p 9999:80 -d nginx
    
    # 复制容器中的文件到挂载目录
    docker cp nginx:/etc/nginx/nginx.conf /var/docker-volume/nginx/conf/nginx.conf
    docker cp nginx:/etc/nginx/conf.d /var/docker-volume/nginx/conf/conf.d
    docker cp nginx:/usr/share/nginx/html /var/docker-volume/nginx/
    ```

  - 移除nginx容器后重新运行

    ```shell
    docker run --name nginx ...
    ```

    







### RabbitMQ

#### 安装

- 拉去镜像

  ```shell
  docker pull rabbitmq
  ```

- 启动

  ```shell
  docker run \
   -e RABBITMQ_DEFAULT_USER=eli \
   -e RABBITMQ_DEFAULT_PASS=root \
   --name mq \
   --hostname mql \
   -p 15672:15672 \
   -p 5672:5672 \
   -d rabbitmq
  ```

- 进入容器，开启web管理

  ```shell
  docker exec -it mq bash
  ```

  ```shell
  rabbitmq-plugins enable rabbitmq_management
  ```




#### 快速入门

- 导入依赖

  ```xml
  <!--AMQP依赖，包含RabbitMQ-->
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-amqp</artifactId>
  </dependency>
  ```

- 发布者

  ```java
  // 引入com.rabbitmq.client的类
  @Test
  public void testSendMessage() throws IOException, TimeoutException {
      // 1.建立连接
      ConnectionFactory factory = new ConnectionFactory();
      // 1.1.设置连接参数，分别是：主机名、端口号、vhost、用户名、密码
      factory.setHost("192.168.36.131");
      factory.setPort(5672);
      factory.setVirtualHost("/");
      factory.setUsername("eli");
      factory.setPassword("root");
      // 1.2.建立连接
      Connection connection = factory.newConnection();
  
      // 2.创建通道Channel
      Channel channel = connection.createChannel();
  
      // 3.创建队列
      String queueName = "simple.queue";
      channel.queueDeclare(queueName, false, false, false, null);
  
      // 4.发送消息
      String message = "hello, rabbitmq!";
      channel.basicPublish("", queueName, null, message.getBytes());
      System.out.println("发送消息成功：【" + message + "】");
  
      // 5.关闭通道和连接
      channel.close();
      connection.close();
  }
  ```

- 接收者

  ```java
  // 引入com.rabbitmq.client的类
  public class ConsumerTest {
      public static void main(String[] args) throws IOException, TimeoutException {
          // 1.建立连接
          ConnectionFactory factory = new ConnectionFactory();
          // 1.1.设置连接参数，分别是：主机名、端口号、vhost、用户名、密码
          factory.setHost("192.168.36.131");
          factory.setPort(5672);
          factory.setVirtualHost("/");
          factory.setUsername("eli");
          factory.setPassword("root");
          // 1.2.建立连接
          Connection connection = factory.newConnection();
  
          // 2.创建通道Channel
          Channel channel = connection.createChannel();
  
          // 3.创建队列
          String queueName = "simple.queue";
          channel.queueDeclare(queueName, false, false, false, null);
  
          // 4.订阅消息
          channel.basicConsume(queueName, true, new DefaultConsumer(channel){
              @Override
              public void handleDelivery(String consumerTag, Envelope envelope,
                                         AMQP.BasicProperties properties, byte[] body) throws IOException {
                  // 5.处理消息
                  String message = new String(body);
                  System.out.println("接收到消息：【" + message + "】");
              }
          });
          System.out.println("等待接收消息。。。。");
      }
  }
  ```
  
  

#### SpringAMQP

- 准备

  - 依赖

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-amqp</artifactId>
    </dependency>
    ```

  - 基础配置文件

    ```yml
    spring:
      rabbitmq:
        host: 192.168.36.132
        port: 5672
        virtual-host: /
        username: eli
        password: root
    ```

  - 发布测试类

    ```java
    @SpringBootTest
    public class Publisher {
        @Autowired
        private RabbitTemplate rabbitTemplate;
        // 测试
    }
    ```

- 类型一

  ![one](D:\picture\typora\java\rabbitmq\one.png)

  - 发送

    ```java
    @Test
    public void test01(){
        String queueName = "simple.queue";
        String message = "hello, spring amqp!";
        rabbitTemplate.convertAndSend(queueName, message);
    }
    ```

  - 接收

    ```java
    @Component
    public class SpringRabbitListener {
        @RabbitListener(queues = "simple.queue")
        public void listen01(String msg)throws InterruptedException {
            System.out.println("接收到消息：【" + msg + "】");
        }
    }
    ```

    

- 类型二

  ![two](D:\picture\typora\java\rabbitmq\two.png)

  - 发送

  - 接收

    - 多个consumer：多个@RabbitListener

    - 配置文件

      ```yaml
      spring:
        rabbitmq:
          host: 192.168.36.131
          port: 5672
          virtual-host: /
          username: eli
          password: root
          listener:
            simple:
              prefetch: 1 # 预处理机制，每次只获取一条消息
      ```

      

- 类型三

  ![three](D:\picture\typora\java\rabbitmq\three.png)

  - 发送到交换机

    ```java
    @Test
    public void test02(){
        String exchangeName = "eli.fanout";
        String message = "hello, everyone!";
        rabbitTemplate.convertAndSend(exchangeName, "", message);
    }
    ```

  - 交换机：fanout（广播）

    ```java
    // 基于配置类声明交换机
    @Configuration
    public class FanoutConfig {
        // 声明FanoutExchange交换机
        @Bean
        public FanoutExchange fanoutExchange(){
            return new FanoutExchange("eli.fanout");
        }
    
        // 声明第一个队列
        @Bean
        public Queue fanoutQueue1(){
            return new Queue("fanout.queue1");
        }
        
        // 绑定队列一和交换机
        @Bean
        public Binding bindingQueue1(Queue fanoutQueue1, FanoutExchange fanoutExchange){
            return BindingBuilder.bind(fanoutQueue1).to(fanoutExchange);
        }
        
        // 声明并绑定其他队列
    }
    ```

  - 接收



- 类型四

  ![four](D:\picture\typora\java\rabbitmq\four.png)

  - 发送

    ```java
    @Test
    public void test03(){
        String exchangeName = "eli.direct";
        String key = "blue";
        String message = "hello, "+key;
        rabbitTemplate.convertAndSend(exchangeName, key, message);
    }
    ```

  - 交换机：direct（路由）

  - 接收

    ```java
    // 基于注解声明交换机
    @RabbitListener(bindings = @QueueBinding(
        value = @Queue(name = "direct.queue1"), // 定义队列
        exchange = @Exchange(name = "eli.direct", type = ExchangeTypes.DIRECT), // 定义交换机
        key = {"red","blue"} //绑定关键词
    ))
    public void Listen04(String msg)throws InterruptedException {
        System.out.println("4接收到消息：【" + msg + "】");
    }
    ```



- 类型五

  ![five](D:\picture\typora\java\rabbitmq\five.png)

  - 发送

    ```java
    @Test
    public void test04(){
        String exchangeName = "eli.topic";
        String key = "china.news.dir";
        rabbitTemplate.convertAndSend(exchangeName, key, key);
    }
    ```

  - 交换机：topic（话题）

  - 接收

    ```java
    // 基于注解声明交换机
    @RabbitListener(bindings = @QueueBinding(
        value = @Queue(name = "topic.queue1"), // 定义队列
        exchange = @Exchange(name = "eli.topic", type = ExchangeTypes.TOPIC), // 定义交换机
        key = {"china.#"} //绑定关键词
    ))
    public void Listen04(String msg)throws InterruptedException {
        System.out.println("4接收到消息：【" + msg + "】");
    }
    ```

- 交换机exchange负责消息路由，不负责存储，路由失败则消息消失

- 如果传输pojo，默认序列化后再传输，该方法传出数据较大，可改用json

  - 在发送、接收服务引入依赖

    ```xml
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
    </dependency>
    ```

  - 在发送、接收服务声明MessageConverter

    ```java
    import org.springframework.amqp.support.converter.MessageConverter;
    
    @Bean
    public MessageConverter messageConverter(){
        return new Jackson2JsonMessageConverter();
    }
    ```

  - 接收

    ```java
    @Component
    public class SpringRabbitListener {
        @RabbitListener(queues = "simple.queue")
        // 形参传递对应的对象类型
        public void Listen01(Obj obj)throws InterruptedException {
        }
    }
    ```







## 高级篇

### 分布式事务

- CAP定理
  - 分布式系统节点通过网络连接，一定会出现分区问题（P）
  - 当分区问题出现时，系统的一致性（C）和可用性（A）就无法同时满足
- BASE理论
  - Basically Available：基本可用，允许损失部分可用性，即保证核心可用
  - Soft State：软状态，允许出现中间状态，如临时的不一致状态
  - Eventually Consistent：最终一致性



#### seata

##### 部署

- 前置操作

  - 下载http://seata.io/zh-cn/blog/download.html

- 修改conf目录下的registry.conf文件

  ```properties
  registry {
    # 配置中心 file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
    type = "nacos"
  
    nacos {
      application = "seata--server"
      serverAddr = "127.0.0.1:8848"
      group = "DEFAULT_GROUP"
      namespace = ""
      # 集群名称
      cluster = "default"
      username = ""
      password = ""
    }
  }
  
  config {
    type = "nacos"
    nacos {
      serverAddr = "127.0.0.1:8848"
      namespace = ""
      group = "SEATA_GROUP"
      username = ""
      password = ""
      dataId = "seataServer.properties"
    }
  }
  ```

- 在nacos中添加配置

  - DataId：seataServer.properties

  - Group：SEATA_GROUP

  - 配置格式：properties

  - 配置内容

    ```properties
    # 数据存储方式，db代表数据库
    store.mode=db
    store.db.datasource=druid
    store.db.dbType=mysql
    store.db.driverClassName=com.mysql.jdbc.Driver
    store.db.url=jdbc:mysql://127.0.0.1:3306/seata?useUnicode=true&rewriteBatchedStatements=true
    store.db.user=root
    store.db.password=123
    store.db.minConn=5
    store.db.maxConn=30
    store.db.globalTable=global_table
    store.db.branchTable=branch_table
    store.db.queryLimit=100
    store.db.lockTable=lock_table
    store.db.maxWait=5000
    # 事务、日志等配置
    server.recovery.committingRetryPeriod=1000
    server.recovery.asynCommittingRetryPeriod=1000
    server.recovery.rollbackingRetryPeriod=1000
    server.recovery.timeoutRetryPeriod=1000
    server.maxCommitRetryTimeout=-1
    server.maxRollbackRetryTimeout=-1
    server.rollbackRetryTimeoutUnlockEnable=false
    server.undo.logSaveDays=7
    server.undo.logDeletePeriod=86400000
    
    # 客户端与服务端传输方式
    transport.serialization=seata
    transport.compressor=none
    # 关闭metrics功能，提高性能
    metrics.enabled=false
    metrics.registryType=compact
    metrics.exporterList=prometheus
    metrics.exporterPrometheusPort=9898
    ```

- 新建表

  ```sql
  SET NAMES utf8mb4;
  SET FOREIGN_KEY_CHECKS = 0;
  
  -- ----------------------------
  -- 分支事务表
  -- ----------------------------
  DROP TABLE IF EXISTS `branch_table`;
  CREATE TABLE `branch_table`  (
    `branch_id` bigint(20) NOT NULL,
    `xid` varchar(128) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
    `transaction_id` bigint(20) NULL DEFAULT NULL,
    `resource_group_id` varchar(32) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
    `resource_id` varchar(256) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
    `branch_type` varchar(8) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
    `status` tinyint(4) NULL DEFAULT NULL,
    `client_id` varchar(64) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
    `application_data` varchar(2000) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
    `gmt_create` datetime(6) NULL DEFAULT NULL,
    `gmt_modified` datetime(6) NULL DEFAULT NULL,
    PRIMARY KEY (`branch_id`) USING BTREE,
    INDEX `idx_xid`(`xid`) USING BTREE
  ) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Compact;
  
  -- ----------------------------
  -- 全局事务表
  -- ----------------------------
  DROP TABLE IF EXISTS `global_table`;
  CREATE TABLE `global_table`  (
    `xid` varchar(128) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
    `transaction_id` bigint(20) NULL DEFAULT NULL,
    `status` tinyint(4) NOT NULL,
    `application_id` varchar(32) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
    `transaction_service_group` varchar(32) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
    `transaction_name` varchar(128) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
    `timeout` int(11) NULL DEFAULT NULL,
    `begin_time` bigint(20) NULL DEFAULT NULL,
    `application_data` varchar(2000) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
    `gmt_create` datetime NULL DEFAULT NULL,
    `gmt_modified` datetime NULL DEFAULT NULL,
    PRIMARY KEY (`xid`) USING BTREE,
    INDEX `idx_gmt_modified_status`(`gmt_modified`, `status`) USING BTREE,
    INDEX `idx_transaction_id`(`transaction_id`) USING BTREE
  ) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Compact;
  
  SET FOREIGN_KEY_CHECKS = 1;
  ```

- 通过bin目录下的指令启动seata



##### 集成

- 引入依赖

  ```xml
  <dependency>
      <groupId>com.alibaba.cloud</groupId>
      <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
      <!-- 排除版本较低的依赖 -->
      <exclusions>
          <exclusion>
              <groupId>io.seata</groupId>
              <artifactId>seata-spring-boot-starter</artifactId>
          </exclusion>
      </exclusions>
  </dependency>
  <!-- 添加1.4.2版本依赖 -->
  <dependency>
      <groupId>io.seata</groupId>
      <artifactId>seata-spring-boot-starter</artifactId>
      <version>${seata.version}</version>
  </dependency>
  ```

- 基于注册中心获取tc服务地址

  ```yaml
  seata:
    registry:
      type: nacos
      nacos:
        server-addr: 127.0.0.1:8848
        namespace: "" # 空表示public命名空间
        group: DEFAULT_GROUP
        application: seata-tc-server
        username:
        password:
    tx-service-group: seata-demo # 事务组，根据这个获取tc服务的cluster名称
    service:
      vgroup-mapping: # 事务组与TC服务cluster的映射关系
        seata-demo: SH
  ```

  



#### 模式

##### XA

- 阶段

  - 一阶段：事务协调者通知事务参与者（RM）执行各自事务，执行后不提交事务并将结果告知事务协调者
  - 二阶段：
    - 若都成功，则告知事务参与者提交事务
    - 否则，则告知事务参与者回滚事务

- 总结

  - 优点
    - 事务的强一致性，满足ACID原则
    - 常用数据库都支持，实现简单，并且没有代码侵入

  - 缺点
    - 因为一阶段需要锁定数据库资源，二阶段结束才释放，性能差
    - 依赖关系型数据库实现事务

- Seata的stater已经完成了XA模式的自动装配，实现非常简单，步骤如下：

  - 修改application.yml文件（每个事务参与者的微服务），开启XA模式

    ```yml
    seata:
      data-source-proxy-mode: XA
    ```

  - 给发起全局事务的入口方法添加@GlobalTransactional注解，本例中是OrderServiceImpl中的create方法

    ```java
    @Override
    @GlobalTransactional
    public Long create(Order order) {
        // 创建订单
        orderMapper.insert(order);
        // 扣减余额
        // 扣减库存
        return order.getId();
    }
    ```



##### AT

- 阶段

  - 一阶段
    - 注册分支事务
    - 记录undo-log（数据快照）
    - 执行业务sql并**提交**
    - 报告事务状态
  - 二阶段
    - 全部成功：异步删除undo-log
    - 存在失败：根据undo-log恢复数据，再删除undo-log

- 总结：弱一致

- 脏数据问题

  - 描述：第一个事务获得DB锁，记录快照，修改数据，释放DB锁后，在等待其他分支事务执行的过程中，第二个事务又修改了该数据，而其他分支事务执行失败，所有事务都要回滚，此时第一个事务根据快照恢复事务是错误的
  - seata内部实现一个全局锁，由seata管理的事务需要获取到该锁才能执行。
    - 若第二个seata事务获取到db锁后，未获取到全局锁，会进行重试，重试一定次数后，会失败（若一直重试，第二个seata事务因无法获取全局锁执行业务而不能释放db锁，第一个seata事务因无法获取db锁而不能修改数据，会一直占用全局锁，从而形成死锁）
    - 若第二个事务并非seata管理，其不需要获取全局锁，进而产生脏数据问题
      - 解决：记录快照时，会记录两份快照，一份是执行前的快照，一份是执行后的快照，执行前的快照用于恢复数据，执行后的快照用于比对数据。在恢复数据前，比对执行后的快照与现在的数据是否一致，若一致，恢复数据，若不一致，则说明出现脏数据问题，可转由人工处理

- 使用

  - 因为快照和全局锁都是通过数据库实现的，因此需要导入两张表

    - 全局锁的表（lock_table）是由tc实现的，要导入tc的关联数据库中

      ```sql
      DROP TABLE IF EXISTS `lock_table`;
      CREATE TABLE `lock_table`  (
        `row_key` varchar(128) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
        `xid` varchar(96) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
        `transaction_id` bigint(20) NULL DEFAULT NULL,
        `branch_id` bigint(20) NOT NULL,
        `resource_id` varchar(256) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
        `table_name` varchar(32) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
        `pk` varchar(36) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
        `gmt_create` datetime NULL DEFAULT NULL,
        `gmt_modified` datetime NULL DEFAULT NULL,
        PRIMARY KEY (`row_key`) USING BTREE,
        INDEX `idx_branch_id`(`branch_id`) USING BTREE
      ) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Compact;
      
      ```

    - 快照的表（undo_log）是由rm实现的，要导入rm的关联的数据库中

      ```sql
      DROP TABLE IF EXISTS `undo_log`;
      CREATE TABLE `undo_log`  (
        `branch_id` bigint(20) NOT NULL COMMENT 'branch transaction id',
        `xid` varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT 'global transaction id',
        `context` varchar(128) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT 'undo_log context,such as serialization',
        `rollback_info` longblob NOT NULL COMMENT 'rollback info',
        `log_status` int(11) NOT NULL COMMENT '0:normal status,1:defense status',
        `log_created` datetime(6) NOT NULL COMMENT 'create datetime',
        `log_modified` datetime(6) NOT NULL COMMENT 'modify datetime',
        UNIQUE INDEX `ux_undo_log`(`xid`, `branch_id`) USING BTREE
      ) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci COMMENT = 'AT transaction mode undo table' ROW_FORMAT = Compact;
      ```

  - 修改application.yml文件（每个事务参与者的微服务），开启AT模式

    ```yaml
    seata:
      data-source-proxy-mode: AT
    ```

  - 入口添加注解@GlobalTransactional



##### TCC

- TCC模式与AT模式相似，每个阶段都是独立的事务，不同的是TCC通过人工编码来实现数据的恢复。需要实现三个方法
  - Try：资源的检测和预留
  - Confirm：完成资源操作业务；要求Try成功，Confirm一定要能成功
  - Cancel：预留资源的释放；要求Try失败，Cancel一定要能成功
- 总结：弱一致，性能好，不依赖于数据库，每项事务各自独立不需要锁

- 使用：以订单扣款为例

  - 需求

    - 修改account-service，编写try、confirm、cancel逻辑
      - try业务：添加冻结金额，扣减可用金额
      - confirm业务：删除冻结金额
      - cancel业务：删除冻结金额，恢复可用金额
      - 保证confirm、cancel接口的幂等性
    - 空回滚：当某个分支事务的try阶段阻塞时，可能导致全局事务超时而触发二阶段的cancel操作。在未执行try操作时先执行了cancel操作，这时cancel不应该做回滚
    - 业务悬挂：对于已经空回滚的业务，如果阻塞结束，可能会继续执行try操作，但此时分布式事务已经结束。因此应当阻止空回滚后的try操作，避免业务悬挂
    - 综上：创建冻结金额表，添加事务id、用户id、冻结金额、事务状态 字段
      - RootContext.getXID()：可获得事务id

  - 添加并实现接口

    ```java
    @LocalTcc
    public interface TCCService {
        @TwoPhaseBusinessAction(name="prepare", commitMethod="confirm", rollbackMethod="cancel")
        // 该注解修饰的参数可在下面BusinessActionContext对象中获取
        void prepare(@BusinessActionContextParameter(paramName="param") String param);
        
        // context.getXid()也可获取事务id
        boolean confirm (BusinessActionContext context);
        
        boolean cancel (BusinessActionContext context);
    }
    ```

    ```java
    public class TCCServiceImpl implements TCCService{
        @Override
        // 设计到原始用户表和冻结金额表两张表数据的修改，要添加事务管理
        @Transactional
        public void prepare(String param) {
            // 。。。
        }
    
        @Override
        public boolean confirm(BusinessActionContext context) {return false;}
    
        @Override
        public boolean cancel(BusinessActionContext context) {return false;}
    }
    ```

    

##### SAGA

- 阶段
  - 一阶段：直接提交本地事务
  - 二阶段：成功则什么也不做；失败则通过编写补偿业务来回滚
- 总结：最终一致、性能好、无锁可能存在脏数据问题

​                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                



### 缓存

![多级缓存](D:\picture\typora\java\多级缓存.png)

#### Caffeine

- 是JVM进程缓存的框架

- 快速入门

  ```xml
  <dependency>
      <groupId>com.github.ben-manes.caffeine</groupId>
      <artifactId>caffeine</artifactId>
      <version>2.5.5</version>
  </dependency>
  ```

  ```java
  @Test
  void test() {
      Cache<String, String> cache = Caffeine.newBuilder().build();
      // 存数据
      cache.put("key", "value");
      // 取数据，不存在返回null
      cache.getIfPresent("key");
      // 取数据，不存在返回后面方法的返回值,可在方法中调用redis
      cache.get("key2", key -> "value2");
  }
  ```

- 缓存驱逐策略：达到条件后不会立即驱逐

  - 基于容量：设置缓存的数量上限

    ```java
    Cache<String, String> cache = Caffeine.newBuilder()
        .initialCapacity(100) // 初始化大小为 100
        .maximumsize(10_000) // 设置缓存大小上限为 10000
        .build();
    ```

  - 基于时间：设置缓存的有效时间

    ```java
    Cache<String, String> cache = Caffeine.newBuilder()
        .expireAfterAccess(Duration.ofSeconds(10)) // 从最后一次获取开始保存10s
        .build();
    ```

  - 基于引用：设置缓存为软引用或弱引用，利用GC来回收缓存数据。





#### Canal

##### 概述

- 缓存同步策略
  - 设置有效期：时效性差
  - 同步双写：有代码侵入，耦合度高
  - 异步通知：时效性较好，耦合度低
- Canal原理
  - MySQL主从同步
    - MySQL主从是基于binlog，主节点需开启binlog才能进行主从
    - 主从复制步骤：
      - 主节点记录binlog日志，并生成log dump线程，将binlog日志传递到从节点。
      - 从节点生成两个线程：I/O线程和SQL线程
        - I/O线程请求并接收主节点的binlog日志，并将其写入relay log中
        - SQL线程读取relay log，并解析执行具体操作（这一过程称为【重放】）
  - Canal就是把自己伪装成MySQL的一个slave节点，从而监听master的binlog变化。再把得到的变化信息通知给Canal客户端。



##### mysql主从

- 开启binlog

  - 修改my.cnf文件

  - 添加内容

    ```shell
    # 在部署mysql的系统的命令行
    # 先创建binlog文件夹
    mkdir /var/lib/mysql/binlog
    # 非必要：changed ownership of 'binlog' from root:root to mysql:mysql
    chown -R -v mysql:mysql /var/lib/mysql/binlog
    ```
    
    ```ini
    # 设置binary log文件存放的地址和文件名，叫做mysql-bin
    log-bin=/var/lib/mysql/binlog/weather-bin
    # 指定对哪个database记录binlog
    binlog-do-db=weather
    ```

- 设置用户权限：添加一个仅用于数据同步的账户，出于安全考虑，仅提供开启binlog的库的操作权限

  ```mysql
  create user canal@'%' IDENTIFIED by 'canal';
  GRANT SELECT, REPLICATION SLAVE, REPLICATION CLIENT,SUPER ON *.* TO 'canal'@'%' identified by 'canal';
  FLUSH PPRIVILEGES;
  
  # 重启容器后查看主从状态
  SHOW MASTER STATUS;
  ```
  
- 创建网络并加入

  ```shell
  docker network create test
  docker network connect test mysql
  ```



##### canal的使用

- 创建容器

  ```shell
  docker run -p 11111:11111 --name canal \
  # 集群名称（canal也可以搭建集群）
  -e canal.destinations=test \
  # 主节点地址，因为在同一docker网络，可用容器名代替
  -e canal.instance.master.address=mysql:3306 \
  -e canal.instance.dbUsername=canal \
  -e canal.instance.dbPassword=canal \
  -e canal.instance.connectionCharset=UTF-8 \
  -e canal.instance.tsdb.enable=true \
  -e canal.instance.gtidon=false \
  # 监听test库下所有的表
  -e canal.instance.filter.regex=test\\..* \
  --network test \
  -d canal/canal-server:v1.1.5
  ```

- 编写canal客户端：监听

  - 引入依赖

    ```xml
    <dependency>
        <groupId>org.springframework.data</groupId>
        <artifactId>spring-data-commons</artifactId>
    </dependency>
    <dependency>
        <groupId>top.javatool</groupId>
        <artifactId>canal-spring-boot-starter</artifactId>
        <version>1.2.1-RELEASE</version>
    </dependency>
    ```

  - 编写配置

    ```yaml
    canal:
      destination: test
      server: 192.168.36.132:11111
    ```

  - 编写监听器

    ```java
    @CanalTable("user") // Canal注解，标注表名
    @Component
    public class UserHandler implements EntryHandler<User> {
        @Override
        public void insert(User user) {
            System.out.println("新增用户："+user);
            // 同步缓存数据
        }
    
        @Override
        public void update(User before, User after) {
            System.out.println("修改用户");
        }
    
        @Override
        public void delete(User user) {
            System.out.println("删除用户："+user);
        }
    }
    ```
  
  - pojo类（需要标注实体类属性与表中字段的映射关系）
  
    ```java
    @Data
    @TableName("tb_item") // Mybatis or MP
    public class User {
        @TableId(type = IdType.AUTO) // Mybatis or MP
        @Id // 主键，Canal注解
        // 必须使用包装类
        private Long id;
        
        @Column(name = "name") // Canal注解，字段与属性值不一致时使用
        private String name;
        private Integer age;
        
        @TableField(exist = false) // Mybatis or MP
        @Transient // 表中没有的字段用该注解
        private String msg;
    }
    ```
    
    









# Nginx



## 部署

### nginx

- 下载镜像

  - ```shell
    docker pull nginx
    ```

- 创建挂载目录

  - ```shell
    mkdir -p /var/docker-volume/nginx/conf
    mkdir -p /var/docker-volume/nginx/log
    mkdir -p /var/docker-volume/nginx/html
    ```

- 复制文件到挂载目录

  - ```shell
    # 先生成容器
    docker run --name nginx -p 9999:80 -d nginx
    
    # 复制容器中的文件到挂载目录
    docker cp nginx:/etc/nginx/nginx.conf /var/docker-volume/nginx/conf/nginx.conf
    docker cp nginx:/etc/nginx/conf.d /var/docker-volume/nginx/conf/conf.d
    docker cp nginx:/usr/share/nginx/html /var/docker-volume/nginx/
    ```

- 移出nginx容器后重新运行

  - ```shell
    # 移出刚才生成的容器
    docker stop nginx
    docker rm nginx
    
    # 重新运行
    docker run \
    -p 9999:80 \
    --name nginx \
    -v /var/docker-volume/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
    -v /var/docker-volume/nginx/conf/conf.d:/etc/nginx/conf.d \
    -v /var/docker-volume/nginx/log:/var/log/nginx \
    -v /var/docker-volume/nginx/html:/usr/share/nginx/html \
    -d nginx
    ```




### openResty

- 拉取镜像

  ```shell
  docker pull openresty/openresty
  ```

- 运行镜像

  ```shell
  docker run --name openresty -p 80:80 -d openresty/openresty
  ```

- 拷贝文件

  ```java
  mkdir -p /var/docker-volume/openresty/openresty
  mkdir -p /var/docker-volume/openresty/conf.d
  docker cp openresty:/usr/local/openresty /var/docker-volume/openresty
  docker cp openresty:/etc/nginx/conf.d /var/docker-volume/openresty
  ```

- 删除容器

  ```shell
  docker stop openresty
  docker rm openresty
  ```

- 运行镜像

  ```shell
   docker run -p 80:80 -p 8081:8081 \
  --name openresty --privileged=true \
  -v /var/docker-volume/openresty/openresty:/usr/local/openresty \
  -v /var/docker-volume/openresty/conf.d:/etc/nginx/conf.d \
  -d openresty/openresty
  ```





## 基础篇

### 配置文件

```ini
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}

server {
    listen       80;
    listen  [::]:80;
    server_name  localhost;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```



#### 虚拟主机

```ini
# 可配置多个主机
server {
    listen       80;
    # 域名、主机名
    server_name  localhost www.test.cn;

    location / {
        root   /usr/share/nginx/html/test01;
        index  index.html index.htm;
    }
}
    
server {
    listen       80;
    # 支持通配符。匹配顺序：精准匹配 > 头通配符匹配 > 尾通配符匹配 > 正则匹配
    server_name  *.test.*;

    location / {
        root   /usr/share/nginx/html/test02;
        index  index.html index.htm;
    }
}
```



#### 反向代理

```ini
server {
    listen       80;
    server_name  localhost www.test.cn;

    location / {
        # 代理
        proxy_pass https://www.baidu.com;
    }
}
```



#### 负载均衡

```ini
upstream weather-service {
    # 基于uri做hash算法，在路由到服务器上
    # hash $request_uri;
	# 配置负载均衡，可配置权重、是否进行负载均衡、配置备用机
	server 192.168.36.133:9999 weight=6 down;
	server 192.168.36.134:9999 weight=2;
	server 192.168.36.134:9999 weight=2 backup;
}
server {
    listen       80;
    server_name  localhost www.test.cn;

    location / {
        proxy_pass http://weather-service;
    }
}
```



#### 动静分离

```ini
# 配置多个location
server {
    listen       80;
    server_name  localhost www.test.cn;

    location / {
    	# 定义匹配规则："$1" 表示匹配第一个 "(正则表达式)"
    	# last 本条规则匹配完成后，获得的新的URI会再次进行location匹配
    	# break 本条规则匹配完成即终止，不再匹配后面任何规则
    	# redirect 返回302临时重定向，浏览器地址栏会显示跳转后的URL地址
    	# permanent 返回301永久重定向，浏览器地址栏会显示跳转后的URL地址
    	rewrite ^/([0-9]+).html$   /index.html?pageNum=$1  break;
        proxy_pass http://httpds;
    }
    
    location ~*/(css|img|css) {
    	root /usr/share/nginx/html;
    	index index.html index.htm;
    }
}
```





## openResty

### lua入门

- 数据类型

  - nil：表示无效值（在条件表达式中相当于false）
  - boolean：false和true
  - number：表示双精度类型的实浮点数
  - string：可用双引号或单引号表示（拼接：str1 .. str2）
  - function：函数
  - table：表
    - local arr = {'java', 'python', 'lua'}：
      - arr[1]（索引从1开始）
    - local map = {name='zhangsan', age=18}
      - map['name']
      - map.name

- 循环

  - local arr = {'java', 'python', 'lua'}

    ```lua
    for index,value in ipairs(arr) do
        print(index, value)
    end
    ```

  - local map = {name='jack', age=18}

    ```lua
    for key,value in pairs(map) do
        print(key, value)
    end
    ```

- 函数

  ```lua
  function 函数名(arg1, arg2 ..., argn)
      -- 函数体
      return 返回值
  end
  ```

- 条件控制

  ```lua
  if(布尔表达式)
      then
      -- true 的语句块
      else
      -- flase 的语句块
  end
  ```

  - and：与
  - or：或
  - not：非



### 快速入门

- 编辑conf.d下的default.config文件

  ```ini
  upstream openresty-service {
      server 192.168.36.132:8081;
  }
  
  server {
      listen       80;
      server_name  localhost;
  
      location / {
          root   /usr/local/openresty/nginx/html;
          index  index.html index.htm;
      }
      
      location /test {
          proxy_pass http://openresty-service;
      }
  
      error_page   500 502 503 504  /50x.html;
      location = /50x.html {
          root   /usr/local/openresty/nginx/html;
      }
  }
  
  # 在nginx.conf的http下面，添加对OpenResty的Lua模块的加载
  # lua 模块
  lua_package_path "/usr/local/openresty/lualib/?.lua;;";
  # c 模块
  lua_package_cpath "/usr/local/openresty/lualib/?.so;;";
  
  server {
      listen       8081;
      server_name  localhost;
  
      location /test {
          # 设置response格式
          default_type application/json;
          # 响应数据由lua文件决定，在 nginx 的目录下寻找
          content_by_lua_file lua/test.lua;
      }
  }
  ```

- 在nginx目录下创建 lua/test.lua 文件

- 编辑lua脚本

  ```lua
  -- 返回假数据，这里的ngx.say()函数，就是将数据写到Response中
  ngx.say('{"name":"zhangsan","gender":"male","age":18}')
  ```

  

### 参数获取

- 路径占位符：/item/1001

  ```ini
  location ~ /item/(\d+) {
      content_by_file lua/item.lua;
  }
  ```

  ```lua
  local id = ngx.var[1]
  ```

- 请求头：id: 1001

  ```lua
  local headers = ngx.req.get_headers()
  ```

- get请求参数：?id=1001

  ```lua
  local getParams = ngx.req.get_uri_args()
  ```

- post表单惨数：id=1001

  ```lua
  ngx.req.read_body() -- 读取请求体
  local postParams = ngx.req.get_post_args()
  ```

- JSON参数：{"id": 1001}

  ```lua
  ngx.req.read_body()
  local jsonBody = ngx.req.get_body_data()
  ```




### 发送请求

- nginx提供了内部API用以发送http请求

  ```lua
  local cjson = require('cjson')
  
  local resp = ngx.location.capture("/path", {
          method = ngx.HTTP_GET, -- 请求方式
          args = {a=1, b=2}, -- get 方式传参
          body = "c=3&d=4" -- post 方式传参
      })
  
  local item = cjson.decode(resp.body)
  local json = cjson.encode(item)
  ngx.say(json)
  ```

- 返回的响应内容包括

  - resp.status：响应状态码
  - resp.header：响应头，是一个table
  - resp.body：响应体，就是响应数据

- 这里的path是路径，并不包括ip端口。这个请求会被nginx内部的server监听并处理

  ```ini
  location /path {
      proxy_pass http://192.168.36.132:8080;
  }
  ```



### 连接redis

- 引入redis模块，并初始化redis对象

  ```lua
  local redis = require("resty.redis") -- 引入redis模块
  local red = redis:new() -- 初始化redis对象
  -- 设置redis超时时间
  -- 分别是 建立连接 发送请求 响应结果 的超时时间
  red:set_timeouts(1000, 1000, 1000)
  ```

- 封装函数，用来释放redis连接，其实是放入连接池

  ```lua
  local function close_redis(red)
      local pool_max_idle_time = 10000 -- 连接的空闲时间，单位是毫秒
      local pool_size = 100
      local ok, err = red:set_keepalive(pool_max_idle_time, pool_size)
      if not ok then
          ngx.log(ngx.ERR, "放入Redis连接池失败", err)
      end
  end
  ```

- 封装函数，从Redis中读取并返回数据

  ```lua
  local function read_redis(ip, port, key)
      local ok, err = red:connect(ip, port)
      if not ok then
          ngx.log(ngx.ERR, "连接redis失败：", err)
          return nil
      end
      -- 密码和选择的桶
      -- red:auth(password)
      -- red:select(17) -- redis桶索引从0开始
      local resp, err = red:get(key)
      if not resp then
          ngx.log(ngx.ERR, "查询Redis失败：", err, ", key = ", key)
      end
      if resp == ngx.null then
          resp = nil
          ngx.log(ngx.ERR, "查询Redis数据为空，key = ", key)
      end
      close_redis(red)
      return resp
  end
  ```

  

### 本地缓存

- OpenResty为Nginx提供了shard dict的功能，可以在nginx的多个worker之间共享数据，实现数据缓存功能

  - 开启共享字典，在nginx.conf的http下添加配置：

    ```ini
    # 共享字典，名称为item_cache，大小为150m
    lua_shared_dict item_cache 150m;
    ```

  - 操作共享字典：

    ```lua
    --  获取本地缓存对象
    local item_cache = ngx.shared.item_cache
    -- 存储，指定key、value、过期时间，单位为s，默认为0代表永不过期
    item_cache:set('key', 'value', 1000)
    -- 读取
    local val = item_cache:get('key')
    ```

    =
