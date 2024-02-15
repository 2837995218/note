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
      order by d.dept_id /* ※ */
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





### 私信交换机



### 惰性队列

### MQ集群









# ELK

![ELFK](D:\picture\typora\java2\ElasticStack\ELFK.png)

## ElasticSearch

### 概念

#### docker部署

- 还需要部署kibana容器，因此需要先创建一个网络，让es和kibana容器互联。

  ```shell
  docker network create es-net
  ```

- 拉去elasticsearch镜像

  ```shell
  docker pull elasticsearch:7.12.1
  ```

- 创建挂载目录

  ```shell
  mkdir -p /var/docker-volume/es
  ```

- 转移数据

  ```shell
  docker run -d \
  	--name es \ 
  	-e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
  	-e "discovery.type=single-node" \
  	--privileged \
  	--network es-net \
  	elasticsearch:7.12.1
  	
  docker cp es:/usr/share/elasticsearch/data /var/docker-volume/es
  docker cp es:/usr/share/elasticsearch/plugins /var/docker-volume/es
  
  docker stop es
  docker rm es
  ```

- 运行es容器

  ```shell
  docker run -d \
  	--name es \
  	-e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
  	-e "discovery.type=single-node" \
  	-v /var/docker-volume/es/data:/usr/share/elasticsearch/data \
  	-v /var/docker-volume/es/plugins:/usr/share/elasticsearch/plugins \
  	--privileged \
  	--network es-net \
  	-p 9200:9200 \
  	-p 9300:9300 \
  	elasticsearch:7.12.1
  ```

- 安装运行kibana

  ```shell
  docker run -d \
  	--name kibana \
  	-e ELASTICSEARCH_HOSTS=http://192.168.36.132:9200 \
  	--network=es-net \
  	-p 5601:5601 \
  	kibana:7.12.1
  ```



#### ik分词器

- 安装ik分词器

  ```shell
  # 进入容器内部
  docker exec -it es /bin/bash
  # 在线下载并安装
  ./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.12.1/elasticsearch-analysis-ik-7.12.1.zip
  # 退出容器
  exit
  # 重启容器
  docker restart es
  ```

