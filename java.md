# Linux



## 基础篇

#### 网络连接的三种方式

- 桥接模式：虚拟系统可以和外部系统通讯，但是容易造成IP冲突
- NAT模式：网络地址转换模式，虚拟系统可以和外部系统通讯，不造成IP冲突
- 主机模式：独立的系统



#### 目录结构

- /bin：【常用】（/usr/bin、/usr/local/bin）是Binary的缩写，存放着最常用的命令
- /sbin：（/usr/sbin、/usr/local/sbin）是系统管理员使用的系统管理程序
- /home：【常用】存放普通用户的主目录，在Linux中每个用户都有一个自己的目录
- /root：【常用】系统管理员的用户目录
- /lib：系统开机所需要的最基本动态连接共享库
- /lost+found：这个目录一般情况下是空的，当系统非法关机后，会在这存放一些文件
- /etc：【常用】所有的系统管理所需要的配置文件和子目录，比如安装mysql数据库的my.conf
- /usr：【常用】这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下
- /boot：【常用】存放的是启动Linux时使用的已一些核心文件，包括一些连接文件和镜像文件
- /proc：这是个虚拟目录，它是系统内存的映射，访问这个目录来获取系统信息
- /srv：service的缩写，该目录存放一些服务启动之后需要提供的数据
- /sys：这是linux2.6内核的一个很大的变化，该目录下安装了2.6内核中新出现的一个文件系统sysfs
- /tmp：这个目录是用来存放一些临时文件的
- /dev：类似于windows的设备管理器，把所有的硬件用文件的形式存储
- /media：【常用】linux系统会自动识别一些设备，如u盘，光驱等，识别后，把设备挂载到这个目录
- /mnt：【常用】系统提供该目录是为了让用户临时挂载别的文件系统的，可以将外部存储挂载在/mnt/上，然后进入该目录就可以查看里面的内容了。myshare
- /opt：这是给主机额外安装软件所摆放的目录。如安装ORACLE数据库就可以放到该目录下。
- /usr/local：【常用】这是另一个给主机上的软件安装的目录，一般是通过编译源码方式安装的程序
- /var：存放着不断扩充着的东西，习惯将经常被修改的目录放到这个目录下，包括各种日志文件
- /silinux [security-enhanced linux]：SELinux是一种安全子系统，它能控制程序只能访问特定的文件，有三种工作模式，可以自行设置



#### vim

- 拷贝当前行 yy，拷贝当前行向下的5行 5yy，并粘贴（输入p）
- 删除当前行 dd，删除当前行向下的5行 5dd
- 在文本中查找某个单词（命令行下 /关键词，回车查找，输入 n 就是查找下一个）
- 设置文件的行号，取消文件的行号（命令行下 :set nu 和 :set nonu）
- 编辑 /etc/profile 文件，一般模式下，使用快捷键到该文件的最末行（G）和最初行（gg）
- 在一个文本中输入“hello”，然后又撤销这个动作（一般模式下：u）
- 编辑 /etc/profile 文件，并将光标移动到20行（一般模式下：20+Shift+g）



#### 基本操作

- 关机重启

  - shutdown -h now（立即关机）

  - shutdown -h 1（1分钟后关机）

  - shutdown -r now（立即重启）

  - halt（关机，作用和上面一样）

  - reboot（立即重启）

  - sync（把内存的数据同步到磁盘）

- 用户管理

  - 用户注销：
    - 提示符下输入 logout 即可注销用户
    - logout 注销指令在图形运行级别无效，在运行级别3下有效
  - 添加用户：
    - useradd 用户名
    - useradd -d 指定目录 新的用户名
    - passwd 用户名
  - 删除用户：
    - 保留home目录：userdel 用户名
    - 全部删除：userdel -r 用户名
  - 查询用户：id 用户名
  - 切换用户：
    - su - 用户名
    - 权限高的用户到权限低的用户不需要密码
  - 当前用户：who am i
  - 添加组：groupadd 组名
  - 删除组：groupdel 组名
  - 添加用户到组：useradd -g 组名 用户名
  - 修改用户的组：usermod -g 组名 用户名
  - 相关文件
    - /etc/passwd：用户的配置文件，记录了用户的各种信息
    - /etc/shadow：口令的配置文件
    - /etc/group：组的配置文件，记录Linux包含的组的信息

- 环境变量

  - vim /etc/profile

  - 文件末尾添加

    ```ini
    export JAVA_HOME=/usr/local/java/jdk1.8.0_333
    export PATH=$JAVA_HOME/bin:$PATH
    ```

    ```shell
    # 刷新环境变量
    source /ect/profile
    # 查看环境变量
    echo $PATH
    ```




#### 运行级别

- 0：关机
- 1：单用户，可找回密码
- 2：多用户状态没有网络服务
- 3：多用户状态有网络服务
- 4：系统未使用保留给用户
- 5：图形界面
- 6：系统重启
- 切换不同级别：init 级别
- CentOS7后运行级别说明
  - 在centos7以前，/etc/inittab文件中。
  - 7后进行简化
    - multi-user.target: analogous to runlevel 3
    - graphical.target: analogous to runlevel 5
  - 查看运行级别：systemctl get-default
  - 设置默认运行级别：systemctl set-default multi-user.target/graphical.target



#### 一些指令

- \>（输出重定向）和\>>（追加）
  - ls -l > 文件：列表的内容覆盖写入指定文件中
  - ls -al >> 文件：列表的内容追加到指定文件中
  - cat 文件1 > 文件2：将文件1的内容覆盖到文件2中
  - echo "内容" >> 文件：追加内容到指定文件

- ln指令：创建一个软（或硬）链接
  - 基本语法：ln [-s] 源文件或目录 软连接名
  - 携带参数 -s：创建软链接（快捷方式）
  - 不携带参数 -s：创建硬链接（复制并让内容保持同步）
- history指令
  - history	：看最近执行的全部命令
  - history 条数：看最近的指定条数
- date指令
  - date：显示当前时间
  - date +%Y：显示当前年份
  - date +%m：月
  - date +%d：日
  - date "+%Y-%m-%d %H:%M:%S"：年-月-日 时:分:秒
  - 设施日期：date -s "2020-01-01 12:00:00"
  - cal (年份)：日历指令
- find指令
  - find 位置 -name 文件名
  - find 位置 -user 用户名
  - find 位置 -size +大小（+n大于、-n小于、n等于，单位有k，M，G）
- locate指令
  - 先执行updatedb
  - locate 文件名

- grep和管道指令 |：
  - cat 文件 | grep (-n显示行数) (-i不区分大小写) "字段"
  - grep (-n) "字段" 文件
- 解压与压缩
  - gzip 文件
  - gunzip 文件.gz
  - zip -r 压缩后的文件名.zip 文件
  - unzip -d 解压的地址 需要解压的文件.zip
  - tar
    - -z：打包的同时压缩
    - -c：创建.tar的打包文件
    - -v：显示运行过程的详细信息
    - -f：指定压缩后的文件名
    - -x：解包.tar文件
    - tar -zcvf 文件名.tar.gz 文件
    - tar -zxvf 需要解压的文件.tar.gz -C 解压的位置









# Java SE



## <a href="https://www.bilibili.com/video/BV16J411h7Rd/?p=209&vd_source=25ad2de4838bd28372a4956bac63c618">线程池</a>

### 快速入门

```java
public ThreadPoolExecutor(int corePollSize, // 核心线程数
                        int maximumPoolSize, // 最大线程数
                        long keepAliveTime, // 最大空闲时间
                        TimeUnit unit, // 时间单位
                        BlockingQueue<Runnable> workQueue, // 任务队列
                        ThreadFactory threadFactory, // 线程工厂
                        RejectedExecutionHandler handler) // 饱和处理机制
```

- 在总线程数量没有达到核心线程数之前，若任务队列有线程，会持续创建线程，直至达到核心线程数
- 任务队列满后，若总线程数量未达到最大线程数，则开辟新线程处理任务



### ExecutorService

- 成员方法

  - void shutdown()：启动一次顺序关闭，执行以前提交的任务，但不再接收新任务
  - List\<Runnable> shutdownNow()：停止所有正在执行的任务，暂停处理等待的任务，并返回等待执行的任务队列 
  - \<T> Future\<T> submit(Callable\<T> task)：执行带返回值的任务，返回一个表示该任务的Future对象
  - Future\<?> submit(Runable task)：执行Runable任务，并返回一个表示该任务的Future
  - \<T> Future\<T> submit(Runnable task, T result)：执行Runnable任务，并返回一个表示该任务的Future对象

- Future

  ```java
  public interface Future<V> {
      // true：取消正在等待执行、正在执行的任务，无法取消已经完成的任务
      // false：取消正在等待执行的任务，无法取消正在执行、已经完成的任务
      boolean cancel(boolean mayInterruptIfRunning);
      // 判断任务是否被取消
      boolean isCancelled();
      // 判断任务是否完成
      boolean isDone();
      // 阻塞等待获得任务结果
      V get() throws InterruptedException, ExecutionException;
      // 阻塞等待一定时间，获得任务结果
      V get(long timeout, TimeUnit unit)
          throws InterruptedException, ExecutionException, TimeoutException;
  }
  ```

  

- 获得对象

  - 介绍
    - ExecutorService是一个接口
    - 可通过Executors类中的静态方法，获得ExecutorService的对象
  - Executors
    - static ExecutorService newCachedThreadPool()：不定线程数
    - static ExecutorService newCachedThreadPool(ThreadFactory factory)
    - static ExecutorService newFixedThreadPool()：指定线程数
    - static ExecutorService newFixedThreadPool(ThreadFactory factory)
    - static ExecutorService newSingleThreadExecutor()：单一线程
    - static ExecutorService newSingleThreadExecutor(ThreadFactory factory)





## jdk新特性

> 语法上的新特性

### jdk8

#### lambda

- 快速入门

  - 原始

    - ```java
      Comparator<Customer> comparator1 = new Comparator<Customer>() {
          @Override
          public int compare(Customer o1, Customer o2) {
              return o1.getAge() - o2.getAge();
          }
      };
      ```

  - lambda

    - ```java
      Comparator<Customer> comparator2 = (o1, o2) -> o1.getAge()-o2.getAge();
      ```

- 语法

  - 格式
    - ->：lambda 操作符 或 箭头操作符
    - ->左边：lambda形参列表（接口中的抽象方法的形参列表）
    - ->右边：lambda体（重写的抽象方法的方法体）
  - 6种情况
    - 无参，无返回值
    - lambda需要一个参数，但是没有返回值
    - 数据类型可以省略，因为可由编译器推断得出，称为”类型推断“
    - lambda若只需要一个参数时，参数的小括号可以省略
    - lambda需要两个或以上的参数，多条执行语句，并且可以有返回值
    - 当lambda只有一条语句时，return与大括号若有，都可以省略



