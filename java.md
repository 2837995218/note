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







# JUC



## 常用API

### Thread

| 方法名                 | 说明                                                         |
| ---------------------- | ------------------------------------------------------------ |
| start()                | 启动一个新线程                                               |
| run()                  | 启动新线程后，新线程调用的方法                               |
| join()                 | 调用该方法的线程等待该方法所属线程运行结束                   |
| join(long n)           | 最多等待 n 毫秒                                              |
| getId()                | 获得线程 long 型的唯一id                                     |
| getName()              | 获取线程名                                                   |
| setName(String)        | 修改线程名                                                   |
| getPriority()          | 获取线程优先级                                               |
| setPriority(int)       | 修改线程优先级                                               |
| getState()             | 获取线程状态：new runnable blocked waiting timed_waiting treminated |
| setDaemon()            | 是否将所属线程设置为守护线程                                 |
| isAlive()              | 线程是否存活                                                 |
| interrupt()            | 打断所属线程，如果被打断线程正在 sleep、wait、join 会导致被打断的<br/>线程抛出InterruptedException ，并清除打断标记；如果被打断的线程<br/>正在运行，则会为被打断的线程设置打断标记，不会直接打断线程 |
| isInterrupted()        | 获取所属线程是否被设置打断标记，获取后不会清除打断标记       |
| static interrupted()   | 获取调用线程是否被设置打断标记，获取后会清除打断标记         |
| static currentThread() | 获取当前正在运行的线程                                       |
| static sleep(long n)   | 休眠 n 毫秒，休眠期间让出CPU资源                             |
| static yield()         | 提示线程调度器让出调用线程对CPU的使用                        |

- 两阶段终止模式

  ```java
  @Slf4j
  public class InterruptTest {
      public static void main(String[] args) throws InterruptedException {
          Thread thread = new Thread(() -> {
              while (true) {
                  if (Thread.interrupted()) {
                      log.info("释放资源、锁等");
                      break;
                  }
                  log.info("执行循环任务");
                  try {
                      Thread.sleep(2000);
                  } catch (InterruptedException e) {
                      log.warn("休眠期间被打断");
                      Thread.currentThread().interrupt();
                  }
              }
          });
          thread.start();
          Thread.sleep(5000);
          thread.interrupt();
      }
  }
  ```





### Stack

| 常用方法             | 作用                                     |
| -------------------- | ---------------------------------------- |
| boolean empty()      | 判断栈是否为空                           |
| E peek()             | 返回栈顶部的对象，但不从栈中移出         |
| E pop()              | 返回栈顶部的对象，并从栈中移出           |
| E push(E item)       | 把对象压入栈中                           |
| int search(Object o) | 返回对象在堆栈中的位置，若不存在则返回-1 |



### Queue

| 常用方法           | 作用                                                 |
| ------------------ | ---------------------------------------------------- |
| boolean add(E e)   | 在队列尾部插入一个元素，若队列已满，抛出异常         |
| boolean offer(E e) | 在队列尾部插入一个元素，若队列已满，只返回false      |
| E element()        | 返回队列头部的对象，但不从队列中移出，若空，抛异常   |
| E peek()           | 返回队列头部的对象，但不从队列中移出，若空，返回null |
| E remove()         | 返回队列头部的对象，并从队列中移出，若空，抛异常     |
| E poll()           | 返回队列头部的对象，并从队列中移出，若空，返回null   |



### BlockingQueue

- 放入数据

  - offer(anObject):表示如果可能的话,将anObject加到BlockingQueue里,即如果BlockingQueue可以容纳,则返回true,否则返回false.（本方法不阻塞当前执行方法的线程）；　　　　　　 

  - offer(E o, long timeout, TimeUnit unit)：可以设定等待的时间，如果在指定的时间内，还不能往队列中加入BlockingQueue，则返回失败。

  - put(anObject):把anObject加到BlockingQueue里,如果BlockQueue没有空间,则调用此方法的线程被阻断直到BlockingQueue里面有空间再继续.

- 获取数据

  - poll(time):取走BlockingQueue里排在首位的对象,若不能立即取出,则可以等time参数规定的时间,取不到时返回null;
  - poll(long timeout, TimeUnit unit)：从BlockingQueue取出一个队首的对象，如果在指定时间内，队列一旦有数据可取，则立即返回队列中的数据。否则知道时间超时还没有数据可取，返回失败。
  - take():取走BlockingQueue里排在首位的对象,若BlockingQueue为空,阻断进入等待状态直到BlockingQueue有新的数据被加入; 
  - drainTo():一次性从BlockingQueue获取所有可用的数据对象（还可以指定获取数据的个数），通过该方法，可以提升获取数据效率；不需要多次分批加锁或释放锁。



### CompletableFuture

- 四种静态方法

  ```java
  // 默认使用 ForkJoin 线程池，ForkJoin 线程池创建的线程是守护线程
  CompletableFuture<Void> static runAsync(Runnable r)
  CompletableFuture<T> static supplyAsync(supplier<T> s)
  
  CompletableFuture<Void> static runAsync(Runnable r, Executor e)
  CompletableFuture<T> static supplyAsync(supplier<T> s, Executor e)
  ```

- 异步调用

  ```java
  completableFuture.get();
  completableFuture
      // 无论是否报错，获得结果后都会进入这个分支
      .whenComplete((t, throwable) -> {
          log.info(t.toString());
      })
      // 报错后还会进入这个分支
      .exceptionally(throwable -> {
          throwable.printStackTrace();
          return t;
      });
  ```



### CyclicBarrier

```java
AtomicInteger times = new AtomicInteger(1);
CyclicBarrier cyclicBarrier = new CyclicBarrier(2, () -> {
    log.info("第 {} 轮执行完毕", times.getAndIncrement());
});
Runnable r = () -> {
    try {
        int costTime = new Random().nextInt(3);
        TimeUnit.SECONDS.sleep(costTime);
        log.info("执行结束，耗时：{} 秒", costTime);
        cyclicBarrier.await();
    } catch (InterruptedException | BrokenBarrierException e) {
        throw new RuntimeException(e);
    }
};
ExecutorService threadPool = Executors.newFixedThreadPool(2);
for (int i = 0; i < 3; i++) {
    threadPool.submit(r);
    threadPool.submit(r);
}
```

```log
[INFO ] 12:26:39:693   pool-1-thread-1 --->    执行结束，耗时：1 秒
[INFO ] 12:26:40:704   pool-1-thread-2 --->    执行结束，耗时：2 秒
[INFO ] 12:26:40:704   pool-1-thread-2 --->    第 1 轮执行完毕
[INFO ] 12:26:41:719   pool-1-thread-1 --->    执行结束，耗时：1 秒
[INFO ] 12:26:42:715   pool-1-thread-2 --->    执行结束，耗时：2 秒
[INFO ] 12:26:42:715   pool-1-thread-2 --->    第 2 轮执行完毕
[INFO ] 12:26:43:717   pool-1-thread-2 --->    执行结束，耗时：1 秒
[INFO ] 12:26:43:717   pool-1-thread-1 --->    执行结束，耗时：1 秒
[INFO ] 12:26:43:717   pool-1-thread-1 --->    第 3 轮执行完毕
```



### ConcurrentHashMap

```java
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();

// 错误用法
while ((str = bufferedReader.readLine()) != null && !str.isBlank()) {
    Integer integer = map.get(str);
    integer = integer == null ? 1 : integer + 1;
    map.put(str, integer);
}


while ((str = bufferedReader.readLine()) != null && !str.isBlank()) {
    map.computeIfAbsent(str, s -> 0);
    map.computeIfPresent(str, (s, integer) -> integer+1);
}
// 利用累加器
ConcurrentHashMap<String, LongAdder> map = new ConcurrentHashMap<>();
while ((str = bufferedReader.readLine()) != null && !str.isBlank()) {
    map.computeIfAbsent(str, s -> new LongAdder());
    map.get(str).increment();
}
```











## 共享模型

### 线程安全

常见的线程安全类：String、Integer、StringBuffer、Random、Vector、Hashtable、java.util.concurrent包下的类

```java
@RestController
public class MyController {
    // HashMap 线程不安全，Hashtable 线程安全
    Map<String,Object> map = new Hashmap<>();
    
    @Autowire
    private MyService myService;
}

@Service
public class MyAservice {
    private Integer insertCount = 0;
    
    // 线程不安全
    public void insert() {
        // insert 操作
        inserCount++;
    }
}
```

```java
// 线程安全，因为 service 中的 MyDao 私有，无法修改
@RestController
public class MyController {
    @Autowire
    private MyService myService;
}

// 线程安全，因为 MyDao 中没有成员变量
@Service
public class MyService {
    @Autowire
    private MyDao myDao;
}

@Service
public class MyDao {
    @Select
    ....
}
```



### Synchronized

- MarkWord

  <table>
      <caption>64位虚拟机</caption>
      <tr>
          <td rowspan="2">锁状态</td>
          <td colspan="4">56bit</td>
          <td rowspan="2">1bit</td>
          <td rowspan="2">4bit</td>
          <td rowspan="2" style="color: red;">1bit<br/>(是否是偏向锁)</td>
          <td rowspan="2">2bit<br/>(锁标记位)</td>
      </tr>
      <tr>
          <td colspan="2">25bit</td>
          <td colspan="2">31bit</td>
      </tr>
      <tr>
          <td>无锁</td>
          <td colspan="2">unused</td>
          <td colspan="2">对象 hashCode</td>
          <td>Cms_free</td>
          <td>分代年龄</td>
          <td>0</td>
          <td>01</td>
      </tr>
      <tr>
          <td>偏向锁</td>
          <td colspan="3" style="color:red;">threadId(54bit)</td>
          <td colspan="1">Epoch(2bit)</td>
          <td>Cms_free</td>
          <td>分代年龄</td>
          <td>1</td>
          <td>01</td>
      </tr>
      <tr>
          <td>轻量级锁</td>
          <td colspan="7">指向栈中的记录的指针</td>
          <td>00</td>
      </tr>
      <tr>
          <td>重量级锁</td>
          <td colspan="7">指向重量级锁的指针</td>
          <td>10</td>
      </tr>
      <tr>
          <td>GC标志</td>
          <td colspan="7">空</td>
          <td>01</td>
      </tr>
  </table>

- 锁升级

  - **偏向锁**：当同步代码块中的代码被第一个线程执行时，该线程会获得锁（锁对象的MarkWord中记录该线程ID），此时锁是偏向锁，该线程是偏向线程。这样偏向线程就一直持有着锁，后续这个线程退出和再进入这段加了同步锁的代码块时，不需要再次释放锁和加锁，而是只检查锁的MarkWord里面记录的是不是当前线程的ID

    - 如果相同：表示偏向锁偏向当前线程，就不需要再尝试获得锁了。以后每次同步，检查锁的偏向线程ID与当前线程ID是否一致，如果一致直接进入同步。无需每次加锁解锁都去CAS更新对象头。
    - 如果不同：表示有另一个线程也执行了这段同步代码，锁已经不能总是偏向于同一个线程了。这时会JVM会暂停所有的线程，这被称为**安全点**（Safepoint，偏向锁的撤销和锁的升级过程都需要在安全点进行，这个过程中会发生“Stop The World”）。这是为了防止第一个线程正在执行synchronized代码块。在STW期间：JVM会检查第一个线程是否还活着
      - 如果第一个线程已经结束，那么偏向锁会被撤销，然后第二个线程可以获取**偏向锁**并继续执行。
      - 如果第一个线程还活着，那么偏向锁会被撤销，并且**锁会被升级**。（由撤销 升级为偏向锁还是轻量级锁）
        - 如果第一个线程已经退出了synchronized代码块，那么锁会被**升级为偏向锁**，然后被第二个线程获取。
        - 如果第一个线程正在执行synchronized代码块，那么锁会被**升级为轻量级锁**。
    - 虚拟机参数：
      - `-XX:+UseBiasedLocking`：开启偏向锁（jdk8及之后默认开启，使用 **-** 表示关闭）
      - `-XX:BiasedLockingStartupDelay=4000`：偏向锁延迟启用时间（默认4000毫秒）

  - **轻量级锁**：

    - 为什么引入轻量级锁：轻量级锁考虑的是竞争锁对象的**线程不多**，而线程持有锁的**时间也不长**。因为阻塞线程需要从用户态切换为内核态，这一过程开销大。在竞争线程不多、执行时间不长的程序中，可以通过让线程不阻塞，而是不断循环等待锁的释放，可能比重量级锁开销小的多。
    - 什么时候升级到轻量级锁
      - 关闭偏向锁：`-XX:-UseBiasedLocking`
      - 多个线程同时竞争偏向锁
    - 原理：
      - JVM会在当前线程的栈帧中建立一个名为锁记录（Lock Record）的空间，用于存储接下来需要用到的锁对象MarkWord的拷贝（官方称为Displaced Mark Word）。如果一个线程获得了轻量级锁，那么它已将将锁对象的MarkWord复制到栈帧中的锁记录（Lock Record）中
      - 加锁过程：线程会尝试利用CAS操作将 锁对象的MarkWord 更新为指向LockRecord的指针
        - 如果成功，表示当前线程竞争到锁，将锁标志位变为00
        - 如果失败，说明MarkWord已经被替换成了其他线程的锁记录
      - 锁的释放：

  - **重量级锁**：实际上如果真的有两个线程同时竞争锁，靠前面的锁是无法解决的，因为synchronized并不是共享锁，它一次只允许一个线程获得锁（进入同步代码块），而另一个线程则会被阻塞。

    - 重量级锁依赖于MonitorObject对象，每个java对象都有一个MonitorObject，所以每个java对象都可以作为同步锁
    - **重量级锁才会涉及线程内核态转换**，做出阻塞和唤醒线程的动作，wait 和 notify方法其实是MonitorObject的方法
      - 内核态（Kernel Mode）是操作系统中的一种运行级别。在内核态下，代码有权访问系统的所有资源，包括硬件和内存。操作系统的核心部分，即内核，通常在内核态下运行。
      - 内核态与用户态（User Mode）相对。在用户态下，运行的代码（通常是用户程序）不能直接访问硬件或内存，而是必须通过系统调用来请求内核提供的服务。这种机制提供了一种保护，防止用户程序直接操作硬件或内存，从而可能影响系统的稳定性或安全性。
      - 当一个用户程序需要执行如读写文件、发送网络数据等操作时，它会切换到内核态（通过系统调用），**由内核代为执行**，然后再切换回用户态，继续执行用户程序。这种从用户态切换到内核态的过程，通常被称为**上下文切换**（Context Switch）。
      - **用户态切换到内核态，实际上不是真正的切换，而是由内核代为执行用户程序的要求**
    - 重量级锁有两个队列，阻塞队列——执行到synchronized方法，但没获得锁，只能在方法外面排队，只有被唤醒并获得锁，才能进入方法体内部；等待队列——已经执行到synchronized方法内部，主动执行等待操作，暂时放弃锁，一旦被唤醒则并重新获得锁，则可以接着原来的代码执行
    - 等待队列的线程被唤醒后也会被扔到阻塞队列，和原本就在外面的阻塞线程同台竞争

    ![Monitor](D:\picture\typora\java2\Monitor.png)


