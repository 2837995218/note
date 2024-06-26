# [EasyExcel](https://easyexcel.opensource.alibaba.com/docs/current/)

## 导入

### 快速入门

- 引入依赖

  ```xml
  <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>easyexcel</artifactId>
      <version>3.1.4</version>
  </dependency>
  ```

- Excel 表格

  | id   | 姓名 | 性别 | 生日          |
  | ---- | ---- | ---- | ------------- |
  | 1    | 张三 | 男   | 2002年3月12日 |

- 读取 Excel 文件

  ```java
  File file  = new File("C:\\Users\\yoga\\Desktop\\test.xlsx");
  List<Map<Integer, Object>> list = EasyExcel
      .read(file)
      .sheet() // 指定工作表。可填参数：表名(sheet1)、索引(0)、空参(第一张表)
      .head(映射模型.class) // 指定映射模型
      .headRowNumber(5) // 指定表头行数，即从（指定行数+1）行开始读取数据，默认0
      .doReadSync();
  
  Map<Integer, Object> map = list.get(0);
  System.out.println(map); // {0=1, 1=张三, 2=男, 3=2002年3月12日}
  ```

- 一次性读取可能会导致内存溢出



### 模型映射

- 关系映射
  - `@ExcelProperty`：默认**属性在映射类中的次序**与**map的key值**对应
  - `@ExcelProperty(value = "列名")`：指定对应列名
  - `@ExcelProperty(index = 0)`：指定对应key值

- 自定义解析

  - `@ExcelProperty(converter = 自定义格式转换.class)`

- **截止到 3.3.1 版本，转换器Converter 仍不支持基本数据类型，因此建议所有基本类型都用其对应的包装类**

  ```java
  @ExcelProperty(value = "性别", converter = GenderConverter.class)
  private Boolean isMan;
  ```

- 使用

  ```java
  List<映射模型类> list = EasyExcel
      .read(file)
      .head(映射模型.class) // 指定映射模型
      .sheet()
      .doReadSync();
  ```

  ```java
  @Data
  @ToString
  public class Person {
      @ExcelProperty("id")
      private Integer id;
  
      @ExcelProperty("姓名")
      private String name;
  
      @ExcelProperty(value = "性别", converter = GenderConverter.class)
      private Boolean isMan;
  
      @ExcelProperty(value = "生日", converter = LocalDateConverter.class)
      private LocalDate birthday;
  }
  ```

  ```java
  public class LocalDateConverter implements Converter<LocalDate> {
      @Override
      public LocalDate convertToJavaData(ReadCellData cellData, ExcelContentProperty contentProperty, GlobalConfiguration globalConfiguration) throws Exception {
  
          String stringValue = cellData.getStringValue();
          return LocalDate.parse(stringValue, DateTimeFormatter.ofPattern("yyyy年MM月dd日"));
      }
  }
  ```




### 监听器(处理器)

> 事实上，为了代码的美观，可以将数据处理部分放在监听器（ReadListener）中。
>
> 最后调用 void doRead() 方法做读取

- 使用

  ```java
  EasyExcel
      .read(file)
      .head(映射模型.class)
      .registerReadListener(new ReadListener<映射模型>() { ... })
      .sheet()
      .doRead();
  ```

- `ReadListener<T>` 接口

  ```java
  public interface ReadListener<T> extends Listener {
      // 读取一条数据后出现异常调用的方法
      default void onException(Exception exception, AnalysisContext context) throws Exception {
          throw exception;
      }
  
      default void invokeHead(Map<Integer, ReadCellData<?>> headMap, AnalysisContext context) {
      }
  
      // 读取到每条数据后调用的方法
      void invoke(T var1, AnalysisContext var2);
  
      default void extra(CellExtra extra, AnalysisContext context) {
      }
  
      // 所有数据执行完后调用的方法
      void doAfterAllAnalysed(AnalysisContext var1);
  
      default boolean hasNext(AnalysisContext context) {
          return true;
      }
  }
  ```

- 异常获取

  ```java
  @Override
  public void onException(Exception exception, AnalysisContext context) throws Exception {
      if (exception instanceof ExcelDataConvertException) {
          ExcelDataConvertException e = (ExcelDataConvertException) exception;
          e.getColumnIndex();
          e.getRowIndex();
          e.getCellData();
          e.getExcelContentProperty();
      }
  }
  ```

- 优点：

  - 可以制定分批读取，从而避免内存溢出
  
  - **访问者模式**
    - 一方面可以将处理逻辑独立出来，使代码更美观
    - 另一方面可以添加多个ReadListener，实现只遍历一遍excel数据，达到多处理器**实时处理**（读取一条处理一条）的效果
  