- 拓展词库

  - 修改ik分词器目录下的config目录中的IkAnalyzer.cfg.xml文件

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
    <properties>
    	<comment>IK Analyzer 扩展配置</comment>
    	<!--用户可以在这里配置自己的扩展字典 -->
    	<entry key="ext_dict"></entry>
    	 <!--用户可以在这里配置自己的扩展停止词字典-->
    	<entry key="ext_stopwords"></entry>
        
    	<!--用户可以在这里配置远程扩展字典 -->
    	<!-- <entry key="remote_ext_dict">words_location</entry> -->
    	<!--用户可以在这里配置远程扩展停止词字典-->
    	<!-- <entry key="remote_ext_stopwords">words_location</entry> -->
    </properties>
    ```





### CRUD

#### devTool

##### 操作索引库

- mapping
  - mapping是对索引库中文档的约束，常见的mapping属性包括
    - type：字段数据类型，常用的简单类型有：
      - 字符串：text（可分次的文本）、keyword（精确值，例如：品牌、国家、ip地址）
      - 数值：long、integer、short、byte、double、float
      - 布尔：boolean
      - 日期：date
      - 对象：object
    - index：是否创建倒排索引，默认为true
    - analyzer：使用哪种分词器
      - ik插件：ik_smart、ik_max_word
    - properties：该字段的子属性
  
- 创建索引库

  - ES中通过Restful请求操作索引库、文档。请求内容用DSL语句来表示

    ```json
    PUT /索引库名称
    {
        "mappings": {
            "properties": {
                "字段名1": {
                    "type": "text",
                    "analyzer": "ik_smart"
                },
                "字段名2": {
                    "type": "keyword",
                    "analyzer": "false",
                    "properties": {
                        "子字段": {
                            "type": "keyword"
                        }
                    }
                },
                // ... 略
            }
        }
    }
    ```

- 查询索引库

  - GET /索引库名

- 删除索引库

  - DELETE /索引库名

- 修改索引库：只能添加新字段

  ```json
  PUT /索引库名/_mapping
  {
      "properties": {
          "新字段名": {
              "type": "integer"
          }
      }
  }
  ```



##### 文档操作

- 新增文档

  ```json
  POST /索引库名/_doc/文档id
  {
      "字段1": "值1",
      "字段2": "值2",
      "字段3": {
          "子属性1": "值3",
          "子属性2": "值4"
      },
      // ...
  }
  ```

- 查询文档：GET /索引库/_doc/文档id

- 删除文档：DELETE  /索引库/_doc/文档id

- 修改文档：

  - 全文修改，会删除旧文档，添加新文档，使用PUT请求

  - 局部修改

    ```json
    PUT /索引库名/_update/文档id
    {
        "doc": {
            "字段名": "新的值"
        }
    }
    ```






#### java

##### 前置配置

- 引入es的 RestHighLevelClient 依赖

  ```xml
  <dependency>
      <groupId>org.elasticsearch.client</groupId>
      <artifactId>elasticsearch-rest-high-level-client</artifactId>
  </dependency>
  ```

- 因为springboot默认配置的es版本是7.6.2，所有需要覆盖默认的es版本

  ```xml
  <properties>
      <elasticsearch.version>7.12.1</elasticsearch.version>
  </properties>
  ```

- 初始化RestHighLevelClient

  ```java
  @SpringBootTest
  public class TestApp {
      private RestHighLevelClient client;
  
      @BeforeEach
      void setUp() {
          this.client = new RestHighLevelClient(RestClient.builder(
              HttpHost.create("http://192.168.36.132:9200")));
      }
  
      @AfterEach
      void tearDown() throws IOException {
          this.client.close();
      }
  }
  ```



##### 操作索引库

- 创建索引库

  ```java
  CreateIndexRequest request = new CreateIndexRequest("anime");
  request.source(ESConstant.animeIndex, XContentType.JSON);
  client.indices().create(request, RequestOptions.DEFAULT);
  ```

- 删除索引库

  ```java
  DeleteIndexRequest request = new DeleteIndexRequest("anime");
  client.indices().delete(request, RequestOptions.DEFAULT );
  ```

- 判断索引库是否存在

  ```java
  GetIndexRequest request = new GetIndexRequest("anime");
  boolean exists = client.indices().exists(request, RequestOptions.DEFAULT);
  System.out.println(exists);
  ```



##### 操作文档

- 新增文档

  ```java
  @Test
  public void addDoc() throws IOException {
      Anime anime = animeService.getById(1);
      AnimeDoc animeDoc = new AnimeDoc(anime);
      IndexRequest request = new IndexRequest("anime").id("1");
      request.source(JSONUtil.toJsonStr(animeDoc), XContentType.JSON);
      client.index(request, RequestOptions.DEFAULT);
  }
  ```

- 查询文档

  ```java
  GetRequest getRequest = new GetRequest("anime").id("1");
  GetResponse response = client.get(getRequest, RequestOptions.DEFAULT);
  AnimeDoc animeDoc = JSONUtil.toBean(response.getSourceAsString(), AnimeDoc.class);
  System.out.println(animeDoc);
  ```

- 删除文档

  ```java
  DeleteRequest request = new DeleteRequest("anime").id("1");
  client.delete(request, RequestOptions.DEFAULT);
  ```

- 修改文档

  - 全量更新：与新增文档类似

  - 局部更新

    ```java
    @Test
    public void updateDoc() throws IOException {
        UpdateRequest request = new UpdateRequest("anime", "1");
        request.doc("state", 10, "type", "日常");
        client.update(request, RequestOptions.DEFAULT);
    }
    ```

- 批量新增

  ```java
  BulkRequest request = new BulkRequest();
  List<Anime> list = animeService.list();
  for (Anime anime : list) {
      AnimeDoc animeDoc = new AnimeDoc(anime);
      IndexRequest indexRequest = new IndexRequest("anime")
          .id(anime.getId()+"")
          .source(JSONUtil.toJsonStr(animeDoc), XContentType.JSON);
      request.add(indexRequest);
  }
  client.bulk(request, RequestOptions.DEFAULT);
  ```





### DSL

#### devTool

##### 基本语法

- DSL Query基本语法

  ```json
  GET /索引库名/_search
  {
      "query": {
          "查询类型": {
              "查询条件": "条件值"
          }
      }
  }
  ```

- match查询

  ```json
  GET /索引库名/_search
  {
      "query": {
          "match": {
              "字段": "内容"
          }
      }
  }
  ```

- multi_match查询

  ```json
  GET /索引库名/_search
  {
      "query": {
          "multi_match": {
              "query": "内容",
              "field": ["字段1", "字段2"]
          }
      }
  }
  ```

- 精确查询

  - term：根据词条精确值查询

    ```json
    GET /索引库名/_search
    {
        "query": {
            "term": {
                "字段": {
                    "value": "值"
                }
            }
        }
    }
    ```

  - range：根据值的范围查询

    ```json
    GET /索引库名/_search
    {
        "query": {
            "range": {
                "字段": {
                    "gte": 最小值,
                    "lte": 最大值
                }
            }
        }
    }
    ```

  - geo_bounding_box：根据geo_point值落在某个矩形范围的所有文档

    ```json
    GET /索引库名/_search
    {
        "query": {
            "geo_bounding_box": {
                "字段": {
                    "top_left": {
                        "lat": 31.1,
                        "lon": 121.5
                    },
                    "bottom_right": {
                        "lat": 30.9,
                        "lon": 121.7
                    }
                }
            }
        }
    }
    ```

  - geo_distance：根据geo_point搜索附近指定范围内的所有文档

    ```json
    GET /索引库名/_search
    {
        "query": {
            "geo_distance": {
                "distance": "15km",
                "字段": "3"
            }
        }
    }
    ```

    

##### 复合查询

- 相关性算分

  - TF
    - TF（词条频率）= 词条出现次数 / 文档中词条总数
  - TF-IDF：elasticsearch5.0之前，分数会随着词频的增加而越来越大
    - IDF（逆文档频率）= log(文档总数 / 包含词条的文档总数)
    - score = ∑^n^~i~ TF  * IDF
  - BM25：elasticsearch5.0后，分数会随着词频增大而增大，但曲线最终会趋于水平

- Function Score Query

  - 修改文档的相关性算分
  - 算分函数：functions
    - filter：定义对哪些文档进行处理
    - weight：给一个常量值，作为函数结果
    - field_value_factor：用文档中的某个字段值作为函数结果
    - random_score：随机生成一个值，作为函数结果
    - scropt_score：自定义计算公式，公式结果作为函数结果
  - 加权模式：boost_mode
    - multiply：function score和query score相乘，默认
    - replace：用function score 替换 query score
    - 其他：sum、avg、max、min

  ```json
  GET /索引库名/_search
  {
      "query": {
          "function_score": {
              "query": {"match": {"字段": "内容"}},
              "functions": [
                  {
                      "filter": {"term": {"字段": "内容"}},
                      "weight": 10
                  }
              ],
              "boost_mode": "multiply"
          }
      }
  }
  ```

- Boolean Query

  - 布尔查询是一个或多个查询子句的组合，子查询的组合方式有：
    - must：必须匹配每个字符串，参与算分
    - should：选择性匹配，参与算分
    - must_not：必须不匹配，不参与算分
    - filter：必须匹配，不参与算分

  ```json
  GET /索引库名/_search
  {
      "query": {
          "bool": {
              "must": [{"term": {"字段": "内容"}}],
              "should": [{"term": {"字段1": "内容2"}, {"term": {"字段2": "内容2"}],
              "must_not": [{"range": {"price": {"let": 500}}}],
              "filter": [
                  {
                      "geo_distance": {
                          "distance": "10km",
                          "location": {"lat": 31.21, "lon": 121.5}
                      }
                  }
              ]
          }
      }
  }
  ```

  

##### 结果处理

- 结果排序

  - 默认根据相关度算分来排序，可排序的字段类型有：keyword、数值类型、地理坐标类型、日期类型
    - asc：升序
    - desc：降序

  ```json
  GET /索引库名/_search
  {
      "query": {
          "match_all": {}
      },
      "sort": [
          {"字段": "desc"},
          {
              "_geo_distance": {
                  "location": {"lat": "纬度", "lon": "经度"},
                  "order": "asc",
                  "unit": "km"
              }
          }
      ]
  }
  ```

- 分页

  - 简单分页
    - from：分页开始的位置
    - size：每页文档条数
    - 优点：支持随机翻页
    - 缺点：深度分页问题，默认查询上限是10000
  - after search
    - 先排序，再记录该页最后条数据，下次查询从该范围查询
    - 优点：利用多次查询，可查出10000以上数据
    - 缺点：只能向后查询，不支持随机翻页
  - scroll：记录快照

- 高亮显示

  - 默认匹配的字段要与需要高亮的字段相同

  ```json
  GET /索引库名/_search
  {
      "query": {
          "match": {
              "字段 ": "内容"
          }
      },
      "highlight": {
          "fields": {
              "字段": {
                  //"reuqire_field_match": "false" //字段可以不相同
                  "pre_tags": "<em>",
                  "post_tags": "</em>"
              }
          }
      }
  }
  ```

  



#### java

##### 简单查询

```java
@Test
public void matchAll() throws IOException {
    SearchRequest request = new SearchRequest("anime");

    BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery();
    boolQueryBuilder.must(QueryBuilders.matchQuery("all", "日常搞笑"));
    boolQueryBuilder.must(QueryBuilders.termQuery("all", "校园"));
    request.source().query(boolQueryBuilder);
    request.source().from(0).size(5);

    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    SearchHits hits = response.getHits();
    System.out.println("共搜索到 " + hits.getTotalHits().value + " 条数据");
    for (SearchHit hit : hits.getHits()) {
        System.out.println(hit.getSourceAsString());
        // 获得一个map集合，可用于替换hit中的信息
        System.out.println(hit.getHighlightFields());
    }
}
```



##### 组合查询

```java
FunctionScoreQueryBuilder functionScoreQueryBuilder = QueryBuilders.functionScoreQuery(
    QueryBuilders.boolQuery()
    .must(QueryBuilders.matchQuery("all", "日常搞笑"))
    .must(QueryBuilders.termQuery("all", "校园")),
    new FunctionScoreQueryBuilder.FilterFunctionBuilder[]{
        new FunctionScoreQueryBuilder.FilterFunctionBuilder(
            QueryBuilders.termQuery("type", "搞笑"),
            ScoreFunctionBuilders.weightFactorFunction(10))
    }
);
```





### 数据聚合

- 聚合的分类
  - Bucket：桶聚合
  - Metric：度量聚合
  - Pipeline：管道聚合

- devTool

  ```json
  GET /索引库名/_search
  {
      //"query": {}, // 可加限定条件，指定对部分数据进行聚合，默认对所有文档聚合 
      "size": 0, // 分页中每页展示数据
      "aggs": {
          "自定义聚合名": {
              "terms": {
                  "field": "聚合字段",
                  //"order": {"_count": "asc"}, // 升序排列，默认降序
                  "size": 10 // 展示数据条数
              }
          }
      }
  }
  ```

  ```json
  GET /索引库名/_search
  {
      "size": 0,
      "aggs": {
          "自定义聚合名1": {
              "terms": {"field": "聚合字段", "size": 10},
              // 聚合的嵌套
              "order": {"自定义聚合名2.avg": "asc"},
              "aggs": {
                  "自定义聚合名2": {
                      "stats": { // 聚合类型，这里的stats可以计算min、max、avg等
                          "field": "聚合字段"
                      }
                  }
              }
          }
      }
  }
  ```

- java

  ```java
  SearchRequest request = new SearchRequest("anime");
  request.source().size(0);
  request.source().aggregation(AggregationBuilders.stats("stateAgg").field("state"));
  ```

  



### 自动补全

#### 分词器

- 拼音分词器

- 自定义分词器：在创建索引库的时候设置分词器

  ```json
  PUT /test
  {
    "settings": {
      "analysis": {
        "analyzer": {
          "my_analyzer": {
            "tokenizer": "ik_max_word",
            "filter": "py"
          }
        },
        "filter": {
          "py": {
            "type": "pinyin",
            "keep_full_pinyin": false,
            "keep_joined_full_pinyin": true,
            "keep_original": true,
            "limit_first_letter_length": 16,
            "remove_duplicated_term": true,
            "none_chinese_pinyin_tokenize": false
          }
        }
      }
    },
    "mappings": {
      "properties": {
        "overview": {
          "type": "text",
          "analyzer": "my_analyzer",
          "search_analyzer": "ik_smart"
        }
      }
    }
  }
  ```

  ```json
  POST /test/_analyze
  {
    "text": "你好世界",
    "analyzer": "my_analyzer"
  }
  ```

  

#### 自动补全

- 自动补全对字段的要求
  - 类型是completion类型
  - 字段值是多词条的数组

- 查询语法

  ```json
  GET /索引库名/_search
  {
      "suggest": {
          "自定义名": {
              "text": "关键字",
              "completion": {
                  "field": "字段",
                  "skip_duplicates": true, // 跳过重复的
                  "size": 10 // 显示条数
              }
          }
      }
  }
  ```





### 数据同步

- mysql与es间数据的同步

- 解决

  - 同步调用

  - 异步通知：使用消息中间键

  - 监听binlog





### 集群

- 海量数据存储问题：
  - 将索引库从逻辑上拆分为n个分片，存储到多个节点
- 单点故障问题：
  - 将分片数据备份，并放在其他节点上
- 集群状态的监控
  - cerebro：https://github.com/lmenezes/cerebro/releases



#### 创建集群

- docker-compose编排文件

  ```shell
  version: '2.2'
  services:
    es01:
      image: elasticsearch:7.12.1
      container_name: es01
      environment:
        - node.name=es01
        - cluster.name=es-docker-cluster # 集群名称相同后，会自动组装为一个集群
        - discovery.seed_hosts=es02,es03 # 已配置容器互连后，地址简写为容器名
        - cluster.initial_master_nodes=es01,es02,es03 # 初始化的主节点
        - bootstrap.memory_lock=true
        - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      ulimits:
        memlock:
          soft: -1
          hard: -1
      volumes:
        - ./data/node0:/usr/share/elasticsearch/data
        - ./logs/node0:/usr/share/elasticsearch/logs
      ports:
        - 9200:9200
      networks:
        - elastic
    es02:
      image: docker.elastic.co/elasticsearch/elasticsearch:7.4.0
      container_name: es02
      environment:
        - node.name=es02
        - cluster.name=es-docker-cluster
        - discovery.seed_hosts=es01,es03
        - cluster.initial_master_nodes=es01,es02,es03
        - bootstrap.memory_lock=true
        - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      ulimits:
        memlock:
          soft: -1
          hard: -1
      volumes:
        - ./data/node1:/usr/share/elasticsearch/data
        - ./logs/node1:/usr/share/elasticsearch/logs
      networks:
        - elastic
    es03:
      image: docker.elastic.co/elasticsearch/elasticsearch:7.4.0
      container_name: es03
      environment:
        - node.name=es03
        - cluster.name=es-docker-cluster
        - discovery.seed_hosts=es01,es02
        - cluster.initial_master_nodes=es01,es02,es03
        - bootstrap.memory_lock=true
        - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      ulimits:
        memlock:
          soft: -1
          hard: -1
      volumes:
        - ./logs/node2:/usr/share/elasticsearch/data
        - ./logs/node2:/usr/share/elasticsearch/logs
      networks:
        - elastic
        
  networks:
    elastic:
      driver: bridge
  ```

- 修改linux的系统权限

  - 编辑文件：vi /etc/sysctl.conf
  - 添加内容：vm.max_map_count=262144
  - 让配置生效：sysctl -p

- 设置分片与备份

  ```json
  PUT /索引库名
  {
      "settings": {
          "number_of_shards": 3, // 分片信息
          "number_of_replicas": 1 // 副本数量
      },
      "mappings": {"properties": { 。。。}}
  }
  ```

  

#### 集群职责

| 节点类型        | 配置参数    | 默认值 | 节点职责                                                     |
| --------------- | ----------- | ------ | ------------------------------------------------------------ |
| master eligible | node.master | true   | 备选主节点：主节点可以管理和记录集群状态、决定分片<br/>在哪个节点、处理创建和删除索引库的请求 |
| data            | node.data   | true   | 数据节点：存储数据、搜索、聚合、CRUD                         |
| ingest          | node.ingest | true   | 数据存储之前的预处理                                         |
| coordinating    |             |        | 所有es节点：路由请求，合并结果                               |

- 脑裂问题
  - 解决：成为主节点需要选票超过（eligible节点数 + 1）/ 2 才能当选，因此eligible节点最好为奇数
- coordinating节点
  - 新增是，通过算法将请求发送到一个data节点
  - 查询时，将请求路由到各个data节点，各个节点查完后，汇总到该coordinating节点
- 故障转移
  - 集群中的master节点会监控各个节点状态，如果有节点宕机
    - 启用其他节点上的备份数据
    - 将宕机节点的分片数据迁移到其他节点，使数据处于健康状态
  - 健康状态：如果有节点宕机，可利用备份数据进行恢复













## Logstash

## Kibana









# Kubernetes













































