#### 函数式接口

- 定义：只有一个方法的接口

- java内置四大核心函数式接口

  - | 函数式接口      | 参数类型 | 返回类型 | 方法              |
    | :-------------- | :------- | -------- | ----------------- |
    | Consumer\<T>    | T        | void     | void accept(T t)  |
    | Supplier\<T>    | 无       | T        | T get()           |
    | Function\<T, R> | T        | R        | R apply(T t)      |
    | Predicate \<T>  | T        | boolean  | boolean test(T t) |

  - ```java
    @Test
    public void test() {
        List<String> list = Arrays.asList("北京", "南京", "天津", "东京");
        List<String> resultList = filterString(list, s -> s.contains("京"));
        System.out.println(resultList);
    }
    
    public List<String> filterString(List<String> list, Predicate<String> predicate) {
        ArrayList<String> filterList = new ArrayList<>();
        list.forEach(str -> {
            if (predicate.test(str)) filterList.add(str);
        });
        return filterList;
    }
    ```

- 其他接口

  - | 函数式接口                                                   | 参数类型              | 返回类型              | 方法                   |
    | ------------------------------------------------------------ | --------------------- | --------------------- | ---------------------- |
    | UnaryOperator\<T>                                            | T                     | T                     | T apply(T t)           |
    | BiFunction\<T, U, R>                                         | T, U                  | R                     | R apply(T t, U u)      |
    | BinaryOperator\<T>                                           | T, T                  | T                     | T apply(T t1, T t2)    |
    | BiConsumer\<T, U>                                            | T, U                  | void                  | void accept(T t, U u)  |
    | BiPredicate\<T, U>                                           | T, U                  | boolean               | boolean test(T t, U u) |
    | ToIntFunction\<T><br>ToLongFunction\<T><br>ToDoubleFunction\<T> | T                     | int<br>long<br>double |                        |
    | IntFunction\<R><br>LongFunction\<R><br>DoubleFunction\<R>    | int<br>long<br>double | R                     |                        |



#### 方法引用

- 方法引用

  - 本质就是lambda表达式，而lambda表达式作为函数式接口的实例。所以方法引用也是函数式接口的实例

  - 使用格式： 类（或对象）**::** 方法名

  - 使用要求

    - 要求函数式接口中的方法的形参列表和返回值类型与方法引用的方法的形参列表和返回值类型相同

  - 具体情况

    - 对象 **::** 非静态方法

    - 类 **::** 静态方法

    - 类 **::** 非静态方法

      - ```java
        BiPredicate<String, String> biPredicate1 = new BiPredicate<String, String>() {
            @Override
            public boolean test(String s, String s2) {
                return s.equals(s2);
            }
        };
        BiPredicate<String, String> biPredicate2 = (s1, s2) -> s1.equals(s2);
        BiPredicate<String, String> biPredicate3 = String::equals;
        System.out.println(biPredicate3.test("abc", "abc"));
        ```

- 构造器引用

  - 使用要求

    - 要求函数式接口中的方法的形参列表与构造器引用的形参列表相同，返回值为构造器创建的对象

  - ```java
    // 调用有参构造器，函数式接口方法：User apply(Integer integer)
    Function<Integer, User> function = User :: new;
    // 调用无参构造器，函数式接口方法：User get()
    Supplier<User> supplier = User :: new;
    ```

- 数组引用

  - ```java
    Function<Integer, String[]> function1 = length -> new String[length];
    Function<Integer, String[]> function2 = String[] :: new;
    ```



#### Stream

- stream是操作渠道，用于操作数据源（集合、数组等》所生成的元素序列

- 注意

  - Stream 自己不会存储元素
  - Stream 不会改变源对象。相反，会返回一个持有结果的新Stream
  - Stream 操作是延时执行的。这意味着它会等到需要结果的时候才执行

- Stream 操作的三个步骤

  - 创建 Stream

    - 通过集合获取

      - ```java
        List<String> list = Arrays.asList("北京", "南京", "天津", "东京");
        Stream<String> stream = list.stream();
        // parallelStream其实就是一个并行执行的流.它通过默认的ForkJoinPool,可能提高你的多线程任务的速度
        Stream<String> parallelStream = list.parallelStream();
        ```

    - 通过数组获取

      - ```java
        String[] arr = {"北京", "南京", "天津", "东京"};
        Stream<String> stream = Arrays.stream(arr);
        ```

    - 通过Stream的of方法

      - ```java
        Stream<String> areas = Stream.of("北京", "南京", "天津", "东京");
        ```

    - 创建无限流

      - ```java
        // Stream.iterate(T seed, UnaryOperator<T> f)
        Stream.iterate("23", t -> t + "3");
        // Stream.generate(Supplier<? extends T>)
        Stream.generate(() -> "Hello,world")
        ```

  - **中间操作**

    - 筛选与切片

      - 过滤：filter(Predicate p)
      - 截断：limit(int maxSize)
      - 跳过：skip(long n)
      - 筛选：distinct()（通过hashCode()和equals()方法去重）

    - 映射

      - map(Function f)

        - 接收一个转化规则函数作为参数，将Stream中的元素按规则转成其他形式

        ```java
        Customer customer = new Customer(1, "张三", 28, true);
        List<Customer> list = Arrays.asList(customer);
        list.stream()
            .map(Customer::getName).filter((str) -> str.length()<3)
            .forEach(System.out::println);
        ```

      - flatMap(Function f)

        - 接收一个转化规则作为参数，将Stream中的元素按规则转成Stream，最终形成Stream套Stream

    - 排序
  
      - 自然排序：sorted()
        - 按 Stream中元素实现Comparable\<T>接口后重写的compareTo方法 进行排序
      - 规则排序：sorted(Comparator com)

  - **终止操作**

    - 匹配与查找
  
      - allMatch(Predicate p)：检查所有元素是否匹配
      - anyMatch(Predicate p)：检查是否有元素匹配
      - noneMatch(Predicate p)：检查是否没有匹配的元素
      - findFirst()：返回第一个元素
      - findAny()：返回当前流中的任意元素
  
      - count()：返回流中元素总数
      - max(Comparator c)：返回流中的最大值
      - min(Comparator c)：返回流中的最小值
      - forEach(Consumer c)：内部迭代
    
  - 规约
    
    - reduce(T identity, BinaryOperator b)：可以将流中的元素反复结合起来，得到一个值
      - reduce(BinaryOperator b)
    - 收集
  
      - collect(Collector c)
      - Collectors的toList，toMap和toCollection方法可以获得一个Collector对象
  
- 串行流与并行流

  - 串行流：`.sequential()`

  - 并行流：`.parallel()`

    - 默认使用的线程池：ForkJoinPool.commonPoll

      - 默认使用的线程数是当前CPU线程个数

      - 修改默认的线程数

        ```java
        System.setProperty("java.util.concurrent.ForkJoinPool.common.parallelism", "20");
        ```

    - 指定线程池（测试的时候注意：ForkJoinPool使用的是守护线程）

      ```java
      ForkJoinPool pool = new ForkJoinPool(20);
      pool.submit(() -> InStream.range(1, 100).parallel());
      ```
    
    - 规范：为确保并发安全问题，应保证并行流的所有操作都是无状态的（除非加锁），即数据处理仅在流中对应函数内，不应该溢出函数
    
      ```java
      List<Integer> list = new ArrayList<>(); // list 在函数外
      
      Stream.of(1, 2, 3, 4, 5)
          .parallel()
          .filter (i -> {
              // list.add(i); // 不应该有此句
              return i > 2;
          })
          .count();
      ```
    
      


#### Option

- Option of(T t)：返回一个包含t值的容器，t不允许为null
- Option empty()：返回一个空容器
- Option ofNullable(T t)：返回一个包含t值的容器，t允许为null
- T get()：如果调用对象包含值，则返回该值，否则抛异常
- T orElse(T other)：如果有值，则返回该值，否则返回指定的other对象





### jdk9

#### FlowAPI

- Flow 是一个 `final class` ，类内定义了几个接口

  - `Publisher<T>`：发布者
    - `void subscribe(Subscriber<? super T> subscriber)`：绑定订阅者
  - `Subscriber<T>`：订阅者
    - `void onSubscribe(Subscription subscription)`：绑定订阅关系
    - `void onNext(T item)`：处理数据
    - `void onError(Throwable throwable)`：处理数据时出现异常，会调用此方法
    - `void onComplete()`：**发布者**关闭时调用
  - `Subscription`：订阅关系：是一种合同，订阅者通过此向发布者按需索取数据
    - `void request(long n)`：索取数据
    - `void cancel()`：不再接收数据
  - `Processor<T, R>`：处理器
    - 该接口 `extends Subscriber<T>, Publisher<R>`：既是发布者又是订阅者，用于接收发布者的发布的数据（订阅者），又将数据发布给订阅者（发布者）
    - T：接收数据类型
    - R：发布数据类型

- 实例

  - 订阅者

    ```java
    public class IntegerSubscriber implements Flow.Subscriber<Integer> {
        private Flow.Subscription subscription;
        @Override
        public void onSubscribe(Flow.Subscription subscription) {
            this.subscription = subscription;
            // 请求一个数据
            this.subscription.request(1);
        }
    
        @Override
        public void onNext(Integer item) {
            System.out.printf("接收器接收到数据【%s】，并处理\n", item);
            // 数据处理完后
            this.subscription.request(1); // 再请求一个数据
            //this.subscription.cancel(); // 已经达到目标，不再接收数据
        }
    
        @Override
        public void onError(Throwable throwable) {
            System.out.println("处理数据时出现异常");
            // 不再接收数据
            this.subscription.cancel();
        }
    
        @Override
        public void onComplete() {
            System.out.println("订阅者处理完成");
        }
    }
    ```

  - 处理器

    ```java
    public class IntegerProcessor extends SubmissionPublisher<Integer> implements Flow.Processor<Integer, Integer> {
        private Flow.Subscription subscription;
        private Predicate<Integer> predicate;
    
        public IntegerProcessor addPredicate(Predicate<Integer> predicate) {
            this.predicate = predicate;
            return this;
        }
    
        @Override
        public void onSubscribe(Flow.Subscription subscription) {
            this.subscription = subscription;
            this.subscription.request(1);
        }
    
        @Override
        public void onNext(Integer item) {
            System.out.printf("处理器得到数据数据【%s】\n", item);
            if (predicate == null || predicate.test(item)) this.submit(item);
            else System.out.printf("拦截数据【%s】\n", item);
            this.subscription.request(1);
        }
    
        @Override
        public void onError(Throwable throwable) {
            System.out.println("处理数据时出现异常");
            this.subscription.cancel();
        }
    
        @Override
        public void onComplete() {
            System.out.println("处理器处理完成");
        }
    }
    ```

  - 测试

    ```java
    Random random = new Random();
    SubmissionPublisher<Integer> publisher = new SubmissionPublisher<>();
    IntegerProcessor processor = new IntegerProcessor().addPredicate(integer -> integer > 5);
    Flow.Subscriber<Integer> subscriber = new IntegerSubscriber();
    
    publisher.subscribe(processor);
    processor.subscribe(subscriber);
    for (int i = 0; i < 10; i++) {
        publisher.submit(random.nextInt(10));
    }
    publisher.close();
    Thread.currentThread().join(1000);
    ```

