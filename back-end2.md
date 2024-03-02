# Netty



## BIO

### 同步阻塞案例

- Server

  ```java
  public class Server {
      public static void main(String[] args) {
          try {
              ServerSocket server = new ServerSocket(8848);
              System.out.println("服务端已启动");
              Socket socket = server.accept();
              InputStream is = socket.getInputStream();
              BufferedReader br = new BufferedReader(new InputStreamReader(is));
              String msg;
              // 此处用if
              if ((msg = br.readLine()) != null){
                  System.out.println("服务端收到：【 "+msg+" 】");
              }
  
          } catch (IOException e) {
              throw new RuntimeException(e);
          }
      }
  }
  ```

- Client

  ```java
  public class Client {
      public static void main(String[] args) throws IOException {
          Socket socket = new Socket("127.0.0.1", 8848);
          OutputStream outputStream = socket.getOutputStream();
          PrintStream ps = new PrintStream(outputStream);
          // 用print未写完一行数据，服务器无法读取到完整一行
          ps.print("hello, server!");
          ps.flush();
          // 未关闭，java.net.SocketException: Connection reset
      }
  }
  ```



### 多发多收

- Server

  ```java
  public class Server {
      public static void main(String[] args) {
          try {
              ServerSocket server = new ServerSocket(8848);
              System.out.println("服务端已启动");
              Socket socket = server.accept();
              InputStream is = socket.getInputStream();
              BufferedReader br = new BufferedReader(new InputStreamReader(is));
              String msg;
              // 此处用while
              while ((msg = br.readLine()) != null){
                  System.out.println("服务端收到：【 "+msg+" 】");
              }
  
          } catch (IOException e) {
              throw new RuntimeException(e);
          }
      }
  }
  ```

- Client

  ```java
  public class Client {
      public static void main(String[] args) throws IOException {
          Socket socket = new Socket("127.0.0.1", 8848);
          OutputStream outputStream = socket.getOutputStream();
          PrintStream ps = new PrintStream(outputStream);
          Scanner scanner = new Scanner(System.in);
          while (true) {
              System.out.print("请说：");
              ps.println(scanner.next());
              ps.flush();
          }
      }
  }
  ```



### 接收多个客户端

- Server：多线程

  ```java
  public class Server {
      public static void main(String[] args) {
          try {
              ServerSocket server = new ServerSocket(8848);
              System.out.println("服务端已启动");
              while (true){
                  Socket socket = server.accept();
                  // 开启线程
                  new Thread(() -> {
                      try {
                          InputStream is = socket.getInputStream();
                          BufferedReader br = new BufferedReader(new InputStreamReader(is));
                          String msg;
                          while ((msg = br.readLine()) != null){
                              System.out.println("服务端收到"+Thread.currentThread()+"的消息：【 "+msg+" 】");
                          }
                      }catch (IOException e) {
                          throw new RuntimeException(e);
                      }
                  }).start();
              }
          } catch (IOException e) {
              throw new RuntimeException(e);
          }
      }
  }
  ```

- Client



### 伪异步

- Server：线程池

- Client



### 文件传输

- Server

  ```java
  public class Server {
      public static void main(String[] args) {
          try {
              ServerSocket server = new ServerSocket(8848);
              Socket socket = server.accept();
              DataInputStream dis = new DataInputStream(socket.getInputStream());
              String suffix = dis.readUTF();
              System.out.println(suffix);
              // 读取文件 ...
  
          }catch (Exception e){
              e.printStackTrace();
          }
      }
  }
  ```

- Client

  ```java
  public class Client {
      public static void main(String[] args) {
          File file = new File("D:\\picture\\4823e7c.31a80e298383812fe498b567.png");
          String fileName = file.getName();
          String[] split = fileName.split("\\.");
  
          try(InputStream is = new FileInputStream(file);) {
              Socket socket = new Socket("127.0.0.1", 8848);
              DataOutputStream dos = new DataOutputStream(socket.getOutputStream());
              dos.writeUTF(split[split.length-1]);
              byte[] buffer = new byte[1024];
              int len;
              while ((len = is.read(buffer)) != -1){
                  dos.write(buffer, 0, len);
              }
              dos.flush();
              dos.close();
              // 及时关闭通道
              socket.shutdownOutput();
          }catch (Exception e){
              e.printStackTrace();
          }
      }
  }
  ```

  

### 端口转发

- Server

  ```java
  public class Server {
      // 定义一个静态集合，用来存储在线的socket
      private static List<Socket> socketsOnline = new ArrayList<>();
  
      public static void main(String[] args) {
          try {
              ServerSocket server = new ServerSocket(8848);
              System.out.println("服务器启动");
              while (true){
                  Socket socket = server.accept();
                  Server.socketsOnline.add(socket);
                  new Thread(() -> {
                      try {
                          BufferedReader br = 
                              new BufferedReader(new InputStreamReader(socket.getInputStream()));
                          String msg;
                          while ((msg = br.readLine()) != null){
                              // 服务端接收到客户端的消息后，推送给所有在线的客户端
                              for (Socket socketOnline : Server.socketsOnline) {
                                  PrintStream printStream = 
                                      new PrintStream(socket.getOutputStream());
                                  printStream.println(msg);
                                  printStream.flush();
                              }
                          }
                      }catch (Exception e){
                          // 捕获到连接重置异常后表示有人下线
                          System.out.println(socket+" 客户端用户下线");
                          Server.socketsOnline.remove(socket);
                      }
                  }).start();
              }
  
          } catch (IOException e) {
              throw new RuntimeException(e);
          }
      }
  }
  ```

- Client





## NIO

### Buffer

#### 常用API

- 缓冲区操作

  - Buffer clear()：清空缓冲区，切换为写模式，并返回对缓冲区的引用
  - Buffer compact()：清空position指针前的数据，切换为写模式
    - compact()前：读模式,capacity=10,limit=5,position=2（此时还剩3个字节未读）
    - compact()后：写模式,capacity=10,limit=10,position=3
  - Buffer flip()：将 limit 的位置设置到 position 上，并将 position 的位置改为0
  - int capacity()：返回 Buffer 的 capacity 大小
  - boolean hasRemaining()：判断缓冲区中是否还有元素
  - int limit()：返回 Buffer 的 limit 的位置
  - Buffer limit(int n)：设置 limit 为n，并返回对缓冲区的引用
  - Buffer mark()：对缓冲区设置标记
  - int position()：返回 position 的位置
  - Buffer position(int n)：设置 position 的位置，并返回对缓冲区的引用
  - int remaining()：返回 position 和 limit 间的元素个数
  - Buffer reset()：将位置 position 转到之前设置的 mark 位置上
  - Buffer rewind()：将 position 设置为0，取消之前的 mark
  
- 数据操作

  - byte get()：读取单个字节
  - ByteBuffer get(byte[] dst)：批量读取多个字节到 dst 中
  - byte get(int index)：读取指定索引位置的字节（不会移动position）
  - ByteBuffer put(byte b)：将单个字节写到 position 位置上
  - ByteBuffer put(byte[] src)：将src中的字节写入到 position 位置上
  - ByteBuffer put(int index, byte b)：将指定字节写入的索引位置上（不会移动position）
  
- 字符串和Buffer的相互转换

  - 字符串 -> ByteBuffer

    ```java
    byteBuffer.put("你好，世界！".getBytes(StandardCharsets.UTF_8));
    ByteBuffer buffer = StandardCharsets.UTF_8.encode("你好，世界");
    ByteBuffer buffer = ByteBuffer.wrap("你好，世界！".getBytes(StandardCharsets.UTF_8));
    ```

  - ByteBuffer -> 字符串

    ```java
    CharBuffer charBuffer = StandardCharsets.UTF_8.decode(buffer);
    String string = charBuffer.toString();
    ```

    





#### 直接内存缓冲区

- 非直接内存作用链
  - 本地IO -> 直接内存 -> 非直接内存 -> 直接内存 -> 本地IO
- 直接内存作用链
  - 本地IO -> 直接内存 -> 本地IO
- 直接内存效率更高，但申请慢，需要及时关闭
- API
  - 创建直接内存：Buffer.allocateDirect(int bytes);
  - 判断是否是直接内存：buffer.isDirect();





### Channel

#### 概述

- 类似于Stream流，但channel是双向的

- 用于在缓冲区和位于通道另一侧的实体间的数据传输

- 通道依赖于缓冲区，从而可以实现异步读写数据

- 支持通道的类（调用getChannel()方法）
  - FileInputStream、FileOutputStream
  - RandomAccessFile
  - DatagramSocket
  - Socket、ServerSocket
  
- channel实现

  - FileChannel：从文件中读写数据
    - 不能与Selector一起使用，只能工作在阻塞模式下
    - 通过FileInputStream获取的channel只能读
    - 通过FileOutputStream获取的channel只能写
    - 通过RandomAccessFile是否能读写根据构造RandomAccessFile时的读写模式决定
  - DatagramChannel：能通过UDP读写网络中的数据
  - SocketChannel：能通过TCP读写网络中的数据
  - ServerSocketChannel：可以监听新来的TCP连接，类似于web服务器，对每个新进来的连接都会创建一个SocketChannel

- 读取：`int readButes = channel.read(buffer)` 返回值表示读取了多少字节，-1表示达到文件末尾

- 写入：

  ```java
  while (buffer.hasRemaining()) {
      // 之所以放在while中，是因为SocketChannel 的写入能力是有上限的，因此可能需要多次写入
      channel.write(buffer);
  }
  ```

- **关闭**：channel必须关闭，不过调用了FileInputStream、FileOutputStream、RandomAccessFile的close方法，会间接地调用channel的close方法



#### 常用API

- FileChannel
  - int read(ByteBuffer dst)：从 channel 中读取数据到 ByteBuffer
  - long read(ByteBuffer[] dsts)：将 Channel 中的数据“分散”到 ByteBuffer[]中
  - int write(ByteBuffer src)：将 ByteBuffer 中的数据写入到 channel
  - long write(ByteBuffer[] srcs)：将 ByteBuffer[] 中的数据“聚集”到 channel
  - long positon()：返回此通道的文件位置
  - FileChannel position(long p)：设置此通道的文件位置
  - long size()：返回此通道的文件当前大小
  - FileChannel truncate(long s)：将此通道的文件截取为给定大小
  - void force(boolean metaData) ：强制将所有对此通道的文件更新写入到存储设备中（操作系统出于性能的考虑，会将数据缓存，不是立即写入磁盘。force(true)会将文件内容和元数据立即写入磁盘）
  - long transferFrom(ReadableChannel str, long position, long count)
  - long transferTo(long position, long, count, WritableByteChannel target)：拷贝channel中的数据，若数据超过 2g ，需要多次传输

- Path（jdk7中引入Path、Paths）

  ```java
  Path path1 = Paths.get("d:/1.txt");
  Path path2 = Paths.get("d:\\data");
  
  // 支持 . 和 ..
  Path path3 = Paths.get("./../bbb/bbb.txt");
  Path normalize = path.normalize();
  ```

- Files

  ```java
  // 检查文件是否存在
  Files.exists(path);
  
  // 创建一级目录，若目录已存在，或是多级目录，都会报错
  Files.createDirectory(path);
  // 创建多级目录
  Files.createDirectorys(path);
  
  // 拷贝文件
  Files.copy(sourcePath, targetPath); // 若文件已存在，报异常
  Files.copy(sourcePath, targetPath, StandardCopyOption.REPLACE_EXISTING); // 覆盖
  
  // 移动文件
  Files.move(sourcePath, targetPath, StandardCopyOption.ATOMIC_MOVE); // 保证文件移动时的原子性
  
  // 删除文件
  Files.delete(targetPath); // 文件不存在，会报错；如果文件为目录，若目录中还有内容，也会报错
  ```

  ```java
  // 遍历目录
  Files.walkFileTree(path, new FileVisitor<Path>() {
      @Override
      public FileVisitResult preVisitDirectory(Path dir, BasicFileAttributes attrs) throws IOException {
          return super.preVisitDirectory(dir, attrs);
      }
  
      @Override
      public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) throws IOException {
          return super.visitFile(file, attrs);
      }
  
      @Override
      public FileVisitResult visitFileFailed(Path file, IOException exc) throws IOException {
          return super.visitFileFailed(file, exc);
      }
  
      @Override
      public FileVisitResult postVisitDirectory(Path dir, IOException exc) throws IOException {
          return super.postVisitDirectory(dir, exc);
      }
  });
  ```





### Selector

#### 概述

- **Selector 是 SelectableChannle 对象的多路复用器，Selector 可以同时监听控制多个 SelectableChannel 的 IO 事件**
- Selector 是非阻塞 IO 的核心
- 不必为每个连接都创建一个线程，避免了多线程间上下文切换导致的开销





### 网络通信

#### 服务端流程

- 创建服务器

  ```java
  ServerSocketChannel sschannel = ServerSocketChannel.open();
  ```

- 切换非阻塞模式（在注册选择器前设置阻塞模式）

  ```java
  sschannel.configureBlocking(false);
  ```

- 绑定监听端口

  ```java
  ssChannel.bind(new InetSocketAddress(8080));
  ```

- 简单使用

  ```java
  List<SocketChannel> channels = new ArrayList<>();
  while(true) {
      // 服务器channel设置为非阻塞后，accept方法将不再阻塞，如果没有连接，将返回null
      SocketChannel sc = ssc.accept();
      if (sc != null) {
          log.info("连接上客户端：{}", sc);
          // 将客户端channel设置为非阻塞模式
          sc.configureBlocking(false);
          channels.add(sc);
      }
      for(SocketChannel channel : channels) {
          // 客户端channel设置为非阻塞后，read方法将不再阻塞，如果没读到数据，将返回0
          int read = channel.read(buffer);
          if (read > 0) {
              // buufer 转字符串 ...
          }
      }
  }
  ```

- 使用选择器解决没有事件（连接，读取...）时，程序的空转问题

  - 将通道注册到选择器上，并且指定“监听接收事件”

    ```java
    // 先获取选择器，一个选择器可以管理多个channel
    Selector selector = Selector.open();
    
    SelectionKey sscKey = sschannel.register(selector, 16， null);
    SelectionKey sscKey = sschannel.register(selector, SelectionKey.OP_ACCEPT);
    
    // 添加关注事件
    SelectionKey sscKey = sschannel.register(selector, 0);
    sscKey.interestOps(sscKey.interestOps() | 16); // sscKey.interestOps() + 16
    ```

    - 监听接收事件
      - 不关注任何事件：0
      - 可读：SelectionKey.OP_READ（1）（read事件包含两种可能，1是客户端下线，2是真正的读。因此须在读事件中，对客户端下线做出响应）
      - 可写：SelectionKey.OP_WRITE（4）
      - 连接：SelectionKey.OP_CONNECT（8）
      - 接收：SelectionKey.OP_ACCEPT（16）
      - 多个事件可用 "|" 来连接

  - 轮询式获取选择器上已经“准备就绪”的事件

    ```java
    ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
    // select()方法，没有事件发生，线程阻塞，有事件发生，线程才会恢复运行
    // 事件最终要么处理，要么取消，不能不处理。若事件不处理，则select方法不会阻塞
    while (selector.select() > 0) {
        Iterator<SelectionKey> iterator = selector.selectedKeys().iterator();
        while (iterator.hasNext()) {
            SelectionKey selectionKey = iterator.next();
            if (selectionKey.isAcceptable()) {
                // 服务器channel设置为非阻塞后，accept方法将不再阻塞，如果没有连接，将返回null
                SocketChannel socketChannel = ssc.accept();
                log.info("接收到连接请求，socketChannel：{}", socketChannel);
                // 将客户端channel设置为非阻塞模式
                socketChannel.configureBlocking(false);
                socketChannel.register(selector, SelectionKey.OP_READ);
            }else if (selectionKey.isReadable()) {
                try {
                    SocketChannel socketChannel = (SocketChannel) selectionKey.channel();
                    int len = 0;
                    // 客户端channel设置为非阻塞后，read方法将不再阻塞，如果没读到数据，将返回0
                    while((len = socketChannel.read(byteBuffer)) > 0){
                        byteBuffer.flip();
                        String string = StandardCharsets.UTF_8.decode(byteBuffer).toString();
                        // 此处可能产生消息边界问题
                        log.info("处理收到的数据【{}】", string);
                        byteBuffer.clear();
                    }
                    if (len == -1) {
                        // 客户端正常关闭时，会触发读事件，但实际上未发内容，所以 len == -1
                        log.info("客户端正常关闭");
                        // 取消此次事件
                        selectionKey.cancel();
                    }
                }catch (IOException e) {
                    // 客户端强制下线时，服务端会报错，需要捕获本次错误
                    log.warn("客户端强制下线")
                    log.warn(e.getMessage());
                    // 客户端在关闭时，也会触发一次read事件，需要取消此次事件
                    selectionKey.cancel();
                }
            }
            // 事件处理完成后，需要移出当前SelectionKey
            // 如果不移出，下一次迭代器遍历时，该key仍保留在集合中，可能导致空指针异常
            iterator.remove();
        }
    }
    ```