- 批量重偏向：当撤销偏向锁超过阈值20次，会由偏向A线程变更为偏向B线程

- 批量撤销：当撤销偏向锁超过阈值40次，会撤销偏向锁变为不可偏向锁

- 锁撤销：即时编译器可以对不需要加锁的流程撤销锁

- 锁粗化：可以将多个连续的小锁合并成一个大锁，从而减少锁的竞争次数，提高程序性能。

- 等待与唤醒（以下两个方法一般**与synchronized一起使用，且锁对象必须是同一个**）

  - obj.wait()：让object对应的Monitor中的Owner上的线程到 waitSet 中等待
  - obj.notify()：从 waitSet 中挑一个唤醒

  ```java
  public class WaitTest {
      public static void main(String[] args) throws InterruptedException, ExecutionException {
          Calculator calculator = new Calculator();
          calculator.calculate();
          for (int i = 0; i < 3; i++) {
              System.out.println("计算结果为：" + calculator.getResult().get());
          }
      }
  }
  
  class Calculator {
      private Integer result;
  
      // 理论上，需要加锁，因为该方法对属性result进行修改
      public synchronized void calculate() {
          new Thread(() -> {
              try {
                  TimeUnit.SECONDS.sleep(2);
              } catch (InterruptedException ignore) {}
              result = 1;
              synchronized (this) {
                  this.notifyAll();
              }
          }).start();
      }
  
      public FutureTask<Integer> getResult() {
          FutureTask<Integer> integerFutureTask = new FutureTask<>(() -> {
              synchronized (this) {
                  // ※ 用while循环而不用if，是因为：
                  // wait() 方法可能会“虚假唤醒”，也就是说，即使没有其他线程调用 notify() 或 notifyAll()，
                  // wait() 方法也可能返回。这是由 JVM 的内部实现决定的，是为了提高系统的整体性能。 
                  while (result == null) {
                      System.out.println("计算结果还未出来，等待中...");
                      this.wait();
                  }
                  return result;
              }
          });
          new Thread(integerFutureTask).start();
          return integerFutureTask;
      }
  }
  ```

  



### 线程间通讯

- synchronized

  ```java
  public class CommunicationTest {
      public static void main(String[] args) {
          new Thread(() -> {while (true) Message.createBun();}, "生产者1").start();
          new Thread(() -> {while (true) Message.sellBun();}, "消费者1").start();
          new Thread(() -> {while (true) Message.createBun();}, "生产者2").start();
          new Thread(() -> {while (true) Message.sellBun();}, "消费者2").start();
      }
  
  }
  
  @Slf4j
  class Message {
      private static Queue<String> msgQueue = new PriorityQueue<>();
      private static AtomicInteger id = new AtomicInteger();
  
      public static synchronized void createBun() {
          String str = "消息"+("M"+id.incrementAndGet());
          msgQueue.offer(str);
          log.info("向消息队列中添加消息：{}，消息队列：{}", str, msgQueue);
          Message.class.notifyAll();
          try {
              Message.class.wait();
          } catch (InterruptedException e) {
              throw new RuntimeException(e);
          }
      }
  
      public static synchronized void sellBun(){
          if (msgQueue.size() > 0) {
              log.info("取出消息：{}，消息队列：{}", msgQueue.poll(), msgQueue);
          }
          Message.class.notifyAll();
          try {
              Message.class.wait();
          } catch (InterruptedException e) {
              throw new RuntimeException(e);
          }
      }
  }
  ```

- Lock 接口

  ```java
  private Lock lock = new ReentrantLock();
  private Condition condition = lock.newCondition();
  public void test() throws RuntimeException {
      lock.lock();
      condition.await();
      condition.signalAll();
      lock.unlock();
  
  }
  ```

- 注意：<font color=red>**线程在哪里进入等待，下次被唤醒将在哪里继续执行**</font>

- 保护性暂停：wait

  ```java
  public class FutureTest {
      public static void main(String[] args) {
          Future future = new Future();
          new Thread(() -> future.calculate(), "计算线程").start();
          Thread.currentThread().setName("结果线程");
          future.getResult();
      }
  }
  
  @Slf4j
  class Future {
      private Object result;
  
      // 因为要通过锁被calculate()唤醒，所以要加和calculate()方法一样的锁
      public synchronized Object getResult() {
          log.info("准备获取结果");
          while (result == null) {
              log.info("尚未得到结果，进入等待");
              try {
                  this.wait();
              } catch (InterruptedException e) {
                  log.info("受到打断请求，但不进行打断操作");
              }
          }
          log.info("得到结果");
          return result;
      }
  
      public synchronized void calculate() {
          try {
              TimeUnit.SECONDS.sleep(1);
          } catch (InterruptedException e) {
              throw new RuntimeException(e);
          }
          result = new Object();
          log.info("已计算出结果");
          this.notifyAll();
      }
  }
  ```

- 保护性暂停：pack（便利：凭“票”判断是否阻塞。不便：唤醒需要指定唤醒线程，且一次只能唤醒一个线程）

  ```java
  public class FutureTest {
      public static void main(String[] args) {
          Calculator calculator = new Calculator2();
          List<Thread> getResultThreads = List.of(
                  new Thread(() -> System.out.println("计算结果为：" + 
                                                      calculator.getResult(Thread.currentThread()))),
                  new Thread(() -> System.out.println("计算结果为：" + 
                                                      calculator.getResult(Thread.currentThread()))),
                  new Thread(() -> System.out.println("计算结果为：" + 
                                                      calculator.getResult(Thread.currentThread())))
          );
          calculator.calculate(getResultThreads);
          // 此处休眠结束后，计算线程结束，获取结果线程还未开启，计算线程的unpark失效
          //TimeUnit.SECONDS.sleep(3);
          getResultThreads.forEach(Thread::start);
      }
  }
  
  @Slf4j
  class Calculator {
      private Integer result;
  
      public synchronized void calculate(List<Thread> threads) {
          new Thread(() -> {
              try {
                  TimeUnit.SECONDS.sleep(2);
              } catch (InterruptedException ignore) {}
              result = 1;
              // unpark 该线程后，回唤醒该线程park状态，并且即使之后被park，也不会停止
              threads.forEach(LockSupport::unpark);
          }).start();
      }
  
      // 查询线程不需要被锁唤醒，因此也不需要加锁
      public Integer getResult(Thread thread) {
          try {
              // 该线程干了些别的事，可能耗费了比计算结果要更长的时间后，再获取结果
              TimeUnit.SECONDS.sleep(3);
          } catch (InterruptedException e) {
              throw new RuntimeException(e);
          }
          // ※ 若有锁，park暂停时不会释放锁，因此不能加与calculate()相同的锁
          // 相较于之前，不需要循环判断result是否等于null
          LockSupport.park();
          return result;
      }
  }
  ```

  - <font color=red>**park 不会释放锁（到此为止的锁指的都是Monitor），或者说 park 和 锁 是不相关的，park 是线程上的属性，锁是作为锁的对象上的属性**</font>
  - <font color=red>**unpark 某条正在运行的线程后，会唤醒该线程park状态，若该线程未被park，则下一次park不会停止**</font>
  - <font color=red>**interrupt 可以打断所属线程的park状态，打断后，打断标记为true，打断标记为true时，该线程将无法再被park**</font>



### ReentranLock

- 使用

  ```java
  ReentrantLock reentrantLock = new ReentrantLock();
  reentrantLock.lock();
  try {
      log.info("获取到锁，执行临界区代码")
  } finally {
      reentrantLock.unlock();
  }
  ```

- 可打断锁

  ```java
  try {
      // reentrantLock.lock(); 无法接收打断信号
      reentrantLock.lockInterruptibly();
  } catch (InterruptedException e) {
      log.info("收到打断信号，打断阻塞状态");
      return;
  }
  ```

- 可设置超时时间

  ```java
  if (!reentrantLock.tryLock()){
      log.info("未获取到锁，结束");
      return;
  }
  
  // 支持打断
  try {
      if (!reentrantLock.tryLock(2, TimeUnit.SECONDS)){
          log.info("未获取到锁，结束");
          return;
      }
  } catch (InterruptedException e) {
      log.info("收到打断信号，打断阻塞状态");
      return;
  }
  ```

- 可设置公平锁

  ```java
  // 构造方法中传递是否开启公平锁
  ReentrantLock reentrantLock = new ReentrantLock(true);
  ```

- 支持多个条件变量：可理解为将 WaitSet 分成了多个休息室，线程可以进入指定的休息室等待，也可以唤醒指定休息室的线程

  ```java
  Condition condition1 = reentrantLock.newCondition();
  Condition condition2 = reentrantLock.newCondition();
  
  condition1.await();
  condition.await(1, TimeUnit.SECONDS);
  condition.awaitNanos(10000000);
  condition.awaitUninterruptibly();
  condition.awaitUntil(new Date());
  
  condition1.signal();
  condition1.signalAll();
  ```

- Lock 与 synchronized 的区别

  - Lock 是一个接口；synchronized 是一个关键字
  - synchronized 在发生异常时，会自动释放线程所占有的锁；而 Lock 在发生异常事，如果不主动使用 unlock() 去释放锁，则可能造成死锁现象
  - Lock 的性能优于 synchronized



### 不可变

- 不可变类设计：类和类中的属性都用 final 修饰

  - 属性用 final 修饰，保证了该属性是只读的，不能修改
  - 类用 final 修饰，保证了该类中的方法不能被覆盖，防止子类无意间破坏不可变性

  ```java
  public final class String 
      implements java.io.Serializable, Comparable<String>, CharSequence {
      private final char[] value;
      
      // 私有，外界无法更改
      private int hash;
  }
  ```

- 无状态设计：不设置成员变量

- 保护性拷贝

- 享元模式



### ThreadLocal

- 实例：销售卖房子，高层只关心销售总数的准确统计值

  - 初始化

    ```java
    class House{
        // 建议使用 static 修饰
        static ThreadLocal count = new ThreadLocal() {
            @Override
            protected Integer initialValue() {
                return 0;
            }
        };
        
        // 最终也是重写 initialValue 方法
        static ThreadLocal<Integer> count = ThreadLocal.withInitial(() -> 0);
        
        public void sell() {
            count.set(1+count.get());
        }
    }
    ```

  - 使用

    ```java
    House house = new House();
    new Thread(() -> {
        try {
            
        } finally {
            // 线程结束时释放，以防内存泄露
            // 如果使用线程池，存在线程复用，还需要通过remove将ThreadLocal中的数据清除
            house.count.remove();
        }
    }).start();
    ```

- 引用：内存回收后，引用会被放入指定的ReferenceQueue中

  - 强引用 Reference
  - 软引用 SoftReference：内存充足时不会进行垃圾收集，内存回收；内存不充足时会进行
  - 弱引用 WeakReference：只要垃圾回收机制运行，就会回收该对象占用的内存
  - 虚引用 PhantomReference：虚引用不会决定对象的生命周期，调用get()返回的是null