- **※ 背压**：订阅者如何调节发布者生产数据的

  ```java
  for (int i = 0; i < 300; i++) {
      // submit 是一个阻塞方法。
      // 一旦订阅关系中存储的数据达到阈值（256），该方法就会阻塞，就无法继续生产数据
      publisher.submit(random.nextInt(10));
  }
  ```



#### 模块化系统

- 创建 module-info.java 文件

  - 暴露

    - ```java
      module customerServer{
          exports cn.Eli.domain;
      }
      ```

  - 引进

    - ```java
      module orderServer{
          requires openFeign;
      }
      ```



#### 接口

- java 8中规定接口中的方法除了抽象方法之外，还可以定义静态方法和默认方法。
- java 9中，方法的访问权限修饰符也可以声明为private

```java
public interface MyInterface {
    // 如下三个方法 java8 可用，权限修饰符省略，都是public
    void methodAbstract();
    
    // 接口中的静态方法只能由接口自己调用，实现类不能调用 MyInterface.methodStatic()
    static void methodStatic() {
        System.out.println("静态方法");
    }
    
    default void methodDefault() {
        System.out.println("默认方法");
    }
    
    // java9 特性：允许接口中定义私有方法，私有方法不能在接口外部使用
    private void methodDefault() {
        System.out.println("私有方法");
    }
}
```



#### 钻石

```java
// jdk7 中特性：类型推断，后面的 String等 可以省略
ArrayList<String> strings = new ArrayList<>();

// jdk9 中特性：后面的 Object等 可以省略
Comparator<Object> comparator = new Comparator<>() {
    @Override
    public int compare(Object o1, Object o2) {
        return 0;
    }
};
```



#### try

- java8 前，资源释放在 finally{} 中

- java8 可在try后的小括号中声明需要释放的资源

  - ```java
    // 要求执行后必须关闭的所有资源必须在try子句中初始化
    try (InputStreamReader isr = new InputStreamReader(System.in)) {
        // IO 操作
    } catch (IOException e) {
        throw new RuntimeException(e);
    }
    ```

- java9 资源的初始化可以放在外面

  - ```java
    InputStreamReader isr = new InputStreamReader(System.in);
    // 此时的资源属性是常量，声明为final，不可修改
    try(isr) {
        // IO 操作
    } catch (IOException e) {
        throw new RuntimeException(e);
    }
    ```

    

#### 其他特性

- 目录结构变更

- JShell：java 的REPL（交互式编程环境）
- String底层由原先的 char[] 变更为 byte[]
- 不可修改集合
  - 调用 Collections 的 unmodifiableCollection(Collection c)、unmodifiableList(List l)、unmodifiableSet(Set s) 返回的集合是不可修改集合
  - List/Set/Map 新增of方法，返回的集合是不可修改集合
  - 补充：Arrays.asList(Object ... ) 返回的集合也是不可修改集合
- InputStream 中的 transferTo(OutputStream os) 方法可以把输入流中的所有数据直接自动复制的输出流中
- Stream增强
  - takeWhile(Predicate p)：中间操作，从头开始一直读取数据直到predicate断定为false
  - dropWhile(Predicate p)：中间操作，从prediate断定为false开始一直往后读取数据
  - ofNullable(T t)：创建操作，能且只能存储一个值，该值可以为null
    - of(T ... values)：创建操作，jdk8提供，存储多个值。但若存且只存入一个null，则报错
  - iterate(T seed, Predicate\<T> p, UnaryOperator\<T> f)：创建操作，jdk9重载方法
- Optional增强
  - Stream\<T> stream()：获得stream
  - ifPresentOrElse(Consumer action, Runnable emptyAction)：value不为空，执行前一个参数功能，否则执行后一个参数功能
  - Optional\<T> or(Supplier\<? extends Optional\<? extends T>> supplier)：value非空，返回对应的Optional；value为空，返回形参函数中返回的Optional
- 升级的Nashorn（为java提供轻量级的javascript运行，jdk11又移除）





### jdk10

#### 类型推断

- 声明变量时，根据所赋的值，推断出变量的类型

```java
var num = 10;
var list = new ArrayList<String>();
for(var i : list) {
    System.out.println(i.getClass());
}
```



#### 集合

- copyOf 方法：返回一个只读的集合

```java
// copyOf(Xxx coll) 如果传入的本身是一个只读的集合，则会返回该集合，否则则会创建一个只读的集合
var list1 = List.of("Java", "Pythod", "C");
var copy1 = List.copyOf(list1);
System.out.println(list1 == copy1); // true

var list2 = new ArrayList<String>();
var copy2 = List.copyOf(list2);
System.out.println(list2 == copy2); // false
```





### jdk11

#### jre

```shell
bin\jlink.exe --module-path jmods --add-modules java.desktop --output jre
```



#### String

- isBlank()：判断字符串是否为空白
- strip()：去除首尾的空白（相比于trim()方法，可去除全角符号）
- stripTrailing()：去除尾部的空白
- stripLeading()：去除首部的空白
- repeat(int times)：复制字符串
- "A\nB\nC".lines().count()：行数统计



#### 其他特性

- ZGC

  ```shell
  -XX:+UseZGC
  ```

- 局部变量类型推断的升级

- 支持编译解释一次完成：java HelloWorld.java

- HttpClient（jdk9提出）升级

- 废弃 Nashorn 引擎





### jdk12-17

#### switch

- 原switch

  ```java
  int days = 21;
  String desc;
  switch (days/10) {
      case 1:
          desc = "一旬";
          break;
      case 2:
          desc = "二旬";
          break;
      default:
          desc = "其他";
          break;
  }
  ```

- 增强 switch（jdk12）：只支持对数值和字符串常量匹配

  ```java
  int days = 21;
  String desc = switch (days/10) {
      case 1 -> "一旬";
      case 2 -> "二旬";
      default -> "其他";
  }
  ```

- 增强 switch（jdk17）

  - 匹配数值

    ```java
    int days = 21;
    String desc = switch (days/10) {
        case 0, 1, 2 -> {
            System.out.println("一个月不到了！");
            yield "一月以内";
        }
        case 3, 4, 5 -> "两月以内";
        default -> "两月以上";
    }
    ```

  - 匹配对象类型

    ```java
    String type = switch(object) {
            case null -> "null";
            case Integer i -> "是个Integer数值";
            default -> "其他类型";
    }
    ```



#### 密封类

```java
// sealed 标注的类，只能有 permits 指定的类继承
public sealed class Animal permits Dog, Cat {
    
}

// 子类必须被 final 或 non-sealed 修饰
// 子类和父类必须同包
public final class Cat {}
public non-sealed class Dog {}
```



#### Record类

```java
public class RecordTest {
    public static void main(String[] args) {
        User userRecord1 = new User(1L, "zhangsan");
        User userRecord2 = new User(1L, "zhangsan");
        // 属性值相同的不同record对象，equals 为 true
        System.out.println(userRecord1.equals(userRecord2))
    }
}

// 提供且只提供 全参构造方法 和 属性获取方法
// 并重写 equals hashcode toString 方法
// 但中途不能修改属性
public record User(Long userId, String name) {}
```



#### 其他特性

- 文本块

  - jdk15

    ```java
    String html = """
        <html>
            <body>
                <h1>%s</h1>
            </body>
        </html>
        """;
    
    String parseHtml = String.format(html, "这是一个标题");
    ```

  - jdk17：新增符号

    - \：至于行尾，用来将两行连接为一行
    - \s：单个空白字符

- 增强 instanceof（jdk14预览版提出，jdk16正式加入）

  - 原

    ```java
    Object o = 1;
    if (o instanceof Integer) {
        Integer i = (Integer) o; // 需要强转
        System.out.println("这是一个Integer，值为："+i);
    }
    ```

  - 新

    ```java
    Object o = 1;
    if (o instanceof Integer i) {
        System.out.println("这是一个Integer，值为："+i);
    }
    ```

- 空指针异常优化：指出那个对象报出空指针异常

- ZGC垃圾收集器：

  - 优化
    - STW：垃圾回收时触发，所有工作线程停止，会出现卡顿，jdk17将 STW 时间控制在10ms内，垃圾回收时不卡顿
    - jdk17 堆内存可以设置特别大，减少垃圾回收次数
  - 使用：jdk11和jdk17默认垃圾回收器依旧是G1
    - 添加JVM选项即可开启ZGC   `-xx:+UseZGC`





### jdk18-21

#### switch

- jdk17

  ```java
  record Point(int x, int y) {}
  
  static void printSum(Object obj) {
      if (obj instanceof Point(int x, int y)) {
          System.out.println(x+y);
      }
  }
  ```

- jdk21

  ```java
  record Point(int x, int y) {}
  
  static void printSum(Object obj) {
      switch(obj) {
          case Point(int x, int y) -> System.out.println(x+y);
          case null, default -> {
              // Do something
          }
      }
  }
  ```









# 结构与算法



## 数据结构

### 分类

- 线性结构：数据之间存在一对一的线性关系，包括数组、队列、链表、栈
  - 顺序存储结构
    - 顺序储存的线性表称为顺序表，顺序表中的存储元素是连续的
  - 链式存储结构
    - 链式存储的线性表称为链表，链表存储的元素不一定是连续的，元素节点存放数据元素以及相邻元素的地址信息
- 非线性结构：包括二维数组、多维数组、广义表、树结构、图结构



### 稀疏数组

- 当一个数组中大部分元素是0，或者同一个值的数组时，可以用稀疏数组来保存该数组

![](D:\picture\typora\java\1234567890.png)

- 二维数组转稀疏数组
  - 遍历二维数组，得到有效数据的个数
  - 根据sum就可以创建稀疏数组sparseArr int[sum+1]\[3]
  - 将二维数组的有效数据存入稀疏数组中
- 稀疏数组转原始数组
  - 先读取稀疏数组的第一行，根据第一行的数据，创建原始的二维数组
  - 再读取稀疏数组的后几行，并赋值给原始的二维数组



### 其他结构

- 队列

  - 队列是一个有序列表，可以用数组或是链表来实现

  - 遵循先入先出的原则


- 链表
  - 单项链表
  - 双向链表
  - 环形链表
- 栈
  - 前缀表达式（波兰表达式）
  - 中缀表达式
  - 后缀表达式（逆波兰表达式）
  - 中缀表达式转后缀表达式
  - 递归
- 哈希表



### 二叉树

