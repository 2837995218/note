### 总览

- <font color='#ccc'>专业知识：CN、DSA、OS、CO、DB</font>

- 语言

  1. 熟练使用各种设计模式，具有一定框架设计能力；熟悉数据结构，了解部分算法
  2. 掌握多线程与JUC技术：线程池相关内容、volatile、CAS、synchronized锁升级、AQS、JMM
  3. 了解JVM：四种引用、类加载器和加载机制、运行时内存区、多种垃圾回收算法和回收器
  4. 掌握多种IO技术：BIO、NIO、AIO；了解IO模型；掌握Netty，了解RPC
  5. 熟练使用编程语言scala；了解使用jdk17

- 后端

  6. 基础：JDBC、Spring、Mybatis、MybatisPlus、SpringBoot、SpringCloud、CloudAli、RabbitMQ 等

  7. 补充：了解Spring两种容器、后处理、三级缓存；了解使用boot装配原理；深入了解AOP；了解四层、七层负载均衡；了解使用分布式事务seata

  8. WebMVC：熟练使用Springmvc；了解内嵌Tomcat、父子容器

  9. WebFlux：掌握Netty；熟练使用Reactor；了解使用R2DBC
  10. 容器：了解云原生；熟练使用docker、掌握dockerfile编写；掌握Kubernetes命令行常用资源配置与调度<font color='#ccc'>、istio</font>

- 数据库系统

  11. MySQL：了解多种数据库对象；掌握8新特性；了解权限、存储结构、调优、事务、MVCC

  12. Redis：掌握多级缓存和缓存同步；了解持久化、主从、哨兵、分片；了解使用Redisson实现可重入锁、公平锁、红锁等

  13. 其他数据库：了解使用 PostgreSQL、elasticsearch、MongoDB

- 前端

  14. 熟练使用 HTML5、CSS3、ES6；掌握ajax技术：XHR、fetch；token技术

  15. 熟练使用 Vue2、Vue3；了解使用 bootstrap、element-ui





### 详细

- Redis(缓存、分布式锁、集群)
  - 三种客户端：Jedis、Lettuce、Redisson
  - 缓存：缓存穿透、缓存雪崩、**缓存击穿**。解决缓存击穿问题需要用的Redis锁，Redis锁又存在很多问题。
  - 分布式锁：
    - 分布式锁一定依赖于高可用，高可用由于集群锁挂钩。
    - 用Redis实现分布式锁的考虑因素很多，而Redisson对我们所能想到的要求都做了实现。
    - Redisson实现了包括：有可重入锁、联锁、**信号量、闭锁**、**红锁**。
  - 集群：
    - 哨兵模式：高可用、高并发读
    - 分片集群：高可用、高并发读/写、海量数据存储





- 基础
  - Mybatis
    - JDBC：
      - DriverManger是如何注册驱动的
      - MySQL5后，为什么不用添加`Class.forName("com.mysql.jdbc.Driver")`
      - Connection(两大作用：获取三种statement；管理事务)、Statement、数据库连接池的作用
    - Mybatis常用功能
      - typeAliases
      - mapper代理
  - JavaWeb：
    - 三大组件：Servlet、Filter、Listener
    - Servlet的生命周期
    - Servlet继承体系结构
    - Request继承体系结构
    - Response继承体系结构
  - Spring
    - BeanDefinition的加载流程
    - bean的生命周期
    - 8种加载bean的方式
    - bean的加载控制：编程式、注解式
    - ApplicationContext继承体系
      - 从BeanFactory接口到AbstractApplicationContext接口，都没有注册BeanDefinition的方法
      - 直到GenericApplicationContext（继承了AbstractApplicationContext）实现了BeanDefinitionRegistry接口才获得了注册BeanDefinition的能力
    - AOP
    - Spring事务：事务传播行为、事务隔离级别
  - SpringMVC















