- 结构

  - ThreadLocalMap 是 ThreadLocal 的静态内部类。
  - Thread 中聚合了 ThreadLocal.ThreadLocalMap

- 部分源码**jdk17**

  ```java
  public class Thread implements Runnable {
      ThreadLocal.ThreadLocalMap threadLocals = null;
  }
  ```

  ```java
  public class ThreadLocal<T> {
      public T get() {
          Thread t = Thread.currentThread(); // 获得当前线程
          ThreadLocalMap map = getMap(t);
          if (map != null) {
              ThreadLocalMap.Entry e = map.getEntry(this);
              if (e != null) {
                  T result = (T)e.value;
                  return result;
              }
          }
          return setInitialValue();
      }
      
      private T setInitialValue() {
          T value = initialValue();
          // 将 value 放入当前线程的ThreadLocalMap中，若为空，则
      }
      
      // 获得 Thread 中的属性 ThreadLocal.ThreadLocalMap
      ThreadLocalMap getMap(Thread t) { return t.threadLocals; }
      
      public void set(T value) {
          Thread t = Thread.currentThread(); // 获得当前线程
          ThreadLocalMap map = getMap(t);
          if (map != null) {
              map.set(this, value);
          } else {
              createMap(t, value);
          }
      }
      
      public void remove() {
           ThreadLocalMap m = getMap(Thread.currentThread());
           if (m != null) { m.remove(this); }
       }
  }
  ```

  ```java
  static class ThreadLocalMap {
      private static final int INITIAL_CAPACITY = 16; // 初始容量
      private Entry[] table;
  
      static class Entry extends WeakReference<ThreadLocal<?>> {
          Object value;
  
          Entry(ThreadLocal<?> k, Object v) {
              super(k); // super是WeakReference
              value = v;
          }
      }
  
      private void set(ThreadLocal<?> key, Object value) {
  
          Entry[] tab = table;
          int len = tab.length;
          // key不是真正的ThreadLocal，而是通过一定算法转成了int索引，以索引当key，获取是也一样
          int i = key.threadLocalHashCode & (len-1);
  
          for (Entry e = tab[i]; e != null; e = tab[i = nextIndex(i, len)]) {
              if (e.refersTo(key)) { // jdk16引入，用于判断传入的对象引用是否是Reference盒子中的引用
                  // 存在，更新table中的Entry
                  e.value = value;
                  return;
              }
              if (e.refersTo(null)) { // 如果传入null，则只有在盒子中的引用对象被gc回收时才返回true。
                  replaceStaleEntry(key, value, i);
                  return;
              }
          }
          // 不存在，创建新Entry，并存放在table中
      }
  
      private void remove(ThreadLocal<?> key) {
          Entry[] tab = table;
          int len = tab.length;
          int i = key.threadLocalHashCode & (len-1);
          for (Entry e = tab[i]; e != null; e = tab[i = nextIndex(i, len)]) {
              // jdk16引入，用于判断传入的对象引用是否是Reference盒子中的引用
              if (e.refersTo(key)) {
                  e.clear(); // 将盒子中的引用清掉，即盒子置空
                  expungeStaleEntry(i); // 清掉table中的空盒子
                  return;
              }
          }
      }
  }
  ```

- **内存泄露**：调用 ThreadLocal 的 set(value) 方法实际上是将 <font color=red>new WeakRefernece(this) 作为 key，value 作为值从而形成的Entry对象</font>存入 ThreadLocalMap 中。（实际上，通过上述源码可知，真正的key是数组索引，真正的value是弱引用子类中 的属性）

  - 两条引用
    - 线程中 `ThreadLocal tl = new ThreadLocal()` 强引用
    - 调用 set() 方法后，新建一个 Entry 对象，Entry对象里的key是WeakReference这个“盒子”，这个“盒子”弱引用指向这个 ThreadLocal 对象

  - 当第一个中的强引用失效时（如设tl=null），ThreadLocal 对象只剩下弱引用，就会被回收，此时就会形成 Entry 中 key 为 WeakReference这个盒子 而 值依旧存在的状况，此时的值无意义，但有引用无法回收
    - 调用 threadLocal 的 set()、get()、remove() 方法时，都会调用 expungeStaleEntry() 方法，将key为空盒子的值全部置为null从而回收值的内存；其中remove先将key(Reference盒子)中的引用清掉






## 拓展

### JMM

- jvm 规范中试图定义一种 java 内存模型（java memory model）来<font color=red>屏蔽各种硬件和操作系统的内存访问</font>，用以实现 java 程序在各种平台下都<font color=red>能达到一致的内存访问效果</font>
  - 描述的是一组约束和规范，定义了程序（尤其是多线程）各个变量的读写访问方式并决定一个线程对共享变量的写入何时以及如何变为对另一个线程可见
  - 三大特性
    - 原子性
    - 可见性
    - 有序性
- happens-before：先行发生
  - 如果一个操作 happens-before 另一个操作，那么第一个操作的执行结果将对第二个操作可见，而且第一个操作的执行顺序排在第二个操作之前
  - 两个操作之间存在 happens-before 关系，并不意味着一定要按照 happens-before 原则制定的顺序来执行。如果重排序之后的执行结果与按照 happens-before 关系执行的结果一致，那么这种重排序并不非法
  - 8条规则
    - 次序规则：一个线程内，写在前面的操作先行发生于写在后面的操作
    - 锁定规则：unlock操作先行发生于
    - volatile 变量规则：对一个 volatile 变量的写操作先行发生于**后面**对这个变量的读操作
    - 传递规则：A 先行发生于 B，B 先行发生于 C，则 A 先行发生于 C
    - 线程启动规则：线程对象的 start() 方法先行发生于此线程中的每个操作
    - 线程中断规则：对线程 interrupt() 方法的调用先行发生于被中断线程的代码检测到中断的发生
    - 线程终止规则：线程中的所有操作都先行发生于对终止线程的终止检测，如可用 isAlive() 检测是否终止
    - 对象终结规则：一个对象的初始化完成（构造函数的执行结束）先行发生于它的 finalize() 方法



### <a href="https://www.bilibili.com/video/BV1ar4y1x727?p=63&vd_source=25ad2de4838bd28372a4956bac63c618">volatile</a>

- 可见性

  ```java
  public class VisiableTest {
      static volatile boolean flag = true;
      public static void main(String[] args) throws InterruptedException {
          System.out.println("start");
          // 循环体中有sout打印语句，不加volatile也可以保证可见性
          new Thread(() -> {while(flag) {}}).start();
          Thread.sleep(2000);
          System.out.println("设置为false");
          flag = false;
      }
  }
  ```

  - 锁对于其加锁的代码部分，既可以保证原子性，也可以保证可见性
  - 锁对于其加锁的代码部分，保证只有一个线程执行，根据先行发生happens-before原则下的次序规则：一个线程内，写在前面的操作先行发生于写在后面的操作，而先行发生虽然不能保证不发生指令重排，但其保证是否发生指令重排对结果不影响，因此锁也可以保证有序性

- 有序性

  ```java
  public class Singleton {
      private Singleton(){};
      private static Singleton instance = null;
      
      public static Singleton getInstance() {
          if (instance == null) {
              synchronized (Singleton.class) {
                  if (instance == null) {
                      
                      instance = new Singleton();
                      // new：创建对象，将对象引用入栈
                      // dup：复制一份对象引用
                      // invokespecial：创建实例关联对象引用
                      // putstatic :将对象引用赋值给 static instance 
                  }
              }
          }
          return instance;
      }
  }
  ```

  - 若 invokespecial 和 putstatic 指令重排，另一个线程返回的可能是未初始完的实例对象

- 原理：底层实现的原理是内存屏障

  - 对 volatile 变量的写指令后会加入写屏障，写屏障保证在该屏障之前的、对共享变量的改动，都同步到主存中；写屏障之前的代码不会被指令重排的写屏障之后
  - 对 volatile 变量的读指令前会加入读屏障，读屏障保证在该屏障之后的、对共享变量的读取，加载的是主存中最新的数据；读屏障之前的代码不会被指令重排到读屏障之前



### 内存布局

- 对象的堆内存中的存储布局

  - 对象头

    - 对象标记（Mark Word）

      <table>
          <caption>64位虚拟机</caption>
          <tr>
              <td rowspan="2">锁状态</td>
              <td colspan="4">56bit</td>
              <td rowspan="2">1bit</td>
              <td rowspan="2">4bit</td>
              <td rowspan="2">1bit<br/>(是否是偏向锁)</td>
              <td rowspan="2">1bit<br/>(锁标记位)</td>
          </tr>
          <tr>
              <td colspan="2">25bit</td>
              <td colspan="2">31bit</td>
          </tr>
          <tr>
              <td>无锁</td>
              <td colspan="2">unused</td>
              <td colspan="2">对象 hashCode</td>
              <td>Cms_free</td>
              <td>对象分代年龄</td>
              <td>0</td>
              <td>01</td>
          </tr>
          <tr>
              <td>偏向锁</td>
              <td colspan="3">threadId(54bit)</td>
              <td colspan="1">Epoch(2bit)</td>
              <td>Cms_free</td>
              <td>对象分代年龄</td>
              <td>1</td>
              <td>01</td>
          </tr>
          <tr>
              <td>轻量级锁</td>
              <td colspan="7">指向栈中的记录的指针</td>
              <td>00</td>
          </tr>
          <tr>
              <td>重量级锁</td>
              <td colspan="7">指向重量级锁的指针</td>
              <td>10</td>
          </tr>
          <tr>
              <td>GC标志</td>
              <td colspan="7">空</td>
              <td>01</td>
          </tr>
      </table>

    - 类元信息（类型指针、klass Pointer）：指向方法区中某个类的Klass类元信息

    - 长度（length）：数组对象拥有

  - 实例数据（instance data）：存放类的属性（Field）数据信息，包括父类的属性信息

  - 对齐填充（padding）：保证对象存储大小为8个字节的倍数

- 问题

  - `Object o = new Object()`中各部分存储位置
    - Object是方法区中的类元信息；o是栈内存中的引用；new Object()在堆内存中
  - new 一个对象占多少内存大小
    - 64位操作系统中，Mark Word 占8个字节，类型指针占8个字节，对象头一共16个字节。所以一个对象最小16个字节
      - 默认开启了压缩指针，一个对象最小 12+4(对齐填充) 个字节
    - 若包含属性`int id`、`boolean flag`，实例数据 4+1 个字节
    - 对齐填充会填充3个字节到24个字节
  - 为什么新生区对象经过15次垃圾回收到养老区
    - 对象标记中对象分代年龄由 4bit 记录，最大 1111 即为15



### AQS

- AQS使用一个 `volatile int state` 的成员变量来表示同步状态，通过内置的 FIFO 队列来完成资源获取的排队工作，将每条要去抢占资源的线程封装成一个静态内部类Node的节点对象来实现锁的分配，通过CAS完成对state值的修改

  ![AQS](D:\picture\typora\java2\AQS.png)