### 上传文件读取

```java
@PostMapping("/upload")
public List<InfoModel> upload(MultipartFile file) {
    InputStream inputStream = file.getInputStream();
    return EasyExcel.read(inputStream).head(InfoModel.class).doReadSync();
}
```





### 读取多个sheet

- 同一个sheet不可重复读取

- 读取所有sheet

  ```java
  // 不指定sheet (.sheet())，即读取所有
  EasyExcel
      .read(...)
      .head(...)
      .registerReadListener(...)
      .doReadAll(); // 或 .doReadAllSync();
  ```

- 读取多个sheet

  ```java
  try(ExcelReader excelReader = EasyExcel.read(inputStream).build()) {
      ReadSheet readSheet = EasyExcel.readSheet()
          .head(Person.class)
          .registerReadListener(...)
          .build();
      ReadSheet readSheet2 = EasyExcel.readSheet()....build();
      ...
          
      excelReader.read(readSheet, readSheet2, ...);
  }
  ```

  





## 导出

### 快速入门

- 到处方法

  ```java
  EasyExcel
      .write(导出目标)
      .sheet()
      .head(映射模型.class) // 指定映射模型
      .registerWriteHandler(new LongestMatchColumnWidthStyleStrategy())
      .doWrite(集合数据);
  ```

- 导出目标

  - String 文件路径
  - File 文件对象
  - OutStream 输出流

- 集合数据

  - `List<List<Object>>`：{{1, "张三", "男"}, ...}
  - `List<Map<Integer, Object>>`：{{0=1, 1="张三", 2="男"}, ...}
  - `List<Object>`：同一个类



### 重复多次写入

> 如果一次性的写入大量数据，需要一个数据量非常大的集合。这很可能导致**内存溢出**
>
> 另一方面，如果数据过多，一个 excel 的 sheet 无法放下，需要写到多个 sheet 中

- 同一类对象，重复多次写入到同一个sheet中

  ```java
  String fileName = "test.xlsx";
  
  // 这里 需要指定写用哪个class去写
  try (ExcelWriter excelWriter = EasyExcel.write(fileName, DemoData.class).build()) {
      // 这里注意 如果同一个sheet只要创建一次
      WriteSheet writeSheet = EasyExcel.writerSheet("模板").build();
      
      for (int i = 0; i < 5; i++) { // 模拟多次
          List<DemoData> data = data(); // 模拟获取数据
          excelWriter.write(data, writeSheet);
      }
  }
  ```

- 同一类对象，重复多次写入到**多个**sheet中

  ```java
  fileName = "test.xlsx";
  
  // 这里 需要指定写用哪个class去写
  try (ExcelWriter excelWriter = EasyExcel.write(fileName, DemoData.class).build()) {
      for (int i = 0; i < 5; i++) {
          // 每次都要创建writeSheet 这里注意必须指定sheetNo 而且sheetName必须不一样
          WriteSheet writeSheet = EasyExcel.writerSheet(i, "模板" + i).build();
          
          List<DemoData> data = data();
          excelWriter.write(data, writeSheet);
      }
  }
  ```

- **不同类对象**，重复多次写入到**多个**sheet中

  ```java
  fileName = "test.xlsx";
  // 这里只指定文件
  try (ExcelWriter excelWriter = EasyExcel.write(fileName).build()) {
      for (int i = 0; i < 5; i++) {
          // 实际上可以一直变
          WriteSheet writeSheet = EasyExcel.writerSheet(i, "模板" + i).head(DemoData.class).build();
          
          List<DemoData> data = data();
          excelWriter.write(data, writeSheet);
      }
  }
  ```



### 模型映射

- 关系映射

  - `@ExcelIgnore`：不导出该属性
  - 无注解、`@ExcelProperty`：默认**属性在映射类中的次序**与**map的key值**对应
  - `@ExcelProperty(value = "列名")`：指定对应列名
  - `@ExcelProperty(index = 0)`：指定对应key值
  - `@ExcelProperty(value = {"标签1", "标签2", ...})`

- 格式转化

  - `@DateTimeFormat(value = "yyyy年MM月dd日")`：截止目前(3.3.1)只支持`java.util.Date`

  - `@NumberFormat(value = "￥#.##%")`：**.**后几个**#**会被解析成几位小数，**.**前**#**无用。加**%**表示转成百分数

  - `@ExcelProperty(converter = 自定义格式转换.class)`

    ```java
    public class LocalDateConverter implements Converter<LocalDate> {
        @Override
        public WriteCellData<?> convertToExcelData(LocalDate value, ExcelContentProperty contentProperty, GlobalConfiguration globalConfiguration) throws Exception {
            String str = value.format(DateTimeFormatter.ofPattern("yyyy年MM月dd日"));
            return new WriteCellData<LocalDate>(str);
        }
    }
    ```