- 消息边界问题处理方案（半包、黏包）

  - 第一种：服务器和客户端约定最大传输长度，接收端根据长度划分ByteBuffer空间

  - 第二种：将传输的数据按分隔符进行拆分

    - 缺点：效率低

    - 问题：

      - 若存在一段无法拆分且数据量大的数据，接收端无法通过一次read读取完，此时会触发两次或多次读取事件。
      - 若大文件在触发第一次读事件时扩容，则需要将 ByteBuffer 对象作用域扩大，提出处理区域，供每次处理时调用，才能允许内部数据不被清除；但若简单从作用域中提出，则可能导致不同的处理事件共享包含残留数据的 ByteBuffer 对象，从而导致数据混乱
    
    - [解决大文件传输问题（如何扩容）](https://www.bilibili.com/video/BV1py4y1E7oA/?p=34&spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=25ad2de4838bd28372a4956bac63c618)：利用附件，可为每一个 SelectionKey 对象（每个事件）绑定一个 ByteBuffer 对象作为附件。这样，同一事件使用的是相同的 ByteBuffer 对象，不同事件也不会混用该对象
    
      ```java
      SelectionKey key = oneChannel.register(选择器, 事件类型, byteBuffer(附件));
      ```
    
      ```java
      // 获取附件
      ByteBuffer byteBuffer = (ByteBuffer) key.attachment();
      ```
    
      ```java
      // 先进行拆分，拆分后，存在某段数据仍大于接收的byteBuffer容量，再扩容
      if (byteBuffer.position() == byteBuffer.limit()) {
          ByteBuffer newBuffer = ByteBuffer.allocate(bytebuffer.capacity()*2);
          byteBuffer.flip();
          newBuffer.put(byteBuffer);
          key.attach(newBuffer);
      }
      ```
    



#### 客户端流程

- 获取通道

  ```java
  SocketChannel sChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1",8080));
  ```

- 切换非阻塞模式

  ```java
  sChannel.configureBlocking(false);
  ```

- 发送数据给服务端

  ```java
  ByteBuffer buf = ByteBuffer.allocate(1024);
  Scanner scanner = new Scanner(System.in);
  while(scanner.hasNext()){
      System.out.println("请输入：");
      String str = scanner.nextLine();
      buf.put((new SimpleDateFormat("yyyy/MM/dd HH:mm:ss").format(System.currentTimeMillis())+"/n"+str).getBytes());
      buf.flip();
      sChannel.write(buf);
      buf.clear();
  }
  ```



#### 可写事件

- 传统发送大文件内容

  ```java
  /*...;
  while (selector.select() > 0) {
      ...;
      if (selectionKey.isAcceptable()) {
          SocketChannel sChannel = ssChannel.accept();
          sChannel.configureBlocking(false);*/
  
          // 在首次连接时，向客户端发送大量数据
          StringBuilder stringBuilder = new StringBuilder();
          for (int i = 0; i < 1000_0000; i++) {
              stringBuilder.append("Hello, world!");
          }
          ByteBuffer byteBuffer = StandardCharsets.UTF_8.encode(stringBuilder.toString());
          long all = 0;
          while (byteBuffer.hasRemaining()) {
              int len = sChannel.write(byteBuffer);
              all += len;
              System.out.println("本次向缓冲区写入了【" + len + "】个字节");
          }
          System.out.println("共向缓冲区写入了【" + all + "】个字节");
          
      /*}
      keyIterator.remove();
  }*/
  ```

- **什么是可写事件**：

  - 服务端会先将需要发送的内容放到操作系统的发送缓冲区中，再由操作系统控制，在一定时机，将发送缓冲区中的数据发送到客户端
  - 当发送缓冲区未满时，`int len = sChannel.write(byteBuffer)`可以向缓冲区中添加内容，即 `len > 0`
  - 但当缓冲区已满，但还未将数据发送出去时，`int len = sChannel.write(byteBuffer)`无法向缓冲区添加内容，即 `len = 0`
  - 所以，当缓冲区未满，还可以向缓冲区添加数据，即为可写事件

- 使用可写事件非阻塞的发送内容

  ```java
  /*ServerSocketChannel ssChannel = ServerSocketChannel.open();
  ssChannel.bind(new InetSocketAddress(8848));
  ssChannel.configureBlocking(false);
  Selector selector = Selector.open();
  SelectionKey key = ssChannel.register(selector, SelectionKey.OP_ACCEPT);
  while (selector.select() > 0) {
      System.out.println("有新事件发生");
      Iterator<SelectionKey> keyIterator = selector.selectedKeys().iterator();
      SelectionKey selectionKey = keyIterator.next();
      if (selectionKey.isAcceptable()) {
          SocketChannel sChannel = ssChannel.accept();
          sChannel.configureBlocking(false);
          SelectionKey sk = sChannel.register(selector, SelectionKey.OP_READ);*/
  
          StringBuilder stringBuilder = new StringBuilder();
          for (int i = 0; i < 1000_0000; i++) {
              stringBuilder.append("Hello, world!");
          }
          ByteBuffer byteBuffer = StandardCharsets.UTF_8.encode(stringBuilder.toString());
          int len = sChannel.write(byteBuffer);
          System.out.println("首次向缓冲区写入【" + len + "】个字节");
          if (byteBuffer.hasRemaining()) {
              //sChannel.register(selector, SelectionKey.OP_WRITE, byteBuffer);
              sk.interestOps(sk.interestOps() | SelectionKey.OP_WRITE);
              sk.attach(byteBuffer);
          }
      }else if (selectionKey.isWritable()) {
          SocketChannel channel = (SocketChannel) selectionKey.channel();
          ByteBuffer byteBuffer = (ByteBuffer) selectionKey.attachment();
          int len = channel.write(byteBuffer);
          System.out.println("本次向缓冲区写入【" + len + "】个字节");
          if (!byteBuffer.hasRemaining()) {
              selectionKey.attach(null);
              // 如果不去，会一直进入可写事件
              selectionKey.interestOps(selectionKey.interestOps() - SelectionKey.OP_WRITE);
          }
      /*}
      keyIterator.remove();
  }*/
  ```



#### 多线程优化

- 原理

  - 主线程处理接收事件
  - 其他线程处理读写等事件

- 客户端测试代码

  ```java
  for (int i = 0; i < 10; i++) {
      SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("localhost", 8848));
      String msg = "Hello, world!";
      ByteBuffer byteBuffer = StandardCharsets.UTF_8.encode(msg); //HeapByteBuffer
      socketChannel.write(byteBuffer);
      // 注意看客户端发了几次，而服务端又收到了几次
      log.info("向服务器发送数据【{}】成功", msg);
      TimeUnit.MILLISECONDS.sleep(1000);
      socketChannel.close();
  }
  ```

- 实例

  ```java
  @Slf4j
  public class MultiThreadedServerTest {
      public static void main(String[] args) {
          List<Worker> workers = new ArrayList<>();
          Random random = new Random();
          try (ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();) {
              serverSocketChannel.bind(new InetSocketAddress(8848));
              serverSocketChannel.configureBlocking(false);
              Selector selector = Selector.open();
              for (int i = 0; i < 3; i++) {
                  workers.add(new Worker("worker-" + i));
              }
              serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
              log.info("服务器启动成功");
              while (selector.select() > 0) {
                  Iterator<SelectionKey> iterator = selector.selectedKeys().iterator();
                  while (iterator.hasNext()) {
                      SelectionKey selectionKey = iterator.next();
                      if (selectionKey.isAcceptable()) {
                          try {
                              SocketChannel socketChannel = serverSocketChannel.accept();
                              log.info("客户端【{}】连接成功", socketChannel.getRemoteAddress());
                              workers.get(random.nextInt(workers.size())).register(socketChannel);
                          } catch (IOException ignore) {}
                      }
                      iterator.remove();
                  }
              }
          } catch (IOException e) {
              throw new RuntimeException(e);
          }
      }
  }
  ```

  ```java
  @Slf4j
  class Worker implements Runnable {
      private final Selector selector;
      private final String name;
      private boolean isRun = false;
      private final Queue<Runnable> runnableQueue = new ConcurrentLinkedQueue<>();
  
      public Worker(String name) {
          this.name = name;
          try {
              selector = Selector.open();
          } catch (IOException e) {
              throw new RuntimeException(e);
          }
      }
  
      public void register(SocketChannel socketChannel) {
          try {
              socketChannel.configureBlocking(false);
              // 不要将事件放在worker线程开启前，虽然看上去没有问题，但是会有数据丢失
              // socketChannel.register(selector, SelectionKey.OP_READ);
              if (!isRun) {
                  new Thread(this, name).start();
                  isRun = true;
              }
              
              // 为什么不在warkup方法后注册事件，而是放在队列，直到下面run方法的循环中执行？
                 // 因为唤醒后，不能保证register一定在唤醒后的下一次循环内执行，而在下下一次循环又会被阻塞
              runnableQueue.add(() -> {
                  try {
                      socketChannel.register(selector, SelectionKey.OP_READ);
                  } catch (ClosedChannelException e) {
                      throw new RuntimeException(e);
                  }
              });
              selector.wakeup();
              // TimeUnit.SECONDS.sleep(1); // 可以这样模拟上述问题
              // socketChannel.register(selector, SelectionKey.OP_READ);
          } catch (IOException | InterruptedException e) {
              throw new RuntimeException(e);
          }
      }
  
      @Override
      public void run() {
          while (true) {
              try {
                  //if (!(selector.select() > 0)) break;
                  selector.select();
                  Runnable runnable;
                  // ※ 循环注册事件。在此执行注册是保证在唤醒后的下一次就完成注册
                  while ((runnable = runnableQueue.poll()) != null) {
                      runnable.run();
                  }
                  Iterator<SelectionKey> iterator = selector.selectedKeys().iterator();
                  while (iterator.hasNext()) {
                      SelectionKey selectionKey = iterator.next();
                      if (selectionKey.isReadable()) {
                          try {
                              // 虽然SocketChannel实现了AutoCloseable接口，但是不要放在上面的try-resource中，而是自己把握释放时间
                              SocketChannel socketChannel = (SocketChannel) selectionKey.channel();
                              SocketAddress remoteAddress = socketChannel.getRemoteAddress();
                              log.info("【{}】收到客户端【{}】的消息", name, remoteAddress);
                              ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
                              int read = socketChannel.read(byteBuffer);
                              if (read == -1) {
                                  selectionKey.cancel();
                                  log.info("客户端【{}】断开连接", remoteAddress);
                                  // 释放
                                  socketChannel.close();
                              } else {
                                  byteBuffer.flip();
                                  CharBuffer msg = StandardCharsets.UTF_8.decode(byteBuffer);
                                  log.info("【{}】收到客户端【{}】的消息：{}", name, remoteAddress, msg.toString());
                              }
                          }catch (Exception e) {
                              try {
                                  SocketChannel channel = (SocketChannel) selectionKey.channel();
                                  log.info("客户端【{}】断开连接", channel.getRemoteAddress());
                                  selectionKey.cancel();
                                  // 释放
                                  channel.close();
                              } catch (IOException ex) {
                                  throw new RuntimeException(ex);
                              }
                          }
                      }
  
                      iterator.remove();
                  }
              } catch (IOException e) {
                  throw new RuntimeException(e);
              }
  
          }
      }
  }
  ```
  






## IO模型

- 通信流程

  - **第一步**：应用A把消息发送到 TCP发送缓冲区。
  - **第二步**：TCP发送缓冲区再把消息发送出去，经过网络传递后，消息会发送到B服务器的TCP接收缓冲区。
  - **第三步**：B再从TCP接收缓冲区去读取属于自己的数据。

  ![通信流程](D:\picture\typora\后端2\netty\通信流程.webp)

- IO模型（从数据接收端的接收方式来看）

  - 等待阶段：等待数据发送端将数据发送到TCP接收缓冲区
  - 复制阶段：将数据从TCP接收缓冲区，复制到用户内存空间

- IO模型的分类

  - 阻塞IO

    ![阻塞IO](D:\picture\typora\后端2\netty\阻塞IO.png)

  - 非阻塞IO

    ![非阻塞IO](D:\picture\typora\后端2\netty\非阻塞IO.png)

  - 多路复用

    ![多路复用IO](D:\picture\typora\后端2\netty\多路复用IO.webp)

  - 信号驱动

    ![信号驱动IO](D:\picture\typora\后端2\netty\信号驱动IO.webp)

  - 异步非阻塞

    ![异步非阻塞IO](D:\picture\typora\后端2\netty\异步非阻塞IO.png)





  





## Netty

### 快速入门

- 引入依赖

  ```xml
  <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-all</artifactId>
      <version>4.1.95.Final</version>
  </dependency>
  ```

- Server

  ```java
  // 启动器，负责组装 netty 组件，启动服务器
  new ServerBootstrap()
      // 一个EventLoop中包含了selector、 thread，一个Group中包含了多个EventLoop
      .group(new NioEventLoopGroup())
      // 选择服务器的 ServerSocketChannel 实现
      .channel(NioServerSocketChannel.class)
      // boss 负责处理连接; child(worker) 负责处理读写
      // handler 规定了 child(worker) 执行的操作
      .childHandler(
      // 是一个用来初始化的 handler，负责添加别的 handler
      new ChannelInitializer<NioSocketChannel>() {
          @Override
          protected void initChannel(NioSocketChannel nsc) throws Exception {
              // 添加具体的 handler
              // 将 ByteBuf 转换为字符串
              nsc.pipeline().addLast(new StringDecoder());
              // 自定义 handler
              nsc.pipeline().addLast(new ChannelInboundHandlerAdapter() {
                  @Override
                  // 读事件处理方案
                  public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                      // 打印上一步转换好的字符串
                      log.info("接收到信息：【{}】", msg);
                  }
              });
          }
      })
      .bind(8080);
  ```

- Client

  ```java
  new Bootstrap()
      // 添加 EventLoop
      .group(new NioEventLoopGroup())
      // 选择客户端 channel 实现
      .channel(NioSocketChannel.class)
      // 添加处理器
      .handler(new ChannelInitializer<NioSocketChannel>() {
          @Override
          protected void initChannel(NioSocketChannel nioSocketChannel) throws Exception {
              nioSocketChannel.pipeline().addLast(new StringEncoder());
          }
      })
      // 异步非阻塞，main 线程调用该方法，但实际执行 连接操作 的是 EventLoopGroup 中的线程
      .connect(new InetSocketAddress("localhost", 8080))
      // main 线程阻塞，直到其他线程建立完连接
      .sync()
      .channel()
      .writeAndFlush("你好，世界！");
  ```
  
- 运行流程

  ![netty运行流程](D:\picture\typora\后端2\netty\netty运行流程.png)



### 组件

#### EventLoop

> EventLoop 本质是一个单线程线程池（同时维护了一个 Selector），里面有 run 方法处理 Channel 上源源不断的 io 事件

- 它的继承关系比较复杂
  - 一条是继承自 J.U.C.ScheduledExecutorService，因此可执行定时任务
  - 另一条是继承自 netty 自己的 OrderedEventExecutor
    - 提供了 `boolean inEventLoop(Thread thread)` 方法判断一个线程是否属于此 EventLoop
    - 提供了 parent 方法来看看自己属于哪个 EventLoopGroup
- EventLoopGroup 是一组 EventLoop，Channel 一般会调用 EventLoopGroup 的 register 方法来绑定其中一个 EventLoop，后续这个 Channel 上的io事件，都会由此 EventLoop 来处理（保证了IO事件处理时的线程安全）
  - 间接实现了Iterable接口，提供了遍历EventLoop的能力
  - 另有next方法，可获取下一个EventLoop

- 创建 EventLoopGroup

  ```java
  // 可处理 io事件，普通任务，定时任务
  // 若不指定创建的 EventLoop 数，默认创建 核心数*2 个
  EventLoopGroup group = new NioEventLoopGroup(2);
  
  // 可处理 普通任务，定时任务
  EventLoopGroup group = new DefaultEventLoopGroup();
  ```

- 获取下一个 EventLoop

  ```java
  // 若指定 group 为2个 EventLoop
  EventLoop eventLoop1 = group.next();
  EventLoop eventLoop2 = group.next();
  EventLoop eventLoop3 = group.next();
  log.info("是否为同一个对象：{}", eventLoop1 == eventLoop3); // true
  ```

- 执行任务

  - 普通任务

    - `group.next().submit(Runnable task)`
    - `group.next().execute(Runnable task)`

  - 定时任务：`group.next().scheduleAtFixedRate(Runnable task, int initialDelay, int period, TimeUnit unit)`

  - IO任务

    ```java
    new ServerBootstrap()
        .group(new NioEventLoopGroup())
        .channel(NioServerSocketChannel.class)
        .childHandler(
        new ChannelInitializer<NioSocketChannel>() {
            @Override
            protected void initChannel(NioSocketChannel nsc) throws Exception {
                // 自定义 handler
                nsc.pipeline().addLast(new ChannelInboundHandlerAdapter() {
                    @Override
                    // 读事件处理方案
                    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                        // 处理IO任务
                        ByteBuf byteBuf = (ByteBuf) msg;
                        String str = byteBuf.toString(StandardCharsets.UTF_8);
                        log.info("收到的信息：【{}】", str);
                    }
                });
            }
        })
        .bind(8080);
    ```

- eventLoop 分工细化

  - 细分1：职责划分

    - boss 只负责 ServerSocketChannel 上的 accept 事件
    - worker 只负责 SocketChannel 上的 读写 事件

    ```java
    new ServerBootstrap()
        // 指定 boss 和 worker 各自的 group
        // boss 一般只需要一个，所以构造方法参数传 1 。也可以不传，调用空参构成方法
        .group(new NioEventLoopGroup(1), new NioEventLoopGroup())
        . ...
    ```

  - 细分2：创建一个独立的 EventLoopGroup 用来处理一些耗时操作

    ```java
    // 创建一个独立的 group
    EventLoopGroup group = new DefaultEventLoopGroup(2);
    
    new ServerBootstrap()
        .group(new NioEventLoopGroup(), new NioEventLoopGroup(2))
        .channel(NioServerSocketChannel.class)
        .childHandler(
        new ChannelInitializer<NioSocketChannel>() {
            @Override
            protected void initChannel(NioSocketChannel nsc) throws Exception {
                // 自定义 handler1
                nsc.pipeline().addLast("handler1", new ChannelInboundHandlerAdapter() {
                    @Override
                    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                        ByteBuf byteBuf = (ByteBuf) msg;
                        String str = byteBuf.toString(StandardCharsets.UTF_8);
                        log.info("收到的信息：【{}】", str);
                        // 再将消息传递给下一个 handler 进行处理
                        ctx.fireChannelRead(msg); // super.channelRead(ctx, msg);
                    }
                });
                
                // 该 handler 所指定的任务，交由独立出的 group 里的线程进行处理
                nsc.pipeline().addLast(group, "handler2", new ChannelInboundHandlerAdapter() {
                    @Override
                    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                        log.info("处理一些耗时操作");
                    }
                });
            }
        })
        .bind(8080);
    ```

- 以细分2中的代码为例，会出现这种情况：

  ```txt
  channel 
  -------->  headHandler  
  -------->  handler1 （这个headler中的channelRead方法由 NioEventLoopGroup 中的线程调用） 
  -------->  handler2 （这个headler中的channelRead方法由独立的 DefaultEventLoopGroup 中的线程调用）
  -------->  其他handler
  -------->  tailHandler
  ```
  
  调用 handler 中的 channelRead 方法的是不同的线程，它是如何切换的
  
  ```java
  static void invokeChannelRead(final AbstractChannelHandler(final AbstractChannelHandlerContext next, Object msg)) {
      final Object m = next.pipeline.touch(ObjectUtil.checkNotNull(msg, "msg"), next);
      // 获得下一个 handler 的 eventLoop（EventExecutor 是 EventLoop 的父类）
      EventExecutor executor = next.executor();
      
      if (executor.inEventLoop()) { // 这个方法判断executor中的线程与当前（handler中的）线程是否是同一个
          // 是，直接调用
          next.invokeChannelRead(m);
      }
      
      else {
          // 不是，封装成任务传递
          executor.execute(new Runnable() {
              @Override
              public void run() {
                  next.invokeChannelRead(m);
              }
          });
      }
  }
  ```
  
  



#### Channel

- 客户端（这一节解释connect()方法下面的代码）

  ```java
  new Bootstrap()
      .group(...)
      .channel(...)
      .handler(...)
      .connect(...)                        // 返回值：ChannelFuture
      .addListener(channelFuture -> {
          channelFuture
              .channel()                   // 返回值：Channel
              .closeFuture()               // 返回值：CloseFuture
              .addListener(...);
      });
  ```

- 常用方法

  - close()：关闭 channel
  - closeFuture()：获得 CloseFuture 对象，用来处理 channel 的关闭
    - CloseFuture 实现了 io.netty.util.current.Future接口，该接口包含下面两个方法
      - sync 方法是同步等待 channel 的关闭
      - 而 addListener 方法是异步等待 channel 的关闭
    - ChannelFuture 也实现了这个接口
  - pipeline()：添加处理器
  - write()：将数据写入到缓冲区（满足一定条件，会将缓冲区中的数据发出）
  - flush()：将缓冲区中的数据发出
  - writeAndFlush()：将数据写入并刷出

- ChannelFuture

  ```java
  ChannelFuture channelFuture = new Bootstrap()
      .group(...)
      .channel(...)
      .handler(...)
      // connect() 方法是异步非阻塞方法
      // main 线程调用 connect()，但实际执行 连接操作 的是 EventLoopGroup 中的线程
      .connect(...);
  
  //channelFuture.sync();
  // 不调用sync()，因为connect是异步非阻塞，返回的channel很可能是尚未建立连接的channel
  Channel channel = channelFuture.channel();
  ```

  - sync()：作用是同步等待连接完成

    ```java
    // 异步转同步。main线程阻塞，等待其他线程建立完连接
    ChannelFuture channelFuture = channelFuture.sync();
    
    // main 线程阻塞结束后，开启通道，传递数据
    Channel channel = channelFuture.channel();
    channel.writeAndFlush("你好，世界！");
    ```

  - addListener()：是异步等待连接完成

    ```java
    // mian 线程不阻塞，由其他线程【建立完】连接后开启通道，传递数据
    channelFuture.addListener(new ChannelFutureListener() {
        @Override
        public void operationComplete(ChannelFuture channelFuture) throws Exception {
            Channel channel = channelFuture.channel();
            channel.writeAndFlush("你好，世界！");
        }
    });
    ```

- closeFuture()：用来处理 channel 的关闭

  ```java
  // channel 的关闭也是一个异步操作，由 main 线程调用，由其他线程真正执行关闭
  ChannelFuture closeFuture = channel.closeFuture();
  ```

  - sync()：作用是同步等待 channel 关闭

    ```java
    // 异步转同步
    closeFuture.sync();
    log.info("执行后续操作");
    ```

  - addListener()：是异步等待 channel 关闭

    ```java
    closeFuture.addListener(new ChannelFutureListener() {
        @Override
        public void operationComplete(ChannelFuture channelFuture) throws Exception {
            log.info("执行后续操作");
        }
    });
    ```

- 完全停止客户端

  > channel 真正关闭后，客户端程序仍无法停止，原因是 EventLoopGroup 中仍有线程在执行，可调用 EventLoopGroup 对象的 shutdownGracefully() 方法优雅停止线程（拒绝新任务，执行完已有任务）





#### Future Promise

> netty Future 继承 jdk Future

> netty Promise 继承 netty Future

-  jdk Future
  - cancel()：取消任务
  - isCanceled()：任务是否取消
  - isDone()：任务是否完成，不能区分成功失败
  - get()：获取任务结果，阻塞等待
  
- netty Future
  - getNow()：获取任务结果，若还未产生，返回null
  - sync()：等待任务结束，如果任务失败，抛出异常
  - await()：等待任务结束，如果任务失败，不会抛出异常，而通过 isSuccess() 判断
  - isSuccess()：判断任务是否成功
  - cause()：获取失败信息，非阻塞，如果尚未失败，返回null
  - addLinstener()：添加回调
  
- netty Promise\<T>：一个存放结果的容器
  - setSuccess(T t)：设置成功结果
  - setFailure(Throwable cause)：设置失败结果
  
  ```java
  EventLoop eventLoop = new NioEventLoopGroup().next();
  DefaultPromise<Integer> promise = new DefaultPromise<>(eventLoop);
  new Thread(() -> {
      try { TimeUnit.Second.sleep(1); } catch (InterruptedException ignore) {}
      // 添加结果
      promise.setSuccess(80);
      log.debug("计算完成");
  }).start();
  
  log.debug("等待结果");
  log.info("结果是：{}", promise.get());
  ```
  
  





#### Handler

- ChannelHandler 用来处理 Channel 上的各种事件，分为入站、出站两种。所有 ChannelHandler 被连成一串，就是 Pipeline

  - 入站Handler 通常是 ChannelInboundHandlerAdapter 的子类，主要用来读取客户端数据，写回结果
  - 出站Handler 通常是 ChannelOutBoundhandlerAdapter 的子类，主要对写回结果进行加工

  ```java
  new ServerBootstrap()
      .group(...)
      .channel(...)
      .childHandler(
      new ChannelInitializer<NioSocketChannel>() {
          @Override
          protected void initChannel(NioSocketChannel nsChannel) throws Exception {
              // 获得 pipeline
              ChannelPipeline pipeline = nsChannel.pipeline();
              
              // 添加处理器
              // 处理顺序（双向链表）：head <=> h1 <=> h2 <=> h3 <=> h4 <=> tail
              // 入站顺序：head -> handler1 -> handler2 -> tail
              // 出站顺序：tail -> handler4 -> handler3 -> head
              pipeline.addLast("handler1", 入站handler1);
              pipeline.addLast("handler2", new ChannelInboundHandlerAdapter() {
                  @Override
                  public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                    // 向channel中写入内容，触发出站处理器处理
                      // 使用pipeline的channel会从tail处理器向前找出站处理器进行处理
                      nsChannel.writeAndFlush(...);
                      // 使用handler的channel会从当前处理器向前找出站处理器进行处理
                      ctx.writeAndFlush(...);
                  }
              });
              pipeline.addLast("handler3", 出站handler3);
              pipeline.addLast("handler4", 出站handler4);
          }
      })
      .bind(...);
  ```

- 使用EmbeddedChannel调试handler链

  ```java
  ChannelInboundHandlerAdapter h1 = ...;
  ChannelInboundHandlerAdapter h2 = ...;
  ChannelOutBoundhandlerAdapter h3 = ...;
  ChannelOutBoundhandlerAdapter h4 = ...;
  ......;
  
  EmbeddedChannel channel = new EmbeddedChannel(h1, h2, h3, h4, ...);
  // 模拟入站操作
  channel.writeInbound(...);
  // 模拟出站操作
  channel.writeOutbound(...);
  ```
  
- 基础 Handler

  - 通用处理器：ChannelInboundHandlerAdapter

  - 专注处理器：SimpleChannelInboundHandler\<ByteBuf>

    > Object msg 的实际类型是 ByteBuf 的，会进入该处理器

  - 日志处理器：new LoggingHandler(LogLevel.INFO)



#### ByteBuf

- 创建

  - 创建**直接内存**ByteBuf，若不指定容量，默认为256

    ```java
    ByteBuf buffer = ByteBufAllocator.DEFAULT.buffer();
    ByteBuf buffer = ByteBufAllocator.DEFAULT.buffer(int capacity);
    ByteBuf buffer = ByteBufAllocator.DEFAULT.buffer(int capacity, int maxCapacity);
    ```

  - 创建堆内存、直接内存ByteBuf，若不指定容量，默认为256

    ```java
    ByteBuf byteBuf = ByteBufAllocator.DEFAULT.heapBuffer(int capacity);
    ByteBuf byteBuf = ByteBufAllocator.DEFAULT.directBuffer(int capacity);
    ```

- 池化：可以重用 ByteBuf

  - 启停：

    - 使用代码：Unpooled.buffer();

    - 配置参数：`-Dio.netty.allocator.type=unpolled或polled`

  - 4.1 前，池化功能不成熟，默认是非池化
  - 4.1 后，非Android平台默认启用池化

- 扩容：ByteBuf 在容量不够时，会**自动扩容**

  - 如果写入后的数据大小未超过512，则会选择扩容到下一个16的整数倍。如：写入后是17，若扩容，会扩容到32

  - 如果写入后的数据大小超过512，则会选择下一个2^n^

  - 扩容不能超过指定的 maxCapacity，否则会报错

- 指针

  - 指针分类
    - 读指针
    - 写指针

  - 空间大小
    - capacity：初始分配时的空间大小
    - max capacity：**自动扩容的上限**，是一个Int的上限

  - ByteBuf的空间划分
    - 可扩容部分：capacity 到 max capacity 的区域
    - 可写部分：写指针 到 剩余部分边界 可写，没有可写空间会自动扩容
    - 可读部分：读指针 到 写指针 间可读
    - 废弃部分：已经读过的区域

- 部分方法

  - 写入

    - writeBoolean/Byte/Short/Int/IntLE/Long/Char/Float/Double
    - writeBytes(ByteBuf src)：写入 netty 的 ByteBuf
    - writeBytes(ByteBuffer src)：写入 Nio 的 ByteBuffer
    - writeBytes(byte[] src)：写入 byte[]
    - int writeCharSequence(CharSequence s, Charset charset)
      - CharSequence：String、StringBuilder、StringBuffer
      - Charset：指定字符集
  - 读取
    - readInt()：一次性读取4个字节，并转换为 int
    - get开头的方法：不会改变读指针
  - 其他方法
    - markReadIndex()：对读指针当前的位置做上标记
    - resetReadIndex()：回到之前读指针的标记上

- 内存的回收

  - UnpooledHeapByteBuf：**非池化堆内存ByteBuf**使用的是JVM内存，只需等GC回收内存即可
  - UnpooledDirectByteBuf：**非池化直接内存ByteBuf**使用的是直接内存，需要特殊的方法来回收内存
  - PooledByteBuf：**池化ByteBuf**，它和它的子类使用了池化机制，需要更复杂的规则来回收内存

- **内存回收机制**

  - 释放原理：ReferenceCounted 接口

    - 每个 ByteBuf 对象的初始计数为 1
    - 调用 release 方法后计数减1，如果计数为0，ByteBuf内存被回收
    - 调用 retain 方法后计数加1，调用该方法后，其他线程调用 release 也不会造成回收，防止被误回收
    - 当计数为 0 时，底层内存被回收，这时即使 ByteBuf 对象还在，但各方法却无法正常使用
  - 说明
    - 在Java中，直接内存的ByteBuffer不需要手动释放。Java的垃圾收集器会自动处理这些内存。当ByteBuffer实例不再被引用时，垃圾收集器会自动回收其占用的内存。
    - Netty中的ByteBuf需要手动释放是因为Netty使用了一种称为引用计数的技术来管理它的内存。（目的是可以更有效地管理和复用内存，特别是在高并发的场景下，可以避免频繁的内存分配和回收，提高性能。）
  - **释放时机（谁最后使用，谁关闭）**
    - 默认情况：head handler 会在出站、tail hanler 会在入站时，对 ByteBuf 对象进行尝试释放处理
    - **实际上**：head 和 tail 间若有 handler 将实际为ByteBuf的 Object msg 转变为了实际为String 的 Object msg后继续向下传递，该msg传递到终点（head/tail）处理器时会判断 <font color=blue>msg instanceof ReferenceCounted</font>，若不是，终点处理器（head/tail）无法强转并释放
      - tail释放源码可从TailContext类中的channelRead方法向下看
      - head释放源码可从AbstractUnsafe in AbstractChannel类的write方法向下看

- slice()（切片，零拷贝复制）：零拷贝切片（切片和原始的ByteBuf共享同一块内存）

  ```java
  ByteBuf buffer = ByteBufAllocator.DEFAULT.buffer(6);
  buffer.writeBytes(new byte[]{'a', 'b', 'c', 'd', 'e', 'f'});
  
  // 在切片的过程中，并没有数据的复制
  ByteBuf slice1 = buffer.slice(0, 3);
  slice1.retain(); // 加的是 buffer 的 referenceCount
  ByteBuf slice2 = buffer.slice(0, 3);
  
  buffer.release();
  log.info("从切片1中读出一个字节，字节为：{}", (char) slice1.readByte());
  slice1.release(); // 减的是 buffer 的 referenceCount
  // 此时 buffer 的 referenceCount 已减为零，不能再调用 slice2.release();
  // slice2.release();
  ```

  - 切片后，将对切片进行容量限制

  - 原始ByteBuf对象的 release 将对切片造成影响

    - 调用切片的 retain 方法。

      > 无论是调用 源ByteBuf 还是 切片 的release、retain方法，计算的都是 源ByteBuf 的引用计数

    - 将 切片 内存转化为非共享的内存

      ```java
      ByteBuf slice1 = buffer.slice(0, 3);
      ByteBuf byteBuf = ByteBufAllocator.DEFAULT.buffer().writeBytes(slice1);
      ```

- duplicate()：零拷贝复制（复制出的ByteBuf与原始的ByteBuf完全共用一块内存，没有容量限制）

- copy()：深拷贝（数据复制）





#### Option

- 配置参数

  - 客户端

    ```java
    new ServerBootstrap()
        // 给 ServerSocketChannel 配置参数
        .option()
        // 给 SocketChannel 配置参数
        .childOption()
        . ...
    ```

  - 服务器端

    ```java
    new ServerBootstrap()
        .option()
        . ...
    ```

- 常用参数（ChannelOption.参数名）

  - CONNECT_TIMEOUT_MILLIS
    - 属于 SocketChannal 参数
    - 客户端在建立连接时，若在指定毫秒内无法连接，会抛出 timeout 异常
    - SO_TIMEOUT 主要用在阻塞IO中，阻塞IO的accept、read都是无限等待的，如果不希望永远阻塞，可配置此超时时间
    - 原理：利用线程池定时任务，延期 timeout 执行导致抛出超时异常的任务，若连接上，取消该任务
      - 实际上客户端调用 sync() 方法后，阻塞等待promise结果；
      - 到达超时时间时，在定时任务中向 promise 添加timeout异常结果，sync() 阻塞结束
  - SO_BACKLOG
    - 属于 ServerSocketChannel 参数
    - tcp 3次交互：为了让自己和对方都意识到自己的接收和发送都没问题
      - 半连接队列：尚为完成3次交互的会记录在半连接队列中
        - 上限通过 linux 目录 /proc/sys/net/ipv4/tcp_max_syn_backlog 指定，在 syncookies 启用的情况下，逻辑上没有最大值限制
      - 全连接队列：成功建立连接的会记录在全连接队列中，如果队列满了，server 将发送Connection refused拒绝连接的错误信息到 client
        - 操作系统层限制：上限通过 linux 目录 /proc/sys/net/core/somaxconn 指定
        - 代码层限制：
          - NIO：bind(port: 8080, backlog: 4069)
          - Netty：配置 SO_BACKLOG
  - TCP_NODELAY：默认关闭不延迟，即开启nagle
    - 属于 SocketChannel 参数
    - nagle 算法
      - 将多个小数据合并为一个大数据，再一起发送
      - 会导致数据延迟发送

  











### 协议

#### 黏包半包

- 黏包

  - 现象：分别发送abc、def，接收到的却是 abcdef

  - 原因
    - 应用层：接收方的 ByteBuf 设置太大
    - 滑动窗口：假设发送方 256 bytes表示一个完整的报文，但由于接收方处理不及时，且窗口大小足够大，这 256bytes 字节就会缓冲在接收方的滑动窗口中，当滑动窗口中缓冲了多个报文就会黏包
    - Nagle算法

- 半包

  - 现象：发送 abcdef ，接收到的是 abc、def
  - 原因
    - 应用层：接收放 ByteBuf 小于实际发送数据量
    - 滑动窗口：接收方窗口只剩 128b ，发送方发送超过 129b 数据，会先发送 128b ，再发送 1b，造成半包
    - MSS限制：Maximum Segment Size，最大报文长度，TCP payload的最大值，TCP协议定义的一个选项，MSS是TCP用来限制应用层最大的发送字节数

- 解决方式

  - 短链接法

    - 客户端若要发送多次消息时，发送一次消息后与服务器断开连接，再建立连接，再发送下一条消息
    - 服务端接收到客户端断开连接后，不会将本次消息与下一次重新连接的客户端发送消息进行粘接

  - 添加 定长消息解码器 FixedLengthFrameDecoder

    - 客户端发送固定长度的消息，不足该长度的，补齐

    - 服务端添加 定长消息解码器

      ```java
      ch.pipeline().addLast(new FixedLengthFrameDecoder(fixedLength: 10));
      ```

  - 分隔符

    - LineBasedFrameDecoder：能解析到 windows 和 linux 的换行符，并作为分隔条件

      > 需要指定一个 maxLength ，若解析消息时，超过该长度，仍未解析到换行符，则报 TooLongFrameException

      - 客户端在发送多条消息后，在每一条消息后加入换行符 '\n' or '\r\n'

      - 服务端添加 解码器

        ```
        ch.pipeline().addLast(new FixedLengthFrameDecoder(maxLength: 1024));
        ```

    - DelimiterBasedFrameDecoder：指定分隔符

  - LengthFieldBaseFrameDecoder

    - 构造方法参数
      - maxFrameLength：最大总长度，单次信息超出该长度报错
      - lengthFieldOffset：长度部分第一个字节位置
      - lengthFieldLength：长度部分的长度
      - lengthAdjustment：长度调节，长度部分后多少个字节才到真正的内容（长度部分后，真正的内容前还可以添加版本号等信息）
      - initialBytesToStrip：解析内容时，跳过开头后面多少个字节

    ```java
    // 嵌入式Channel，可用来模拟处理器链处理过程
    EmbeddedChannel channel = new EmbeddedChannel(
        // 若不想解析长度和版本号，initialBytesToStrip 设置为 长度4+版本号8=12
        new LengthFieldBasedFrameDecoder(1024, 0, 4, 8, 0),
        new ChannelInboundHandlerAdapter() {
            @Override
            public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                if (msg instanceof ByteBuf) {
                    ByteBuf byteBuf = (ByteBuf) msg;
                    log.info("收到的信息长度：【{}】，版本号：【{}】", 
                             byteBuf.readInt(), byteBuf.readLong());
                    log.info("收到的信息是：【{}】", byteBuf.readCharSequence(byteBuf.readableBytes(), StandardCharsets.UTF_8));
                }
                // 若每次接收到的都是新划分的ByteBuf，需要释放
                if (msg instanceof ReferenceCounted)
                    ((ReferenceCounted) msg).release();
            }
        }
    );
    
    AtomicLong stamp = new AtomicLong(0);
    Consumer<String> send = (String s) -> {
        ByteBuf byteBuf = ByteBufAllocator.DEFAULT.directBuffer();
        byte[] bytes = s.getBytes(StandardCharsets.UTF_8);
        // 一个Int占四个字节，所以 lengthFieldLength 为4
        byteBuf.writeInt(bytes.length);
        // 模拟在长度部分后，添加一个 Long 的版本。lengthAdjustment 设为8
        byteBuf.writeLong(stamp.incrementAndGet());
        byteBuf.writeBytes(bytes);
        channel.writeInbound(byteBuf);
    };
    
    send.accept("Hello, world!"); // 收到的信息长度：【13】，版本号：【1】，内容是：【Hello, world!】
    send.accept("你好，世界！"); // 收到的信息长度：【18】，版本号：【2】，内容是：【你好，世界！】
    ```
    





#### 自定义协议

- 常见协议

  - Redis 协议：以 SET name zhangsan 为例

    ```aof
    *3
    $3
    SET
    $4
    name
    $8
    zhangsan
    ```

  - Http 协议

    ```java
    // Http协议的处理器。HttpServerCodec：HttpRequestDecoder + HttpResponseEncoder
    pipeline.addLast(new HttpServerCodec());
    pipeline.addLast(new ChannelInboundHandlerAdapter() {
        @Override
        public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
            log.info("类型为：{}", msg.getClass());
            if (msg instanceof HttpRequest) {
                DefaultFullHttpResponse httpResponse =
                    new DefaultFullHttpResponse(HttpVersion.HTTP_1_0, HttpResponseStatus.OK);
                byte[] bytes = "<h1>Hello!<h1>".getBytes(StandardCharsets.UTF_8);
                httpResponse.headers().setInt(HttpHeaderNames.CONTENT_LENGTH, bytes.length);
                httpResponse.content().writeBytes(bytes);
                nioSocketChannel.writeAndFlush(httpResponse);
            }
            if (msg instanceof LastHttpContent) log.info("LastHttpContent");
            super.channelRead(ctx, msg);
        }
    });
    ```

- <a href="https://www.bilibili.com/video/BV1py4y1E7oA?p=101&vd_source=25ad2de4838bd28372a4956bac63c618">自定义协议</a>

  - 组成部分

    - 魔数：用来第一时间判断是否为无效数据包
    - 版本号：可以支持协议的升级
    - 序列化算法：支持正文到底采用哪种序列化反序列化方式，如json
    - 请求类型：是登录、注册、...
    - 请求序号：用于双工通信下，确定多个请求的顺序
    - 正文长度
    - 正文

  - 消息

    ```java
    @Data
    public abstract class MyMessage implements Serializable {
        private int messageType;
        priavte int sequenceId;
        
        // 定义子类的一些细分类型，供子类返回
        public static final int LoginRequestMessage = 0;
        public static final int LoginResponseMessage = 1;
        public static final int ChatRequestMessage = 3;
        public static final int ChatResponseMessage = 4;
        
        // 子类实现该方法，并返回自己的类型
        public abstract int getMessageType();
        
        private static final Map<Integer, Class<?>> messageClasses = new HashMap<>();
        
        static {
            messageClasses.put(LoginRequestMessage, LoginRequestMessage.class);
            messageClasses.put(LoginResponseMessage, LoginResponseMessage.class);
            ......
        }
        
        public static Class<?> getMessageClass(int messageType) {
            return messageClasses.get(messageType);
        }
    }
    ```
  
  - 处理器编码
  
    ```java
    @Sharable
    public class MyProtocolCodec extends ByteToMessageCodec<MyMessage> {
        @Override
        protected void encode(ChannelHandlerContext chc, MyMessage msg, ByteBuf byteBuf) throws Exception {
            // 将 魔数、版本号。。。msg 写入到 ByteBuf 中
        }
    
        @Override
        protected void decode(ChannelHandlerContext chc, ByteBuf byteBuf, List<Object> list) throws Exception {
            // 将 ByteBuf 转为 魔数、版本号。。。msg
        }
    }
    ```
    
    - @ Sharable 注解：添加在类上，表示该处理器可以被共享
      - LoggingHandler：没有记录状态信息，可以被共享
      - LengthFieldBasedFrameDecoder：由于记录了半包数据等状态信息，不能被多线程共享
    
  - 拓展序列化算法
  
    ```java
    public interface Serializer {
        <T> byte[] serialize(T t);
    
        <T> T deserialize(Class<T> clazz, byte[] bytes);
    
        enum Algorithm implements Serializer{
            JDK {
                @Override
                public <T> byte[] serialize(T t) {
                    return new byte[0];
                }
    
                @Override
                public <T> T deserialize(Class<T> clazz, byte[] bytes) {
                    return null;
                }
            },
            JSON {
                ......
            }
        }
    }
    ```





#### RPC

- RpcRequestMessage

  ```java
  @Getter
  public class RpcRequestMessage extends MyMessage {
      // 调用的接口全限定名，服务端根据它找到实现
      private String interfaceName;
      // 调用接口中的方法名
      private String methodName;
      // 方法返回类型
      private Class<?> returnType;
      // 方法参数类型数组
      private Class[] parameterTypes;
      // 方法参数值数值
      private Object[] parameterValue;
      
      public RpcRequestMessage(...){...}
      
      @Override
      public int getMessageType() {
          return RPC_MESSAGE_TYPE_REQUEST;
      }
  }
  ```









# Reactive



## Reactor

### Publisher

```java
import org.reactivestreams.Publisher;
import org.reactivestreams.Subscriber;
import org.reactivestreams.Subscription;

import java.util.concurrent.BlockingQueue;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.LinkedBlockingQueue;

public class MyPublisher implements Publisher<Integer> {
    @Override
    public void subscribe(Subscriber<? super Integer> subscriber) {
        MySubscription subscription = new MySubscription(subscriber);
        subscriber.onSubscribe(subscription);
    }

    static class MySubscription implements Subscription {
        // 订阅者必须是Integer的父类，才能处理Integer
        private final Subscriber<? super Integer> subscriber;
        private final ExecutorService executorService = Executors.newSingleThreadExecutor();
        private final BlockingQueue<Integer> queue = new LinkedBlockingQueue<>();
        private final int BUFFER_SIZE = 10;

        public MySubscription(Subscriber<? super Integer> subscriber) {
            this.subscriber = subscriber;
        }

        @Override
        public void request(long n) {
            System.out.printf("生产者收到需要 {} 的请求\n", n);
            executorService.submit(() -> { // 异步生产数据
                for (int i = 0; i < n; i++) {
                    try {
                        if (queue.size() >= BUFFER_SIZE) {
                            System.out.println("生产者生产数据过快，队列已满，丢弃数据: " + i);
                            continue;
                        }
                        Thread.sleep(1000);  // 模拟数据生产需要一些时间
                        int data = i*10; 
                        System.out.println("生产者生产数据: " + data);
                        queue.put(data);
                    } catch (InterruptedException e) {
                        Thread.currentThread().interrupt();
                        throw new RuntimeException(e);
                    }
                }
                // 一般所有数据生产完，在一起传递给订阅者
                deliver();
            });
        }

        private void deliver() {
            Integer i;
            while ((i = queue.poll()) != null) {
                subscriber.onNext(i); // 生产数据并传递给订阅者
            }
        }

        @Override
        public void cancel() {
            // 取消订阅的逻辑
            executorService.shutdown();  // 停止生产数据
        }
    }
}
```



### Flux、Mono

- 依赖

  ```xml
  <dependency>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-core</artifactId>
  </dependency>
  ```

- Mono：0或1个数据，API与Flux类似

- Flux：多个数据

  - 创建

    - Flux.just(T.. t)

    - Flux.range(int start, int count)

    - Flux.fromXXX

      - `Flux.fromStream(Stream<T> stream)`

      - `Flux.fromStream<Supplier<Stream<T>> supplier>`

      - `Flux.fromArray(T[] arr)`

      - `Flux.fromIterable(Iterable<T> iterable)`

    - Flux.concat：连接多个流

      ```java
      Flux.concat(Flux.fromIterable(List.of(1)), Flux.just(2));
      ```

    - 编程式创建流

      - Flux.generate
      - Flux.create

  - 整流：delayElements(Duration.ofSeconds(1))

  - 订阅：subscribe(...)

  - 事件(doOnXXX)

    - `doOnNext`：每个数据（流中的数据）到达的时候触发
    - `doOnEach`：每个元素（流的数据和信号）到达的时候触发
    - `doOnRequest`：消费者请求流元素的时候触发
    - `doOnSubscriber`：流被订阅时触发
    - `doOnError`：流发生错误时触发
    - `doOnCancle`：流被取消时触发
    - `doOnTerminate`：发送取消/异常信号中断流时触发
    - `doOnDiscard(Class<?> 流中的元素类型, Consumer<?> c)`：流中的元素被忽略(被filter过滤了)时触发

  - onErrorXXX：异常处理

  - 日志：log()

    ```java
    Flux.range(1, 7)  // stream1
        .log()        // 记录stream1的日志
        .filter(integer -> integer > 3)  // stream2
        .log()                           // 记录stream2的日志
        .subscribe(System.out::println);
    ```

  - 缓冲区：buffer(int maxSize)

    ```java
    Flux.range(1, 7) // Flux<Integer>
        .buffer(3)   // Flux<List<Integer>>
        .subscribe(System.out::print); // [1, 2, 3] [4, 5, 6] [7]
    ```

    - 此处的buffer和jdk9中的Flow API中的buffer（jdk9中buffer是256）不同（reactor和jdk9中都有各自的publisher、subscriber等）

    - 订阅者的request(N)：表示请求N次数据（如 request(1) 表示请求一次数据，**默认缓冲区大小是1**，`.buffer(3)`后缓冲区是3，即请求一次得到三个数据）

    - 缓冲区满后，会触发背压

      ```java
      Flux.fromStream(Stream.iterate(1, integer -> ++integer))
          .log()
          .buffer(2)
          .subscribe(integer -> {
              log.info("接收到数据：{}", integer);
              try {TimeUnit.SECONDS.sleep(2);} catch (Exception ignore) {}
          });
      ```

  - limitRate(int n)：限流

    ```java
    Flux.range(1, 150)
        .log()
        .limitRate(40) // 一次性请求40条数据
        .subscribe();
    ```

    ```shell
    20:39:37.546 [main] INFO reactor.Flux.Range.1 -- | onSubscribe([Synchronous Fuseable] FluxRange.RangeSubscription)
    # 先请求 40 条数据
    20:39:37.562 [main] INFO reactor.Flux.Range.1 -- | request(40)
    20:39:37.562 [main] INFO reactor.Flux.Range.1 -- | onNext(1)
    ......
    20:39:37.562 [main] INFO reactor.Flux.Range.1 -- | onNext(30)
    # 75% 预取策略：limitRate(40) 则每处理30条再请求30条
    20:39:37.562 [main] INFO reactor.Flux.Range.1 -- | request(30)
    20:39:37.562 [main] INFO reactor.Flux.Range.1 -- | onNext(31)
    ......
    20:39:37.580 [main] INFO reactor.Flux.Range.1 -- | onNext(60)
    20:39:37.580 [main] INFO reactor.Flux.Range.1 -- | request(30)
    20:39:37.580 [main] INFO reactor.Flux.Range.1 -- | onNext(61)
    ......
    20:39:37.591 [main] INFO reactor.Flux.Range.1 -- | onNext(150)
    20:39:37.591 [main] INFO reactor.Flux.Range.1 -- | request(30)
    20:39:37.592 [main] INFO reactor.Flux.Range.1 -- | onComplete()
    ```

  - cache缓存

    ```java
    Flux.range(1, 10)
        .delayElements(Duration.ofSeconds(1))
        .cache(2);
    
    // 热序列用于缓存数据
    ```
    
  - handle：与map类似
  
    ```java
    Flux.range(1, 10) // Flux<Integer>
        .handle((integer, synchronousSink) -> 
                synchronousSink.next(integer.toString())) // Flux<Object>
        .log()
        .subscribe();
    ```



### API补充

- concatWith：连接新**同类型**发布者（该方法与concat方法作用一样，区别在于前者是成员方法，后者是静态方法）

  concatMap：先元素转发布者，再将发布者扁平化并入原流；**保证**元素顺序

  flatMap：先元素转发布者，再将发布者扁平化并入原流；**不保证**元素顺序

  ```java
  Flux.just(1, 2, 3)
      .concatWith(Flux.just(4, 5))
      .map(integer -> integer+"、")
      .subscribe(System.out::print); // 1、2、3、4、5
  
  Flux.just(1, 2, 3)
      .concatMap(integer -> Flux.just(integer, 4))
      .map(integer -> integer+"、")
      .subscribe(System.out::print); // 1、4、2、4、3、4
  
  Flux.just(1, 2, 3)
      .flatMap(integer -> Flux.just(integer, 4))
      .map(integer -> integer+"、")
      .subscribe(System.out::print); // 1、4、2、4、3、4
  ```

- transform：**无状态**转换（原理是无论多少了订阅者订阅，该方法只执行一次）

  transfromDeferred：**有状态**转换（原理是每一个订阅者订阅，该方法都执行）

  ```java
  AtomicInteger counter = new AtomicInteger(0);
  Flux<String> stringFlux = Flux
      .just("a", "b")
      //.transform(flux -> {
      .transformDeferred(flux -> {
          if (counter.getAndIncrement() == 0) 
              return flux.map(String::toUpperCase);
          else return flux;
      })
      .map(s -> s+" ");
  
  // 两个订阅者订阅了这个流
  // transfrom: A B  transfromDeferred: A B
  stringFlux.subscribe(System.out::print);
  // transfrom: A B  transfromDeferred: a b
  stringFlux.subscribe(System.out::print);
  ```

- defaultIfEmpty：静态兜底数据

  switchIfEmpty：可使用 动态兜底

  ```java
  Flux.just(null); // 流中有一个元素，该元素是null
  Flux.empty(); // 流中没有元素，只有完成信号/结束信号
  
  Flux.empty()
      .defaultIfEmpty("为空")
      .subscribe(System.out::println);
  
  Flux.empty()
      .switchIfEmpty(Flux.create(fluxSink -> fluxSink.next("为空")))
      .subscribe(System.out::println);
  ```

- merge：合并

  concat：连接

  mergeWith：（该方法与merge类似，区别在于前者是成员方法，后者是静态方法）

  mergeSequential：严格遵守流的时间顺序合并（哪个流最先产生数据，哪个流在先）

  ```java
  //Flux.concat(
  Flux.merge(
  //Flux.mergeSequential
      Flux.just(1, 2).delayElements(Duration.ofSeconds(1)),
      Flux.just(3, 4).delayElements(Duration.ofMillis(750)))
      .map(integer -> integer + " ")
      .subscribe(log::info);
  
  System.in.read();
  ```

  ```text
  时间线：
        比例：+-----+ 500ms
  
  concat                       1           2        3        4
                   +-----+-----+-----+-----+-----+-----+-----+-----+
  merge/mergeWith           3  1     4     2
  mergeSequential           3        41    2
  ```

- zip、zipWith

  ```java
  Flux.just(1, 2, 3)  // Flux<Integer>
      .delayElements(Duration.ofSeconds(1))
      .zipWith(Flux.just("Hello", "world"))// Flux<Tuple2<Integer, String>>
      .subscribe(tuple2 -> 
                 System.out.printf("键：%d，值：%s\n", tuple2.getT1(), tuple2.getT2()));
  System.in.read();
  
  键：1，值：Hello
  键：2，值：world
  ```





### 异常处理

```java
Flux.range(1, 4)
    .map(integer -> {
        if (integer == 2) throw new RuntimeException("为5");
        return integer;
    })
    .doOnError(throwable -> log.error("before 错误"))
    .doOnComplete(() -> log.info("before 完成"))
    
    //.onErrorMap(err -> 自定义异常) // 常用于统一的异常处理项目中
    .onErrorComplete()
    //.onErrorContinue((throwable, o) -> log.error(o))
    //.onErrorResume(throwable -> Flux.just(6))
    //.onErrorReturn(Exception.class, 7)
    
    .doOnError(throwable -> log.error("after 错误"))
    .doOnComplete(() -> log.info("after 完成"))
    .log()
    .subscribe();
```

- onErrorMap：由 异常A 转 异常B
- onErrorComplete
  - 异常信号转结束：只能接收到数据 1
  - 打印：before 错误；after 完成
- onErrorContinue
  - 能接收到数据 1、 3、 4
  - 打印：before 完成；after 完成
- onErrorResume
  - 能接收到数据 1、 6、 3、 4
  - 打印：before 错误；after 完成
- onErrorReturn
  - 能接收到数据 1、 7
  - 打印：before 错误；after 完成





### 冷、热序列

- 在反应式编程中，数据流可以被分类为“热”序列和“冷”序列。

  - **热序列**是指那些**产生数据不依赖于订阅者**的数据流。也就是说，无论是否有订阅者，热序列都会产生数据。所有的订阅者都会获取到他们订阅之后产生的数据，但是他们可能会错过他们订阅之前产生的数据。一个常见的热序列的例子是鼠标移动事件：鼠标移动会产生事件，无论是否有监听器在监听。（Sinks工具类、create方法 生成的是热序列）
  - 相反，**冷序列**的**数据产生是依赖于订阅者**的。<font color=red>只有当有订阅者订阅时，冷序列才会开始产生数据</font>。所有的订阅者都会获取到完整的数据序列。一个常见的冷序列的例子是文件读取：只有当有读取请求时，数据才会被读取。（generate、just、range、fromIterable、interval等 生成的是冷序列）
    - 冷序列流的判断依据是，订阅者订阅行为与流数据的产生和发放的关系：即**每个订阅者接收到的是否是一个全新的流**，just等方法生成的流虽然每个订阅者拿到的数据相同，但流却是全新的流
- 理解热序列和冷序列的概念对于我们编写反应式编程代码非常重要，因为它们决定了数据流的行为和性能特性。

  - **数据产生的时间和方式**：冷序列只有在订阅时才开始产生数据，而热序列则无论是否有订阅者都会产生数据。这意味着，对于冷序列，每个订阅者都会从头开始接收数据，而对于热序列，订阅者可能会错过一些在它订阅之前产生的数据。
  - **数据共享**：在热序列中，**所有订阅者共享同一份数据流**，这意味着热序列可以支持多个订阅者同时接收数据。而在冷序列中，每个订阅者都会接收到独立的数据流，这可能会导致数据被重复产生，从而影响性能。（例如，如果你有一个Flux，它的数据源是一个数据库查询，那么每次有新的订阅者订阅这个Flux时，都会重新执行这个数据库查询，从而可能导致数据被重复产生。）
  - **资源管理**：由于冷序列只有在订阅时才开始产生数据，因此它们通常更能有效地管理资源。例如，如果一个冷序列代表一个文件读取操作，那么只有在有订阅者订阅时，文件才会被打开和读取。而对于热序列，即使没有订阅者，相关的资源（如网络连接、数据库连接等）也可能被占用。
  - **错误处理**：在热序列中，如果在订阅者订阅之前发生了错误，那么订阅者可能无法接收到这个错误。而在冷序列中，由于数据只有在订阅者订阅时才开始产生，因此订阅者总是能够接收到在数据产生过程中发生的所有错误。 

- defer：创建的是一个**冷序列**，每次有新的订阅者订阅时，都会创建一个**新**的 Flux 实例（具有**Lazy**的特性）。这意味着每个订阅者都会看到序列的所有元素，即使他们在不同的时间开始订阅。

  **冷序列的数据生成是懒加载的**，<font color=red>也就是说，只有当有订阅者订阅时，才会**发出**数据，而不是产生数据</font>

  Mono.just()和Flux.range()方法返回的都是冷序列。<font color=red>这些方法在被调用时，会立即计算其参数，但是生成的数据并不会立即发出</font>，而是等到有订阅者订阅时才发出。

  defer(Supplier)方法是在流被订阅时，方法中的Supplier**才**会被调用

  ```java
  // Mono.just(new Object())会立即创建一个新的Object实例，并将这个实例保存在Mono中。 
  // 即 Obect 这个对象的发出是在流被订阅时，但是 Object 对象的创建是在该方法调用时
  Mono.just(new Object()); 
  Mono.just(1); //会立即计算表达式1，并将结果保存在Mono中。
  
  // 一般来说，静态数据没有必要放在 defer 中
  Flux.defer(() ->  Flux.range(1, 10));
  
  // DispatcherHandler 源码
  Mono.defer(() -> {
      Exception ex = new ResponseStatusException(HttpStatus.NOT_FOUND);
      return Mono.error(ex);
  }
  ```

- <font color=red>对于冷序列，为什么一会说，数据是在订阅时生成的，一会说数据是在初始化时生成的</font>

  冷序列流的判断依据是，订阅者订阅行为与流数据的产生和发放的关系：即**每个订阅者接收到的是否是一个全新的流**，just等方法生成的流虽然每个订阅者拿到的数据相同，但流却是全新的流

  ```java
  @Test
  public void testCold() throws InterruptedException {
      Mono<Object> mono =
          // 下面三个方法返回的都是 冷序列 
          // Mono.just(LocalDateTime.now()); // 结果 true
          // Mono.defer(() -> Mono.just(LocalDateTime.now())); // 结果 false
          Mono.fromCallable(LocalDateTime::now); // 结果 false
  
      CountDownLatch latch = new CountDownLatch(2);
  
      AtomicReference<Object> obj1 = new AtomicReference<>();
      AtomicReference<Object> obj2 = new AtomicReference<>();
      mono.subscribe(o -> {
          System.out.println(o.toString());
          obj1.set(o);
          latch.countDown();
      });
  
      TimeUnit.SECONDS.sleep(1);
  
      mono.subscribe(o -> {
          System.out.println(o.toString());
          obj2.set(o);
          latch.countDown();
      });
  
      latch.await();
      System.out.println(obj1.get() == obj2.get());
  }
  ```

- 冷热序列的转化

  ```java
  @Test
  public void testColdToHot() {
      Flux<Integer> sourceFlux = Flux.range(1, 10)
          .delayElements(Duration.ofSeconds(1));  // 创建一个冷序列
      Flux<Integer> hotFlux = sourceFlux.publish().autoConnect();  // 将冷序列转换为热序列
      Flux<Integer> coldFlux = hotFlux.replay().autoConnect();  // 将热序列转换为【伪】冷序列
  
      //subscribe(hotFlux);
      subscribe(coldFlux);
  }
  
  @SneakyThrows
  public void subscribe(Flux<?> flux) {    
      flux.subscribe(o -> {
          System.out.println("第一个订阅者收到: " + o);
      });
  
      TimeUnit.SECONDS.sleep(5);
  
      flux.subscribe(o -> {
          System.out.println("第二个订阅者收到: " + o);
      });
  
      System.in.read();
  }
  ```

  - publish()方法：会将这个冷序列转换为一个ConnectableFlux，这是一种特殊的Flux，它可以有多个订阅者，但是只有当connect()方法被调用时，才会开始发出数据。 
  - replay()方法：会将这个热序列转换为一个ConnectableFlux，这是一种特殊的Flux，它可以有多个订阅者，但是只有当connect()方法被调用时，才会开始发出数据。**基于缓存实现的 伪 冷序列**
  - autoConnect([int n])方法：会自动调用connect()方法，开始发出数据。可接收一个int参数，表示有n个订阅者订阅后开始发送数据，默认为 1

- 第二个订阅者中途订阅**热序列**只获得之后的数据：

  ```java
  @Test
  public void testHotSequence() throws InterruptedException {
      // 创建一个热序列
      Sinks.Many<Integer> hotSource = Sinks.many().multicast().onBackpressureBuffer();
      Flux<Integer> hotFlux = hotSource.asFlux();
  
      // 创建一个生产者线程，向热序列发送数据
      new Thread(() -> {
          for (int i = 0; i < 10; i++) {
              hotSource.tryEmitNext(i);
              try {
                  TimeUnit.SECONDS.sleep(1); // 模拟数据生产速度
              } catch (InterruptedException e) {
                  e.printStackTrace();
              }
          }
          hotSource.tryEmitComplete();
      }).start();
  
      subscribe(hotFlux); // 该方法为两个订阅者错时订阅流
  }
  ```

- 第二个订阅者中途订阅**冷序列**获得全部数据：

  ```java
  @Test
  public void testColdSequence() throws InterruptedException {
      // 创建一个冷序列
      Flux<Integer> coldFlux = Flux.range(0, 10).delayElements(Duration.ofSeconds(1));
      
      subscribe(hotFlux); // 该方法为两个订阅者错时订阅流
  }
  ```

  



### 编程式创建流

- Flux.generate：编程式创建流，单线程下可使用

  ```java
  Flux.generate(() -> 0, // 提供初始值，seed，并发下可使用原子变量
                (integer, synchronousSink) -> {
                    if (integer < 3) {
                        // 热序列，之所以产生输出结果那样“生产一条，消费一条”是因为
                        // 每次调用 next 方法后会立即调用消费者的 onNext 方法
                        synchronousSink.next(integer);
                    }
                    else synchronousSink.complete(); // 完成
                    // sink.error(new RuntimeException(".."));
                    return integer + 1;
                })
      .subscribe(integer -> System.out.printf("接收到数据：%s \n", integer););
  ```
  
  ```shell
  生产数据：0
  接收到数据：0 
  生产数据：1
  接收到数据：1 
  生产数据：2
  接收到数据：2 
  ```
  
- Flux.create：热序列，**并发(多线程)向流中添加元素时使用**

  ```java
  // 单线程添加元素
  Flux.create(
      fluxSink -> {
          for (int i = 0; i < 10; i++) {
              fluxSink.next(i);
          }
      })
      .log()
      .subscribe();
  ```

  ```java
  // 模拟使用监听器在多线程下添加元素
  
  @AllArgsConstructor
  static class LoginListener {
      private final FluxSink fluxSink;
  
      public void onUserLogin (Integer user_id) {
          log.info("用户 {} 登录", user_id);
          fluxSink.next(user_id); // 本质是将fluxSink的作用域扩大
      }
  }
  
  public static void main(String[] args){
      AtomicReference<LoginListener> listener = new AtomicReference<>();
      Random random = new Random();
  
      for (int i = 0; i < 3; i++) {
          new Thread(() -> {
              while (listener.get() == null) {
                  try {TimeUnit.MILLISECONDS.sleep(100);} 
                  catch (Exception ignore) {}
              }
              for (int j = 0; j < 10; j++) {
                  try {TimeUnit.MILLISECONDS.sleep(random.nextInt(1000));} 
                  catch (Exception ignore) {}
                  listener.get().
                      onUserLogin(random.nextInt(1000)); // 模拟触发用户登录
              }
          }).start();
      }
  
      // 本质将fluxSink的作用域扩大
      Flux.create(fluxSink -> listener.set(new LoginListener(fluxSink)))
          .log()
          .subscribe();
  }
  ```



### 线程调度

- 使用

  ```java
  Flux.create(
      fluxSink -> {
          for (int i = 0; i < 5; i++) {
              fluxSink.next(i);
              log.info("添加数据 {}", i);  // subscribeOn 线程调用
          }
      })
      .subscribeOn(Schedulers.newSingle("subscribeOn", false))
      .publishOn(Schedulers.newSingle("publishOn", false))
      .map(o -> {
          log.info("map方法调用"); // publishOn 线程调用
          return o.toString();
      })
      .log() // publishOn 线程调用
      .subscribe(log::info); // publishOn 线程调用
  ```

  ```java
  fluxSink -> {
      for (int i = 0; i < 5; i++) {
          fluxSink.next(i);
          log.info("添加数据 {}", i); // subscribeOn 线程调用
      }
  })
      .map(o -> {
          log.info("map方法调用"); // subscribeOn 线程调用
          return o.toString();
      })
      .log() // subscribeOn 线程调用
      .subscribeOn(Schedulers.newSingle("subscribeOn", false))
      .publishOn(Schedulers.newSingle("publishOn", false))
      .subscribe(log::info); // publishOn 线程调用
  ```

- Schedulers获得线程调度器

  - Schedulers.immediate()：【默认】，当前线程执行所有操作
  - Schedulers.single()：获得一个单例模式的单线程调度器
  - Schedulers.newSingle(线程名【, 是否是守护线程】)：新建一个单例模式的单线程调度器
  - Schedulers.boundedElastic()：单例模式的有限、弹性的线程池调度器（默认：线程数为cpu*10个；任务队列为100K；时间为60s）
  - Schedulers.newBoundedElastic(...)：新建一个有限、弹性的线程池调度器
  - Schedulers.fromExecutor(Executor e)：以自己新建的线程池建立调度器
  - Schedulers.parallel()：单例模式的ForkJoin线程池调度器
  - Schedulers.newParallel(...)：新建一个ForkJoin线程池调度器
  
- runOn

  ```java
  Flux.range(1, 10)
      .delayElements(Duration.ofSeconds(1))
      .parallel(3)
      .runOn(Schedulers.newParallel("myParallel"))
      .subscribe(i -> log.info("{}", i));
  
  TimeUnit.SECONDS.sleep(12);
  ```

  



### 超时与重试

```java
Flux.range(1, 3)
    .delayElements(Duration.ofSeconds(1))
    .concatWith(Flux.range(1, 2).delayElements(Duration.ofSeconds(3)))
    .timeout(Duration.ofSeconds(2)) // 设置超时时间为2秒
    .retry(3) // 重试3次，每次重试从头开始
    .map(i -> i+" ")
    .onErrorReturn("error")
    .subscribe(System.out::print);
System.in.read();

// 1 2 3 1 2 3 1 2 3 1 2 3 error 
```



### Sinks

- 使用

  ```java
  Sinks.many(); // 发送Flux数据
  Sinks.one(); // 发送Mono数据
  
  Sinks.many().unicast(); // 单播：这个管道只能绑定一个订阅者
  Sinks.many().multicast(); // 多播：可以绑定多个订阅者
  Sinks.many().replay(); // 重放：给后来的订阅者从头开始把元素发给它
  
  Sinks.Many<Object> many = Sinks.many().unicast()
      .onBackPressureBuffer(new LinkBlockingQueue<>(5)); // 背压队列
  
  many.tryEmitNext(i);
  
  many.asFlux().subscribe(System.out::println);
  ```

- 该方法产生的流是**热序列**

  Sinks.many().replay()方法创建的是一个热序列（Hot Sequence），但是它具有“重放”（Replay）的特性。这意味着，即使订阅者在数据产生后才订阅这个序列，它仍然可以接收到所有的数据，包括在它订阅之前产生的数据。

  这是通过内部维护一个缓冲区来实现的。当有数据被发送到Sinks.Many对象时，这些数据会被存储在缓冲区中。然后，当有订阅者订阅这个序列时，它会立即接收到缓冲区中的所有数据。

  这种“重放”特性使得Sinks.many().replay()方法创建的序列**既具有热序列的特性**（即无论是否有订阅者，都会产生数据），**又具有冷序列的特性**（即每个订阅者都可以接收到完整的数据序列）。

  需要注意的是，由于Sinks.many().replay()方法**需要维护一个缓冲区来存储所有的数据，因此它可能会消耗大量的内存**。如果你的数据流包含大量的数据，或者数据的生产速度远大于消费速度，那么你可能需要考虑使用其他的方法，如Sinks.many().unicast().onBackpressureBuffer()，它可以在阻塞队列满时应用背压策略，防止内存溢出。

- 背压队列效果展示

  ```java
  Sinks.Many<Integer> many = Sinks.many()
      .unicast()
      .onBackpressureBuffer(new LinkedBlockingQueue<>(2));
  
  // 创建一个生产者线程，尝试向Sinks.Many对象发送大于阻塞队列容量的数据
  Thread producer = new Thread(() -> {
      for (int i = 0; i < 5; i++) {
          Sinks.EmitResult result = many.tryEmitNext(i);
          System.out.println("Emitting: " + i + ", Result: " + result);
          try {
              TimeUnit.MILLISECONDS.sleep(100); // 模拟数据生产速度
          } catch (InterruptedException e) {
              e.printStackTrace();
          }
      }
  });
  producer.start();
  
  // 订阅Sinks.Many对象，模拟消费者行为
  many.asFlux().subscribe(value -> {
      try {
          TimeUnit.SECONDS.sleep(2); // 模拟数据消费速度
      } catch (InterruptedException e) {
          e.printStackTrace();
      }
      System.out.println("Consumed: " + value);
  });
  
  // 等待生产者线程结束
  producer.join();
  ```

  ```shell
  Emitting: 0, Result: OK
  Emitting: 1, Result: OK
  Emitting: 2, Result: OK
  Emitting: 3, Result: FAIL_OVERFLOW  # 丢弃
  Emitting: 4, Result: FAIL_OVERFLOW  # 丢弃
  Consumed: 0
  Consumed: 1
  Consumed: 2
  ```

  



### 收集

- blockXXX

  ```java
  Integer integer = Flux.range(1, 10)
      .delayElements(Duration.ofSeconds(1))
      .blockLast();
  ```

- collectXXX

  ```java
  Mono<List<Integer>> listMono = Flux.range(1, 10)
      .delayElements(Duration.ofSeconds(1))
      .collectList();
  ```

  



## WebFlux

| API功能      | servlet-阻塞式web                  | webflux-响应式web                                          |
| ------------ | ---------------------------------- | ---------------------------------------------------------- |
| 前端控制器   | DispatcherServlet                  | DispatcherHandler                                          |
| 处理器       | Controller                         | WebHandler/Controller                                      |
| 请求、响应   | ServletRequest<br/>ServletResponse | ServerWebExchange:<br>  ServerRequest<br/>  ServerResponse |
| 过滤器       | Filter(httpFilter)                 | WebFilter                                                  |
| 异常处理器   | HandlerExceptionResolver           | DispatchExeptionHandler                                    |
| Web配置      | @EnableWebMvc                      | @EnableWebFlux                                             |
| 自定义配置   | WebMvcConfigurer                   | WebFluxConfigurer                                          |
| 返回结果     | 任意                               | Mono、Flux、任意                                           |
| 发送Rest请求 | RestTemplate                       | WebClient                                                  |



### 异步Servlet

- 依赖

  ```xml
  <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
  </dependency>
  ```

- 实例

  ```java
  @WebServlet(value = "/async", asyncSupported = true)
  public class AsyncServlet extends HttpServlet {
      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          AsyncContext asyncContext = req.startAsync();
          long start = System.currentTimeMillis();
  
          CompletableFuture.runAsync(() -> 
                  doSomeThings(asyncContext, asyncContext.getRequest(), asyncContext.getResponse()));
  
          System.out.printf("共用时：%sms\n", System.currentTimeMillis()-start);
      }
  
      private void doSomeThings(AsyncContext asyncContext, ServletRequest req, ServletResponse resp) {
          try {
              TimeUnit.SECONDS.sleep(5);
          } catch (InterruptedException ignore) {}
          try {
              resp.setContentType("text/html;charset=utf-8");
              resp.getWriter().write("完成");
          } catch (IOException e) {
              throw new RuntimeException(e);
          }
          asyncContext.complete();
      }
  }
  ```

- 如果你的servlet或filter链中有任何一个不支持异步操作，那么就会抛出 `java.lang.IllegalStateException: A filter or servlet of the current chain does not support asynchronous operations.`

  - 解决这个问题的方法是，需要**确保所有servlet和filter都支持异步操作**。可以通过在servlet或filter的注解中添加`asyncSupported=true`来启用异步支持。



### 原生请求

- 引入依赖（注意：不要引入spring-boot-starter-web依赖）

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-webflux</artifactId>
  </dependency>
  ```

- 原生API

  ```java
  HttpHandler httpHandler = (ServerHttpRequest serverHttpRequest,
                             ServerHttpResponse serverHttpResponse) -> {
  
      InetSocketAddress address = serverHttpRequest.getRemoteAddress();
      log.info("请求ip：{}", address);
      log.info("请求路径：{}", serverHttpRequest.getPath());
      log.info("请求方法：{}", serverHttpRequest.getMethod());
  
      // 返回响应
      DataBufferFactory factory = serverHttpResponse.bufferFactory();
      byte[] data = String.format("Hello, %s .", address).getBytes();
      DataBuffer dataBuffer = factory.wrap(data);
      serverHttpResponse.writeWith(Mono.just(dataBuffer));
  
      // 返回一个空的Mono，表示响应已经结束（结束信号）
      Mono<Void> complete = serverHttpResponse.setComplete();
      return complete;
  };
  
  ReactorHttpHandlerAdapter adapter = new ReactorHttpHandlerAdapter(httpHandler);
  
  // 创建一个服务器
  HttpServer.create()
      .bindAddress(() -> new InetSocketAddress(80))
      .handle(adapter)
      .bindNow();
  log.info("服务启动成功");
  
  System.in.read();
  ```

  



### @EnableWebFlux

> 与WebMVC中@EnableWebmvc类似

- mvc：@EnableWebmvc，开启mvc的自定义配置

  flux：@EnableWebFlux，开启WebFlux的自定义，添加后会禁用WebFlux的默认配置（默认配置在 WebFluxAutoConfiguration 自动配置类中配置），会造成网页不会自动映射路径等

- WebmvcConfig

  WebFluxConfigurer：可用来修改配置

  ```java
  @Configuration
  public MyConfiguration {
      @Bean 
      public WebFluxConfigurer webFluxConfigurer() {
          return new WebFluxConfigurer() {
              // 可重写路径映射，跨域等方法来修改配置
              @Override
              public void addCorsMappings(CorsRegistry registyr) {
                  registry.addMapping("/**")
                      // 配置跨域
                      .allowHeaders("*")
                      .allowedMethods("*")
                      .allowedOrigins("localhost");
              }
          }
      }
  }
  ```

  



### Controller

- 返回 `Mono` 对象

  ```java
  @GetMapping(value = "/{id}", produces = {MediaType.APPLICATION_JSON_VALUE})
  public Mono<User> findById(@PathVariable int id) {
      return userService.findById(id);
  }
  ```

- 返回 `Flux` 对象

  ```java
  @GetMapping(value = "/getAll", produces = "text/event-stream;charset=UTF-8")
  public Flux<User> getAll() {
      return userService.findAll()
          .delayElements(java.time.Duration.ofSeconds(1));
  }
  ```

- **请求**：获得请求的参数如下，其中 Request、Response、Session 较mvc有变化，新增 FilePart

  ```java
  @GetMapping(value = "/{id}")
  public Mono<User> findById(@PathVariable int id,
                             @RequestBody String s, // 只是获取请求体
                             HttpEntity<String> entity, // 包装了 请求头和请求体
                             @CookieValue(value/name="myCookie") String value,
                             WebSession webSession, // 自动注入session
                             HttpMethod method, // 请求方式
                             MultipartFile file, // 文件上传：multipart/form-data
                             FilePart file, // 文件上传，非阻塞API，底层使用零拷贝：transferTo方法
                             @RequestAttribute String attribute, // 转发请求的请求域数据
                             ServerWebExchange exchange) { // 包装了 请求和响应
      // 获得Request
      ServerHttpRequest request = exchange.getRequest();
      // 获得Response
      ServerHttpResponse response = exchange.getResponse();
      
      return userService.findById(id);
  }
  ```





### Filter

```java
@Slf4j
@Component
public class MyWebFilter implements WebFilter {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, WebFilterChain chain) {

        log.info("目标方法处理请求前");
        Mono<Void> filter = chain.filter(exchange)
            .doFinally(signalType -> {
                log.info("目标方法处理请求后！");
            });

        // 因为webflux是全异步，所以这里的日志打印一般不会在请求处理后
        log.info("目标方法处理请求后？");

        return filter;
    }
}
```

```java
[INFO ] 18:27:14:598   reactor-http-nio-2 --->    目标方法处理请求前
[INFO ] 18:27:14:598   reactor-http-nio-2 --->    目标方法处理请求后？
[INFO ] 18:27:14:624   reactor-http-nio-2 --->    目标方法执行
[INFO ] 18:27:14:649   reactor-http-nio-2 --->    目标方法处理请求后！
```





### SSE

- sse 和 websocket 的两种协议的区别
  - 通信方式：WebSocket 是全双工的，这意味着服务器和客户端可以同时发送和接收信息。而 SSE 是单向的，只能由服务器向客户端发送信息。
  - 协议：WebSocket 是一个独立的协议，它在 TCP 上建立了一个持久的连接。而 SSE 是基于 HTTP 协议的，它通过 HTTP 连接发送事件。
  - 使用场景：由于 WebSocket 是全双工的，因此它更适合需要双向通信的应用，如聊天应用、多人在线游戏等。而 SSE 由于只能由服务器向客户端发送信息，因此它更适合需要服务器向客户端推送信息的应用，如股票价格更新、新闻更新等。 

- Servlet代码

  ```java
  @WebServlet("/sse")
  public class SSEServlet extends HttpServlet {
      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          resp.setContentType("text/event-stream");
          resp.setCharacterEncoding("utf-8");
  
          for (int i = 0; i < 10; i++) {
              // 指定事件标识，格式：event: + 事件名字 + 1个换行
              resp.getWriter().write("event:me\n");
              // data 上还可以添加 id、retry、注释等
              // 发送数据流，格式：data: + 数据 + 2个换行
              resp.getWriter().write("data: " + i + "\n\n");
              resp.getWriter().flush();
              try {
                  TimeUnit.SECONDS.sleep(1);
              } catch (InterruptedException e) {
                  e.printStackTrace();
              }
          }
          
          resp.getWriter().write("event:me\n");
          resp.getWriter().write("data: " + "end" + "\n\n");
      }
  }
  ```

- 构建SSE对象

  ```java
  @GetMapping(value = "/sse", produces = MediaType.TEXT_EVENT_STREAM_VALUE)
  public Flux<ServerSentEvent<String>> sse() {
      return Flux.interval(Duration.ofMillis(100))
          .map(l -> ServerSentEvent
               .builder(String.format("这是第 %d 条数据", l))
               .id(String.valueOf(l))
               .event("message")
               .comment("这是一条注释")
               .retry(Duration.ofSeconds(3))
               .build()
              );
  }
  ```
  
- 前端

  ```js
  let sse = new EventSource("/servlet_test_war/sse");
  let textarea = document.querySelector(".textarea");
  
  // 不指定事件标志，会默认使用 “message” 事件
  sse.onmessage = function (event) {
      console.log("event: ", event)
      console.log("event.data: ", event.data);
      textarea.innerHTML = textarea.innerHTML + " " + event.data;
  };
  
  // 指定事件标志，不会使用默认的 message 事件
  sse.addEventListener("me", function (event) {
      // 如果服务端发送 end，则关闭连接，否则会不断发起数据流请求
      if (event.data === "end") sse.close(); 
      console.log("event: ", event)
      console.log("event.data: ", event.data);
      textarea.innerHTML = textarea.innerHTML + " " + event.data;
  });
  ```



### DispatchHandler

> SpringMVC：DispatcherServlet
>
> SpringWebFlux：DispatchHandler

- DispatchHandler 中的三个成员变量，存储了处理请求的处理器

  ```java
  @Nullable
  private List<HandlerMapping> handlerMappings;
  @Nullable
  private List<HandlerAdapter> handlerAdapters;
  @Nullable
  private List<HandlerResultHandler> resultHandlers;
  ```

- 请求处理流程

  - HandllerMapping：请求映射处理器，保存了每个请求由那个方法进行处理
  - HandlerAdapter：处理器适配器
  - HandlerResultHandler：[处理器结果]处理器：处理[处理器产生的结果]的处理器

- SpringMVC：DispatcherServlet 有一个 doDispatch() 方法，来处理所有的请求；

  WebFlux：DispatchHandler 有一个 handle() 方法，来处理所有请求

  ```java
  @Override
  public Mono<Void> handle(ServerWebExchange exchange) {
      if (this.handlerMappings == null) {
          return createNotFoundError(); // 返回 Mono.error(ex)
      }
      if (CorsUtils.isPreFlightRequest(exchange.getRequest())) {
          return handlePreFlight(exchange);
      }
      return Flux.fromIterable(this.handlerMappings)
          .concatMap(mapping -> mapping.getHandler(exchange))
          .next()
          .switchIfEmpty(createNotFoundError())
          .onErrorResume(ex -> handleDispatchError(exchange, ex))
          .flatMap(handler -> handleRequestWith(exchange, handler));
  }
  ```

  1. 请求和响应都封装在 ServerWebExchange 对象中，有 handle 方法进行处理

  2. 首先判断是否存在映射处理器，如果没有任何的映射处理器，直接以 Mono.error 结束流

  3. 再通过跨域工具，判断是否跨域请求

  4. ```java
         // 以映射处理器集合创建流
     Flux.fromIterable(this.handlerMappings)
         // 找到能处理该请求的 映射处理器
         .concatMap(mapping -> mapping.getHandler(exchange)) // FLux<处理器>
         // 一旦上游找到处理器，直接获得（获得第一个能处理该请求的映射处理器）
         .next() // Mono<处理器>
         // 如果没拿到则转为 Mono.erro
         .switchIfEmpty(createNotFoundError()) // Mono<处理器 或 异常信号>
         // 如果上游出现异常信号，从 处理器适配器集合 中找 异常处理器
         .onErrorResume(ex -> handleDispatchError(exchange, ex)) // Mono<处理 请求或异常 的处理器>
         // 处理请求或异常
         .flatMap(handler -> handleRequestWith(exchange, handler));
     ```



### 异常处理器

> 和WebMVC的写法相同
>
> >  webmvc 的异常处理：依赖于 AOP
>
> > webflux 的异常处理：依赖于 Reactor 的错误处理机制。在 WebFlux 中，所有的请求处理都是通过一个反应式流进行的，当在这个流中发生错误时，Reactor 提供了一种错误处理机制，允许你在流的末端捕获并处理错误

```java
@RestControllerAdvice
public class GlobalExecptionHandler {
    @ExeptionHandler(Exception.class)
    public  error(Exception e) {
        return Mono.just(e.getMessage());
    }
}
```





### Router Functions

- Service：返回值是Mono、Flux对象

  ```java
  @Service
  public class UserService {
      public Flux<User> findAll() { ... }
      public Mono<User> findById(String id) { ... }
  }
  ```

- 路由

  ```java
  //@Configuration
  public class AllRouters {
      @Bean
      RouterFunction<ServerResponse> userRouter(UserRouterHandler userRouterHandler) {
          return RouterFunctions.nest(RequestPredicates.path("/user"),
                  RouterFunctions
                          .route(RequestPredicates.GET("/getAll"), userRouterHandler::getAllUser)
                          .andRoute(RequestPredicates.GET("/{id}"), userRouterHandler::getUserById));
      }
  }
  ```

- 处理

  ```java
  @Component
  public class UserRouterHandler {
      @Autowired
      private UserService userService;
  
      public Mono<ServerResponse> getAllUser(ServerRequest request) {
          return ServerResponse
                  .ok()
                  .contentType(MediaType.APPLICATION_NDJSON)
                  .body(userService.findAll().delayElements(Duration.ofMillis(1000)), User.class);
      }
  
      public Mono<ServerResponse> getUserById(ServerRequest request) {
          return ServerResponse
                  .ok()
                  .contentType(MediaType.APPLICATION_NDJSON)
                  .body(userService.findById(request.pathVariable("id")),
                          User.class);
      }
  }
  ```

- `MediaType.APPLICATION_NDJSON`："application/x-ndjson"

  - 在Spring 5.2及更高版本中，`MediaType.APPLICATION_STREAM_JSON`已被弃用。取而代之的是`MediaType.APPLICATION_NDJSON`，它表示Newline Delimited JSON，也就是每个JSON对象之间由换行符分隔。
  - NDJSON，全称为Newline Delimited JSON，是一种存储多个JSON对象的格式，每个JSON对象占一行，对象之间通过换行符分隔
  - 这种格式主要用于处理大量的JSON对象，特别是在流式处理中
  - 如果直接访问`Content-Type`为`application/x-ndjson`的地址，浏览器可能不知道如何处理这种类型的数据，所以它选择下载文件。需要在前端使用JavaScript来处理





## R2DBC

### 规范

- R2DBC规范：r2dbc-spi

- R2DBC实现：

  - oracle-r2dbc
  - r2dbc-h2
  - r2dbc-mariadb
  - r2dbc-mssql
  - r2dbc-mysql
  - r2dbc-postgresql

- 导入依赖

  ```xml
  <dependency>
      <groupId>dev.miku</groupId>
      <artifactId>r2dbc-mysql</artifactId>
      <version>0.8.2.RELEASE</version>
  </dependency>
  ```

- 原生API

  ```java
  MySqlConnectionConfiguration configuration =
      MySqlConnectionConfiguration.builder()
      .username("root")
      .password("root")
      .host("192.168.81.128")
      .database("test")
      .build();
  ConnectionFactory connectionFactory = MySqlConnectionFactory.from(configuration);
  
  Mono.from(connectionFactory.create())
      .flatMapMany(connection ->
                   connection.createStatement("select * from emp " +
                                              "where dept_id = ? and salary > ?salary")
                   .bind(0, 10)
                   .bind("salary", 2000)
                   .execute())
      .flatMap(result ->
               result.map(readable -> {
                   Emp emp = new Emp();
                   emp.setEmp_id(readable.get("emp_id", Integer.class));
                   emp.setEmp_name(readable.get("emp_name", String.class));
                   emp.setSalary(readable.get("salary", BigDecimal.class));
                   return emp;}))
      .take(5)
      .doOnNext(System.out::println)
      .subscribe();
  TimeUnit.SECONDS.sleep(2);
  ```





### r2dbc-starter

- 自动配置类
  - R2dbcAutoConfiguration：主要配置连接工厂、连接池
  - R2dbcDataAutoConfiguration
    - R2dbcEntityTemplate：数据库的响应式客户端，该对象提供了一些curd api；类似的如：RedisTemplate等
    - 数据类型、映射关系、转换器、自定义转化器组件 等：作用是将数据库的 int、decimal等 与 java的Integer、BigDecimal等 映射转化
  - R2dbcRepositoriesAutoConfiguration
    - 开启Spring Data声明式接口的CRUD
      - mybatis-plus实现了该接口，提供了BaseMapper、IService等简化开发
      - Spring Data 本身也提供了基础的CURD实现
  - R2dbcTransactionMangerAutoConfiguration：事务管理

- 使用

  - 依赖

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-r2dbc</artifactId>
    </dependency>
    <dependency>
        <groupId>dev.miku</groupId>
        <artifactId>r2dbc-mysql</artifactId>
        <version>0.8.2.RELEASE</version>
    </dependency>
    ```

  - 配置文件

    ```yml
    spring:
      r2dbc:
        username: root
        password: root
        url: r2dbc:mysql://192.168.81.128:3306/test
    ```

  - pojo

    ```java
    @Table("emp")
    public class Emp {
        @Id
        @Column("emp_id")
        private Integer id;
        private Integer deptId; // deptId 不需要使用 @Column("dept_id")
        private LocalDate hireDate; // 日期的映射不能使用 Date，只能用 LocalXXX、Instant
        private Integer deptId;
        @Transient // 表示临时字段，并不是数据库表中的一个字段
        //private String deptName; // 错误写法
        private Dept dept;
        // ...
    }
    ```

  - 持久层

    ```java
    @Repository
    public interface EmpDao extends ReactiveCrudRepository<Emp, Integer> {
        // 利用IDE提示功能 和 R2DBC根据方法名自动生成查询功能 编写单表查询方法
        Flux<Emp> findEmpsBySalaryGreaterThanEqualAndDeptIdIn(BigDecimal salary, Collection<Integer> deptId);
        // findAllXXX  => select *
        // findEmpsXXX => select emp.emp_id emp.emp_name ...
        
        
        @Query("""
        select e.emp_id, e.emp_name, d.dept_name
        /* 如果实体类Emp中有 deptName 属性上，且该属性上加了@Transient，则不会为该字段赋值
           如果没加该注解，则在使用上面 findEmpsXXX 时，会将该属性作为字段查询数据库从而报错
           正确做法是在实体类 Emp 中添加属性 Dept ，并为该属性添加 @Transient */
        from emp e
        left join dept d
        on e.dept_id = d.dept_id
        where e.emp_id = :id; // 冒号+形参名
        """)
        Mono<Emp> customFindById(Integer id);
    }
    ```

    - @EnableR2dbcRepositories注解，会自动查找所有的存储库接口，并自动实现它们（上面的findxxx方法）。
    - 如果类路径下有Spring Data R2DBC的类，Spring Boot会自动启用R2DBC存储库的支持。这意味着，即使你没有在你的主应用类上使用@EnableR2dbcRepositories注解，Spring Boot仍然会自动配置并启用R2DBC存储库。
    - 如果你需要更细粒度的控制，比如指定存储库接口的位置，或者自定义存储库的实现，那么就需要使用@EnableR2dbcRepositories注解。

  - 业务层

    ```java
    @Service
    public class EmpService {
        @Autowired
        private EmpDao empDao;
        @Autowired
        @SuppressWarnings("SpringJavaInjectionPointsAutowiringInspection")
        private R2dbcEntityTemplate template; // 一般用于单表查询
        @Autowired
        @SuppressWarnings("SpringJavaInjectionPointsAutowiringInspection")
        private DatabaseClient databaseClient; // 更底层的查询api，适用于多表查询（join）
    
        public Flux<Emp> getAllEmps() {
            Criteria criteria = Criteria.empty()
                .and("dept_id")
                .in(10, 20)
                .and("salary")
                .greaterThanOrEquals(2000);
            Query query = Query.query(criteria); // QBC(Query by Criteria) 查询，还有QBE(Example)
            return template.select(query, Emp.class); // 使用Template
        }
    
        public Mono<Emp> saveEmp(Emp emp) {
            return empDao
                .findAllBySalaryGreaterThanEqualAndDeptIdIn(
                BigDecimal.valueOf(2000), List.of(10, 20)); // 使用Dao
        }
    
        public Flux<Map<String, Object>> queryDeptName(Integer id) {
            String sql = """
                select e.emp_id as 员工id, e.emp_name 员工姓名, d.dept_name as 所在部门 
                from emp e
                left join dept d
                on e.dept_id = d.dept_id 
                where e.emp_id = ?id;
            """;
                return databaseClient.sql(sql) // 使用databaseClient
                .bind("id", id)
                .fetch() // FetchSpec<Map<String, Object>> ：map是一行数据，key是字段名，value是字段值
                .first(); // first、all、one(结果有多条会报错)  Mono<Map<String, Object>>
        }
    }
    ```