- 遍历

  - 前序：父节点 -> 左子树 -> 右子树

  - 中序：左子树 -> 父节点 -> 右子树

  - 后序：左子树 -> 右子树 -> 父节点

    ![二叉树](D:\picture\typora\java\二叉树.jpeg)

- 顺序存储二叉树

  - 顺序存储二叉树只考虑完全二叉树
  - 第n个元素的左子节点为 2*n+1
  - 第n个元素的右子节点为 2*n+2
  - 第n个元素的父节点为 (n-1)/2
    - n表示二叉树的第几个元素（从上到下，从左到右）

- 线索化二叉树

  - 利用二叉树链表中的空指针域，存放指向该节点在**某种遍历次序下**的前驱和后继节点的指针
    - 一个节点的前一个节点叫**前驱**节点，后一个节点叫**后继**节点
  - 遍历线索化二叉树

- 霍夫曼树

- 二叉排序树

- 平衡二叉树

- B树、B+树、B*树



### 图

- 常用概念
  - 顶点
  - 边
  - 路径
  - 无向图
  - 有向图
  - 带权图
- 表达方式
  - 二维数组表示（邻接矩阵）
  - 链表表示（邻接表）
- 遍历
  - 深度优先遍历
    - 压栈与弹栈

  - 广度优先遍历
    - 入队与出队











## 算法

### 排序

- 插入排序
  - 直接插入排序
  - 希尔排序
- 选择排序
  - 简单选择排序
  - 堆排序
- 交换排序
  - 冒泡排序
  - 快速排序
- 归并排序
- 基数排序
- 堆排序

| 排序法 | 平均时间    | 最差情况      | 稳定度 | 额外空间 | 备注                    |
| ------ | ----------- | ------------- | ------ | -------- | ----------------------- |
| 冒泡   | O(n²)       | O(n²)         | 稳定   | O(1)     | n较小时好               |
| 交换   | O(n²)       | O(n²)         | 不稳定 | O(1)     | n较小时好               |
| 选择   | O(n²)       | O(n²)         | 不稳定 | O(1)     | n较小时好               |
| 插入   | O(n²)       | O(n²)         | 稳定   | O(1)     | 大部分已排序时较好      |
| 基数   | O(log~R~ B) | O(log~R~ B)   | 稳定   | O(n)     | B（0~9）<br>R（个十百） |
| Shell  | O(logn)     | O(n^s^) 1<s<2 | 不稳定 | O(1)     | 指数大小取决于所选分区  |
| 快速   | O(logn)     | O(n²)         | 不稳定 | O(nlogn) | n较大时好               |
| 归并   | O(logn)     | O(nlogn)      | 稳定   | O(1)     | n较大时好               |
| 堆     | O(logn)     | O(nlogn)      | 不稳定 | O(1)     | n较大时好               |

- 解释
  - 稳定：原数组中相同的元素，经过排序后可能不再维持原排序



### 查找

- 线性查找
- 二分法查找
  - mid=(low+high)/2=low+(high-low)/2
- 插值查找
  - mid=low+(key-arr[low])/(arr[high]-arr[low])*(high-low)
- 斐波那契查找
  - mid=low+F(k-1)-1





### 十大算法

#### 分治算法

- 分治算法在每一层递归上都有三个步骤

  - 分解：将原问题分解为若干个规模较小，相互独立，与原问题形式相同的子问题
  - 解决：若子问题规模较小而容易被解决则直接解决，否则递归地解决各个子问题
  - 合并：将各个子问题的解合并为原问题的解

- 汉诺塔问题

  - ```java
    public static void hanoiTower(int num, char a, char b, char c) {
        if (num = 1) {
            System.out.println("第1个盘从 " +a+ "->" +c);
        } else {
            // 如果有 n>2 的情况，我们总是可以看作为两个盘：最下面的盘和上面所有的盘作为一个整体
            // 先把最上面的所有盘从a->b，期间用到c
            hanoiTower(num - 1, a, c, b);
            System.out.println("第"+ num +"个盘从 " +a+ "->" +c);
            // 把b塔中所有的盘从b->c，期间用到a
            hanoiTower(num - 1, b, a, c);
        }
    }
    ```



#### 动态规划算法



#### KMP算法

- 暴力匹配算法



#### 贪心算法















# 设计模式



## 设计原则

- 单一职责原则
  - 应该有且仅有一个原因引起类的变更
- 接口隔离原则
  - 客户端不应该依赖它不需要的接口，或者说类间的依赖关系应该建立在最小的接口上
- 依赖倒置原则
  - 抽象不应该依赖细节，细节应该依赖抽象
  - 依赖倒置的中心思想是面向接口编程
- 里氏替换原则
  - 子类必须完全实现父类的方法
  - 子类可以有自己的个性
  - 覆盖或实现父类的方法时，输入参数可以被放大
  - 覆盖或实现父类的方法时，输出结果可以被缩小
- 开闭原则
  - 一个软件实体如：类、模块和函数应该对扩展开放，对修改关闭。也就是说一个软件实体应该通过扩展来实现变化，而不是通过修改已有的代码来实现变化
- 迪米特原则
  - 一个对象应该对其他对象有最少的了解
- 合成复用原则
  - 尽量使用组合、聚合的方式，而不是高耦合的继承方式







## 设计模式



### 创建型模式

#### 单例模式

##### 构建单例

- 静态常量、静态代码块（饿汉式）

  ```java
  class Singleton01{
      // 类的内部创建
      private final static Singleton01 instance = new SingleTon01();
      /*private final static Singleton01 instance;
      static {instance = new Singleton01();}*/
      
      // 构造器私有化
      private Singleton01(){}
      
      // 向外暴露一个静态的公共方法。getInstance
      public static Singleton01 getInstance(){
          return instance;
      }
  }
  ```

  - 说明
    - 写法简单，在类装载的时候就完成实例化，避免了线程的同步问题
    - 在类装载是就完成实例化，没有达到Lazy Loading的效果，如果从始至终未使用过这个实例，则会造成内存的浪费

- 懒加载（懒汉式）

  ```java
  class Singleton02(){
      private Singleton02(){}
      
      private static Singleton instance;
      
      public static synchronized Singleton02 getInstance(){
          if(instance == null){
              instance = new Singleton02();
          }
          return instance;
      }
  }
  ```

  - 说明
    - 此方法上：不加同步处理，线程不安全；加同步处理，效率低

- 双重检查

  ```java
  class Singleton03(){
      private Singleton03(){}
      
      private static volatile Singleton instance;
      
      public static Singleton getInstance(){
          if(instance == null){
              synchronized (Singleton03.class){
                  if(instance == null){
                      instance = new Singleton03();
                  }
              }
          }
          return instance;
      }
  }
  ```

  - 说明
    - 线程安全，效率加快

- 静态内部类

  ```java
  class Singleton04(){
      private Singleton04(){}
      
      private static class SingleInstance{
          private static final Singleton04 INSTANCE = new Singleton4();
      }
      
      private static Singleton getInstance(){
          return SingletonInstance.INSTANCE;
      }
  }
  ```

  - 说明
    - 外部类装载时，静态内部类并不会立即装载
    - 类装载时，线程是安全的
    - 调用getInstance方法时，才会让静态内部类装载
    - 类的静态属性，只会在第一次加载类的时候初始化

- 枚举

  ```java
  public class SingletonTest {
      public static void main(String[] args){
          Singleton instance1 = Singleton.INSTANCE;
          Singleton instance2 = Singleton.INSTANCE;
          // instance1 == instance2
      }
  }
  
  enum Singleton {
      INSTANCE;
  }
  ```

  - 说明
    - jdk1.5添加的枚举可以实现单例模式
    - 不仅能避免线程同步问题，而且还能防止反序列化重新创建新的对象



##### 破坏单例

- 序列化反序列化

  - SingletonTest实现可序列化接口Serializable

  - 测试反序列化后，对象是否一致

    ```java
    public class App {
        public static void main(String[] args) throws Exception {
            // writeObjectToFile();
            SingletonTest instance1 = readObjectFromFile();
            SingletonTest instance2 = readObjectFromFile();
            System.out.println(instance1 == instance2); // false
        }
    
        public static SingletonTest readObjectFromFile() throws Exception {
            ObjectInputStream ois = new ObjectInputStream(new FileInputStream("C:\\Users\\123\\Desktop\\singletonTest.obj"));
            SingletonTest instance = (SingletonTest) ois.readObject();
            ois.close();
            return instance;
        }
    
        public static void writeObjectToFile() throws Exception {
            SingletonTest instance = SingletonTest.getInstance();
            ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("C:\\Users\\123\\Desktop\\singletonTest.obj"));
            oos.writeObject(instance);
            oos.close();
        }
    }
    ```

  - 解决方法

    ```java
    class Singleton04(){
        private Singleton04(){}
    
        private static class SingleInstance{
            private static final Singleton04 INSTANCE = new Singleton4();
        }
    
        private static Singleton getInstance(){
            return SingletonInstance.INSTANCE;
        }
    
        // 添加readResolve()方法，当进行反序列化时，会自动调用该方法，将该方法的返回值直接返回
        public Object readResolve(){
            return SingleInstance.INSTANCE;
        }
    }
    ```

- 反射

  - 破坏单例

    ```java
    public class App2 {
        public static void main(String[] args) throws Exception {
            // 1.获取SingletonTest的字节码对象
            Class<SingletonTest> clazz = SingletonTest.class;
            // 2.获取无参构造方法
            Constructor<SingletonTest> cons = clazz.getDeclaredConstructor();
            // 3.取消访问检查
            cons.setAccessible(true);
            // 4.创建Singleton对象
            SingletonTest singletonTest1 = cons.newInstance();
            SingletonTest singletonTest2 = cons.newInstance();
            System.out.println(singletonTest1 == singletonTest2); // false
        }
    }
    ```

  - 改善方法

    ```java
    class Singleton04(){
        // 这个方法也能通过反射改flag值来破坏
        private static boolean flag = false;
        private SingletonTest(){
            // 需要解决线程安全
            synchronized (SingletonTest.class){
                if (flag) throw new RuntimeException("不能创建多个对象");
                flag = true;
            }
        }
    
        private static class SingleInstance{
            private static final Singleton04 INSTANCE = new Singleton4();
        }
    
        private static Singleton getInstance(){
            return SingletonInstance.INSTANCE;
        }
    }
    ```






#### 简单静态工厂模式

- 简单静态工厂模式（不是23种设计模式之一）

  ```java
  public class SimpleCoffeeFactory {
      public static Coffee createCoffee(String type){
          switch (type){
              case "美式":
                  return new AmericanCoffee();
              case "拿铁":
                  return new LatteCoffee();
              default:
                  throw new RuntimeException("没有这种咖啡");
          }
      }
  }
  ```

  ```java
  public class CoffeeStore {
      public Coffee orderCoffee(String type){
          Coffee coffee = SimpleCoffeeFactory.createCoffee(type);
          coffee.addMilk();
          coffee.addSugar();
          return coffee;
      }
  }
  ```

  - 依旧违背开闭原则