- 多value值

  ```java
  public class Person {
      @ExcelProperty(value = {"基本信息", "姓名"})
      private String name;
      
      @ExcelProperty(value = {"基本信息", "性别"})
      private String gender;
      
      @ExcelProperty("积分")
      @NumberFormat("#.##")
      private Double score;
      
      @ExcelProperty(value = {"基本信息", "生日"})
      @DateTimeFormat(value = "yyyy年MM月dd日")
      private Date birthday;
  }
  ```

  <table>
      <tr><td colspan=2>基本信息</td><td rowspan=2>积分</td><td>基本信息</td></tr>
      <tr><td>姓名</td><td>性别</td><td>生日</td></tr>
      <tr><td>张三</td><td>男</td><td>78.33</td><td>2003年5月1日</td></tr>
  </table>

- 排除部分属性导出

  - `@ExcelIgnore`
  - EasyExcel 静态方法
  
    - `.excludeColumnFieldNames(Arrays.asList("password", ...))`：排除
    - `.includeColumnFieldNames(Arrays.asList("username", ...))`：包括
  



### 行高列宽

- 手动设置（加在映射模型类上、属性上）

  - `@ContentRowHeight(value = 10)`
  - `@HeadRowHeight(value = 15)`
  - `@ColumnWidth(value 25)`

- 自动列宽（依旧可能存在问题，建议与注解一起使用）

  ```java
  EasyExcel
      .write(file)
      .sheet()
      .registerWriteHandler(new LongestMatchColumnWidthStyleStrategy()) // 长度匹配内容
      .registerWriteHandler(new AbstractColumnWidthStyleStrategy(){ ... }) // 也可以自己实现该接口
      .doWrite(data);
  ```



### 跨行列合并

- 静态合并

  - 使用注解
    - `@OnceAbsoluteMerge(firstRowIndex = 5, lastRowIndex = 6, firstColumnIndex = 1, lastColumnIndex = 2)`
      - 加载模型类上
      - 表示：从1列5行 到 2列6行 合并
    - `@ContentLoopMerge(eachRow=2, columnExtend=2)`
      - 添加在映射模型类的属性上
      - 仅对数据部分生效
      - eachRow=2：数据部分每两行进行合并
      - columnExtend=2：数据部分跨两列

  - 使用默认策略

    ```java
    EasyExcel
        .write(file)
        .sheet()
        .registerWriteHandler(new LoopMergeStrategy(eachRow: 2, columnIndex: 0))
        .registerWriteHandler(new OnceAbsoluteMergeStrategy(FR: 0, LR: 1, FC: 4, LC: 4))
        .doWrite(data);
    ```