### 映射与转换

> 1对1 or 1对多 都需要自定义封装逻辑
>
> > r2dbc 中可使用 自定义转换器、DatabaseClient
> >
> > mybatis 中可使用 ResultMap 标签来封装

- pojo

  ```java
  public class Emp {
      private Integer empId;
      private String empName;
      private Integer deptId;
      @Transient
      private Dept dept; // 员工所在部门
  }
  ```

  ```java
  public class Dept {
      private Integer deptId;
      private String deptName;
      @Transient
      private List<Emp> emps; // 该部门所有员工
  }
  ```

- dao

  ```java
  @Query("""
        select e.emp_id, e.emp_name, e.dept_id, d.dept_name
        from emp e
        left join dept d
        on e.dept_id = d.dept_id
        where e.emp_id = :id;
        """)
  // 查询指定id用户的及其所在部门：1对1
  Mono<Emp> customFindById(Integer id);
  ```

- **1对1** 问题：上述查询虽然会为emp对象添加 empId、empName、deptName 属性值，但不会为属性 dept 创建对象并赋值

- 方法1（建议）：使用DatabaseClient

  ```java
  @Autowired
  private DatabaseClient databaseClient; // 更底层的查询api，适用于多表查询（join）
  ```

- 方法2：自定义转换器

  - 自定义转换器

    ```java
    @ReadingConverter
    public class EmpConverter implements Converter<Row, Emp> {
        @Override
        public Emp convert(Row source) {
            Integer id = source.get("emp_id", Integer.class);
            Integer deptId = source.get("dept_id", Integer.class);
            String name = source.get("emp_name", String.class);
            String deptName = "relax公司："+source.get("dept_name", String.class);
            return new Emp(id, name, new Dept(deptId, deptName, null));
        }
    }
    ```

  - 添加Converter

    ```java
    @Bean
    public R2dbcCustomConversions conversions() {
        return R2dbcCustomConversions.of(MySqlDialect.INSTANCE, new EmpConverter());
    }
    ```

  - **问题**：一旦使用自定义转换器（如实现Converter<Row, Emp>），所有返回值为Emp的curd都会使用该转换器，从而会产生非常繁杂的问题

    - 解决方法1：创建一个类专门用于自定义转换
    - 解决方法2：在Converter中添加一些判断逻辑，让Converter兼容更多的表结构