- 源码分析

  - 结构

    - ReentrantLock 类中有三个静态内部类，分别是：抽象类Sync，Sync的子类NonfairSync、FairSync

    - 抽象类Sync继承AbstractQueuedSynchro

    - AbstractQueuedSynchro 中有一个静态内部类Node，有一个volatile int属性state，Node属性的head，Node属性的tail等

    - AbstractQueuedSynchro 中定义了多个**模板方法**，常用的是加锁时的**acquire(int)**和释放锁时的**release(int)**

    - AbstractQueuedSynchro 继承了 AbstractOwnableSynchronizer

      ```java
      public abstract class AbstractOwnableSynchronizer implements java.io.Serializable {
          private static final long serialVersionUID = 3737899427754241961L;
          protected AbstractOwnableSynchronizer() { }
          // 被transient关键字修饰的成员属性变量不被序列化
          // 该属性用于记录当前获得锁的线程，可用于判断是否是重入线程
          private transient Thread exclusiveOwnerThread;
          protected final void setExclusiveOwnerThread(Thread thread) {
              exclusiveOwnerThread = thread;
          }
          protected final Thread getExclusiveOwnerThread() {
              return exclusiveOwnerThread;
          }
      }
      ```

  - 流程

    - ReenTrantLock lock = new ReentrantLock(true/flase)

      ```java
      public class ReentrantLock implements Lock, java.io.Serializable {
          private final Sync sync;
          
          public ReentrantLock() {
              sync = new NonfairSync();
          }
          
          public ReentrantLock(boolean fair) {
              sync = fair ? new FairSync() : new NonfairSync();
          }
      }
      ```

    - lock.lock() 方法会调用模板方法 sync.acquire(1)

      ```java
      // 此方法在 Sync 的父类 AQS 中定义并实现，下面是它的源码
      public final void acquire(int arg) {
          if (!tryAcquire(arg) && acquireQueued(addWaiter(Node.EXCLUSIVE), arg)) selfInterrupt();
      }
      
      // 将以上代码展开
      public final void acquire(int arg) {
          if (!tryAcquire(arg)) { // 这一部分类似于轻量级锁
              // 在tryAcquire方法的 CompareAndSet ，的自旋争抢锁阶段没有获得锁
              
              // 这一部分类似于重量级锁，但并没有用户态到内核态的切换
              // i.初始化等待链表  ii.并获得当前线程，封装为Node，返回
              Node node = addWaiter(Node.EXCLUSIVE, arg);
              if (acquireQueued(node, arg)) {
                  selfInterrupt;
              } 
          }
      }
      ```

      - 首先调用 tryAcquire() 尝试抢占资源，<font color=red>compareAndSetState(**0**, acquires)</font>（具体方式看下面源码）
      - 抢占失败
        - 调用 addWaiter方法
          - 初始化或更新等待链表
            - 初始化：创建一个虚拟占位空节点放在head，虚拟头节点的thread属性为null
            - 更新：后续加入的包含线程的节点依次放在head的后面
          - 并获得当前线程，封装为Node，返回
        - 调用 acquireQueued 方法
          - 先判断当前节点的前一个节点是否为虚拟节点，再 tryAcquire() 再次尝试抢占资源
            - 若抢占成功，将头节点设置为当前抢占成功的节点，虚拟节点的next 置为null
            - 若失败，则判断waitstate的值
              - 若为0，将值改为-1，返回flase。进入第二次循环，回到上一步（判断虚拟节点，尝试抢占资源）
              - 若为-1，返回true，在执行 LockSupport.park(this) 进入等待

    - unlock() 方法会调用模板方法 asq.release(1)

      ```java
      public final boolean release(int arg) {
          if (tryRelease(arg)) {
              Node h = head;
              if (h != null && h.waitStatus != 0)
                  unparkSuccessor(h);
              return true;
          }
          return false;
      }
      ```

  - NonfairSync 和 FairSync 都实现了 tryAcquire() 方法，区别在于非公平锁获取锁时比公平锁中少一个判断 !hasQueuedPredecessors()；hasQueuedPredecessors() 中判断了FIFO中是否有非空Node（是否需要排队）

    ```java
    // 此处为公平锁的 tryAcquire 方法实现
    protected final boolean tryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        int c = getState();
        if (c == 0) {
            // 公平锁会去检查FIFO中是否有等待的节点，若有，则不会去执行compareAndSetState
            if (!hasQueuedPredecessors() &&
                // ※ 这里CAS的比较值是 0 ，只有当state为0时，才可以抢到锁
                compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        // 如果已经获得了锁，线程还是当前线程，表示发生了锁重入
        else if (current == getExclusiveOwnerThread()) {
            // 重入一层，state加1
            int nextc = c + acquires;
            if (nextc < 0)
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }
        return false;
    }
    ```

  - AQS类已经实现了addWaiter()

    ```java
    private Node addWaiter(Node mode) {
        // mode：Node.EXCLUSIVE 排他模式
        // 创建一个封装当前线程的新node，新node的waitState属性值为0
        Node node = new Node(mode);
    
        for (;;) {
            Node oldTail = tail;
            if (oldTail != null) {
                node.setPrevRelaxed(oldTail);
                if (compareAndSetTail(oldTail, node)) {
                    oldTail.next = node;
                    return node;
                }
            } else {
                // 初始化队列。new一个空节点作为虚拟节点，head = tail = new Node()
                initializeSyncQueue();
            }
        }
    }
    ```

  - AQS类已经实现了acquiredQueued()

    ```java
    // 不可打断模式：当线程抢到锁时，才会跳出循环，返回打断标记，再打断
    final boolean acquireQueued(final Node node, int arg) {
        try {
            boolean interrupted = false;
            for (;;) {
                // 获得node前一个节点，若前一个节点为null，报错
                final Node p = node.predecessor();
                if (p == head && tryAcquire(arg)) {
                    // node设置为头节点，node的thread、prev属性置为null
                    setHead(node);
                    // 此时原先的虚拟头节点无引用指向，将被回收；node节点将成为新的虚拟头节点
                    p.next = null; // help GC
                    // 当线程抢到锁时，才会跳出循环，返回打断标记
                    return interrupted;
                }
                // 若p节点的waitstate为0，则设置为-1并返回false；若为-1，则返回true
                if (shouldParkAfterFailedAcquire(p, node) &&
                    // 调用 LockSupport.park(this)
                    parkAndCheckInterrupt())
                    interrupted = true;
            }
        } catch (Throwable t) {
            cancelAcquire(node);
            throw t;
        }
    }
    ```

    ```java
    private final boolean parkAndCheckInterrupt() {
        LockSupport.park(this);
        // 返回为当前线程在park时是否被打断过，并清除打断标记
        return Thread.interrupted();
    }
    ```

  - tryRelease(int releases)

    ```java
    protected final boolean tryRelease(int releases) {
        // 若是重入锁，释放一层是 state减1
        int c = getState() - releases;
        if (Thread.currentThread() != getExclusiveOwnerThread())
            throw new IllegalMonitorStateException();
        boolean free = false;
        if (c == 0) {
            free = true;
            setExclusiveOwnerThread(null);
        }
        setState(c);
        return free;
    }
    ```

- 问题

  - 锁重入原理：通过查看tryAcquire和tryRelease方法可知，**利用state的增减来实现**。state为2，表示重入一层；为0，表示释放

  - 可打断原理：lock.lockInterruptible() 调用的是 aqs.doAcquireInterruptibly() 方法

    ```java
    public void lockInterruptibly() throws InterruptedException {
        sync.acquireInterruptibly(1);
    }
    ```

    ```java
    private void doAcquireInterruptibly(int arg)
        throws InterruptedException {
        final Node node = addWaiter(Node.EXCLUSIVE);
        try {
            for (;;) {
                final Node p = node.predecessor();
                if (p == head && tryAcquire(arg)) {
                    setHead(node);
                    p.next = null; // help GC
                    return;
                }
                if (shouldParkAfterFailedAcquire(p, node) &&
                    parkAndCheckInterrupt())
                    // 与不可打断相比，这里在被打断时，直接抛出异常，不会在进入下一次循环
                    throw new InterruptedException();
            }
        } catch (Throwable t) {
            cancelAcquire(node);
            throw t;
        }
    }
    ```

  - 公平锁原理：通过tryAcquire方法可知，公平锁会去检查FIFO中是否有等待的节点

  - <a href="https://www.bilibili.com/video/BV16J411h7Rd?p=245&vd_source=25ad2de4838bd28372a4956bac63c618">await 和 singal 原理</a>




### 读写锁

- ReentrantReadWriteLock使用

  - 读锁不支持条件变量
  - 重入时不支持升级：即持有读锁的情况下去获取写锁，会导致获取写多永久等待
  - 重入时支持降级：即持有写锁的情况下可以获取读锁

  ```java
  class CacheData {
      Object data;
      // 判断数据是否有效，如果为false，说明要更新数据
      volatile boolean cacheValid;
      final ReentrantReadWriteLock rwl = new ReentrantReadWriteLock();
      
      void processCachedData() {
          rwl.readLock().lock();
          if (! cacheValid) {
              // 获取写锁前必须释放读锁
              rwl.readLock().unlock();
              rwl.writeLock().lock();
              try {
                  // double check
                  if (! cacheValid) {
                      data = ...; // 更新数据操作
                      cacheValid = true;
                  }
                  // 将写锁降级为读锁，释放写锁
                  rwl.readLock().lock();
              } finally {
                  rwl.writeLock().unlock();
              }
          }
          // 使用完数据后释放读锁
          try {
              use(data);
          } finally {
              rwl.readLock().unlock();
          }
      }
  }
  ```

- ReentrantReadWriteLock原理

  - 读写锁用的是同一个Sycn同步器，因此等待队列、state等也是同一个
  - state 的低16位供写锁使用，高16位供读锁使用

- StampedLock 读写锁

  ```java
  @Slf4j
  class DataContainer {
      private int data = 0;
      private final StampedLock lock = new StampedLock();
  
      public int optimisticRead() throws InterruptedException {
          long stamp = lock.tryOptimisticRead();
          log.info("乐观读锁添加成功，stamp：{}", stamp);
          TimeUnit.MILLISECONDS.sleep(1000);
          if(lock.validate(stamp)) {
              log.info("乐观读锁未升级，stamp：{}", stamp);
              return data;
          }
          log.info("乐观读锁升级，stamp：{}", stamp);
          try {
              stamp = lock.readLock();
              log.info("读锁添加成功，stamp：{}", stamp);
              TimeUnit.MILLISECONDS.sleep(1000);
              return data;
          }finally {
              log.info("释放读锁，stamp：{}", stamp);
              lock.unlockRead(stamp);
          }
      }
  
      public void write() throws InterruptedException {
          TimeUnit.MILLISECONDS.sleep(500);
          long stamp = lock.writeLock();
          log.info("写锁添加成功，stamp：{}", stamp);
          data ++;
          TimeUnit.MILLISECONDS.sleep(2000);
          lock.unlockWrite(stamp);
          log.info("释放写锁，stamp：{}", stamp);
      }
  }
  ```

  ```log
  [INFO ] 12:05:56:426   Thread-0   --->    乐观读锁添加成功，stamp：256
  [INFO ] 12:05:56:936   Thread-1   --->    写锁添加成功，stamp：384
  [INFO ] 12:05:57:437   Thread-0   --->    乐观读锁升级，stamp：256
  [INFO ] 12:05:58:943   Thread-1   --->    释放写锁，stamp：384
  [INFO ] 12:05:58:943   Thread-0   --->    读锁添加成功，stamp：513
  [INFO ] 12:05:59:954   Thread-0   --->    释放读锁，stamp：513
  ```

  - 乐观读锁不会修改stamp值
  - 读锁和写锁会修改stamp值









# JVM

> The Java Virtual Machine

> 虚拟机参数：-XX:+PrintFlagsFinal（执行前，打印所有虚拟机参数及状态）

## 基础

### 字节码文件

- 基本信息：魔数、字节码文件对应的 java 版本号、访问标识（public final 等等）、父类和接口

  - Magic魔数

    - 文件不能通过文件扩展名来确定文件类型

    - 软件使用文件的头几个字节（文件头）来校验文件的类型，如果软件不支持该种类型就会报错

      | 文件类型   | 字节数 | 文件头                     |
      | ---------- | ------ | -------------------------- |
      | JPEG       | 3      | FFD8FF                     |
      | PNG        | 4      | 89504E47（文件尾也有要求） |
      | bmp        | 2      | 424D                       |
      | XML        | 5      | 3C3F786D6C                 |
      | AVI        | 4      | 41564920                   |
      | JAVA字节码 | 4      | CAFEBABE                   |

  - 版本号：jdk1.2后，主版本号减44 为字节码jdk版本

- 常量池：保存了字符串常量、类或接口名、字段名（主要在字节码指令中使用）

- 字段：当前类或接口中声明的字段信息