- 可通过【工厂模式+配置文件】形式解耦合

  - CoffeeFactory

    ```java
    public class CoffeeFactory {
        // 存储配置文件的解析数据
        private static HashMap<String, Coffee> map = new HashMap<>();
    
        // 读取并解析配置文件
        static {
            Properties properties = new Properties();
            InputStream is = CoffeeFactory.class.getClassLoader().getResourceAsStream("bean.properties");
            try {
                properties.load(is);
                Set<Object> keys = properties.keySet();
                for (Object key : keys) {
                    String className = properties.getProperty((String) key);
                    Class clazz = Class.forName(className);
                    Coffee coffee = (Coffee) clazz.newInstance();
                    map.put((String) key, coffee);
                }
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        }
    
        // 根据名称获取对象
        public static Coffee createCoffee(String name){
            return map.get(name);
        }
    }
    ```

  - bean.properties

    ```properties
    american = cn.Eli.coffee.AmericanCoffee
    latte = cn.Eli.coffee.LatteCoffee
    ```






#### 工厂方法模式

- 定义抽象工厂类

  ```java
  public abstract class CoffeeFactory {
      public abstract Coffee createCoffee();
  }
  ```

- 细化工厂

  ```java
  public class AmericanCoffeeFactory extends CoffeeFactory{
      @Override
      public Coffee createCoffee() {
          return new AmericanCoffee();
      }
  }
  ```

- 使用工厂

  ```java
  public class CoffeeStore {
      private CoffeeFactory factory;
  
      public void setFactory(CoffeeFactory factory){
          this.factory = factory;
      }
  
      public Coffee orderCoffee(){
          Coffee coffee = factory.createCoffee();
          coffee.addMilk();
          coffee.addSugar();
          return coffee;
      }
  }
  ```

- 遵循开闭原则





#### 抽象工厂模式

- uml图

  ![抽象工厂模式](D:\picture\typora\java\uml\抽象工厂模式.png)

- 具体工厂类

  ```java
  public class AmericanDessertFactory implements DessertFactory{
      @Override
      public Coffee createCoffee() {
          return new AmericanCoffee();
      }
  
      @Override
      public Dessert createDessert() {
          return new MatchMouse();
      }
  }
  ```





#### 原型模式

- 概述

  - 定义：用一个已经创建的实例对象作为原型，通过复制该原型对象来创建一个和原型对象相同的新对象

  - 结构

    - 抽象原型类：规定了具体原型对象必须实现的 clone() 方法
    - 具体原型类：实现抽象原型类的 clone() 方法
    - 访问类：使用具体原型类中的 clone() 方法类复制新的对象

  - uml图

    ![原型模式](D:\picture\typora\java\uml\原型模式.png)

- 代码实现

  - 浅克隆：对于引用类型的属性，复制出来的对象中的属性仍指向原对象中属性的地址

    ```java
    public class Award implements Cloneable{
        private Student student;
        
        public Award(Student student){this.student = student;}
    
        @Override
        public Award clone() throws CloneNotSupportedException {
            return (Award) super.clone();
        }
    }
    ```

  - 深克隆：属性中引用的其他对象也会被克隆，不再指向原有地址

    - 可通过将对象序列化到硬盘上，再反序列化获得原对象的深克隆对象





#### 建造者模式

- 概述

  - 结构

    - 抽象建造者类(Builder)：这个接口规定要实现复杂对象的那些部分的创建，并不涉及具体的对象部件的创建
    - 具体建造者类(ConcreteBuilder)：实现Builder接口，完成复杂产品的哥哥部件的具体创建方法。在构造过程完成后，提供产品的实例
    - 产品类(Product)：要创建的复杂对象（要求：创建的产品要有较多共同点）
    - 指挥者类(Director)：调用具体建造者来创建复杂对象的各个部分，在指导者中不涉及具体产品的信息，只负责保证对象各部分完整创建或按某种顺序创建

  - uml图

    ![建造者模式](D:\picture\typora\java\uml\建造者模式.png)

- 代码实现

  - 抽象建造者

    ```java
    public abstract class Builder {
        protected Bike bike = new Bike();
    
        public abstract void buildFrame();
        public abstract void buildSeat();
        public abstract Bike createBike();
    }
    ```

  - 具体建造者

    ```java
    public class MobileBuilder extends Builder{
        @Override
        public void buildFrame() {bike.setFrame("铝合金车架");}
        
        @Override
        public void buildSeat() {bike.setSeat("真皮车座");}
        
        @Override
        public Bike createBike() {return bike;}
    }
    ```

  - 指挥者

    ```java
    public class Director {
        private Builder builder;
    
        public Director(Builder builder){this.builder = builder;}
    
        // 控制各部分是否组装，如何组装
        public Bike construct(){
            builder.buildFrame();
            builder.buildSeat();
            return builder.createBike();
        }
    }
    ```

- 拓展

  ```java
  public class Laptop {
      private String cpu;
      private String screen;
      private String memory;
      private String mainBoard;
  
      private Laptop(Builder builder){
          this.cpu = builder.cpu;
          this.screen = builder.screen;
          this.memory = builder.memory;
          this.mainBoard = builder.mainBoard;
      }
  
      public static final class Builder{
          private String cpu;
          private String screen;
          private String memory;
          private String mainBoard;
  
          public Builder cpu(String cpu){
              this.cpu = cpu;
              return this;
          }
          public Builder screen(String screen){
              this.screen = screen;
              return this;
          }
          public Builder memory(String memory){
              this.memory = memory;
              return this;
          }
          public Builder mainBoard(String mainBoard){
              this.mainBoard = mainBoard;
              return this;
          }
  
          public Laptop build(){
              return new Laptop(this);
          }
      }
  }
  ```

  ```java
  Laptop laptop = new Laptop.Builder()
      .cpu("intel")
      .screen("三星屏幕")
      .memory("金士顿内存条")
      .mainboard("华硕主板")
      .build();
  ```

  





### 结构型模式

#### 代理模式

- 静态代理模式

  - ```java
    public interface sellTickets {
        void sell();
    }
    ```

  - ```java
    public class TrainStation implements sellTickets {
        public void sell(){
            System.out.println("火车站卖票");
        }
    }
    ```

  - ```java
    public class ProxyPrint implements sellTickets {
        // 聚合火车站对象
        private TrainStation trainStation = new TrainStation();
        
        public void sell(){
            System.out.println("代售点收取一些手续费");
            trainStation.sell();
        }
    }
    ```

- jdk代理

  - ```java
    public class ProxyFactory {
        private Object target;
        
        public ProxyFactory(Object target) {
            this.target = target;
        }
        
        public Object getProxyInstance() {
            Object pi = Proxy.newProxyInstance(
                // 第一个参数：目标对象的类加载器
            	target.getclass().getClassLoader(),
                // 第二个参数：目标对象实现的所有接口
                target.getclass().getInterfaces(),
                new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args){
                        Object result = method.invoke(target, args);
                        return result;
                    }
                }
            );
            return pi;
        }
    }
    ```

  - 根据目标对象实现的接口得到目标对象中重写的方法，因此，目标对象中的方法没有对应的接口则不能代理

- Cglib代理

  - 引入Cglib相关依赖包

    ```xml
    <dependency>
        <groupId>cglib</groupId>
        <artifactId>cglib</artifactId>
        <version>3.3.0</version>
    </dependency>
    ```

  - 实现

    ```java
    public class ProxyFactory<T> implements MethodInterceptor {
        private T target;
        
        public ProxyFactory(T target) {
            this.target = target;
        }
        
        // 返回target的代理对象
        public T getProxyInstance() {
            // 1.创建一个工具类
            Enhancer enhancer = new Enhancer();
            // 2.设置父类
            enhancer.setSuperClass(target.getClass());
            // 3.设置回调函数
            enhancer.setCallback(this);
            // 4.创建子类对象，即代理对象
            return (T) enhancer.create();
        }
        
        @Override
        public Object intercept(Object arg0, Method method, Object[] args, MethodProxy arg3) throws Throwable {
            Object result = method.invoke(target, args);
            return result;
        }
    }
    ```





#### 适配器模式

- 简单适配器（适配器类违背合成复用原则）

  - 类图

    - ![适配器模式](D:\picture\typora\java\uml\适配器模式.png)

  - SDAdapterTF类

    - ```java
      public class SDAdapterTF extends TFCardImpl implements SDCard{
          @Override
          public String readSD() {
              return readTF();
          }
      
          @Override
          public void writeSD() {
              writeTF();
          }
      }
      ```

- 对象适配器模式

  - ```java
    public class SDAdapterTF implements SDCard{
        private TFCard tfCard;
    
        public SDAdapterTF(TFCard tfCard) {
            this.tfCard = tfCard;
        }
    
        @Override
        public String readSD() {
            return tfCard.readTF();
        }
    
        @Override
        public void writeSD() {
            tfCard.writeTF();
        }
    }
    ```

- 接口适配器模式

  - 当不希望实现接口中的所有方法时，可以创建一个抽象类Adapter，实现所有方法，而此时我们只需要继承该抽象类即可





#### 桥接模式

- uml图
  - ![桥接模式](D:\picture\typora\java\uml\桥接模式.png)





#### 装饰模式

- uml图

  - ![装饰者模式](D:\picture\typora\java\uml\装饰者模式.png)

- 代码

  - ```java
    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    public abstract class FastFood {
        private float price;
        private String desc;
    
        public abstract float cost();
    }
    ```

  - ```java
    public class FiredRice extends FastFood{
        public FiredRice() {
            super(10, "炒饭");
        }
    
        @Override
        public float cost() {
            return getPrice();
        }
    }
    ```

  - ```java
    public abstract class Garnish extends FastFood{
        private FastFood fastFood;
    
        public FastFood getFastFood() {
            return fastFood;
        }
    
        public void setFastFood(FastFood fastFood) {
            this.fastFood = fastFood;
        }
    
        public Garnish(FastFood fastFood, float price, String desc) {
            super(price, desc);
            this.fastFood = fastFood;
        }
    }
    ```

  - ```java
    public class Egg extends Garnish {
    
        public Egg(FastFood fastFood) {
            super(fastFood, 2, "鸡蛋");
        }
    
        @Override
        public float cost() {
            return getFastFood().cost() + getPrice();
        }
    
        @Override
        public String getDesc() {
            return super.getDesc() + getFastFood().getDesc();
        }
    }
    ```

  - ```java
    @Test
    public void test() {
        FastFood fastFood = new FiredRice();
        Egg egg = new Egg(fastFood);
        System.out.println(egg.getDesc()+"   price："+egg.cost());
        Egg doubleEgg = new Egg(egg);
        System.out.println(doubleEgg.getDesc()+"   price："+doubleEgg.cost());
    }
    ```

    