- **一对多** 问题：查询出各个部门及各自所有员工

  ```java
  String sql = """
      select d.dept_id, d.dept_name, e.emp_id, e.emp_name
      from dept d
      left join emp e
      on d.dept_id = e.dept_id
      order by d.dept_id /* ※，该方法一定要排序整合 */
      """;
  databaseClient.sql(sql)
      .fetch()
      .all()
      .bufferUntilChanged(rowMap -> Integer.parseInt(rowMap.get("dept_id").toString())) // ※
      .map(empMapList -> {
          Map<String, Object> map = empMapList.get(0);
          int deptId = Integer.parseInt(map.get("dept_id").toString());
          String deptName = map.get("dept_name").toString();
          List<Emp> emps = empMapList.stream()
              .map(empMap -> {
                  Object empIdObj = empMap.get("emp_id");
                  if (empIdObj == null) return null;
                  int empId = Integer.parseInt(empIdObj.toString());
                  String empName = empMap.get("emp_name").toString();
                  return new Emp(empId, empName, null);
              })
              .filter(Objects::nonNull)
              .collect(Collectors.toList());
          return new Dept(deptId, deptName, emps);
      })
      .doOnNext(System.out::println)
      .subscribe();
  TimeUnit.SECONDS.sleep(3);
  ```