- 动态合并（自定义策略 `registerWriteHandler()`）

  - `CellWriteHandlerContext`：该类对象存储了对应单元格中的相关信息

  - 实现此接口并注册：`CellWriteHandler`（类似的接口：`RowWriteHandler`）

    ```java
    public interface CellWriteHandler extends WriteHandler {
        default void beforeCellCreate(CellWriteHandlerContext context) {
            this.beforeCellCreate(context.getXXX, context.getXXX, ...);
        }
        default void beforeCellCreate(
            WriteSheetHolder writeSheetHolder, 
            WriteTableHolder writeTableHolder, 
            Row row, 
            Head head, 
            Integer columnIndex, 
            Integer relativeRowIndex, 
            Boolean isHead) {
        }
    
    
        default void afterCellCreate(CellWriteHandlerContext context) {
            this.afterCellCreate(context.getXXX, context.getXXX, ...);
        }
        default void afterCellCreate(
            WriteSheetHolder writeSheetHolder, 
            WriteTableHolder writeTableHolder, 
            Cell cell, 
            Head head, 
            Integer relativeRowIndex, 
            Boolean isHead) {
        }
    
    
        default void afterCellDataConverted(CellWriteHandlerContext context) {
            WriteCellData<?> writeCellData = CollectionUtils.isNotEmpty(context.getCellDataList()) ? (WriteCellData)context.getCellDataList().get(0) : null;
            this.afterCellDataConverted(writeCellData, context.getXXX, context.getXXX, ...);
        }
        default void afterCellDataConverted(
            WriteSheetHolder writeSheetHolder, 
            WriteTableHolder writeTableHolder, 
            WriteCellData<?> cellData, 
            Cell cell, 
            Head head, 
            Integer relativeRowIndex, 
            Boolean isHead) {
        }
    
    
        default void afterCellDispose(CellWriteHandlerContext context) {
            this.afterCellDispose(context.getXXX, context.getXXX, ...);
        }
        default void afterCellDispose(
            WriteSheetHolder writeSheetHolder, 
            WriteTableHolder writeTableHolder, 
            List<WriteCellData<?>> cellDataList, 
            Cell cell, 
            Head head, 
            Integer relativeRowIndex, 
            Boolean isHead) {
        }
    }
    ```

  - 实现类

    ```java
    import com.alibaba.excel.metadata.Head;
    import com.alibaba.excel.metadata.data.WriteCellData;
    import com.alibaba.excel.write.handler.CellWriteHandler;
    import com.alibaba.excel.write.metadata.holder.WriteSheetHolder;
    import com.alibaba.excel.write.metadata.holder.WriteTableHolder;
    import org.apache.poi.ss.usermodel.Cell;
    import org.apache.poi.ss.usermodel.CellType;
    import org.apache.poi.ss.usermodel.Row;
    import org.apache.poi.ss.usermodel.Sheet;
    import org.apache.poi.ss.util.CellRangeAddress;
    
    import java.util.List;
    
    public class MergeCellWriteHandler implements CellWriteHandler {
        private final List<String> fieldsNeedMerge;
    
        public MergeCellWriteHandler(String... fieldsNeedMerge) {
            this.fieldsNeedMerge = Arrays.asList(fieldsNeedMerge);
        }
    
        @Override
        public void afterCellDispose(WriteSheetHolder writeSheetHolder, WriteTableHolder writeTableHolder, List<WriteCellData<?>> cellDataList, Cell cell, Head head, Integer relativeRowIndex, Boolean isHead) {
            // 如果该行属于表头行，则不处理
            if (isHead) return;
    
            // 如果该行属于数据区第一行，则不处理。relativeRowIndex：是对应数据的相对行数
            if (relativeRowIndex == 0) return;
    
            // 如果该列不需要合并，则不处理
            if (fieldsNeedMerge == null || head == null || !fieldsNeedMerge.contains(head.getFieldName())) return;
    
            // 获得本单元格和本列上一行的单元格，并判断两格内值是否相同，从而判断是否需要合并
            Sheet sheet = cell.getSheet();
            int rowIndexCurrent = cell.getRowIndex();
            int rowIndexPrev = rowIndexCurrent-1;
            Row rowPrev = sheet.getRow(rowIndexPrev);
            int columnIndex = cell.getColumnIndex();
            Cell prevCell = rowPrev.getCell(columnIndex);
    
            // 判断两格内容是否相同。（只做了简单的数据校验，可能存在问题，如：null值，其他类型的CellType）
            Object currentCellValue = cell.getCellType() == CellType.STRING ?
                    cell.getStringCellValue() : cell.getNumericCellValue();
            Object prevCellValue = prevCell.getCellType() == CellType.STRING ?
                    prevCell.getStringCellValue() : prevCell.getNumericCellValue();
            boolean flag = currentCellValue.equals(prevCellValue);
            // 不相同，则不处理
            if (!flag) return;
    
            // 获取一个表中已经合并的区域（所有的大单元格）
                // 理论上只要获取需要合并列的所有大单元格，但没有这个api，
                // 因为单元格还可以跨列合并，跨列的大单元格不能确定属于哪一列
            List<CellRangeAddress> mergedRegions = sheet.getMergedRegions();
            // 上一个单元格是否与之前的单元格形成大单元格
            boolean merged = false;
            for (int i = 0; i < mergedRegions.size(); i++) {
                CellRangeAddress cellAddresses = mergedRegions.get(i);
                // 判断上一个单元格是否在合并的大单元格内
                if (cellAddresses.isInRange(rowIndexPrev, columnIndex)) {
                    // 在，说明现单元格需要和上一个大单元格合并
                    // 需要先取消上一个大单元格
                    sheet.removeMergedRegion(i);
                    cellAddresses.setLastRow(rowIndexCurrent);
                    // 再将重新划分好的新大单元格设置回去
                    sheet.addMergedRegion(cellAddresses);
                    merged = true;
                    break;
                }
            }
            // 如果上一单元格没有与之前的单元格形成大单元格，则只需将上一个单元格和现单元格合并
            if (!merged) {
                CellRangeAddress addresses = new CellRangeAddress(rowIndexPrev, rowIndexCurrent, columnIndex, columnIndex);
                sheet.addMergedRegion(addresses);
            }
        }
    }
    ```