#### 组合模式

- 透明组合模式
  - ![组合模式](D:\picture\typora\java\uml\组合模式.png)

- 安全组合模式

  - ![组合模式-安全性](D:\picture\typora\java\uml\组合模式-安全性.png)

  - 将方法从remove、add等方法从父类中移走，在Menu类中添加
    - 优点：MenuItem无法调用其本身不应该调用的方法，因此不会出现异常
    - 缺点：本质上区分了Menu类对象和MenuItem类对象，不能完全针对抽象编程，必须区别对待叶子构件和容器构件





#### 外观模式

- uml图
  - ![外观模式](D:\picture\typora\java\uml\外观模式.png)





#### 享元模式

- 使用框架

  - Integer 中 -128到127共用一个对象

    ```java
    System.out.println(Integer.valueOf(1) == Integer.valueOf(1)); // true
    System.out.println(Integer.valueOf(128) == Integer.valueOf(128)); // false
    ```

    

- uml图
  - ![享元模式](D:\picture\typora\java\uml\享元模式.png)





### 行为型模式

#### 模板方式模式

- 主要组件
  - 抽象类
    - 模板方法：定义了具体方法的调用流程等算法
    - 基本方法
      - 抽象方法：父类定义抽象方法
      - 具体方法：子类重写父类的抽象方法
      - 钩子方法
  - 具体子类
- 特点：父类中的抽象方法由子类实现，子类执行结果会影响父类的结果，形成反向控制的结构





#### 命令模式

- uml图
  - ![命令模式](D:\picture\typora\java\uml\命令模式.png)

- 特点
  - 请求调用者和请求接受者解耦
  - 支持命令的撤销与恢复





#### 访问者模式

- 使用框架
  - easyexcel 的 ReadListener
  - `java.nio.file.Files.walkFileTree(Path对象, FileVisitor实现类对象)`

- uml图

  - ![访问者模式](D:\picture\typora\java\uml\访问者模式.png)

- 双分派

  - ```java
    public class Dog implements Animal {
        public void acceptPerson(Person person) {
            person.feed(this);
        }
    }
    ```

    





#### 迭代器模式

- uml图
  - ![迭代器模式](D:\picture\typora\java\uml\迭代器模式.png)

- jdk中使用迭代器
  - 聚合类实现 java.lang.Iterable 接口，并实现 iterator() 方法返回一个java.util.Iterator 的实现类





#### 观察者模式

- uml图

  - ![观察者模式](D:\picture\typora\java\uml\观察者模式.png)

- jdk中提供的实现

  - Observable：jdk9开始被废弃

    - Observable类：抽象主题类

      - void addObserver(Observer o)

      - void notifyObservers(Object arg)：越晚加入的观察者越早通知

      - void setChange()：将一个flag设置为true，该flag为true后，notify方法才能通知观察者，通知过后自动变为false

        ```java
        @Override
        public void notifyObservers(Object arg) {
            super.setChanged();
            super.notifyObservers(arg);
        }
        ```

    - Observer：抽象观察者

  - PropertyChangeSupport：监听bean

    - bean

      ```java
      @AllArgsConstructor
      @ToString(exclude = "changeSupport")
      class Student {
          private String name;
          private final PropertyChangeSupport changeSupport = new PropertyChangeSupport(this);
      
          public void setName(String name) {
              changeSupport.firePropertyChange("name", this.name, name);
              this.name = name;
          }
      
          public void addPropertyChangeListener(PropertyChangeListener listener) {
              changeSupport.addPropertyChangeListener(listener);
          }
      
          public void removePropertyChangeListener(PropertyChangeListener listener) {
              changeSupport.removePropertyChangeListener(listener);
          }
      }
      ```

    - linstener

      ```java
      @Slf4j
      public class ListenerImpl implements PropertyChangeListener {
          @Override
          public void propertyChange(PropertyChangeEvent evt) {
              log.info("修改的属性是：{}", evt.getPropertyName());
              log.info("由原来的值：{} ，改为新的值：{}", evt.getOldValue(), evt.getNewValue());
          }
      }
      ```





#### 中介者模式

- uml图
  - ![中介者模式](D:\picture\typora\java\uml\中介者模式.png)





#### 备忘录模式

- 白盒备忘录

  - ![白箱备忘录模式](D:\picture\typora\java\uml\白箱备忘录模式.png)
  - RoleStateCaretaker 作为管理 RoleStateMemento（存储数据类）的类，也可以对存储的数据进行修改

- 黑盒备忘录

  - 对发起人提供宽接口，对管理数据类提供窄接口
    - 定义一个空接口Memento作为窄接口 供RoleStateCaretaker声明使用
    - 在GameRole内部定义 Memento 的实现类，作为宽接口
  - ![黑箱备忘录模式](D:\picture\typora\java\uml\黑箱备忘录模式.png)

  - RoleStateCaretaker 不拿到具体实现类，无法向下转型





#### 解释器模式

- uml图
  - ![解释器模式](D:\picture\typora\java\uml\解释器模式.png)





#### 状态模式

- 简单模式
  - ![状态模式前](D:\picture\typora\java\uml\状态模式前.png)

- 状态模式
  - ![状态模式](D:\picture\typora\java\uml\状态模式.png)





#### 策略模式

- uml图
  - ![策略模式](D:\picture\typora\java\uml\策略模式.png)





#### 责任链模式

- uml图
  - ![责任链模式](D:\picture\typora\java\uml\责任链模式.png)







## 自定义Spring框架

### 7.1 spring使用回顾

自定义spring框架前，先回顾一下spring框架的使用，从而分析spring的核心，并对核心功能进行模拟。

* 数据访问层。定义UserDao接口及其子实现类

  ```java
  public interface UserDao {
      public void add();
  }
  
  public class UserDaoImpl implements UserDao {
  
      public void add() {
          System.out.println("userDaoImpl ....");
      }
  }
  ```

* 业务逻辑层。定义UserService接口及其子实现类

  ```java
  public interface UserService {
      public void add();
  }
  
  public class UserServiceImpl implements UserService {
  
      private UserDao userDao;
  
      public void setUserDao(UserDao userDao) {
          this.userDao = userDao;
      }
  
      public void add() {
          System.out.println("userServiceImpl ...");
          userDao.add();
      }
  }
  ```

* 定义UserController类，使用main方法模拟controller层

  ```java
  public class UserController {
      public static void main(String[] args) {
          //创建spring容器对象
          ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
          //从IOC容器中获取UserService对象
          UserService userService = applicationContext.getBean("userService", UserService.class);
          //调用UserService对象的add方法
          userService.add();
      }
  }
  ```

* 编写配置文件。在类路径下编写一个名为ApplicationContext.xml的配置文件

  ```java
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://www.springframework.org/schema/beans"
         xmlns:context="http://www.springframework.org/schema/context"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          http://www.springframework.org/schema/context/spring-context.xsd">
  
      <bean id="userService" class="com.itheima.service.impl.UserServiceImpl">
          <property name="userDao" ref="userDao"></property>
      </bean>
  
      <bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl"></bean>
  
  </beans>
  ```

  代码运行结果如下：

  ![image-20200429165544151](D:\picture\typora\java\IOC\image-20200429165544151.png)

通过上面代码及结果可以看出：

* userService对象是从applicationContext容器对象获取到的，也就是userService对象交由spring进行管理。
* 上面结果可以看到调用了UserDao对象中的add方法，也就是说UserDao子实现类对象也交由spring管理了。
* UserService中的userDao变量我们并没有进行赋值，但是可以正常使用，说明spring已经将UserDao对象赋值给了userDao变量。

上面三点体现了Spring框架的IOC（Inversion of Control）和DI（Dependency Injection, DI）



### 7.2 spring核心功能结构

Spring大约有20个模块，由1300多个不同的文件构成。这些模块可以分为:

核心容器、AOP和设备支持、数据访问与集成、Web组件、通信报文和集成测试等，下面是 Spring 框架的总体架构图：

![image-20200429111324770](D:\picture\typora\java\IOC\image-20200429111324770.png)



核心容器由 beans、core、context 和 expression（Spring Expression Language，SpEL）4个模块组成。

* spring-beans和spring-core模块是Spring框架的核心模块，包含了控制反转（Inversion of Control，IOC）和依赖注入（Dependency Injection，DI）。BeanFactory使用控制反转对应用程序的配置和依赖性规范与实际的应用程序代码进行了分离。BeanFactory属于延时加载，也就是说在实例化容器对象后并不会自动实例化Bean，只有当Bean被使用时，BeanFactory才会对该 Bean 进行实例化与依赖关系的装配。
* spring-context模块构架于核心模块之上，扩展了BeanFactory，为它添加了Bean生命周期控制、框架事件体系及资源加载透明化等功能。此外，该模块还提供了许多企业级支持，如邮件访问、远程访问、任务调度等，ApplicationContext 是该模块的核心接口，它的超类是 BeanFactory。与BeanFactory不同，ApplicationContext实例化后会自动对所有的单实例Bean进行实例化与依赖关系的装配，使之处于待用状态。
* spring-context-support模块是对Spring IoC容器及IoC子容器的扩展支持。
* spring-context-indexer模块是Spring的类管理组件和Classpath扫描组件。
* spring-expression 模块是统一表达式语言（EL）的扩展模块，可以查询、管理运行中的对象，同时也可以方便地调用对象方法，以及操作数组、集合等。它的语法类似于传统EL，但提供了额外的功能，最出色的要数函数调用和简单字符串的模板函数。EL的特性是基于Spring产品的需求而设计的，可以非常方便地同Spring IoC进行交互。



#### 7.2.1 bean概述

Spring 就是面向 `Bean` 的编程（BOP,Bean Oriented Programming），Bean 在 Spring 中处于核心地位。Bean对于Spring的意义就像Object对于OOP的意义一样，Spring中没有Bean也就没有Spring存在的意义。Spring IoC容器通过配置文件或者注解的方式来管理bean对象之间的依赖关系。

spring中bean用于对一个类进行封装。如下面的配置：

```xml
<bean id="userService" class="com.itheima.service.impl.UserServiceImpl">
    <property name="userDao" ref="userDao"></property>
</bean>
<bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl"></bean>
```

为什么Bean如此重要呢？

* spring 将bean对象交由一个叫IOC容器进行管理。
* bean对象之间的依赖关系在配置文件中体现，并由spring完成。



### 7.3 Spring IOC相关接口分析

#### 7.3.1 BeanFactory解析

Spring中Bean的创建是典型的工厂模式，这一系列的Bean工厂，即IoC容器，为开发者管理对象之间的依赖关系提供了很多便利和基础服务，在Spring中有许多IoC容器的实现供用户选择，其相互关系如下图所示。

![image-20200429185050396](D:\picture\typora\java\IOC\image-20200429185050396.png)