# MQ



## 问题处理

- 消息可靠性问题：确保发送的消息至少被消费一次
- 消息延迟问题
- 消息堆积问题
- 高可用问题：避免单点mq故障



### 消息可靠性

- 哪些可能会导致消息丢失
  - 发送者发送的消息未送达exchange
  - 消息到exchange后未到达queue

- mq宕机，queue将消息丢失
- consumer接收到消息后未消费就宕机



#### 生产者消息确认

- Rabbitmq提供了publisher confirm机制来避免消息发送到MQ的过程中丢失。消息发送到MQ以后，会返回一个结果给发送者，表示消息是否处理成功。结果有两种请求
  - publisher-confirm，发送者确认
    - 消息成功投递到交换机，返回ack
    - 消息未成功投递到交换机，返回nack
  - publisher-return，发送者回执
    - 消息投递到交换机，但是没有路由到队列，返回ack，及路由失败原因
  - 确认机制发送消息时，需要给每个消息设置一个全局唯一id，以区分不同消息





#### 消息持久化

#### 消费者消息确认

#### 消费失败重试机制





### 死信交换机



### 惰性队列

### MQ集群









# Spring底层

















# Kubernetes



## 部署

### 安装docker

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
    
    # 安装指定版本的 docker：此处部署 k8s 使用该版本 docker
    yum install -y docker-ce-20.10.7 docker-ce-cli-20.10.7 containerd.io-1.4.6
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
    
    # 现在启用docker，并设置为开机自启
    systemctl enable docker --now
    
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