- 方法：当前类或接口声明的方法信息（字节码指令）

  - 操作数栈（临时存放）

  - [局部变量表数据](#局部变量表)（局部变量存放位置）

    ```java
    public static void main(String args) {
        int i = 0;
        int j = i+1;
    }
    ```

    | 索引 | 0    | 1    | 2    |
    | ---- | ---- | ---- | ---- |
    | 数组 | args | i    | j    |

  - 实例

    > i++ 与 ++i 的区别
    >
    > > i++：会先让栈中保留一份原始值，再计算
    > >
    > > ++i：会先计算，在让栈中保留一份计算后的值

    ```java
    public static void main(String args) {
        int i = 0;
        i = i++;
        // System.out.println(i) // 0
    }
    
    // 指令
    iconst_0     // 将0存到操作数栈中           栈：->[0] 数组：[args, i]
    istore_1     // 从栈中取出一个存到数组1号位   栈：->[] 数组：[args, i=0]
    iload_1      // 将数组1号位的值加载入栈      栈：->[0] 数组：[args, i=0]
    iinc 1 by 1  // 对数组1号位的值加1          栈：->[0] 数组：[args, i=1]
    istore_1     // 从栈中取出一个存到数组1号位   栈：->[] 数组：[args, i=0]
    return
    ```

    ```java
    public static void main(String args) {
        int i = 0;
        i = ++i;
        // System.out.println(i) // 1
    }
    
    // 指令
    iconst_0     // 将0存到操作数栈中           栈：->[0] 数组：[args, i]
    istore_1     // 从栈中取出一个存到数组1号位   栈：->[] 数组：[args, i=0]
    iinc 1 by 1  // 对数组1号位的值加1          栈：->[] 数组：[args, i=1]
    iload_1      // 将数组1号位的值加载入栈      栈：->[1] 数组：[args, i=1]
    istore_1     // 从栈中取出一个存到数组1号位   栈：->[] 数组：[args, i=1]
    return
    ```

    ```java
    private static int number = 3;
    public static void add1() {
        return number++;
    }
     // 先复制 dup，后计算
    getstatic #1   // #1处静态字段值入栈      栈：->[3]       数组：[]
    dup            // 复制栈顶部数据          栈：->[3, 3]    数组：[]
    iconst_1       //                      栈：->[1, 3, 3] 数组：[]
    iadd           // 让栈顶部两个数相加      栈：->[4, 3]    数组：[]
    putstatic #1   // 将栈顶部值存到#1处      栈：->[3]       数组：[]
    ireturn        // 返回栈顶部值           栈：->[]        数组：[]
    ```

    ```java
    private static int number = 3;
    public static void add1() {
        return ++number;
    } 
         
     // 先计算，后复制dup
    getstatic #1   // #1处静态字段值入栈      栈：->[3]       数组：[]
    iconst_1       //                      栈：->[1, 3]    数组：[]
    iadd           //                      栈：->[4]       数组：[]
    dup            // 复制栈顶部数据          栈：->[4, 4]    数组：[]
    putstatic #1   // 将栈顶部值存到#1处      栈：->[4]       数组：[]
    ireturn        // 返回栈顶部值           栈：->[]        数组：[]
    ```

    效率：

    ```java
    public static void add6() {
    int i = 0, j = 0, k = 0, l = 0, m = 0;
    i++;          // iinc 0 by 1
    j = j+1;      // iload_1  iconst_1  iadd  istore_1
    k+=1;         // iinc 2 by 1
    ++l;          // iinc 3 by 1
    m+=3;         // iinc 4 by 3   效率最高
    }
    ```

- 属性：类的属性，比如源码的文件名，内部类的列表等





### Arthas

> [Arthas 是一款线上监控诊断的产品](https://arthas.aliyun.com/doc/)

- 启动

  ```shell
  java -jar arthas-boot.jar
  ```

- 常用指令

  - 参数使用说明

    - class-pattern：表示 `指令 类的全路径限定名`
    - [i:]：表示 `指令 -i 值`
    - [a]：表示 `指令 -a`

  - memory：查看JVM内存信息

  - dashboard ：显示当前系统的实时数据面板

    - [i:]：刷新实时数据的时间间隔（ms），默认5000ms
    - [n:]：刷新实时数据的次数

  - dump：将已加载类的字节码文件输出到特定目录

    - class-pattern：类的全路径限定名（cn.eli.SpringApp)
    - [d:]：指定目录

  - jad：反编译已加载类的源码

    - class-pattern：类的全路径限定名

  - classloader：查看 classloader 的继承树，urls，类加载信息

    - [l]：按类加载的实例进行统计
    - [t]： 打印所有 ClassLoader 的继承树
    - [a]：列出所有 ClassLoader 加载的类，请谨慎使用
    - [c:]：加上指定 ClassLoader 的 hashcode，可查看指定classloader的信息

  - sc：查看 JVM 已加载的类信息

    - class-pattern
    - [d]：输出当前类的详细信息，包括这个类所加载的原始文件来源、类的声明、加载的 ClassLoader 等详细信息。如果一个类被多个 ClassLoader 所加载，则会出现多次

  - mc：将 .java 文件编译为 .class 文件

    > 注意，mc 命令有可能失败。如果编译失败可以在本地编译好`.class`文件，再上传到服务器。具体参考[retransform](https://arthas.aliyun.com/doc/retransform.html)命令说明。

    - menu/OneJavaPro.java：目录/文件名.java
    - [d:]：指定输出目录
    - [c:]：加上指定 ClassLoader 的 hashcode（一个java程序往往还用到了其他类，如果不结合其他类共同编译，往往会失败，而ClassLoader可以得到其他类的信息，因此，如果该java程序来自部署的class反编译，还要加上原本加载这个java程序的ClassLoader。如果对反编译的java程序进行修改，新添加的代码可能用到了该ClassLoader中未存储的类，可能导致编译失败）

  - retransform：加载外部的`.class`文件，替换内存中 jvm 已加载的类（热部署）

    - menu/OneJavaPro.class：目录/文件名.class
    - 问题
      - 重启会失效
      - 不能添加方法或者字段、也不能更新正在执行的方法





### 类的生命周期

- <span id="加载">加载</span>

  - 首先，类加载器根据类的全限定名通过不同渠道以二进制流的方式获取字节码信息
    - 本地文件中的类
    - 动态代理生成的类
    - 通过网络传输的类
  - 类加载器在加载完类之后，java虚拟机会将<font color=red>字节码中的信息保存到[方法区](#方法区)中</font>：生成一个 InstanceKlass 对象，保存类的所有信息（如基本信息、常量池、字段、方法等），还包含实现特定功能比如多态的信息（虚方法表）
  - java虚拟机<font color=red>还会在堆中生成一份与方法区中数据类似的 java.lang.Class 对象</font>，作用是在 java代码中去获取类的信息以及存储静态字段的数据（如反射）（jdk8及之后静态字段数据存放在堆中的类信息中）
  - 方法区中的类数据 和 堆区中的类数据 相关联
  - 为什么在方法区中和堆中都生成一份类数据
    - 方法区中的是由c++生成的，不便操作
    - 方法区中的类的信息包含了一些不需要开发者使用的数据（安全性和便携性）

- 连接

  - 验证：验证内容是否满足《java虚拟机规范》

    - 魔数的校验，文件是否以0xCAFEBABE开头
    - 元信息的验证，如类必须有父类
    - 验证程序执行指令的语义，如 goto 10  跳转的10号指令是否存在
    - 字节码版本与jdk版本校验

  - 准备：给静态变量赋初始值（如果静态变量被final修饰，则在准备阶段就赋上指定值）

    | 数据类型 | 初始值   | 数据类型     | 初始值 |
    | -------- | -------- | ------------ | ------ |
    | int      | 0        | byte         | 0      |
    | long     | 0L       | boolean      | false  |
    | short    | 0        | double       | 0.0    |
    | char     | '\u0000' | 引用数据类型 | null   |

  - 解析：将常量池中的符号引用（如#2）替换为指向内存的直接引用

- 初始化：执行静态代码块中的代码，为静态变量赋值

  ```java
  public class Demo {
      static {
          value = 2;
      }
      public static int value = 1;
      public static void main(String[] args) {
          System.out.println(value); // 值为1，static自上而下执行，但main方法最后执行
      }
  }
  
  // 方法
  // [0] <init> 构造方法
  // [1] main
  // [2] <clinit> 该方法不一定存在。表示初始化阶段执行的操作
  //      iconst_2;  putstatic #2;  iconst_1;   putstatic #2;   return;
  ```

  - 以下几种方式会导致类的初始化

    > 添加  **-XX:+TranceClassLoading** 参数可以打印出加载并初始化的类

    - 访问一个类的静态变量或静态方法，<font color="red">如果变量是final修饰的，并且等号右边是常量不会触发初始化</font>

      ```java
      private static final int a = 1; // 访问该变量不会触发初始化
      private static final LocalDateTime dateTime = LocalDateTime.now(); // 会
      ```

    - 调用 Class.forName(String className) 

    - new 一个该类的对象

    - 执行 Main 方法的当前类

      ```java
      // 打印顺序 D A C B C B
      public class OneClass {
          public static void main(String[] args) {
              System.out.println("A");
              new OneClass();
              new OneClass();
          }
          public OneClass() {
              System.out.println("B");
          }
          
          // 实例代码块会与构造方法混合，在构造方法前执行，多个实例代码块自上而下执行
          {
              System.out.println("C");
          }
          static {
              System.out.println("D");
          }
      }
      ```

    - 数组的创建不会导致数组中元素的类的初始化

      ```java
      OneClass[] arr = new OneClass[10]; // 不会初始化 OneClass 类
      ```

    - 存在继承关系的初始化

      - 直接访问父类静态变量，不会触发子类的初始化
      - 子类的初始化\<clinit>调用前，会先调用父类的\<clinit>初始化方法

      ```java
      public class OneClass {
          public static void main(String[] args) {
              A a = new B();
              // B类中不定义a变量，结果为：3  3  3
              // B类中定义a变量，结果为：1  1  3
              System.out.println(a.a+" "+A.a+" "+B.a);
          }
      
          static class A {
              static {
                  a = 2;
                  System.out.println("A 类初始化");
              }
              static int a = 1;
          }
      
          static class B extends A {
              // static int a = 4;
              static  {
                  a = 3;
                  System.out.println("B 类初始化");
              }
          }
      }
      ```

- 使用：略

- 卸载：略





### 类加载器

> 是java虚拟机提供给应用程序去实现获取类和接口的字节码技术

#### 类加载器的分类

> jdk8 及 之前版本的类加载器

- 根据定义位置分类

  - 一类是 Java代码中实现的（如拓展类加载器、应用程序类加载器、自定义加载器）

    - jdk 中默认提供或者自定义
    - 都继承自抽象类 ClassLoader

  - 另一类是 Java虚拟机底层源码实现的（启动类加载器）

    - 源代码位于 Java虚拟机的源码中，实现语言与虚拟机底层语言一致，比如 Hotspot 使用的C++

    - 作用是加载程序运行时的基础类，如 java.lang.String，确保其可靠性

    - 通过该加载器加载的类获取类加载器时，会返回null

      ```java
      System.out.println(String.class.getClassLoader() == null); // true
      ```

- 根据作用分类

  - BootstrapClassLoader（启动类加载器）：加载 java 中最核心的类

    > 添加  -Xbootclasspath/a:<font color=blue>jar包目录</font>/<font color=blue>jar包名</font>  启动参数，可以指定该jar包中的类由启动类加载器加载 

    - 默认加载java安装目录下的 /jre/lib 下的类文件

  - ExtClassLoader（拓展类加载器）：允许扩展 java 中比较通用的类

    > 添加 -Djava.ext.dirs=<font color=blue>jar包目录</font>  启动参数，可以指定该jar包中的类由拓展类加载器加载 
    >
    > > 该参数默认后覆盖掉原始加载目录(/jre/lib/ext)，可以用 **对应的分隔符** 分隔多个路径，再追加原始加载目录
    > >
    > > > windows用 <font color=red>**;**</font> 
    > > >
    > > > macos/linex用 <font color=red>**:**</font> 

    - 默认加载java安装目录下的 /jre/lib/ext 下的类文件

  - AppClassLoader（应用程序类加载器）：加载应用使用的类

    - 该加载器加载的类范围覆盖了启动类加载器和拓展类加载器

  - 。。。。。。等等



#### 双亲委派机制

- 作用
  - 避免恶意代码替换 JDK 中的核心类库，如自定义的String类无法替换jdk中的String类，确保核心类库的完整性和安全性
  - 避免同一个类被多次加载
- 原理：当一个类加载器接收到加载类的任务时，会<font color=red>自底向上查找是否加载过，再自顶向下尝试进行加载（看是否在自己的加载范围内）</font> 
  - 顺序（父子关系）：顶——启动类加载器；中——拓展类加载器；底——应用程序类加载器
  - 当某个类都不在各层类加载器中，会报类找不到错误



#### 打破双亲委派机制

> 打破方法有三种
>
> > 自定义类加载器
> >
> > 线程上下文类加载器
> >
> > Osgi框架的类加载器（允许同级委派，可以实现热部署功能）

##### 自定义类加载器

- 重写 loadClass 方法，破坏双亲委派机制

- ClassLoader 抽象类源码

  - 构造方法

    ```java
    // 继承该类得到的类加载器 可以指定父类加载器
    protected ClassLoader(ClassLoader parent) {
        this(checkCreateClassLoader(), null, parent);
    }
    // 继承该类得到的类加载器 若不指定父类加载器，默认为 应用程序类加载器
    protected ClassLoader() {
        this(checkCreateClassLoader(), null, getSystemClassLoader());
    }
    ```

  - loadClass方法：双亲委派机制

    ```java
    public Class<?> loadClass(String name) throws ClassNotFoundException {
        return loadClass(name, false);
    }
    
    protected Class<?> loadClass(String name, boolean resolve) throws ClassNotFoundException {
        synchronized (getClassLoadingLock(name)) {
            // 首先，检查这个类是否已经被加载（双亲委派机制）
            Class<?> c = findLoadedClass(name);
            if (c == null) {
                long t0 = System.nanoTime();
                try {
                    if (parent != null) {
                        // 先让父类去加载
                        c = parent.loadClass(name, false);
                    } else {
                        c = findBootstrapClassOrNull(name);
                    }
                } catch (ClassNotFoundException e) {
                }
    
                // 如果当前类仍为空，由该类加载器加载
                if (c == null) {
                    long t1 = System.nanoTime();
                    c = findClass(name);
    
                    // this is the defining class loader; record the stats
                    PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                    PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                    PerfCounter.getFindClasses().increment();
                }
            }
            // 若传递为flase，则不进行连接，也不会执行到初始化方法<clinit>
            if (resolve) {
                resolveClass(c);
            }
            return c;
        }
    }
    ```

  - findClass方法：由子类去实现，核心逻辑是获得字节码文件并将字节码文件给到defineClass方法

    ```java
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        throw new ClassNotFoundException(name);
    }
    ```

  - defineClass方法：最后会追溯到本地方法，核心逻辑是做一系列检验

    ```java
    protected final Class<?> defineClass(String name, byte[] b, int off, int len, ProtectionDomain protectionDomain) throws ClassFormatError {
        // ......
    }
    ```

  - resolveClass方法：执行类生命周期中的连接阶段

    ```java
    protected final void resolveClass(Class<?> c) {
        if (c == null) {
            throw new NullPointerException();
        }
    }
    ```

  - <font color=red>正常实现一个自定义类加载器，不应该破坏双亲委派机制，应该重写findClass方法，比如从数据库中获得字节码文件传入到defineClass</font>

##### 线程上下文类加载器

> 以jdbc中 DriverManger和第三方数据库驱动Driver类的加载为例

- DirverManger 类位于rt.jar中，由启动类加载

- 引入第三方数据库驱动依赖，DriverManger加载第三方依赖

- 问题1：DriverManger 怎么知道第三方jar包中要加载的驱动在哪？（利用到SPI机制）

  - SPI机制（已jdbc的Driver为例）

    ```java
    package com.mysql.cj.jdbc;
    
    public class Driver enxtends NonRegisteringDriver implements java.sql.Driver {
        public Driver() throws SQLException;
        
        // 只要Driver类被加载，就会执行下面初始化代码，从而完成注册。
        // 因此此时关注该类如何被加载
        static {
            try {
                DriverManger.registerDriver(new Driver());
            }catch(SQLException var1) {
                throw new RuntimeException("Can't register driver!");
            }
        }
    }
    ```

    - 暴露实现类

      - 在ClassPath路径下创建 META-INF/services 的文件夹
      - 该目录下创建文件，以实现类作为文件内容，实现类实现的接口作为文件名（如文件名：java.sql.Driver  文件内容：com.mysql.cj.jdbc.Driver）

    - 使用ServiceLoader加载实现类com.mysql.cj.jdbc.Driver

      ```java
      // 代码中的 Driver.class 是 java.sql.Driver 接口
      // 目的是读取文件名与该接口名一致的文件的内容
      ServiceLoader<Driver> loadedDrivers = ServiceLoader.load(Driver.class);
      
      // 该迭代器存放着对应接口的实现类，与上述文件内容保持一致
      Iterator<Driver> driversIterator = loadedDrivers.iterator();
      ```

  - DriverManger 在初始化代码（static静态代码块）中通过 ServiceLoader 加载Driver类，即可让 Driver 类在初始化阶段完成注册

- 问题2：debug模式下可得到 Driver 类是由应用程序类加载器加载的，而DriverManger 本身是由启动类加载器加载的，它是如何拿到应用程序类加载器的呢？

  - SPI中ServiceLoader加载实现类Driver时，使用的是线程上下文保存的类加载器进行类的加载，这个类加载器默认是应用程序类加载器

    ```java
    public class ServiceLoader {
        // .......
        public static <s> ServiceLoader<S> load(Class<S> service) {
            // 默认是 应用程序类加载器
            ClassLoader cl = Thread.currentThread().getContextClassLoader();
            return ServiceLoader.load(service, cl);
        }
    }
    ```

- 问题3：JDBC案例中真的打破了双亲委派机制吗？

  - 周志明《深入理解Java虚拟机》提到打破了该机制
  - 实际上：没有，无论是DriverManger类的加载还是Driver实现类的加载，都是按该机制流程加载的。只是Driver实现类的加载由指定的类加载器直接加载（区别于访问类的静态变量导致类加载等间接加载方式）



#### Jdk8之后的类加载器

> 由于JDK9引入了module的概念，类加载器在设计上发生了很多变化

- 启动类加载器使用 Java 编写，位于 jdk.internal.loader.ClassLoaders 类中。
  - 启动类加载器由 Bootstrap（c++)   变为   BootClassLoader（java）
  - java中的BootClassLoader继承自BuiltinClassLoader实现从模块中找到要加载的字节码资源文件
  - 启动类加载器依然无法通过java代码获取到，返回的依然是null，保持了统一
- 扩展类加载器被替换为平台类加载器（Platform Class Loader）
  - 平台类加载器遵循模块化方式加载字节码文件，所以从继承 URLClassLoader 变成了 BuiltinClassLoader，BuiltinClassLoader实现了从模块加载字节码文件。
  - 平台类加载器的存在更多的是为了与老版本的设计方案兼容，自身没有特殊的逻辑





### 运行时内存区

#### 线程不共享区

> 每个线程都有各自的数据区，有三块区域
>
> > 程序计数器
> >
> > java虚拟机栈
> >
> > 本地方法栈

- 程序计数器：在代码执行过程中，程序计数器会记录下一行字节码指令的地址。执行完当前指令后，虚拟机的执行引擎根据程序计数器执行下一行指令

  - 控制解释器解释指令的顺序
  - 在多线程执行情况下，java虚拟机需要通过程序计数器记录指令执行到的位置

- java虚拟机栈：采用栈的数据结构来管理方法调用中的基本数据，每个方法的调用使用一个栈帧来保存

  - 栈帧的组成

    - <span id="局部变量表">局部变量表</span>：在运行过程中存放所有的局部变量

      > 栈帧中的局部变量表是一个数组，数组中的每一个位置称之为槽（slot），<font color=red>long和double类型占用两个槽，其他类型占用一个槽</font>

      ```java
      public int add(int i, int j) {
          int k = 2;
          return i+j+k;
      }
      ```

      ```java指令
      0 iconst_2
      1 istore_3
      2 iload_1
      3 iload_2
      4 iadd
      5 iload_3
      6 iadd
      7 ireturn
      ```

      | Nr.  | 起始pc（起始生效位置） | 长度（生效范围） | 序号（槽的编号） | 名字 |
      | ---- | ---------------------- | ---------------- | ---------------- | ---- |
      | 0    | 0                      | 8（0-7）         | 0                | this |
      | 1    | 0                      | 8（0-7）         | 1                | i    |
      | 2    | 0                      | 8（0-7）         | 2                | j    |
      | 3    | 2                      | 6（2-7）         | 3                | k    |

    - 操作数栈：

      - 用来存放中间数据的一小块区域
      - <font color=red>在编译期就可以确定操作数栈的最大深度，从而在执行时正确的分配内存大小</font>

    - 帧数据

      - 动态链接：保存了符号引用与运行时常量池内存地址的映射关系

        - 本类的静态引用会在连接的解析阶段将符号引用变为内存地址引用，但对其他类的静态变量的符号引用暂时不会去解析。
        - 对其他类中的静态变量的符号引用会在运行时通过帧数据中的动态链接进行解析

      - 方法出口：在method2方法入栈前，需要在method2的栈帧中记录method1方法中已执行到的位置，之后method2栈帧出站时，method2将栈帧中记录的method1执行到的位置交给程序计数器

        ```java
        public static void main() {
            method1();
        }
        public static void method1() {
            // ...
            method2();
            // ...
        }
        public static void method2() {
            // ...
        }
        ```

      - 异常表：存放的是代码中异常的处理信息，包含了try代码块和catch代码块执行后跳转到的字节码指令位置

- 本地方法栈

  - 区别于java虚拟机栈，本地方法栈存储的是native本地方法的栈帧
  - 在Hotspot虚拟机中，<font color=red>java虚拟机栈和本地方法栈实现上使用了同一个栈空间</font>



#### 线程共享区

> 所有线程共享的数据区，有两块区域
>
> > 堆
> >
> > 方法区

- 堆（heap space）

  - 说明

    - 一般java程序中堆内存是空间最大的一块内存区域。创建出来的对象都存在堆内存上
    - 栈上的局部变量表中，可以存放堆上对象的引用
    - 静态变量也可以存放堆上对象的引用，通过静态变量就可以实现对象在线程之间的共享
    - java虚拟机<font color=red>还会在类的加载阶段在堆中生成一份与方法区中数据类似的 java.lang.Class 对象</font>，作用是在 java代码中去获取类的信息以及存储静态字段的数据（如反射）（jdk8及之后静态字段数据存放在堆中的类信息中）

  - 使用上可分为三部分

    - used：指的是当前已使用的堆内存
    - total：java虚拟机已经分配的可用堆内存
    - max：java虚拟机可以分配的最大堆内存

  - [作用上可分为：新生代、老年代](#分代GC算法)

  - 测试代码（可用 arthas 的 memory 命令检测）

    ```java
    ArrayList<Object> objects = new ArrayList<>();
    while (true) {
        System.in.read(); // 每按一次回车，提交一次输入
        int bytes = 100*1024*1024;
        objects.add(new byte[bytes]);
        System.out.printf("增加了 %s MB%n", bytes/1024/1024);
    }
    ```

- <span id="方法区">方法区</span>

  > 方法区是《java虚拟机规范》中设计的虚拟概念，每款java虚拟机在实现上都各不相同。

  - Hotspot 方法区设计（可通过 arthas 的 memory 指令查看）
    - <font color=red>JDK7及之间版本</font>将方法区存放在<font color=red>堆区域的永生代空间（ps_perm_gen）</font>，堆的大小由虚拟机参数来控制
    - <font color=red>JDK8及之后版本</font>将方法区存放在<font color=red>元空间（metaspace）</font>中，元空间位于操作系统维护的<font color=red>直接内存</font>中，默认情况下只要不超过操作系统的承受上限，可以一直分配

  - 存放的数据
    - 类的元信息：保存了所有类的基本信息（如基本信息、常量池位置引用、字段、方法位置引用、虚方法表）
    - 运行时常量池：保存了字节码文件中的常量池的内容
    - 字符串常量池（jdk7之前）



#### 特殊的内存区

- 字符串常量池（位于堆中）：保存了字符串常量

  ```java
  // 先创建对象，再加载字符串常量池，再完成String初始化
  // 最后，局部变量表中 s1槽 存储堆中该String对象的内存地址
  String s1 = new String("abc"); 
  // 局部变量表中 s2槽 存储字符串常量池“abc”的内存地址
  String s2 = "abc";
   // 内存地址对比，false
  System.out.println(s1 == s2);
  
  
  
  // 创建String对象,获得其堆引用(1)  操作数栈 ->[(1)]
   0 new #2 <java/lang/String>
  // 复制操作数栈顶部数据   栈->[(1),(1)]
   3 dup
  // 从字符串常量池中读取"abc"引用(2)  栈->[(2),(1),(1)]
   4 ldc #3 <abc> 
  // 调用String初始化方法，将堆中String对象初始化完成    栈->[(1)]
   6 invokespecial #4 <java/lang/String.<init> : (Ljava/lang/String;)V>
  // 将String对象堆引用(1)存储到 s1槽 上  栈->[]
   9 astore_1                   
  10 ldc #3 <abc>  // 栈->[(2)]
  12 astore_2
  // 此时 s1槽 和 s2槽 存放的是不同的内存地址
  // 。。。。。。
  ```

  - 版本迭代

    - jdk7之前：运行时常量池逻辑包含字符串常量池，跟随方法区位于堆的永久代中
    - jdk7版本：字符串常量池被从方法区拿到了堆中，运行时常量池还在永久代
    - jdk8及之后：字符串常量池还在堆中，永久代空间被移出，运行时常量池跟随方法区移到元空间中

  - String对象的 intern() 方法往常量池中存放数据，并返回常量池中该数据的引用（jdk7之前）

    ```java
    String s1 = "a";
    String s2 = "b";
    String s3 = "ab";
    // 底层调用StringBuilder的拼接方法，返回一个String对象的引用
    String s4 = s1+s2;
    // "ab"在字符串常量池中已存在，不会再重复存放，只返回该引用
    String s5 = s4.intern();
    System.out.println(s3==s4);  // false
    System.out.println(s3==s5);  // true
    ```

  - 版本迭代

    ```java
    // jdk 11 环境
    // String s0 = "ab";  // 字符串常量池中会存在“ab”字符串
    String s1 = "a";
    String s2 = "b";
    String s3 = s1+s2;
    String s4= s3.intern();
    // jdk7之前：无论有没有第二行代码，都为false
    // jdk7及之后：有第二行代码，返回false；没有第二行代码，返回true
    System.out.println(s3 == s4);
    ```

    - jdk 7 之前：intern()方法会把第一次遇到的字符串实例**复制**到永久代的字符串常量池中，返回的也是永久代里面这个字符串实例的引用
    - jdk 7 及之后：由于字符串常量在堆中，为节省内存，intern()方法只将**第一次遇到的** **堆上字符串对象的引用**放入字符串常量池，并返回**堆上字符串对象的引用**
      - `字符串常量池中对象.intern()`：返回字符串常量池中对象引用
      - `堆中字符串对象.intern()`：将堆中该字符串对象的**引用**放入字符串常量池，并返回该引用

  - **字符串常量池是程序运行时加载的，而不是类被加载时就从静态常量池中加载**

- 静态变量的存储：JDK7及之后的版本中，静态变量是存放在[堆中的Class对象](#加载)中，脱离的永久代

- 直接内存

  - 直接内存并不在《Java虚拟机规范》中，所以并不属于java运行时内存区域。在JDK1.4中引入了NIO机制，使用了直接内存，主要为了解决以下两个问题
    - Java堆中的对象如果不再使用要回收，回收是会影响对象的创建和使用
    - IO操作比如读文件，需要先把文件读入直接内存（缓冲区）再把数据复制到Java堆中。现在直接放入直接内存即可，同时Java堆上维护了直接内存的地址引用，减少了数据复制的开销。写文件也是类似的思路。



#### 内存溢出

- **参数设置总览**
  - 栈：`-Xss栈大小`
  - 堆：total 和 max 一般设为相同值
    - total：`-Xms大小`
    - max：`-Xmx大小`
  - 方法区：一般需要设置
    - jdk8之前设置永久代大小：`-XX:MaxPermSize=值`
    - jdk7之后设置元空间大小：`-XX:MaxMetaspaceSize=值`
  - 直接内存（若使用到NIO，一般需要设置）：`-XX:MaxDirectMemorySize=值`



- java虚拟机栈内存溢出（StackOverflowError）

  - 栈内存默认大小
    - Linux x86（64位）：1MB
    - Windows：基于操作系统

  - 设置栈内存大小
    - 语法：-Xss栈大小
    - 单位：字节（默认，必须是1024的倍数）、k/K（KB）、m或M（MB）、g或G（GB）
    - 范围：Windows（64位）下的JDK8测试最小值位180K，最大值为1024M

- 堆内存溢出（OutOfMemoryError：Java heap space）

  - 堆内存默认大小

    - max大小默认是系统内存的1/4
    - total大小默认是系统内存的1/64

  - 设置total、max大小

    > 实际生产中，一般将total和max设置为同样的值

    - 设置total大小：-Xms大小（必须大于1M）
    - 设置max大小：-Xmx大小（必须大于2M）
    - 单位：字节（默认，必须是1024的倍数）、k/K（KB）、m或M（MB）、g或G（GB）

  - 是否直到 used=total=max 时，报出堆内存溢出

    > 不是，堆内存溢出的判断条件比较复杂，实际上，used接近max时就可能报出

- 方法区内存溢出（OutOfMemoryError：PermGen space（jdk7）/Metaspace（jdk8））

  > 实际生产中，需要设置元空间最大大小

  - 默认大小
    - jdk7及之间版本：方法区存放在堆中的永久代空间，默认有上限，堆中永久代的大小可通过虚拟机参数 -XX:MaxPermSize=值  来控制
    - jdk8及之后版本：方法区存放在直接内存中，默认无上限。可以使用 -XX:MaxMetaspaceSize=值  将元空间最大大小进行限制
  - 单位：字节（默认，必须是1024的倍数）、k/K（KB）、m或M（MB）、g或G（GB）

- 直接内存溢出（OutOfMemoryError：Direct buffer memory）

  > 实际生产中，如果使用到NIO，需要设置直接内存的最大大小

  - 语法：-XX:MaxDirectMemorySize=值
  - 单位：字节（默认，必须是1024的倍数）、k/K（KB）、m或M（MB）、g或G（GB）





### 垃圾回收

#### 概述

- C/C++的内存管理

  - C/C++中没有自动的回收机制，一个对象如果不再使用，需要手动释放，否则就会出现<font color=red>内存泄露</font>

    ```c
    #include "Test.h"
    int main() {
        while(true) {
            Test* test = new Test();
        }
        return 0;
    }
    ```

    ```c
    #include "Test.h"
    int main() {
        while(true) {
            Test* test = new Test();
            delete test; // 手动回收
        }
        return 0l
    }
    ```

  - 内存泄露指的是不再使用的对象所占的内存空间在系统中未被回收，内存泄露的积累可能导致内存溢出

- Java的内存管理：Java中为了简化对象的释放，引入了自动的垃圾回收机制。通过垃圾回收器来对不再使用的对象完成自动的回收，<font color=red>垃圾回收机制主要负责对堆上内存的回收</font>。其他很多现代语言比如C#、Python、Go都拥有自己的垃圾回收器

- 两种垃圾回收机制的对比

  - Java自动垃圾回收的缺点：无法控制垃圾回收的及时性
  - C/C++手动垃圾回收的缺点：编写不当容易出现悬空指针、重复释放、内存泄露等问题



#### 线程不共享区的回收

> 线程不共享的内存部分，往往是伴随着线程的创建而创建，线程的销毁而销毁。而方法的栈帧在执行完方法后就会自动弹出栈并释放掉对应的内存。



#### 方法区的回收

> 虚拟机参数
>
> > 打印 类的加载日志：-XX:+TraceClassLoading
> >
> > 打印 类的卸载日志：-XX:+TraceClassUnloading

- System.gc()：向虚拟机发送一个垃圾回收的请求，具体是否以及何时进行垃圾回收由Java虚拟机自行判断

- 方法区中能回收的内容主要就是不再使用的类。判定一个类可以被卸载，需要同时满足下面<font color=red>三个条件</font>
  - 此类所有的实例对象都已被回收，在堆中不存在任何该类的实例对象以及子类对象
  - 加载该类的类加载器已经被回收
  - 该类对应的 java.lang.Class 对象没有在任何地方被引用
- 由于平时使用中，大多数类一般由应用程序类加载器加载，该加载器不会被回收，所有类一般不会被卸载



#### 对象回收算法

> 判断对象能否被回收的算法

- 引用计数法

  - 对象能否能被回收，会根据对象是否<font color=red>被引用</font>来决定。

  - 引用计数法会在每个对象维护一个引用计数器，当对象被引用时加1，取消引用时减1

  - 说明
    - 优点：实现简单，C++中的智能指针就采用了引用计数法
    - 缺点：
      - 每次引用和取消引用都需要维护计数器，对系统性能有一定的影响
      - <font color=red>存在循环引用问题。</font>所谓循环引用，就是A对象引用了B对象，B对象右引用了A对象，此时会出现对象内存无法回收的问题

- 可达性算法（Java虚拟机使用）

  - 可达性算法将对象分为两类：垃圾回收的根对象（GC Root）和普通对象，GC Root对象一般不会被回收，对象与对象之间存在引用关系

  - 哪些对象是GC Root对象

    - 尚未结束的线程 Thread 对象，该对象会引用线程栈帧中的方法参数、局部变量等

      ```java
      public void method() {
          A a = new A();
          B b = new B();
          a.b = b;
          b.a = a;
          a = null;
          b = null;
      }
      ```

    - 系统类加载器加载的 java.lang.Class 对象

      ```java
      // sun.misc.Launcher 是GC Root对象，该对象可以找到 AppClassLoader
      // 该类由 AppClassLoader 加载，静态引用变量的内存地址存储在Class对象中
      public class MyClass {
          // 静态变量不会被回收
          private static A a = new A();
      }
      ```

    - 监视器对象（Monitor），用来保存同步锁 synchronized 关键字持有的对象

    - 本地方法调用时使用的全局对象



#### 五种对象引用

- 强引用：最常见的引用，由可达性分析算法来判断

- 软引用：软引用相对于强引用是一种比较弱的引用关系，如果一个对象只有软引用关联到它，<font color=red>当程序内存不足时，就会将软引用中的数据进行回收</font>，在JDK1.2版本后提供了SoftReference类来实现软引用，软引用常用于缓存中

  ```java
  // 添加虚拟机参数 -Xms200m -Xmx200m
  
  byte[] bytes = new byte[100*1024*1024];
  // GC Root强引用软引用对象，软引用对象软引用其内部数据
  SoftReference<byte[]> softReference = new SoftReference<>(bytes);
  bytes = null; // 去掉强引用
  System.out.println(softReference.get()); // 内存足够，不会被回收
  
  byte[] bytes2 = new byte[100*1024*1024];
  System.out.println(softReference.get()); // 内存不足，执行一次垃圾回收，仍不足，回收软引用
  
  byte[] bytes2 = new byte[100*1024*1024]; // OutOfMemoryError: Java heap space
  ```

  - 问题：软引用这个”盒子“里的数据被回收，但”盒子“却是强引用，不会被回收

    ```java
    // 添加虚拟机参数 -Xms200m -Xmx200m
    
    ReferenceQueue<byte[]> queues = new ReferenceQueue<>();
    List<SoftReference<byte[]>> list = new ArrayList<>();
    
    byte[] bytes = new byte[100*1024*1024];
    // 参数传入ReferenceQueue对象，每次盒子内容被回收，对应盒子就会被放入该Queue
    list.add(new SoftReference<>(bytes, queues));
    bytes = null;
    
    byte[] bytes2 = new byte[100*1024*1024];
    list.add(new SoftReference<>(bytes2, queues));
    bytes2 = null;
    
    int count = 0;
    while (list.remove((SoftReference<byte[]>) queues.poll())) {
        count++;
    }
    System.out.printf("本次回收盒子 %s 个\n", count);
    ```

  - 设计一个Student的缓存类

    ![Student缓存类设计](D:\picture\typora\后端2\Student缓存类设计.png)

- 弱引用：弱引用的机制和软引用类似，区别在于弱引用包含的对象在垃圾回收时，不管内存是否够用，都会被直接回收。在JDK1.2版之后提供了WeakReference类来实现弱引用，弱引用主要在ThreadLocal中使用。弱引用对象本身也可以用引用队列进行回收

- 虚引用：不能通过虚引用对象（盒子）获得内部包含的对象（盒子里的内容）。唯一用途是当对象被垃圾回收器回收时可以利用其获得通知。Java中使用PhantomReference实现了虚引用，直接内存中为了及时知道直接内存对象不再使用，从而回收内存，使用了虚引用来实现

  ```java
  ByteBuffer directBuffer = ByteBuffer.allocateDirect(size);
  directBuffer = null;
  
  // 当 directBuffer直接内存中的对象被回收时，需要及时通知JVM释放直接内存还给操作系统
  ```

- 终结器引用：（一般不会使用）指的是在对象需要被回收时，对象将被放置在Finalizer类中的引用队列中，在稍后由一条FinalizerThread线程从列表中获取对象，然后执行对象的finalize方法，在对象第二次被回收时，该对象才真正的被回收。在这个过程可以在finalize方法中再将自己对象使用强引用关联上，但不建议这样做

  



#### 垃圾回收算法

> 虚拟机参数：-verbose:gc （打印出垃圾回收的日志）

> 1960年John McCarthy发布了第一个GC算法：标记-清除算法
>
> 1963年Marvin L. Minsky发布了复制算法
>
> 本质上后续的所有垃圾回收算法，都是在上述两种算法的基础上优化而来的。

- 分类

  - 标记-清除算法（Mark Sweep GC）

  - 复制算法（Copying GC）

  - 标记-整理算法（Mark Compact GC）

  - [分代GC（Generational GC）](#分代GC算法)

- STW（Stop The World）：Java垃圾回收过程会通过单独的GC线程来完成，但是不管使用哪一种GC算法，都会有部分阶段需要停止所有的用户线程。这个过程被称之为 Stop The World 。如果STW时间过长，则会影响用户的使用过程

- 垃圾回收算法的评价标准

  - **吞吐量**：指的是CPU用于执行用户代码的时间与CPU总执行时间的比值（即吞吐量=执行用户代码时间/（执行用户代码时间 + GC时间）。吞吐量数值越高，垃圾回收的效率就越高。
  - **最大暂停时间**：STW时间的最大值
  - **堆使用的效率**：不同的垃圾回收算法，对堆内存的使用方式是不同的。比如标记清除法，可以使用完整的堆内存。而复制算法会将堆内存一分为二，每次只能使用一半内存。

  > 总结：三者不可兼得，<font color=red>不同的垃圾回收算法，适用于不同的场景。</font>

- 标记-清除算法

  - 核心思想是分为两个阶段
    - 标记阶段：将所有存活的对象进行标记。Java中使用可达性算法，从GC Root开始通过引用链遍历出所有存活的对象。
    - 清除阶段：从内存中释放没有被标记（非存活）的对象。
  - 优点：实现简单，只需要在第一阶段为每个对象维护一个标志位，第二阶段根据标志位进行清除即可
  - 缺点：
    - 碎片化：由于内存的分配很多情况是需要一块连续的区域，在对象被删除之后，内存中会出现很多细小的可用的内存单元，该单元组合起来可以放下一个较大的内存对象，但它们是分散的，可能没有容纳该对象的连续的内存空间
    - 分配速度慢：由于内存碎片的存在，需要维护一个空闲的链表，极有可能发生每次需要遍历到链表的最后才能获得合适的内存空间

- 复制算法

  - 核心思想：
    - 准备两块空间 From空间和 To空间，每次在对象分配阶段，只能使用其中一块空间。
    - 在垃圾回收阶段，将 From空间中存活的对象复制到 To空间，现From空间中的对象可以释放
    - 将两块空间的名字互换
  - 优点：
    - 吞吐量高：只需要遍历一次存活对象复制到 To空间，虽然比标记清除算法少一次遍历，但因为需要对对象进行内存空间的移动，性能逊色于标记清除算法
    - 不会产生碎片化
  - 缺点：内存使用效率低

- 标记-整理算法

  - 核心思想分为两个阶段
    - 标记阶段：将所有存活的对象进行标记。Java中使用可达性分析算法，从GC Root开始通过引用链遍历出所有存活的对象。
    - 整理算法：将存活对象移动到堆的一端。释放掉不再使用的对象的空间。
  - 优点：
    - 内存使用率高
    - 不会产生碎片化
  - 缺点：整理阶段的效率不高（整理算法有很多，如Lisp2、Two-Finger、表格算法、ImmixGC等算法等算法）

- <font color=red id="分代GC算法">分代GC算法</font>

  > 现代优秀的垃圾回收算法，会将上述的垃圾回收算法组合进行使用，其中应用最广的就是分代垃圾回收算法。

  - 将内存分为多个区域

    - 年轻代（Young）：存放存活时间比较短的对象
      - 伊甸园（Eden）
      - 幸存者区（Survivor）：复制算法
        - 幸存区0（S0、From/To）
        - 幸存区1（S1、To/From）
    - 老年代（Old）：存放存活时间比较长的对象

  - 虚拟机参数设置（使用SerialGC：-XX:+UseSerialGC）

    | 参数名                             | 参数含义                                                     |
    | ---------------------------------- | ------------------------------------------------------------ |
    | -Xmn值                             | 新生代大小                                                   |
    | -XX:SurvivorRatio=比值             | 伊甸园和每块幸存区的比例，默认8（8:1）<br/>若比值为3，年轻代20m，则伊甸区12m，幸存区0、1各4m |
    | -XX:+PrintGCDetails<br/>verbose:gc | 打印GC日志                                                   |

    - 内存的设置，其生效结果可能因 jdk版本 与 垃圾回收器 的不同而不同，此处使用jdk8、SerialGC

  - 核心思想

    - 分代回收时，创建出来的对象，首先会被放在Eden伊甸园
    - 如果Eden满了，新创建的对象无法放入，就会触发<font color=red>年轻代的GC</font>，称为Minor GC 或 Young GC
    - Minor GC 会把 Eden和From区 中需要回收的对象回收，把没有回收的对象放入 To区，随后将 From区和 To区名字互换
    - 每次 Minor GC中都会为对象记录年龄，初始值为0，每次GC完加1，当年龄达到阈值（具体值和垃圾回收器有关。最大15，因为对象标记mark word用了4bit记录年龄，最大值1111即15），对象就会被移到老年代
    - 如果整个年轻代满了，Minor GC后仍无法清出足够的空间存放新的对象，一些未到阈值年龄的对象也会被放入老年代

    - 当老年代空间不足时，无法放入新对象是，会先尝试minor GC，如果还是不足，就会触发Full GC，Full GC会对整个堆进行垃圾回收



#### 垃圾回收器

- 为什么分代GC算法要将堆分为新生代和老年代

  - 可以通过调整年轻代和老年代的比例来适应不同类型的应用程序，提高内存的利用率和性能
    - 在大多数应用中，老年代的大小都是远大于新生代的
    - 但在如购物节背景下的购物应用，订单对象可能快速增加，但该对象生命周期短，此时可以增大新生代，防止一些对象未到阈值年龄进入老年代而无法被及时回收
  - 新生代与老年代可以使用不同的垃圾回收算法，新生代一般使用复制算法，老年代可以选择标记-清除和标记-整理算法，灵活度较高
  - 新生代、老年代划分合理时，大多数情况下只会堆新生代进行GC（Minor GC），STW时间短。

- 分类与组合关系

  - <font color=red>指定垃圾回收器</font>

    > -XX:+PrintFlagsFinal：运行时，输出JVM所有参数

    - -XX:+UseSerialGC（年轻代、老年代都使用串行回收器）
    - -XX:+UseParNewGC（年轻代使用ParNew，老年代使用串行）
    - -XX:+UseParNewGC -XX:+UseConcMarkSweepGC（年轻代使用ParNew，老年代使用CMS）
      - -XX:CMSFullGCsBeforeCompaction=N（默认0），调整在N次FullGC之后整理碎片。

    - -XX:+UseParallelGC 或 -XX:+UseParallelOldGC（jdk8默认，使用 PS+PO 组合）
      - -XX:MaxGCPauseMillis=n：设置每次垃圾回收的最大停顿毫秒数
      - -XX:GCTimeRatio=n：设置吞吐量为n（用户线程执行时间=n/n +1）
      - -XX:+UseAdaptiveSizePolicy：（默认开启）设置让垃圾回收根据吞吐量和最大停顿的毫秒数自动调整内存大小

  - 垃圾回收器是垃圾回收算法的具体实现

  - 除G1之外，垃圾回收器分为年轻代和老年代。因此，除G1外，其他垃圾回收器需要成对组合使用。组合关系如下

    ![盒子模型](D:\picture\typora\后端2\垃圾回收器组合关系.png)

- 详细介绍

  - 年轻代-Serial：是一种<font color=red>单线程串行</font>回收年轻代的垃圾回收器
    - 步骤：Eden满后，会停止用户线程，使用单线程进行垃圾回收，在继续用户线程
    - 算法：复制算法
    - 优点：单CPU处理器下吞吐量优秀
    - 缺点：多CPU下吞吐量不如其他垃圾回收器，堆如果偏大会让用户线程长时间等待
    - 适用场景：Java编写的客户端程序或者硬件配置有限的场景

  - 老年代-SerialOld：是一种<font color=red>单线程串行</font>回收老年代的垃圾回收器
    - 算法：标记-整理算法
    - 优缺点、适用场景与年轻代-Serial 垃圾回收器相似


    - 年轻代-ParNew：本质是对Serial在多CPU下的优化，使用<font color=red>多线程</font>进行垃圾回收
      - 步骤：Eden满后，会停止用户线程，使用多线程进行垃圾回收，在继续用户线程
      - 算法：复制算法
      - 优点：多CPU处理器下停顿时间短
      - 缺点：吞吐量和停顿时间不入G1，所以在JDK9之后不建议使用
      - 适用场景：JDK8及之前版本中，与CMS老年代垃圾回收器搭配使用


    - 老年代-CMS：<font color=red>关注的是系统的暂停时间</font>，允许用户线程和垃圾回收线程在某些步骤中同时执行
      - 步骤：
        - 初始标记：先暂停用户线程；在单线程下，用极短的时间标记出GC Roots直接关联的对象
        - 并发标记：与用户线程并发调用，标记出GCRoots间接关联的对象
        - 重新标记：先暂停用户线程；由于并发标记阶段有些对象发生了变化，在多线程下，用极短时间纠正错标、漏标情况。
        - 并发清理：与用户线程并发调用，进行垃圾回收
      - 算法：标记-清除算法
      - 优点：系统由于垃圾回收出现的停顿时间较短，用户体验好
      - 缺点
        - 内存碎片：使用并发-清除算法，在垃圾收集结束后会存在大量的内存碎片，CMS会在Full GC时进行碎片整理，这是会导致用户线程暂停，可以使用-XX：CMSFullGCsBeforeCompaction=N（默认0），调整在N次FullGC之后整理碎片。
        - 浮动垃圾问题：无法清理在并发清理过程中产生的“浮动垃圾”，该“垃圾”只能等到下一次垃圾回收时清除，不能做到完全的垃圾回收
        - 退化：当内存不足无法分配对象时，CMS就会退化成SerialOld单线程回收老年代
      - 适用场景：大型互联网系统中，用户请求数据量大、频率高的场景。如订单接口、商品接口等


    - 年轻代-Parallel Scavenge：是jdk8默认的年轻代垃圾回收器，多线程并行回收，<font color=red>关注的是系统的吞吐量</font>。具备<font color=red>自动调整堆内存大小</font>的特点。

      - 算法：复制算法

      - 优点：吞吐量高，而且手动可控。为了提高吞吐量，虚拟机会动态调整堆的参数。

        - -XX:MaxGCPauseMillis=n：设置每次垃圾回收的最大停顿毫秒数
        - -XX:GCTimeRatio=n：默认99，设置吞吐量为n（用户线程执行时间=n/(n+1)）

        > 上面两个参数的设定值应根据多次测试得出。
        >
        > <font color=red>提高吞吐量，可能会让JVM减小年轻代的可用空间</font>

        - -XX:+UseAdaptiveSizePolicy：默认开启，设置让垃圾回收根据吞吐量和最大停顿的毫秒数自动调整内存大小

      - 缺点：不能保证单次的停顿时间

      - 适用场景：后台任务，不需要与用户交互（不用着重关注用户体验），并且容易产生大量的对象。如：大数据的处理，大文件的导出


    - 老年代-Parallel Old：与 PS 组合，利用多线程进行垃圾回收

      - 算法：标记-整理算法
      - 优点：并发收集，在多核CPU下效率最高
      - 缺点：不能保证单次的停顿时间
      - 适用场景：与PS组合使用


    - [G1](#G1)：JDK9及之后默认使用的垃圾回收器


  - 垃圾回收器的选择

    - JDK8及之前

      - ParNew + CMS：关注暂停时间
      - Parallel Scavenge + Parallel Old：关注吞吐量
      - G1：JDK8之前不建议，较大堆并且关注暂停时间

    - JDK9及之后：G1（默认）




#### <span id="G1">G1</span>

> JDK9及之后后默认使用的垃圾回收器
>
> -XX:+UseG1GC（打开G1开关，jdk9之前使用）

- 回收年代及算法：年轻代+老年代、复制算法

- 优点

  - 支持巨大的堆空间回收（如超过6G），并有较高的吞吐量
  - 不会产生内存碎片
  - 支持多CPU并行垃圾回收，并发标记长SATB算法效率高
  - 允许用户设置最大暂停时间

- 核心思想

  <table style="table-layout: fixed" border="1px" cellspacing="0">
      <tr>
          <td></td>
          <td style="background-color:#b7da88;">Eden</td>
          <td></td>
          <td style="background-color:#b7da88;">Eden</td>
          <td></td>
          <td></td>
      </tr>
      <tr>
          <td></td>
          <td></td>
          <td></td>
          <td></td>
          <td></td>
          <td style="background-color:rgba(32,178,178,0.96);">Old</td>
      </tr>
      <tr>
          <td></td>
          <td style="background-color:#ffd04c;" colspan="2">Humongous</td>
          <td style="background-color:#81eaea;">Survivor</td>
          <td></td>
          <td></td>
      </tr>
      <tr>
          <td></td>
          <td></td>
          <td></td>
          <td></td>
          <td></td>
          <td style="background-color:rgba(32,178,178,0.96);">Old</td>
      </tr>


  - G1的整个堆会被划分为多个大小相等的区域，称之为Region，区域不要求是连续的。分为多个 Eden、Survivor、Old区。Region的大小默认可通过 堆空间大小/2048 计算得到，也可以通过参数 -XX:G1HeapRegionSize=值（值必须是2的指数幂，取值范围为1M到32M）指定
  - 部分对象如果大小超过了Region的一半，会直接放到老年代，这类老年代被称为Humongous区。如果超过了Region，则会横跨多个Region
  - G1垃圾回收的两种方式
    - 年轻代回收（Young GC）
    - 混合回收（Mixed GC）

- 年轻代回收（Young GC）

  - 回收Eden和Survivor区中不用的对象。

  - 会导致STW，G1中可以通过参数 -XX:MaxGCPauseMillis=值（默认200）设置每次垃圾回收时的最大暂停时间，G1会尽可能的保证暂停时间

  - 执行流程

    - 新创建的对象会被放在Eden区。当G1判断年轻代不足（默认在年轻代占总堆空间的60%时），无法分配对象需要回收时会触发 YoungGC

    - 标记出Eden和Survivor区域中存活的对象

    - 根据最大配置的暂停时间选择某些区域，将这些区域中存活的对象复制到一个新的Survivor区中（对象年龄+1），然后清空这些区域

      > G1在进行YoungGC的过程中会去记录每次垃圾回收时每一个回收区域的平均回收耗时，作为下次回收的参考依据。这样就可以根据配置的最大暂停时间，计算出本次回收时最多能回收多少个Region区域。
      >
      > 比如 -XX:MaxGCPauseMillis=200，每个Region回收耗时40ms，那么这次回收最多只能回收4个区域（回收5个可能会超时）

    - 后续的YoungGC与之前相同，只不过Survivor区中存活的对象会被搬运到另一个Survivor区中

    - 当某个存活对象的年龄达到阈值（默认15），将会被放入老年代

    - 多次回收之后，会出现很多Old老年代，此时总堆占有率达到阈值时（可通过 -XX:InitiatingHeapOccupancyPercent=45 默认即45%）会触发混合回收。回收所有年轻代和部分老年代的对象以及大对象区。采用复制算法来完成。

- 混合回收（Mixed GC）

  - 执行流程
    - 初始标记：先暂停用户线程；再多线程并发将GCRoots直接关联的对象标记为存活
    - 并发标记：与用户线程并发执行，将与GCRoots间接关联的对象也标记为存活
    - 最终标记：先暂停用户线程；由于并发标记阶段有些对象发生了变化，在多线程下，标记一些引用改变漏标的对象，不管新创建、不再关联的对象
    - 并发清理：与用户线程并发执行，将存活的对象复制到别的Region，不会产生内存碎片
  - G1对老年代的清理会优先选择存活度最低的区域来回收，这样可以保证回收效率最高，这也就是G1（Garbage First）名字的由来
  - 如果清理过程中发现没有足够的空Region来存放转移的对象，会触发FullGC（单线程执行标记-整理算法），此时可能会导致用户线程长时间暂停