其中，BeanFactory作为最顶层的一个接口，定义了IoC容器的基本功能规范，BeanFactory有三个重要的子接口：ListableBeanFactory、HierarchicalBeanFactory和AutowireCapableBeanFactory。但是从类图中我们可以发现最终的默认实现类是DefaultListableBeanFactory，它实现了所有的接口。

那么为何要定义这么多层次的接口呢？

每个接口都有它的使用场合，主要是为了区分在Spring内部操作过程中对象的传递和转化，对对象的数据访问所做的限制。例如，

* ListableBeanFactory接口表示这些Bean可列表化。
* HierarchicalBeanFactory表示这些Bean 是有继承关系的，也就是每个 Bean 可能有父 Bean
* AutowireCapableBeanFactory 接口定义Bean的自动装配规则。

这三个接口共同定义了Bean的集合、Bean之间的关系及Bean行为。最基本的IoC容器接口是BeanFactory，来看一下它的源码：

```java
public interface BeanFactory {

	String FACTORY_BEAN_PREFIX = "&";

	//根据bean的名称获取IOC容器中的的bean对象
	Object getBean(String name) throws BeansException;
	//根据bean的名称获取IOC容器中的的bean对象，并指定获取到的bean对象的类型，这样我们使用时就不需要进行类型强转了
	<T> T getBean(String name, Class<T> requiredType) throws BeansException;
	Object getBean(String name, Object... args) throws BeansException;
	<T> T getBean(Class<T> requiredType) throws BeansException;
	<T> T getBean(Class<T> requiredType, Object... args) throws BeansException;
	
	<T> ObjectProvider<T> getBeanProvider(Class<T> requiredType);
	<T> ObjectProvider<T> getBeanProvider(ResolvableType requiredType);

	//判断容器中是否包含指定名称的bean对象
	boolean containsBean(String name);
	//根据bean的名称判断是否是单例
	boolean isSingleton(String name) throws NoSuchBeanDefinitionException;
	boolean isPrototype(String name) throws NoSuchBeanDefinitionException;
	boolean isTypeMatch(String name, ResolvableType typeToMatch) throws NoSuchBeanDefinitionException;
	boolean isTypeMatch(String name, Class<?> typeToMatch) throws NoSuchBeanDefinitionException;
	@Nullable
	Class<?> getType(String name) throws NoSuchBeanDefinitionException;
	String[] getAliases(String name);
}
```

在BeanFactory里只对IoC容器的基本行为做了定义，根本不关心你的Bean是如何定义及怎样加载的。正如我们只关心能从工厂里得到什么产品，不关心工厂是怎么生产这些产品的。

BeanFactory有一个很重要的子接口，就是ApplicationContext接口，该接口主要来规范容器中的bean对象是非延时加载，即在创建容器对象的时候就对象bean进行初始化，并存储到一个容器中。

![image-20200430220155371](D:\picture\typora\java\IOC\image-20200430220155371.png)

要知道工厂是如何产生对象的，我们需要看具体的IoC容器实现，Spring提供了许多IoC容器实现，比如：

* ClasspathXmlApplicationContext : 根据类路径加载xml配置文件，并创建IOC容器对象。
* FileSystemXmlApplicationContext ：根据系统路径加载xml配置文件，并创建IOC容器对象。
* AnnotationConfigApplicationContext ：加载注解类配置，并创建IOC容器。



#### 7.3.2 BeanDefinition解析

Spring IoC容器管理我们定义的各种Bean对象及其相互关系，而Bean对象在Spring实现中是以BeanDefinition来描述的，如下面配置文件

```xml
<bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl"></bean>

bean标签还有很多属性：
	scope、init-method、destory-method等。
```

其继承体系如下图所示。

![image-20200429204239868](D:\picture\typora\java\IOC\image-20200429204239868.png)



#### 7.3.3 BeanDefinitionReader解析

Bean的解析过程非常复杂，功能被分得很细，因为这里需要被扩展的地方很多，必须保证足够的灵活性，以应对可能的变化。Bean的解析主要就是对Spring配置文件的解析。这个解析过程主要通过BeanDefinitionReader来完成，看看Spring中BeanDefinitionReader的类结构图，如下图所示。

![image-20200429204700956](D:\picture\typora\java\IOC\image-20200429204700956.png)

看看BeanDefinitionReader接口定义的功能来理解它具体的作用：

```java
public interface BeanDefinitionReader {

	//获取BeanDefinitionRegistry注册器对象
	BeanDefinitionRegistry getRegistry();

	@Nullable
	ResourceLoader getResourceLoader();

	@Nullable
	ClassLoader getBeanClassLoader();

	BeanNameGenerator getBeanNameGenerator();

	/*
		下面的loadBeanDefinitions都是加载bean定义，从指定的资源中
	*/
	int loadBeanDefinitions(Resource resource) throws BeanDefinitionStoreException;
	int loadBeanDefinitions(Resource... resources) throws BeanDefinitionStoreException;
	int loadBeanDefinitions(String location) throws BeanDefinitionStoreException;
	int loadBeanDefinitions(String... locations) throws BeanDefinitionStoreException;
}
```



#### 7.3.4 BeanDefinitionRegistry解析

BeanDefinitionReader用来解析bean定义，并封装BeanDefinition对象，而我们定义的配置文件中定义了很多bean标签，所以就有一个问题，解析的BeanDefinition对象存储到哪儿？答案就是BeanDefinition的注册中心，而该注册中心顶层接口就是BeanDefinitionRegistry。

```java
public interface BeanDefinitionRegistry extends AliasRegistry {

	//往注册表中注册bean
	void registerBeanDefinition(String beanName, BeanDefinition beanDefinition)
			throws BeanDefinitionStoreException;

	//从注册表中删除指定名称的bean
	void removeBeanDefinition(String beanName) throws NoSuchBeanDefinitionException;

	//获取注册表中指定名称的bean
	BeanDefinition getBeanDefinition(String beanName) throws NoSuchBeanDefinitionException;
    
	//判断注册表中是否已经注册了指定名称的bean
	boolean containsBeanDefinition(String beanName);
    
	//获取注册表中所有的bean的名称
	String[] getBeanDefinitionNames();
    
	int getBeanDefinitionCount();
	boolean isBeanNameInUse(String beanName);
}
```

继承结构图如下：

![image-20200429211132185](D:\picture\typora\java\IOC\image-20200429211132185.png)

从上面类图可以看到BeanDefinitionRegistry接口的子实现类主要有以下几个：

* DefaultListableBeanFactory

  在该类中定义了如下代码，就是用来注册bean

  ```java
  private final Map<String, BeanDefinition> beanDefinitionMap = new ConcurrentHashMap<>(256);
  ```

* SimpleBeanDefinitionRegistry

  在该类中定义了如下代码，就是用来注册bean

  ```java
  private final Map<String, BeanDefinition> beanDefinitionMap = new ConcurrentHashMap<>(64);
  ```



#### 7.3.5 创建容器

ClassPathXmlApplicationContext对Bean配置资源的载入是从refresh（）方法开始的。refresh（）方法是一个模板方法，规定了 IoC 容器的启动流程，有些逻辑要交给其子类实现。它对 Bean 配置资源进行载入，ClassPathXmlApplicationContext通过调用其父类AbstractApplicationContext的refresh（）方法启动整个IoC容器对Bean定义的载入过程。





### 7.4 自定义SpringIOC

现要对下面的配置文件进行解析，并自定义Spring框架的IOC对涉及到的对象进行管理。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans>
    <bean id="userService" class="com.itheima.service.impl.UserServiceImpl">
        <property name="userDao" ref="userDao"></property>
    </bean>
    <bean id="userDao" class="com.itheima.dao.impl.UserDaoImpl"></bean>
</beans>
```

#### 7.4.1 定义bean相关的pojo类

##### 7.4.1.1 PropertyValue类

用于封装bean的属性，体现到上面的配置文件就是封装bean标签的子标签property标签数据。

```java
public class PropertyValue {

  private String name;
  private String ref;
  private String value;

  public PropertyValue() {
  }

  public PropertyValue(String name, String ref,String value) {
    this.name = name;
    this.ref = ref;
    this.value = value;
  }

  public String getName() {
    return name;
  }

  public void setName(String name) {
    this.name = name;
  }

  public String getRef() {
    return ref;
  }

  public void setRef(String ref) {
    this.ref = ref;
  }

  public String getValue() {
    return value;
  }

  public void setValue(String value) {
    this.value = value;
  }
}
```



##### 7.4.1.2 MutablePropertyValues类

一个bean标签可以有多个property子标签，所以再定义一个MutablePropertyValues类，用来存储并管理多个PropertyValue对象。

```java
public class MutablePropertyValues implements Iterable<PropertyValue> {

    private final List<PropertyValue> propertyValueList;

    public MutablePropertyValues() {
        this.propertyValueList = new ArrayList<PropertyValue>();
    }

    public MutablePropertyValues(List<PropertyValue> propertyValueList) {
        this.propertyValueList = (propertyValueList != null ? propertyValueList : new ArrayList<PropertyValue>());
    }

    public PropertyValue[] getPropertyValues() {
        return this.propertyValueList.toArray(new PropertyValue[0]);
    }

    public PropertyValue getPropertyValue(String propertyName) {
        for (PropertyValue pv : this.propertyValueList) {
            if (pv.getName().equals(propertyName)) {
                return pv;
            }
        }
        return null;
    }

    @Override
    public Iterator<PropertyValue> iterator() {
        return propertyValueList.iterator();
    }

    public boolean isEmpty() {
        return this.propertyValueList.isEmpty();
    }

    public MutablePropertyValues addPropertyValue(PropertyValue pv) {
        for (int i = 0; i < this.propertyValueList.size(); i++) {
            PropertyValue currentPv = this.propertyValueList.get(i);
            if (currentPv.getName().equals(pv.getName())) {
                this.propertyValueList.set(i, new PropertyValue(pv.getName(),pv.getRef(), pv.getValue()));
                return this;
            }
        }
        this.propertyValueList.add(pv);
        return this;
    }

    public boolean contains(String propertyName) {
        return getPropertyValue(propertyName) != null;
    }
}
```



##### 7.4.1.3 BeanDefinition类

BeanDefinition类用来封装bean信息的，主要包含id（即bean对象的名称）、class（需要交由spring管理的类的全类名）及子标签property数据。

```java
public class BeanDefinition {
    private String id;
    private String className;

    private MutablePropertyValues propertyValues;