### 安装kubeadm

> 安装 kubelet、kubeadm、kebectl

- 要求

  - 兼容的 Linux 主机。Kubernetes 项目为基于 Debian 和 Red Hat 的 Linux 发行版以及一些不提供包管理器的发行版提供通用的指令
  - 每台机器 2GB 以上的内存
  - 2 核或更多
  - 集群中所有的机器的网络彼此互通
  - 集群中的每个节点，不可以使用重复的主机名、MAC地址、product_uuid
  - 永久关闭交换分区。为保证 kubelet 正常工作，必须禁用交换分区

- 前置操作（尽量在root用户下执行）

  - 关闭防火墙

    ```shell
    systemctl stop firewalld
    systemctl disable firewalld
    ```

  - 设置不同主机名

    ```shell
    # 查看当前主机名
    hostname
    # 设置主机名
    hostnamectl set-hostname <hostname>
    
    # [root@k8s-master ~]   # 重新建立会话，即可看到更新后的主机名
    # root：当前用户； k8s-master：主机名； ~：当前所在目录
    ```

  - 关闭交换分区 等操作

    ```shell
    # 查看交换分区
    free -m
    
    # 将 SELinux 设置为 permissive 模式
    sudo setenforce 0 
    sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
    
    # 关闭swap
    swapoff -a
    sed -ri 's/.*swap.*/#&/' /etc/fstab
    
    # iptables 检查桥接流量
    cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
    br_netfilter
    EOF
    
    cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
    net.bridge.bridge-nf-call-ip6tables = 1
    net.bridge.bridge-nf-call-iptables = 1
    EOF
    
    # 更新配置
    sudo sysctl --system
    
    # 时间同步
    yum install ntpdate -y
    ntpdate time.windows.com
    ```

- 安装 kubelet、kubeadm、kebectl

  ```shell
  # 配置yum源：告诉linux去哪下载
  cat > /etc/yum.repos.d/kubernetes.repo << EOF
  [kubernetes]
  name=Kubernetes
  baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
  enabled=1
  gpgcheck=0
  repo_gpgcheck=0
  gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
  EOF
  
  
  
  sudo yum install -y kubelet-1.20.9 kubeadm-1.20.9 kubectl-1.20.9 --disableexcludes=kubernetes
  
  sudo systemctl enable --now kubelet
  # kubelet 现在会每隔几秒就会重启，因为它陷入了一个等待 kubeadm 指定的死循环
  ```

  

### 引导集群

> 使用 kubeadm 引导集群

- 下载各个机器需要的镜像

  ```shell
  # 编写一个脚本，让它循环去拉去 docker 镜像（用完后可删除）
  sudo tee ./images.sh <<- 'EOF'
  #!/bin/bash
  images=(
  kube-apiserver:v1.20.9
  kuber-proxy:v1.20.9
  kuber-controller-manager:v1.20.9
  kube-scheduler:v1.20.9
  coredns:1.7.0
  etcd:3.4.13-0
  pause:3.2
  )
  for imageName in ${images[@]} ; do
  # docker pull registry.aliyuncs.com/google_containers/$imageName
  docker pull registry.cn-hangzhou.aliyuncs.com/lfy_k8s_images/$imageName
  done
  EOF
  
  chmod +x ./images.sh && ./images.sh
  ```

- 为每个节点添加一个域名解析

  ```shell
  # echo "master节点的内网IP地址 自定义master节点名" >> /etc/hosts
  echo "192.168.81.129 k8s-master" >> /etc/hosts
  
  # 保证所有节点 ping master节点名 可以ping到master节点
  ping k8s-master
  ```

- 主节点初始化（**只在主节点上运行**）

  ```shell
  kubeadm init \
  --apiserver-advertise-address=主节点ip地址  \
  --control-plane-endpoint=主节点名（上一步配置的域名解析中的主节点名）  \
  --image-repository registry.cn-hangzhou.aliyuncs.com/lfy_k8s_images  \
  # --image-repository registry.aliyuncs.com/google_containers  \
  --kubernetes-version   v1.20.9  \
  --service-cidr=10.96.0.0/16  \
  --pod-network-cidr=172.16.0.0/16
  # 最后两个ip网络范围不需要改，如果改，应保证：两个网络范围不重叠，与上面各个节点ip也不重叠
  ```

  ```shell
  # 最终打印的日志，该日志中，还指明了需要执行的其他几个步骤
  
  Your Kubernetes control-plane has initialized successfully!
  
  # 在启用集群前，需要在主节点运行下面紧跟着的这段代码，去创建一个合规的用户
  To start using your cluster, you need to run the following as a regular user:
  
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
  
  # 可选：如果当前是root用户，可运行下面这个代码
  Alternatively, if you are the root user, you can run:
  
    export KUBECONFIG=/etc/kubernetes/admin.conf
  
  # 需要在主节点部署一个podnetwork（从下面指定的网站中选一个使用）
      # curl https://docs.projectcalico.org/v3.20/manifests/calico.yaml -O  # 下载calico配置文件
      # kubectl apply -f calico.yaml # 应用该配置文件
  You should now deploy a pod network to the cluster.
  Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
    https://kubernetes.io/docs/concepts/cluster-administration/addons/
  
  
  # 下面的两个操作中，token令牌有效期为 24 小时
  
  # 在其他主机运行下面指令，可以让改主机加入 k8s 集群，并成为主节点
  You can now join any number of control-plane nodes by copying certificate authorities
  and service account keys on each node and then running the following as root:
  
    kubeadm join k8s-master:6443 --token 9183ef.9mygpdi3fbvet4wx \
      --discovery-token-ca-cert-hash sha256:a534b9dbf3154561db549dee065586c23d74798157333049a5a8dca5abc6ef4c \
      --control-plane 
  
  # 在其他主机运行下面指令，可以让改主机加入 k8s 集群，并成为工作节点
  Then you can join any number of worker nodes by running the following on each as root:
  
  kubeadm join k8s-master:6443 --token 9183ef.9mygpdi3fbvet4wx \
      --discovery-token-ca-cert-hash sha256:a534b9dbf3154561db549dee065586c23d74798157333049a5a8dca5abc6ef4c
  ```

- 在主节点部署pod网络（**主节点运行**）

  ```shell
  # 此处选择 calico podnetwork
  
  # 下载 calico 的配置文件（下载到当前目录，用完后可删除）
  curl https://docs.projectcalico.org/v3.20/manifests/calico.yaml -O
  
  # 应用该配置文件
  kubectl apply -f calico.yaml
  ```

- 一些指令

  ```shell
  kubectl get nodes # 查看所有节点
  kubectl get pods -A # 查看所有的pod，及其状态
  ```

