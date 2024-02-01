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















