    public BeanDefinition() {
        propertyValues = new MutablePropertyValues();
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getClassName() {
        return className;
    }

    public void setClassName(String className) {
        this.className = className;
    }

    public void setPropertyValues(MutablePropertyValues propertyValues) {
        this.propertyValues = propertyValues;
    }

    public MutablePropertyValues getPropertyValues() {
        return propertyValues;
    }
}
```



#### 7.4.2 定义注册表相关类

##### 7.4.2.1 BeanDefinitionRegistry接口

BeanDefinitionRegistry接口定义了注册表的相关操作，定义如下功能：

* 注册BeanDefinition对象到注册表中
* 从注册表中删除指定名称的BeanDefinition对象
* 根据名称从注册表中获取BeanDefinition对象
* 判断注册表中是否包含指定名称的BeanDefinition对象
* 获取注册表中BeanDefinition对象的个数
* 获取注册表中所有的BeanDefinition的名称

```java
public interface BeanDefinitionRegistry {

    //注册BeanDefinition对象到注册表中
    void registerBeanDefinition(String beanName, BeanDefinition beanDefinition);

    //从注册表中删除指定名称的BeanDefinition对象
    void removeBeanDefinition(String beanName) throws Exception;

    //根据名称从注册表中获取BeanDefinition对象
    BeanDefinition getBeanDefinition(String beanName) throws Exception;

    boolean containsBeanDefinition(String beanName);

    int getBeanDefinitionCount();

    String[] getBeanDefinitionNames();
}
```



##### 7.4.2.2 SimpleBeanDefinitionRegistry类

该类实现了BeanDefinitionRegistry接口，定义了Map集合作为注册表容器。

```java
public class SimpleBeanDefinitionRegistry implements BeanDefinitionRegistry {

    private Map<String, BeanDefinition> beanDefinitionMap = new HashMap<String, BeanDefinition>();

    @Override
    public void registerBeanDefinition(String beanName, BeanDefinition beanDefinition) {
        beanDefinitionMap.put(beanName,beanDefinition);
    }

    @Override
    public void removeBeanDefinition(String beanName) throws Exception {
        beanDefinitionMap.remove(beanName);
    }

    @Override
    public BeanDefinition getBeanDefinition(String beanName) throws Exception {
        return beanDefinitionMap.get(beanName);
    }

    @Override
    public boolean containsBeanDefinition(String beanName) {
        return beanDefinitionMap.containsKey(beanName);
    }

    @Override
    public int getBeanDefinitionCount() {
        return beanDefinitionMap.size();
    }

    @Override
    public String[] getBeanDefinitionNames() {
        return beanDefinitionMap.keySet().toArray(new String[1]);
    }
}
```



#### 7.4.3 定义解析器相关类

##### 7.4.3.1 BeanDefinitionReader接口

BeanDefinitionReader是用来解析配置文件并在注册表中注册bean的信息。定义了两个规范：

* 获取注册表的功能，让外界可以通过该对象获取注册表对象。
* 加载配置文件，并注册bean数据。

```java
public interface BeanDefinitionReader {

	//获取注册表对象
    BeanDefinitionRegistry getRegistry();
	//加载配置文件并在注册表中进行注册
    void loadBeanDefinitions(String configLocation) throws Exception;
}
```



##### 7.4.3.2 XmlBeanDefinitionReader类

XmlBeanDefinitionReader类是专门用来解析xml配置文件的。该类实现BeanDefinitionReader接口并实现接口中的两个功能。

```java
public class XmlBeanDefinitionReader implements BeanDefinitionReader {

    private BeanDefinitionRegistry registry;

    public XmlBeanDefinitionReader() {
        this.registry = new SimpleBeanDefinitionRegistry();
    }

    @Override
    public BeanDefinitionRegistry getRegistry() {
        return registry;
    }

    @Override
    public void loadBeanDefinitions(String configLocation) throws Exception {

        InputStream is = this.getClass().getClassLoader().getResourceAsStream(configLocation);
        SAXReader reader = new SAXReader();
        Document document = reader.read(is);
        Element rootElement = document.getRootElement();
        //解析bean标签
        parseBean(rootElement);
    }

    private void parseBean(Element rootElement) {

        List<Element> elements = rootElement.elements();
        for (Element element : elements) {
            String id = element.attributeValue("id");
            String className = element.attributeValue("class");
            BeanDefinition beanDefinition = new BeanDefinition();
            beanDefinition.setId(id);
            beanDefinition.setClassName(className);
            List<Element> list = element.elements("property");
            MutablePropertyValues mutablePropertyValues = new MutablePropertyValues();
            for (Element element1 : list) {
                String name = element1.attributeValue("name");
                String ref = element1.attributeValue("ref");
                String value = element1.attributeValue("value");
                PropertyValue propertyValue = new PropertyValue(name,ref,value);
                mutablePropertyValues.addPropertyValue(propertyValue);
            }
            beanDefinition.setPropertyValues(mutablePropertyValues);

            registry.registerBeanDefinition(id,beanDefinition);
        }
    }
}
```



#### 7.4.4 IOC容器相关类

##### 7.4.4.1 BeanFactory接口

在该接口中定义IOC容器的统一规范即获取bean对象。

```java
public interface BeanFactory {
	//根据bean对象的名称获取bean对象
    Object getBean(String name) throws Exception;
	//根据bean对象的名称获取bean对象，并进行类型转换
    <T> T getBean(String name, Class<? extends T> clazz) throws Exception;
}
```



##### 7.4.4.2 ApplicationContext接口

该接口的所以的子实现类对bean对象的创建都是非延时的，所以在该接口中定义 `refresh()` 方法，该方法主要完成以下两个功能：

* 加载配置文件。
* 根据注册表中的BeanDefinition对象封装的数据进行bean对象的创建。

```java
public interface ApplicationContext extends BeanFactory {
	//进行配置文件加载并进行对象创建
    void refresh() throws IllegalStateException, Exception;
}
```



##### 7.4.4.3 AbstractApplicationContext类

* 作为ApplicationContext接口的子类，所以该类也是非延时加载，所以需要在该类中定义一个Map集合，作为bean对象存储的容器。

* 声明BeanDefinitionReader类型的变量，用来进行xml配置文件的解析，符合单一职责原则。

  BeanDefinitionReader类型的对象创建交由子类实现，因为只有子类明确到底创建BeanDefinitionReader哪儿个子实现类对象。

```java
public abstract class AbstractApplicationContext implements ApplicationContext {

    protected BeanDefinitionReader beanDefinitionReader;
    //用来存储bean对象的容器   key存储的是bean的id值，value存储的是bean对象
    protected Map<String, Object> singletonObjects = new HashMap<String, Object>();

    //存储配置文件的路径
    protected String configLocation;

    public void refresh() throws IllegalStateException, Exception {

        //加载BeanDefinition
        beanDefinitionReader.loadBeanDefinitions(configLocation);

        //初始化bean
        finishBeanInitialization();
    }

    //bean的初始化
    private void finishBeanInitialization() throws Exception {
        BeanDefinitionRegistry registry = beanDefinitionReader.getRegistry();
        String[] beanNames = registry.getBeanDefinitionNames();

        for (String beanName : beanNames) {
            BeanDefinition beanDefinition = registry.getBeanDefinition(beanName);
            getBean(beanName);
        }
    }
}
```

> 注意：该类finishBeanInitialization()方法中调用getBean()方法使用到了模板方法模式。



##### 7.4.4.4 ClassPathXmlApplicationContext类

该类主要是加载类路径下的配置文件，并进行bean对象的创建，主要完成以下功能：

* 在构造方法中，创建BeanDefinitionReader对象。
* 在构造方法中，调用refresh()方法，用于进行配置文件加载、创建bean对象并存储到容器中。
* 重写父接口中的getBean()方法，并实现依赖注入操作。

```java
public class ClassPathXmlApplicationContext extends AbstractApplicationContext{

    public ClassPathXmlApplicationContext(String configLocation) {
        this.configLocation = configLocation;
        //构建XmlBeanDefinitionReader对象
        beanDefinitionReader = new XmlBeanDefinitionReader();
        try {
            this.refresh();
        } catch (Exception e) {
        }
    }

    //根据bean的id属性值获取bean对象
    @Override
    public Object getBean(String name) throws Exception {

        //return singletonObjects.get(name);
        Object obj = singletonObjects.get(name);
        if(obj != null) {
            return obj;
        }

        BeanDefinitionRegistry registry = beanDefinitionReader.getRegistry();
        BeanDefinition beanDefinition = registry.getBeanDefinition(name);
        if(beanDefinition == null) {
            return null;
        }
        String className = beanDefinition.getClassName();
        Class<?> clazz = Class.forName(className);
        Object beanObj = clazz.newInstance();
        MutablePropertyValues propertyValues = beanDefinition.getPropertyValues();
        for (PropertyValue propertyValue : propertyValues) {
            String propertyName = propertyValue.getName();
            String value = propertyValue.getValue();
            String ref = propertyValue.getRef();
            if(ref != null && !"".equals(ref)) {

                Object bean = getBean(ref);
                String methodName = StringUtils.getSetterMethodNameByFieldName(propertyName);
                Method[] methods = clazz.getMethods();
                for (Method method : methods) {
                    if(method.getName().equals(methodName)) {
                        method.invoke(beanObj,bean);
                    }
                }
            }

            if(value != null && !"".equals(value)) {
                String methodName = StringUtils.getSetterMethodNameByFieldName(propertyName);
                Method method = clazz.getMethod(methodName, String.class);
                method.invoke(beanObj,value);
            }
        }
        singletonObjects.put(name,beanObj);
        return beanObj;
    }

    @Override
    public <T> T getBean(String name, Class<? extends T> clazz) throws Exception {

        Object bean = getBean(name);
        if(bean != null) {
            return clazz.cast(bean);
        }
        return null;
    }
}
```



#### 7.4.5 自定义Spring IOC总结

##### 7.4.5.1 使用到的设计模式

* 工厂模式。这个使用工厂模式 + 配置文件的方式。
* 单例模式。Spring IOC管理的bean对象都是单例的，此处的单例不是通过构造器进行单例的控制的，而是spring框架对每一个bean只创建了一个对象。
* 模板方法模式。AbstractApplicationContext类中的finishBeanInitialization()方法调用了子类的getBean()方法，因为getBean()的实现和环境息息相关。
* 迭代器模式。对于MutablePropertyValues类定义使用到了迭代器模式，因为此类存储并管理PropertyValue对象，也属于一个容器，所以给该容器提供一个遍历方式。

spring框架其实使用到了很多设计模式，如AOP使用到了代理模式，选择JDK代理或者CGLIB代理使用到了策略模式，还有适配器模式，装饰者模式，观察者模式等。

##### 7.4.5.2 符合大部分设计原则

##### 7.4.5.3 整个设计和Spring的设计还是有一定的出入

spring框架底层是很复杂的，进行了很深入的封装，并对外提供了很好的扩展性。而我们自定义SpringIOC有以下几个目的：

* 了解Spring底层对对象的大体管理机制。
* 了解设计模式在具体的开发中的使用。
* 以后学习spring源码，通过该案例的实现，可以降低spring学习的入门成本。