- 令牌过期（令牌有效期为 24 小时），在主节点执行下面代码，重新生成令牌

  ```shell
  kubeadm token create --print-join-command
  ```



### dashboard

- 添加web可视化控制台 dashboard

  ```shell
  kubectl apply -f \
  https://raw.githubusercontent.com/kubernetes/dashboard/v2.3.1/aio/deploy/recommended.yaml
  
  # 编辑该文件
  # 打开文件后，在vim的命令模式下输入 "/type:" 找到 "type: ClusterIP"，将其修改为 "type: NodePort"
  kubectl edit svc kubernetes-dashboard -n kubernetes-dashboard
  
  kubectl get svc -A | grep kubernetes-dashboard
  # kubernetes-dashboard   dashboard-metrics-scraper   ClusterIP   10.96.186.200   <none>        8000/TCP                 5m22s
  # kubernetes-dashboard   kubernetes-dashboard        NodePort    10.96.103.72    <none>        443:31753/TCP            5m22s
  # 若配置了防火墙，或在云服务器中，要开放 31753 端口（该端口不固定）
  ```

- 云服务器记得开放上面的端口

- 访问（https协议）：`https://192.168.81.129:31753/`

- 获取token

  - 创建访问者账号：准备一个yaml文件：vim dash.yaml

    ```yaml
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: admin-user
      namespace: kubernetes-dashboard
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
      name: admin-user
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: ClusterRole
      name: cluster-admin
    subjects:
    - kind: ServiceAccount
      name: admin-user
      namespace: kubernetes-dashboard
    ```

  - 应用配置文件

    ```shell
    kubectl apply -f ./dash.yaml
    ```

  - 获取访问令牌

    ```shell
    kubectl -n kubernetes-dashboard get secret $(kubectl -n kubernetes-dashboard get sa/admin-user -o jsonpath="{.secrets[0].name}") -o go-template="{{.data.token | base64decode}}"
    ```



### Helm

- 下载二进制文件

  ```shell
  wget https://get.helm.sh/helm-v3.2.3-linux-amd64.tar.gz
  ```

- 解压

  ```shell
  tar -zxvf helm-v3.10.2-linux-amd64.tar.gz
  ```

- 将目录中的 helm 程序移动到 /usr/local/bin/helm

- 权限

  ```shell
  chmod 777 helm
  ```

  





## 介绍

### 容器介绍

- 控制面板组件（Master）
  - kube-apiserver：接口服务，基于 REST 风格开发 k8s 的接口的服务
  - kube-controller-manager：控制器管理器，管理各种类型的控制器，针对 k8s 中的各种资源进行管理
  - cloud-controller-manager：云控制器管理器，第三方云平台提供的控制器 API 对接管理功能
  - kube-scheduler：调度器，负责将 pod 基于一定的算法，将其调用到更合适的节点上
  - etcd：可理解为 k8s 的数据库，键值类型存储的分布式数据库，提供了基于 Raft 算法实现自主的集群高可用
    - 老版本：基于内存
    - 新版本：持久化存储
- 节点组件
  - kubelet：负责 pod 生命周期、存储、网络的管理
  - kube-porxy：网络代理，负责 service 的服务发现、负载均衡
  - container runtime：容器的运行时环境（如：docker、containerd、CRI-O）
- 附加组件
  - kube-dns：负载为整个集群提供 DNS(域名解析) 服务
  - ingress controller：网关，外部访问的入口
  - heapster/Prometheus：提供资源监控
  - dashboard
  - federation
  - fluentd-elasticsearch：提供集群日志采集、存储与查询





### 专业术语

- 服务的分类

  - 无状态
    - 代表应用：Nginx、Apache
    - 优点：对客户端透明，无依赖关系，可以高效实现扩容、迁移
    - 缺点：不能存储数据，需要提供额外的数据服务支持
  - 有状态
    - 代表应用：MySQL、Redis
    - 优点：可以独立存储数据，实现数据管理
    - 缺点：集群环境下实现主从、数据同步、备份、水平扩容复杂

- 资源和对象

  Kubernetes 中的所有内容都被抽象为“资源”，如 Pod、Service、Node 等都是资源。“对象”就是资源的实例，是持久化的实体。

  对象的创建、删除、修改都是通过 Kubernetes API ，也就是 API Server 组件提供的 API 接口，这些是 Restful 风格的 API，与 k8s “万物皆对象”理念相符。命令行工具 kubectl ，实际上调用的是Kubernetes api。

  - 对象的归约和状态

    - 归约：Spec

      描述了对象的期望状态，以及关于对象的一些基本信息

    - 状态：Status

      表示对象的实际状态，该属性由 k8s 自己维护，k8s 会通过一系列的控制器对对象进行管理，让对象尽可能的符合期望状态

  - 资源的配置：资源清单、资源配置文件：k8s 中资源的管理，可通过 yaml 配置文件进行配置

  - 资源的分类

    - 元数据类型

      - Horizontal Pod Autoscaler（HPA）：Pod自动扩/缩容，可以根据cpu使用率或自定义指标自动对pod进行扩/缩容
      - PodTemplate：Pod模板，控制器通过 Pod Template 信息来创建Pod。（配置文件中通过 spec.template.* 来配置）
      - LimitRange：限制范围，可以对集群内的 Request 和 Limits 做一个全局统一限制。

    - 集群

      - Namespace
      - Node
      - ClusterRole
      - ClusterRoleBinding

    - 命名空间

      - **工作负载型** - Pod

        - 副本（replicas）

        - 控制器：控制pod的创建

          - **适用于无状态服务**

            - ReplicationController（RC）：帮助我们动态更新 pod 的副本数
            - ReplicaSet（RS）：帮助我们动态更新 pod 的副本数，可以通过 selector 来选择对具有哪些标签（Label）的 pod 生效
            - Deployment：针对 RS 的更高层次的封装，提供了更多的功能
              - 创建 Replica Set / Pod
              - 滚动升级/回滚
              - 平滑扩容、缩容（基于 RS 实现）
              - 暂停与恢复

          - **适用于有状态服务** - StatefulSet

            StatefulSet 中，每个pod的 DNS 格式为：

            statefulSetName-{0|...|n-1}.serviceName.namespace.svc.cluster.local

            1. statefulSetName：StatefulSet的名字

            2. 0/.../n-1：pod所在的序号

            3. serviceName：Headless Service 的名字

            4. namespace：Headless Service 和 StatefulSet 必须在相同的 namespace

            - 要求
              - 稳定的持久化存储
              - 稳定的网络标志
              - 有序部署，有序拓展
              - 有序收缩，有序删除
            - 组成
              - Headless Service：用于定义网络标志（DNS domain），将域名与ip地址绑定（服务名 => 访问路径（域名） => ip）
              - volumeClaimTemplate
            - 注意事项
              - kubernetes v1.5 版本以上才支持
              - 所有 pod 的 volume 必须使用 PersistentVolumn 或者是 管理员事先创建好
              - 为了保证数据安全，删除 StatefulSet 时不会删除 Volumn
              - StatefulSet 需要一个 Headless Service 来定义 DNS domain，需要在 StatefulSet 之前创建好

          - **守护进程** - DaemonSet：一般用于 日志收集、系统监控、系统程序(kube-proxy、kube-dns) 等

          - **任务/定时任务**

            - Job：一次性任务，运行完后 pod 销毁
            - CronJob

      - **服务发现**

        - Service：实现 k8s 集群内网络调用、负载均衡
        - Ingress：反向代理，负载均衡

      - **存储**

        - Volume
        - CSI：Container Storage Interface 是由 Kubernetes、docker、mesos 等社区成员联合制定的一个行业标准接口，旨在将任意存储系统暴露给容器化应用程序

      - **特殊类型配置**

        - ConfigMap
        - Secret
        - DownwardAPI

      - 其他

        - Role
        - RoleBinding





## 资源

### 命名空间

- 指令操作命名空间

  ```shell
  # 查看所有的命名空间
  kubectl get ns
  
  # 查看指定命名空间下的pods
  kubectl get pods -n 空间名
  
  # 创建/删除 命名空间
  kubectl create ns 空间名
  kubectl delete ns 空间名 # 会删除该空间内的所有pods
  ```

- 文档操作命名空间

  编写配置文件：自定义配置文件名.yaml

  ```yaml
  apiVersion: v1
  kind: Namespace
  metadata:
    name: 命名空间名
  ```

  应用配置文件

  ```shell
  kubectl apply -f ./hello.yaml
  # 若配置文件还存在，可用配置文件删除该资源
  kubectl delete -f ./hello.yaml
  ```





### 工作负载

#### Pod

>  Pod：运行中的一组容器，Pod是kubernetes中应用的最小单位

- 命令行创建pod（默认创建的pod在default命名空间下）

  ```shell
  # kubectl run pod名 --image=使用的镜像名
    kubectl run mynginx --image=nginx
    
  # kubectl delete pod pod名 -n 所在命名空间
    kubectl delete pod mynginx # 不加 -n 表示删除default命名空间的pod
  ```

- 文档配置pod

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata: 
    name: mynginx
    namespace: default
    labels: # 可自定义标签的键和值
      app: mynginx
      version: 1.0.0
  spec:
    containers: # 一个pod中可以配置多个容器
    - image: nginx
      name: mynginx
      command: # 还可以配置一些 容器启动后 需要执行的指令
      # Always: 每次都拉取远程镜像；Never: 只使用本地库；IfNotPresent: 优先使用本地库，本地库没有则拉取
      imagePullPolicy: IfNotPresent
      # Always: 默认，一旦停止运行，就重启服务；OnFailure: 若容器正常结束，则不重启；Never: 不重启
      restartPolicy: Always
      workingDir: /usr/share/nginx # 进入容器后，默认进入的位置
      resources:
        requests: # 配置系统资源使用的下限
          cpu: 100m # cpu至少使用 100/1000 个核心
          memory: 128Mi # 内存至少使用 128 Mb
        limits: # 配置系统资源使用的上限
          cpu: 200m # cpu 至多使用 200/1000 个核心
          memory: 256Mi
  ```
  
  ```shell
  kubectl apply -f ./mynginx.yaml
  kubectl delete -f ./mynginx.yaml
  ```
  
- 实用技巧：如果不知道yaml如何配置，可查看集群中运行的其他资源的 yaml，照猫画虎

  注意：原生 pod 的配置文件可以用 edit 查看，但无法编辑生效，若需要动态跳转 pod ，可编辑 RS、Deployment 配置文件
  
  ```shell
  kubectl edit 资源类型 资源对象名 [-n 该资源对象所在命名空间]
  kubectl edit deploy coredns -n kube-system
  # vim在命令模式下使用  "/"+需要查找的内容  可快速定位到对应位置
  
  # 查看该资源对象的yaml（可查看到 status）
  kubectl get 资源类型 资源对象名 -o yaml
  ```
  
- 其他

  ```shell
  # 查看 default 命名空间内的pod
  kubectl get pod
  # 查看运行信息
  kubectl describe pod pod名
  
  # 查看日志
  kubectl logs mynginx
  kubectl logs -f mynginx # 追踪显示日志
  
  # 每一个pod，k8s 都会分配一个ip
  kubectl get pod -owide # 查看default空间下、pod的详细信息（可查到ip信息）
  # 使用pod的ip+pod里面运行容器的端口，可在集群内的任意节点访问该应用
  curl 172.16.169.131:80
  
  # 进入pod内部
  kubectl exec -it mynginx -- /bin/bash
  ```

- 探针

  - 种类

    - StartupProbe(启动探针)：k8s 1.16提供，用于服务当前是否完成启动（该探针具有排他性，即服务尚未完成启动时，其他探针不可用，防止出现服务因启动时间过长，被其他探针认为是运行出错）
    - LivenessProbe(存活探针)：服务当前是否正常运行。检测失败，则会执行重启策略
    - ReadinessProbe(就绪探针)：服务当前是否具有处理请求的能力

  - 探测方式

    - ExecAction：通过是否成功执行某一个指令判断是否检测成功
    - TCPSocketAction：通过是否建立tcp连接判断（适用于Nginx等）
    - HttpGetAction：通过是否处理HttpGet请求判断（适用于java服务）

  - 参数配置

    ```yaml
    initialDelaySeconds: 60 # 初始化时间
    timeoutSeconds: 2
    periodSeconds: 5 # 检测间隔时间
    successThreshold: 1 # 检测 1 次成功就表示成功
    failureThreshold: 2 # 检测失败 2 次就表示失败
    ```

  - 实例

    ```yaml
    kind: Pod
    spec:
      containers:
      - name: ...
        image: ...
        startupProbe: # 配置启动探针
          tcpSocket:
            port: 80
          timeoutSeconds: 3
          ...
        livenessProbe: # 配置存活探针
          httpGet:
            path: /api/living
            port: 80
            scheme: HTTP
          ...
    ```

- 生命周期

  ![pod生命周期](D:\picture\typora\后端2\kubernetes\pod生命周期.png)

  - 初始化容器：配置后，在主容器运行前运行
  
    ```yaml
    kind: Deployment
    spec:
      template:
        spec:
          initContainers: # 配置初始化容器
          - name: init-container
            image: 镜像
            command: [一些初始化指令]
            imagePullPolicy: IfNotPresent
          containers: # 主容器
    ```
  
  - pod退出流程
  
    - Endpoint 删除 pod 的 ip 地址
  
    - Pod 状态变为 Termination 状态
  
      ```yaml
      # 变为删除中的状态后，会给pod一个缓冲期，让pod去执行一些销毁操作
      terminationGracePeriodSeconds: 30
      containers:
      - ...
      ```
  
    - 执行 preStop 钩子函数的指令
  
  - preStop 的应用
  
    - 注册中心下线
    - 数据记录、销毁等
  
  - 实例
  
    ```yaml
    kind: Pod
    spec:
      containers:
        command: # 配置一些 容器启动后 需要执行的指令
        lifecycle:
          postStart: 
          # 一般不使用，因为我们使用该钩子函数是需要让其在容器启动后立即执行
          # 但实际上无法确定和上面command中的指令的执行先后顺序
            exec:
              command: 
              ...
          preStop:
            httpGet: # 除了配置指令，还可以发送http请求
              path: /api/preStop
              port: 80
    ```





#### Deployment、ReplicaSet

> Deployment（部署计划）：控制Pod，使Pod拥有多副本、自愈、扩容、缩容、暂停与恢复等功能
>
> 创建 deploy 时，会创建一个 ReplicaSet，deploy 的扩/缩容功能来自于RS

- label 和 selector

  - label

    - 配置文件配置

    - kubectl 配置

      ```shell
      # 创建临时 label
      kubectl label 资源种类 资源对象名 标签键=标签值 [-n namespace]
      # 修改label
      kubectl label 资源种类 资源对象名 标签键=标签值 --overwrite
      # 查看资源对象的labels
      kubectl get pods --show-labels
      ```

  - selector

    - 配置文件配置

    - kubectl

      ```shell
      # 根据标签，查询指定资源对象，多个条件，逗号两边是 与 的关系
      kubectl get po -l 'version in (1.0, 1.1, 1.2), author=eli, app!=order'
      ```

- 命令

  ```shell
  # 自愈：指定一个部署计划。 会自动部署指定pod，若该pod被删除，会自动重新部署
  kubectl create deployment dep-mynginx --image=nginx
  
  # 查看所有部署计划
  kubectl get deploy [-n 指定namespace]
  # 删除部署计划
  kubectl delete deploy 部署计划名 [-n namespace名]
  
  # 多副本
  kubectl create deployment my-dep --image=nginx --replicas=3
  ```

- 配置文件

  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata: 
    labels:
      run: mynginx-dep
      version: '1.0' # 数值类型的标签值需要加 引号
    name: mynginx-dep
    namespace: default
  spec:
    replicas: 3
    revisionHistoryLimit: 10 # 进行滚动更新后，保留的历史版本数（本质是保留RS的最大个数）
    selector: # deploy 依靠 rs 完成扩缩容，此处用于找到匹配的 RS
      matchLabels:
        app: mynginx-dep # 会自动为对应 rs 创建该标签
    strategy: # 更新策略
      rollingUpdate: # 滚动更新策略
        maxSurge: 25% # 可以同时启动最多 25% 的新实例
        maxUnavailable: 25% # 可以同时停止最多 25% 的旧实例
      type: RollingUpdate # 更新类型为滚动更新
    template: # 用来描述 RS 创建的 pod 的模板信息
      metadata:
        labels:
          app: mynginx-dep
      spec:
        containers:
        - image: nginx
          name: mynginx
  ```

- 扩容、缩容

  ```shell
  # 原本副本数为 3
  kubectl create deployment mynginx-dep --image=nginx --replicas=3
  
  # 流量高峰时，可将原部署计划，扩容到5
  kubectl scale deploy/mynginx-dep --replicase=5 [-n namespace]
  # 流量低谷时，可将其缩容到2
  kubectl scale deploy/mynginx-dep --replicase=2
  
  # 还可以编辑 yaml 实现扩容、缩容
  kubectl edit deploy 部署计划名
  ```

- 自愈 和 故障转移：deployment的固有属性

- 滚动更新（**本质是RS的变动**）

  - 只有修改了 deploy 中的 template 属性后，才会触发更新操作（扩缩容不是更新，RS创建pod的模板不变）
  - 滚动更新的本质是：创建一个新的 RS，两个 RS 中的 pod 进行依次上下线
  - 回滚的本质是：当新的 RS 中的 pod 完全部署完后，旧的 RS 并不会删除（可通过 `kubectl get rs` 查看），回退到某个版本，就是与该版本对应的 RS 进行滚动更新

  ```shell
  # 将 pod 中 nginx(:lastest) 镜像更换为 nginx:1.16.1
  # --record 记录更新
  kubectl set image deptloy/mynginx-dep nginx=nginx:1.16.1 --record
  
  # 查看更新记录（可查看的历史版本序号）
  kubectl rollout history deptloyment/mynginx-dep
  
  # 回滚到历史某一版本（使用历史版本需要进行回滚）
  kubectl rollout undo deploy/mynginx-dep --to-revision=1
  ```

- 暂停与恢复

  由于每次对 pod template 中的信息发生修改后，都会触发更新操作。如果短时间内多次修改template信息，就会触发多次更新，但实际上只要执行最后一次修改后的template即可。此时可以先暂停rollout，最后一次修改完成后，再恢复

  ```shell
  # 暂停滚动更新
  kubectl rollout pause deploy deploy名
  # 恢复滚动更新
  kubectl rollout resume deploy deploy名
  ```



#### StatefulSet

- 配置文件

  ```yaml
  # 需要先配置一个网络
  apiVersion: v1
  kind: Service
  metadata: 
    name: nginx
    labels: nginx
      app: nginx
  spec:
    ports:
    - port: 80
      name: web
    clusterIP: None
    selector:
      app: nginx
  ---
  apiVersion: apps/v1
  kind: StatefulSet
  metadata:
    name: web
  spec:
    serviceName: "nginx" # 使用哪个 service 来管理 dns
    replicas: 2
    selector: 
      matchLabels:
        app: nginx
    template: 
      metadata:
        labels:
          app: nginx
      spec:
        containers:
        - name: nginx
          image: nginx:1.7.9
          ports: # 容器内部要暴露的端口
          - containerPort: 80
            name: web
  ```
  
- 扩容与缩容：statefulset的缩容会保证顺序性

  ```shell
  # 使用命令 扩缩容
  kubectl scale sts sts名 --replicas=4
  ```

- 镜像更新：可以通过 patch 来间接实现，或者修改 yaml 更新

  ```shell
  # 通过 patch 更新镜像
  kubectl patch statefulset sts名 --type="json" \
  -p '[{"op":"replace","path":"/spec/template/spec/containers/0/image","value":"nginx:1.9.1"}]'
  
  # 修改 yaml 来更新镜像
  kubectl edit sts sts名
  ```

  ```shell
  # 查看历史版本2的sts信息
  kubectl rollout history sts sts名 --revision=2
  # 查看扩缩容、更新等操作的详细信息
  kubectl describe sts sts名
  ```

  - 滚动更新（RollingUpdate）：更新是，会保证顺序（倒序更新）

    使用滚动更新中的 partition 属性，可以实现 **灰度发布**（金丝雀发布）。例如一共有5个pod，将 partition 设置为 3，那么此时滚动更新只会更新那些 **序号 >= 3** 的 pod。

    利用该机制，可以在更新过程中，在前一次修改后没有出现问题的情况下，多次缩小 partition 的值，最终实现全部 pod 的更新

    ```yaml
    kind:
    spec:
      template:
      updateStrategy:
        rollingupdate:
          partition: 3
      type: RollingUpdate
    ```

  - OnDelete：type 设置为 OnDelete 后，即使修改了 spec.template 中的内容，也不会触发更新。只有删除该容器，sts自愈部署pod时，才会使用新的template

    ```yaml
    kind:
    spec:
      template:
      type: OnDelete
    ```