### 导出图片

- **File类型**、**byte[]类型**、**InputStream类型**、**URL类型** 的图片均可直接导出到单元格

  ```java
  public class ExportModel {
      @ExcelProperty("File类型")
      private File fileImage;
      
      @ExcelProperty("byte[]类型")
      private byte[] byteImage;
      
      @ExcelProperty("InputStream类型")
      private InputStream inputStreamImage;
      
      @ExcelProperty("URL类型")
      private URL urlImage;
  }
  ```

- 获得方式 `import com.alibaba.excel.util.FileUtils;`

  - File类型：`new File("D:\\logo.png")`
  - byte[]类型：`FileUtils.readFileToByteArray(new File("D:\\logo.png"))`
  - InputStream类型：`FileUtils.openInputStream(new File("D:\\loge.png"))`
  - URL类型：`new URL("https://easyexcel.opensource.alibaba.com/img/logo.png")`

- **字符串类型**：使用自带的Converter

  ```java
  @ExcelProperty(value = "字符串地址", converter = StringImageConverter.class)
  private String pathStr; // "D:\\loge.png"
  ```

- 其他类型（如：Base64）：自定义Converter



### 导出超链接、备注等

- 创建映射类

  ```java
  @Data
  public class OneClass {
      @ExcelIgnore
      private String data1;
      @ExcelProperty("为这组数据设置超链接")
      private WriteCellData<String> data1Cell;
  
      @ExcelIgnore
      private Double data2;
      @ExcelProperty("为这组数据设置备注")
      private WriteCellData<String> data2Cell;
  
      public OneClass(String data1, Double data2) {
          this.data1 = data1;
          this.data2 = data2;
      }
  }
  ```

- 转化：将其他类型 转成 `WriteCellData<String>`

  ```java
  List<OneClass> list = 
      List.of(new OneClass("这是一个url", 5.20), new OneClass("这是另一个url", 13.14));
  
  list = list.stream()
      .peek(oneClass -> {
          // 此处转换
      })
      .collect(Collectors.toList());
  
  // 导出到 Excel
  ```

- 超链接：`HyperlinkData`

  ```java
  oneClass -> {
      WriteCellData<String> data1Cell = new WriteCellData<>(oneClass.getData1());
      HyperlinkData hyperlinkData = new HyperlinkData();
      hyperlinkData.setAddress("https://github.com/alibaba/easyexcel");
      hyperlinkData.setHyperlinkType(HyperlinkData.HyperlinkType.URL);
      data1Cell.setHyperlinkData(hyperlinkData);
      oneClass.setData1Cell(data1Cell);
  }
  ```

  - 常用超链接类型
    - URL：地址
    - FILE：本地文件
    - EMAIL：邮箱

- 备注：`CommentData`

  ```java
  oneClass -> {
      WriteCellData<String> data2Cell = new WriteCellData<>(String.format("%.2f", oneClass.getData2()));
      CommentData commentData = new CommentData();
      commentData.setAuthor("作者");
      commentData.setRichTextStringData(new RichTextStringData("备注内容"));
      
      // 备注的默认大小是按照单元格的大小 
      // 这里想调整到4个单元格那么大，所以向后、向下各额外占用了一个单元格
      commentData.setRelativeLastColumnIndex(1);
      commentData.setRelativeLastRowIndex(1);
      data2Cell.setCommentData(commentData);
      oneClass.setData2Cell(data2Cell);
  }
  ```