- 删除

  - 级联删除：默认情况下，删除 statefulset 时会同时删除所有 pod

  - 非级联删除：删除 sts 后，不会删除 pod

    ```shell
    # --cascade=false 已经过时，可用 --cascade=orphan 代替
    kubectl delete sts sts名 --cascade=false
    ```




#### HPA

- 适用于 RS、Deployment、StatefulSet

- 开启指标监控服务：下载 metrics-server 组件配置文件，并运行

- cpu、内存指标监控

  实现根据 cpu、内存 动态调节副本数的前提是 必须配置了 resources.requests.cpu 或 resources.requests.memory。可以配置，当 cpu/memory 达到上述配置的百分之几后进行自动扩容、缩容

  ```shell
  # 创建一个 HPA （cpu占用到20%时，需要创建最多5个节点）
  kubectl autoscale deploy deploy名 \
      --cpu-percent=20 \
      --min=2 --max=5
  
  # 查看 hpa
  kubectl get hpa
  ```





#### CronJob

- 配置文件

  ```yaml
  apiVersion: batch/v1
  kind: CronJob
  metadata:
    name: cron-job-test
  spec:
    # Allow 允许并发调度；Forbid：不允许并发调度；Replace：如果之前的任务没有执行完，直接执行新的，放弃上一个任务
    concurrencyPolicy: Allow # 并发调度策略
    failedJobsHistoryLimit: 1 # 保留多少个失败任务
    successfulJobsHistoryLimit: 3 # 保留多少个成功任务
    suspend: false # 是否挂起任务，若为 true ，表示该任务不会执行
    schedule: "* * * * *" # 调度策略
    jobTemplate:
      spec:
        template:
          spec:
            containers:
            ...
  ```

  







### 服务

#### 负载均衡

- 七层网络模型

  1. 物理层

     这一层的数据叫做**比特**。

     解决两个硬件之间怎么通信的问题，常见的物理媒介有光纤、电缆、中继器等。它主要定义物理设备标准，如网线的接口类型、光纤的接口类型、各种传输介质的传输速率等。

     它的主要作用是传输比特流（就是由1、0转化为电流强弱来进行传输，到达目的地后在转化为1、0，也就是我们常说的数模转换与模数转换）。

  2. 数据链路层

     这一层的数据叫做**帧**。

     当一个网络数据被传输到交换机上时，交换机会根据设备上网卡的 Mac 地址，找到数据最终发送到的设备

  3. 网络层

     这一层的数据叫做**报文**。

     计算机网络中如果有多台计算机，怎么找到要发的那台？如果中间有多个节点，怎么选择路径？这就是路由要做的事。

     该层的主要任务就是：通过路由选择算法，为报文通过通信子网选择最适当的路径。这一层定义的是IP地址，通过IP地址寻址，所以产生了IP协议。

  4. 传输层

     当发送大量数据时，很可能会出现丢包的情况，另一台电脑要告诉是否完整接收到全部的包。如果缺了，就告诉丢了哪些包，然后再发一次，直至全部接收为止。

     简单来说，传输层的主要功能就是：**建立端连接**。主要操作是：建立 TCP 或 UDP 连接，并给数据报文打上端口号。端口号的作用是确定哪个软件获取网络数据。

  5. 会话层

     两个程序之间的接口。如美团APP可以调用微信支付，就是两者之间的会话层接口

  6. 表示层

     负责数据格式的转换与内容的加密、解密。如：聊天表情(图片) <=> [笑脸]\(文字)

  7. 应用层

     应用层是计算机用户，以及各种应用程序和网络之间的接口，其功能是直接向用户提供服务，完成用户希望在网络上完成的各种工作。前端同学对应用层肯定是最熟悉的。

- 四层负载均衡：处于第四层，如路由器的负载均衡。路由器只负责数据包的转发，并不处理应用层数据

- 七层负载均衡：处于第七层，如nginx的负载均衡。nginx在转发请求前，可能**分析请求、修改请求**，再**转发请求**

  如nginx在处理 http://localhost/order?id=1001 这个请求时，可能将它改写为 http://localhost/order/1001 请求，再通过 /order 判断出需要发送的 order 服务





#### Service

> pod 的服务发现和负载均衡

- k8s 集群内所有操作经过 api-server 通过 service，找到并作用于所有pod

  ![k8s service](D:\picture\typora\后端2\kubernetes\k8s service.png)
  
- 命令

  ```shell
  # 创建service
  kubectl expose deploy mynginx-dep --port=8080 --target-port=80 # 暴露的service，只能集群内访问
  kubeclt expose deploy mynginx-dep \
      --port=8080 \
      --target-port=80 \
      --type=ClusterIP(只能集群内访问)/NodePort(可以集群外访问)
  
  # 删除
  kubectl delete service mynginx-dep
  
  # 查看所有service（可查看到对外暴露的ip地址）
  kubectl get service [-n namespace]
  kubectl get svc
  # Name          Type         Cluster-Ip     External-Ip    Port(s)           AGE
  # mynginx-dep   ClusterIP    10.96.91.238   <none>         8080/TCP          20s
  # mynginx-dep   NodePort     10.96.91.238   <none>         8080:30948/TCP    20s
  
  
  # 查看 pod 的 labels 信息
  kubectl get pod --show-labels
  ```

  - 负载均衡：service 的固有属性

  - 服务发现：deployment部署计划中自愈的、扩容的pod，也会自动加入该service，具有负载均衡能力

  - Type：
    - ClusterIp的访问方式
      - 集群内节点可通过 **暴露的ip:端口** 访问，即：10.96.91.138:8080 
      - 集群内节点中pod内部还可以通过 **service名:端口** 访问，如：http://mynginx-svc:8080；也可以通过 **service名.命名空间名:端口** 跨命名空间访问
      
    - NodePort 除了有上面两种访问形式，还可以
      - 集群外，可使用 **集群内任意节点的ip:分配的端口** 在浏览器访问，即：192.168.81.130:30948
      
      - 注意：NodePort 自动分配的端口范围为 30000 ~ 32767 之间
      
        分配的端口是绑在在**所有节点主机**上的，所以让这些pod**具有了供外界访问的能力**，但是，这种方式在**生产中不推荐**，一方面效率低（O(n)的时间复杂度），另一方面Service是**四层负载**
      
    - ExternalName：代理其他域名

- yaml

  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: mynginx-svc
    labels:
      app: mynginx-dep
  spec:
    selector: # 制定一个选择器，此处用于选择需要暴露的所有 pod
      app: my-dep
    ports:
    - port: 8080 # service 自己的端口，在使用内网 ip 访问时使用
      protocol: TCP
      # nodePort: 32000 # 固定绑定到所有节点的指定端口上
      targetPort: 80 # 目标 pod 的端口
    type: NodePort
  ```

- 代理k8s外部服务：让集群内任意节点中的 pod 中容器内部，可以通过 k8s 的访问规则（即：service名:端口），访问外部服务

  - 代理外部ip

    - 编写 service 配置文件，不指定 selector 属性（不指定selector属于，就不会自动创建 Endpoint）

      ```yaml
      apiVersion: v1
      kind: Service
      metadata:
        name: nginx-svc-external
        labels:
          app: nginx
      spec:
        ports: # 端口映射
        - port: 80
          target: 80
          name: web
      ```

    - 自己创建 endpoint

      ```yaml
      apiVersion: v1
      kind: Endpoints
      metadata:
        labels:
          app: nginx # 与 service 一致
        name: nginx-svc-external # 与 service 一致
        namespace: default # 于 service 一致
      subsets:
      - addresses:
        - ip: <target ip> # 外部服务ip地址
        ports:
        - name: web # 与 service 一致
          port: 80
          protocol: TCP
      ```

      - 一些指令

        ```shell
        # 应用
        kubectl apply -f endpoint.yaml
        # 查看
        kubectl get ep
        # 查看代理信息
        kubectl describe ep ep名 
        ```

  - 代理外部域名

    - service

      ```yaml
      apiVersion: v1
      kind: Service
      metadata:
        labels:
          app: service-external
        name: service-external
      spec: # 只要配置 service 的下面两项，即可代理外部域名
        type: ExternalName
        externalName: www.baidu.com
      ```

  - 作用

    - 各个环境访问名统一
    - 使用k8s的访问规则，可以访问 k8s 集群外的其他服务
    - 项目迁移未完全迁移到 k8s 时可使用





#### Ingress

- 安装

  - 通过 yaml 安装 Ingress `kubectl apply -f ./nginx-ingress.yaml` （yaml文件在 doc文件夹中）

  - 部署多种、每种多个微服务，并让每种服务形成网络

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: hello-server
    spec:
      replicas: 2
      ...
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-demo
    ...
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-demo
    ...
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: hello-server
    ...
    ```

- 编写路由规则（基础使用）

  ```yaml
  apiVersion: networking.k8s.io/v1
  kind: Ingress  
  metadata:
    name: ingress-host-bar
  spec:
    ingressClassName: nginx
    rules:
    - host: "hello.relax.space"
      http:
        paths:
        - pathType: Prefix
          # 访问该host下的根路径，路由到对应服务
          path: "/"
          backend:
            service:
              name: hello-server
              port:
                number: 8000
    - host: "nginx.relax.space"
      http:
        paths:
        - pathType: Prefix
          # 访问该host下的 /nginx/... ，路由到对应服务
          path: "/nginx"
          backend:
            service:
              name: nginx-demo
              port:
                number: 8000
  ```

- [注解使用](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/)

  - Rewrite

    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      annotations:
        # ? 取第二个捕获分组，即 (.*) 捕获的字符 传给占位符 $2
        nginx.ingress.kubernetes.io/rewrite-target: /$2
      name: rewrite
      namespace: default
    spec:
      ingressClassName: nginx
      rules:
      - host: hello.relax.space
        http:
          paths:
            # 用 (/|$) 而 不用 (/?)，是因为防止出现 /somethingabc
          - path: /something(/|$)(.*)
            backend:
              service:
                name: http-svc
                port: 
                  number: 80
    ```

  - Rate Limiting：流量限制

    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: ingress-limit-rate
      annotations:
        # rps：request per second，每秒访问数，超过该数，返回503。 还有 rpm
        nginx.ingress.kubernetes.io/limit-rps: "1"
    spec:
      ingressClassName: nginx
      rules:
      - host: "hello.relax.space"
        http:
          paths:
            # Exact 表示只匹配 / ，不匹配 /abc、/abc/def 等
          - pathType: Exact
            path: "/"
            backend:
              service:
                name: nginx-demo
                port:
                  number: 8000
    ```

  

  

### 配置和存储

#### 持久化存储

- volumes

  - hostpath：将节点上的文件或目录挂载到pod上

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: test-pd
    spec:
      containers:
      - image: nginx
        name: my-nginx
        volumeMounts:
        - mountPath: /test-pd # 挂载到容器里的那个目录
          name: test-volume # 挂载哪个 volume
      volumes:
      - name: test-volume
        hostPath: # 与节点共享目录
          path: /data # 加载节点中的指定目录到容器中
          type: Directory # 在挂载前，对目录做检查操作。默认为空字符串，表示不做任何检查
    ```

    - type
      - Directory：挂载的是目录，且该目录必须存在
      - DirectoryOrCreate：挂载的是目录，若该目录不存在，则创建权限 755 的空目录
      - File
      - FileOrCreate：若该文件不存在，创建一个权限 644 的新文件

  - emptydir：主要用于一个pod中不同container共享数据使用

    ```yaml
    spec:
      containers:
      - image: nginx
        name: my-nginx-1 # 容器 1
        volumeMounts:
        - mountPath: /test-pd-1 # 两个容器共享该文件夹
          name: test-volume
      - image: nginx
        name: my-nginx-2 # 容器 2
        volumeMounts:
        - mountPath: /test-pd-2 # 两个容器共享该文件夹
          name: test-volume
      volumes:
      - name: test-volume
        emptyDir: {}
    ```

- [NFS 挂载](https://www.bilibili.com/video/BV1MT411x7GH?p=62)：能将 NFS（网络文件系统）挂载到pod中，但由于 磁盘IO 上又多了一层 网络IO ，效率和稳定性降低

- PV、PVC







#### 配置管理

- ConfigMap

  - 创建

    ```shell
    # 查看 help
    kubectl crete configmap -h
    
    # 基于配置文件所在的文件夹创建 configmap(cm)，将该文件夹中的所有文件都作为配置文件加载到 cm 中
    kubectl create cm my-config --from-file=path/to/bar
    # 基于配置文件创建 cm，key1是对该文件所形成的那组配置进行重命名（若不添加，则使用文件名）
    kubectl create cm my-config --from-file=key1=/path/to/bar/db.properties \
                                --from-file=/path/to/bar/application.yaml
    # 基于键值对创建 cm
    kubectl create cm my-config --from-literal=JAVA_OPTS_TEST='-Xms512m -Xmx512m' [...]
    
    # 查看 cm
    kubectl get cm
    # 查看 cm 中记录的详细内容
    kubectl describe cm cm名 # cm 以明文形式记录配置
    ```

  - 在项目中使用

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata: 
      name: test-pod
    spec:
      containers:
      - name: config-test
        image: alpine
        # 该命令中包含 pod启动后打印环境变量 的指令。据此，可查看pod的日志，判断 环境变量是否成功加载 configmap
        command: ["/bin/sh", "-c", "env;sleep 3600"]
        # 用法一：环境变量加载 configamp
        env:
        - name: JAVA_VM_OPTS
          valueFrom: # 配置该环境变量的值来自的位置
            configMapKeyRef:
              name: my-config # 所需配置来自的 configmap 的名字
              key: JAVA_OPTS_TEST # configmap 中的 key
        volumeMounts: # 挂载数据卷
        - name: db-config #  表示加载 volumes 中的哪个数据卷
          mountPath: "/usr/local/mysql/conf" # 表示将数据卷中的文件，加载到某个目录下
          readOnly: true # 是否只读，默认false
      # 用法二：数据卷加载 configmap、secret
      volumes: # 定义数据卷
      - name: db-config # 数据卷的名字
        configMap: # 数据卷类型为 configmap
          name: my-config # 所需配置来自的 configmap 的名字
          items: # 若不定义 items ，表示将该 cm 中的所有配置组都转成文件
          - key: "key1" # configmap 中的key
            path: "db.properties" # 将该 key 对应的配置转换成文件
      restartPolicy: Never
    ```

- Secret：

  与configmap类似，用于存储配置信息。Secret可以提供数据加密、解密功能。

  但实际上不常用，因为默认的加密功能其并非真正的加密，而是 base64编码

  在创建 Secret 时，要注意如果要加密的字符中，包含了特殊字符，需要使用转义符转义（如 `$` => `\$`）,或用引号引起来

- SubPath 的使用

  使用场景一：一个共享卷，挂载多个路径

  使用场景二：使用 configmap 或 Secret 挂载到目录的时候，会将容器内的其他文件全部覆盖掉，而我们可能只想添加或覆盖某个文件，此时可以用到 SubPath

  - 配置方式

    - 定义 volumes 时需要增加 items 属性，配置 key 和 path，且 path 的值不能从 / 开始
    - 在容器内的 volumeMounts 中增加 subPath 属性，该值与 volumes 中的 items.path 的值相同

    ```yaml
    containers:
      ...
      volumesMounts:
      - name: db-config
        mountPath: /etc/nginx/nginx.conf # 路径写到文件
        subPath: etc/nginx/nginx.conf # 与下面 volumes[0].configMap.items[0].path 一致
        #subPath: nginx.conf # 与下面保持一致
    volumes:
    - name: db-config
      configMap:
        name: my-config
        items:
        - key: "key1"
          path: etc/nginx/nginx.conf # subPath 路径
          #path: nginx.conf
    ```

- 配置热更新

  - 使用默认数据卷加载 configmap 的方式，配置会热更新，更新周期是：更新时间+缓存时间

  - subPath：不会更新

    解决方式：不使用 subPath，而是使用上面的默认方式：创建一个文件夹，将单个文件挂载到该文件夹中；删除原本需的被挂载文件，并在相同位置创建一个链接到该单个文件的软链接（删除与创建软链接操作可放在pod生命周期的初始化容器阶段）

  - 变量形式：如果 pod 中的一个变量从 configmap 或 secret 中得到，同样也不会更新

- 不可变的 configmap 和 secret

  对于一些敏感的配置文件，在上线后是不允许修改的。可以在编辑 configmap ，添加 immutable: true 来禁止修改

  ```shell
  kubectl edit cm cm名
  ```

  ```yaml
  apiVersion: v1
  kind: ConfigMap
  immutable: true # 与 kind 等同一级
  ```

  






## 高级调度

### 污点、容忍

- 污点（Taint）

  - 影响

    - NoSchedule：如果即将部署的 新pod 无法容忍该污点，则不会部署到该节点上
    - NoExecute：已经部署了的pod，如果存在无法容忍该节点上污点的，也会移出该节点

  - 指令

    ```shell
    # 打上污点
    kubectl taint node node名 key=value:影响
    kubectl taint node k8s-node1 memory=pressure:NoExecute
    
    # 移除污点（加 - 号即可移出污点）
    kubectl taint node node名 key=value:影响-
    
    # 查看污点
    kubectl describe node node名
    ```

- 容忍（Toleration）

  - Equal：容忍污点 key 、 value 都匹配的节点
  - Exists：容忍污点key匹配的节点

  ```yaml
  kind: Deployment
  spec:
    template:
      spec:
        tolerations:
        - effect: NoExecute
          key: "memory"
          operator: "Equal" # Exists 不需要配置 value
          value: "pressure"
          # 配置了下面属性值的 pod，虽然可以容忍 memory=pressure:NoExecute 这个污点，
          #          也会在 60 秒后被驱逐出该节点
          # 而没有配置下面属性的 pod，可以一直待在该节点
          tolerationSeconds: 60
        containers:
  ```



### 亲和力

- 节点亲和力

  ```yaml
  kind: Pod
  spec:
    affinity:
      nodeAffinity: # 节点亲和力
        requiredDuringSchedulingIgnoredDuringExecution: # 必须满足
          nodeSelectorTerms:
          - matchExpressions:
            - key: topology.kubernetes.io/zone
              operator: In
              values:
              - antarctica-east1
              - antarctica-west1
        preferredDuringSchedulingIgnoredDuringExecution: # 满足则优先
        - weight: 1 # 取值范围 1~100
          preference:
            matchExpressions:
            - key: another-node-label-key
              operator: In
              values:
              - another-node-label-value
    containers:
  ```

  - 种类
    - requiredDuringSchedulingIgnoredDuringExecution：硬亲和力
    - preferredDuringSchedulingIgnoredDuringExecution：软亲和力
  - 条件
    - In
    - NotIn
    - Exists
    - DoesNotExist
    - Gt
    - Lt

- pod亲和力

  ```yaml
  kind: Pod
  spec:
    affinity:
      podAffinity: # pod 亲和力
        requiredDuringSchedulingIgnoredDuringExecution: # 硬亲和力
        - labelSelector:
            matchExpressions: # 必须与含有 security=S1 标签的pod部署到一起
            - key: security
              operator: In
              values:
              - S1
          topologyKey: topology.kubernetes.io/zone # 节点上必须有该标签key
          
      # 该反亲和性规则表示：尽量不将 pod 部署到 【节点上存在标签key为topology.kubernetes.io/zone；节点内
      #       存在包含 security=S2 标签的pod】的节点上
      podAntiAffinity: # pod 反亲和力
        preferredDuringSchedulingIgnoredDuringExecution: # 软亲和力
        - weight: 100
          podAffinityTerm:
            labelSelector:
              matchExpressions: # 尽量不与含有 security=S2 标签的pod部署到一起
              - key: security
                operator: In
                values:
                - S2
            topologyKey: topology.kubernetes.io/zone
    containers:
  ```

  



### 身份与权限

















































