- [其他格式：](https://easyexcel.opensource.alibaba.com/docs/current/quickstart/write#超链接备注公式指定单个单元格的样式单个单元格多种样式)easyexcel官网查看



## 模板填充

### 快速入门

- Excel模版文件（占位符前面加**.**表示填充列表，否则只能填充一条数据）

  <table>
      <tr><td colspan=2>用户信息</td><td colspan=2>用户信息</td></tr>
      <tr><td>id</td><td>姓名</td><td>性别</td><td>生日</td></tr>
      <tr><td>{.id}</td><td>{.name}</td><td>{.gender}</td><td>{.birthday}</td></tr>
  </table>

- java代码

  ```java
  InputStream is = EasyExcelTest.class.getClassLoader().getResourceAsStream("template.xlsx");
  
  EasyExcel
      .write("D:\\export.xlsx")
      .withTemplate(is)
      .sheet()
      .doFill(obj);
  ```

- obj类型（填充列表）

  - `List<Map<String, Object>>`：map集合的键与模版中占位字段一致
  - `List<实体类>`：属性名与模版中占位字段一致









# Crawler

## 快速入门

- 坐标导入

```xml
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.13</version>
</dependency>
```

- 基础案例

```java
public class CrawlerFirst {
    public static void main(String[] args) throws IOException {
        // 1. 打开浏览器 => 创建 HttpClient 对象
        CloseableHttpClient httpClient = HttpClients.createDefault();

        // 2. 输入网址 => 发起 Get 请求创建 HttpGet 对象
        HttpGet httpGet = new HttpGet("https://www.baidu.com");

        // 3. 发起请求，返回响应 => 使用 HttpClient 对象发起请求
        CloseableHttpResponse response = httpClient.execute(httpGet);

        // 4. 解析响应，获取数据
        HttpEntity entity = response.getEntity();
        String html = EntityUtils.toString(entity,"utf-8");
    }
}
```



## HttpClient

### 请求参数

- 参数在url中

```java
URIBuilder uriBuilder = new URIBuilder("http://www.baidu.com/s");
uriBuilder.setParameter("wd","crawler");
URI uri = uriBuilder.build();
HttpGet httpGet = new HttpGe(uri);
```

- 参数在请求体中

```java
HttpPost httpPost = new HttpPost("http://www.baidu.com/s");
//声明List集合，封装表单中的数据
List<NameValuePair> params = new ArrayList<nameValuePair>();
params.add(new BasicNameValuePair("wd","crawler"));
//创建表单的Entity对象
UrlEncodeFormEntity formEntity = new UrlEncodeFormEntity(params,"utf-8");
```



### 连接池

```java
public class HttpClientPoolTest {
    public static void main(String[] args) throws IOException {
        // 创建连接池管理器
        PoolingHttpClientConnectionManager cm = new PoolingHttpClientConnectionManager();
        // 设置最大连接数
        cm.setMaxTotal(10);
        // 设置每个主机的最大连接数
        cm.setDefaultMaxPerRoute(5);

        doGet(cm);
        doGet(cm);
    }

    private static void doGet(PoolingHttpClientConnectionManager cm) throws IOException {
        // 从连接池中获取 HttpClient 对象
        CloseableHttpClient httpClient = HttpClients.custom().setConnectionManager(cm).build();
        HttpGet httpGet = new HttpGet("https://www.baidu.com");
        CloseableHttpResponse response = httpClient.execute(httpGet);
        response.close();
    }
}
```



### 配置参数

```java
RequestConfig requestConfig = RequestConfig.custom().setConnectTimeout(1000)    //设置创建连接的最长时间
    .setConnectionRequestTimeout(500)   //设置获取连接的最长时间
    .setSocketTimeout(10 * 1000)  //设置数据传输的最长时间
    .build();

HttpGet httpGet = new HttpGet("http://www.baidu.com/s");
httpGet.setConfig(requestConfig);
```





## Jsoup

### 坐标

```xml
<dependency>
    <groupId>org.jsoup</groupId>
    <artifactId>jsoup</artifactId>
    <version>1.14.3</version>
</dependency>

<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.6</version>
</dependency>

<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.8.1</version>
</dependency>
```



### 基础案例

- 解析 url

```java
// 解析url地址
Document document = Jsoup.parse(new URL("http://www.baidu.com"), 1000);
// 使用标签选择器，获取 title 标签中的内容
String title = document.getElementsByTag("title").first().text();
```

- 解析字符串


```java
// 使用工具类读取文件，获取字符串
String html = FileUtils.readFileToString(new File("文件位置"), "编码格式");
Document doc = Jsoup.parse(html);
```

- 文件的解析

```java
Document doc = Jsoup.parse(new File("文件位置"), "编码格式");
```



### Dom

- 获取元素

  - 根据 id 查询元素：getElementById()

  - 根据 标签 获取元素：getElementByTag()

  - 根据 class 获取元素：getElementByClass()

  - 根据 属性 获取元素：
    - getElementByAttribute()
    - getElementByAttributeValue()

- 元素中获取数据

  - 获取 id ：element.id()
  - 获取 className ：
    - element.className()
    - element.classNames()：将className中的内容按空格做拆分
  - 获取 属性 ：
    - element.attr("attributeKey")
    - element.attributes()



### 选择器

#### 获取元素

- tagname：通过标签查找元素，例如：span
- #id：通过 id 查找元素，例如：#city_bj
- .class：通过 class 名称查找数据，例如：.class_a
- [attribute]：通过 属性 查找元素，例如：[abc]
- [attr=value]：通过 属性值 查找元素，例如：[class=s_name]

```java
doc.select("[class=s_name]");
```

#### 组合选择器

- el#id：元素+id，例如：h3#city_bj
- el.class：元素+class，例如：li.class_a
- el[attr]：元素+属性名，例如：span[abc]
- ancestor child：查找某个元素下子元素，例如：.city_con li 查找"city_con"下的所有 "li"
- parent > child：查找某个父元素下的直接子元素，例如：.city > ul > li
- parent > *：查找某个父元素下所有的直接子元素





## WebMagic

![webmagic](D:\picture\typora\java\image-20220717091809167.png)

### 入门程序

```xml
<dependency>
    <groupId>us.codecraft</groupId>
    <artifactId>webmagic-core</artifactId>
    <version>0.7.5</version>
</dependency>
<dependency>
    <groupId>us.codecraft</groupId>
    <artifactId>webmagic-extension</artifactId>
    <version>0.7.5</version>
</dependency>
```

```java
public class JobProcessor implements PageProcessor {
    // 解析页面
    public void process(Page page) {
        // 解析返回的数据 page 。并且把解析的结果放到 ResultItems 中
        page.putField("div",page.getHtml().css("div.mt h2").all());
    }

    private Site site = Site.me()
        .setCharset(String charset);
    public Site getSite() {
        return site;
    }

    // 主函数，执行爬虫
    public static void main(String[] args) {
        Spider.create(new JobProcessor())
                .addUrl("地址") // 设置获取数据的页面
                .run(); // 执行爬虫
    }
}
```



### PageProcessor

- 抽取元素（链式）
  - XPath
    - //title[@lang] ： 获取所有拥有名为 lang 的属性的 title 元素
    - //div[@id=div_01]/ul/li/a
  - 正则表达式
  - css 选择器

- 获取连接
  - `page.addTargetRequests(page.getHtml().css("").links().all());`

- Site

| 方法                     | 说明                              |
| ------------------------ | --------------------------------- |
| setCharset(String)       | 设置编码                          |
| setUserAgent(String)     | 设置UserAgent                     |
| setTimeOut(int)          | 设置超时时间                      |
| setRetryTime(int)        | 设置重试时间                      |
| setCycleRetryTimes(int)  | 设置重试次数                      |
| addCookie(String,String) | 添加一条cookie                    |
| setDomain(String)        | 设置域名，设置后，setCookie才生效 |
| addHeader(String,String) | 添加一条Header                    |
| setHttpProxy(HttpHost)   | 设置代理                          |



### Spider

- 是爬虫启动的入口。

  ```java
  Spider.create(new JobProcessor())
      .addUrl("地址") // 设置获取数据的页面
      .run(); // 执行爬虫
  ```

- Spider的其他组件（Downloader、Scheduler、Pipeline）都可以通过set方法来进行设置

  | 方法                        | 说明                                             |
  | --------------------------- | ------------------------------------------------ |
  | create（PageProcessor）     | 创建Spider                                       |
  | addUrl（String ...）        | 添加初始化的URL                                  |
  | 添加初始化的URL             | 开启的线程数                                     |
  | run（）                     | 启动，会阻塞当前线程执行                         |
  | start（）/runAsync（）      | 异步启动，当前线程继续执行                       |
  | addPipeline（Pipeline）     | 添加一个Pipeline，一个Spider可设置多个Pipeline   |
  | setSchedule（Schedule）     | 设置Schedule，一个Spider只能设置一个Schedule     |
  | setDownloader（Downloader） | 设置Downloader，一个Spider只能设置一个Downloader |
  | get（String）               | 同步调用，并直接取得结果                         |
  | getAll（String ...）        | 同步调用，并直接取得一堆结果                     |



### 定时任务

- spring内置的Spring Task，Spring3.0加入

- @EnableSchedule
- 使用@Scheduled注解，属性如下
  - cron：cron表达式，指定任务在特定时间执行
  - fixedDelay/fixedDelayString：上一次任务执行完成后多久再执行，单位ms
  - fixedRate/fixedRateString：按一定的频率执行任务，单位ms
  - initialDelay/initialDelayString：延迟多久再第一次执行任务，单位ms
  - zone：时区，默认当前时区
- cron表达式
  - 由七个子表达式组成，描述个别细节的时间段
    - Second：0-59
    - Minutes：0-59
    - Hours：0-23
    - Day-of-Month：1-31
    - Month：0-11（或JAN、FEB、MAR......）
    - Day-of-Week：1-7（或SUN、MON、TUE......）（1是周日）
    - Year（可选字段）
  - 例如 "0 0 12 ? * WED "：在每个星期三下午 12:00 执行
    - /：表示每，如在Minutes字段上的 "3/20"，表示从3分开始，每20分钟
    - \*：代表所有
    - ?：表示每月（或每周）的某一天
    - L：用于每月，或每周，表示未每月的最后一天，或每个月的最后星期几



### 网页去重

- simhash算法



### 代理

```java
// 创建下载器Downloader
HttpClientDownloader downloader = new HttpClientDownloader();
// 给下载器设置代理服务信息
downloader.setProxyProvider(SimpleProxProvider.from(new Proxy("代理ip",端口)));
// 设置下载器
Spider.create(new P())
    .addUrl("")
    .setDownloader(downloader)
    .run();
```





## 正则

### 基础

#### 转义符

- 元字符-转义符 

  - 需要用到转义符号的字符有：

    ```java
    . * ( ) $ / \ ? [ ] ^ { }
    ```



#### 字符匹配符

| 符号 | 含义                     | 示例       | 解释                                                |
| ---- | ------------------------ | ---------- | --------------------------------------------------- |
| [ ]  | 可接收的字符列表         | [efgh]     | 表示e、f、g、h中的任意1个字符                       |
| [^ ] | 不可接收的字符列表       | [^abc\]    | 除a、b、c之外的任意1个字符，<br/>包括数字和特殊字符 |
| -    | 连字符                   | A-Z        | 任意单个大写字母                                    |
| .    | 匹配除\n以为的任何字符   | a..b       | 以a开头，b结尾，<br/>中间包括2个任意字符的字符串    |
| \d   | 匹配单个数字字符         | \d{3}(\d)? | 包含3或4个数字的字符串                              |
| \w   | 匹配单个数字或字母的字符 | \d{3}\w{4} | 3个数字开头长度为7的数字字母字符串                  |
| \D   | 匹配单个非数字字符       |            |                                                     |
| \W   | 匹配单个非数字非字母字符 |            |                                                     |
| \s   | 空白字符：空格、制表符等 |            |                                                     |
| \S   | 非空白字符               |            |                                                     |



#### 选择匹配符

- | ：(H|h)ello：匹配 Hello、hello



#### 限定符

| 符号  | 指定字符串重复n次 | 示例    | 说明                    |
| ----- | ----------------- | ------- | ----------------------- |
| *     | n无要求           | (abc)*  | 仅包含任意个abc的字符串 |
| +     | n>=1              | m+abc   | 以至少1个m开头，后接abc |
| ?     | n=0或1            | colou?r | 匹配color和colour       |
| {i}   | n=i               | 23{3}   | 匹配2333                |
| {i,j} | i<n<j             |         |                         |



#### 定位符

| 符号 | 含义                   | 示例          | 说明                                        |
| ---- | ---------------------- | ------------- | ------------------------------------------- |
| ^    | 指定起始字符           | ^[0-9]+[a-z]* | 以至少一个数字开头，<br/>后接任意个小写字母 |
| $    | 指定结束字符           |               |                                             |
| \b   | 匹配目标字符串的边界   | 233\b         | 233233：匹配后一个233<br/>23323：匹配不到   |
| \B   | 匹配目标字符串的非边界 | 233\B         | 2333233233：匹配前两个233                   |





### 拓展

#### 分组

- (pattern)：如 (/d{2}){2}（前面括号里的是一组，一共就一组）
- (?\<name> pattern)：为组命名，可在mather.group(String name)时使用
- (?:pattern)：非捕获分组组
- (?=pattern)：非捕获分组，（ win(?=10|11) 会匹配win10和win11中的win，不会匹配win8中的win）
- (?!pattern)：非捕获分组，（ win(?!10|11) 不会匹配win10和win11中的win，会匹配win8中的win）
- (?<=pattern)：非捕获分组，（ (?<=10|11)win 会匹配10win和11win中的win，不会匹配8win中的win）
- (?<!pattern)：非捕获分组，（ (?<!10|11)win 不会匹配10win和11win中的win，会匹配8win中的win）



#### 非贪婪匹配

- 默认使用贪婪匹配
  - {非常}+："这里非常非常非常好看" -> "非常非常非常"
  - 23+："23333" -> "23333"
- 使用非贪婪匹配
  - {非常}+?："这里非常非常非常好看" -> "非常"、"非常"、"非常"
  - 23+?："23333" -> "23"



#### 反向引用

- 格式：(pattern)\组号
  - 匹配多个连续相同的字：(\w+)\1
  - 回文：(\w+)(\w+)\2\1

