# Git

## 常用命令

| 命令名称                             | 作用                         |
| ------------------------------------ | ---------------------------- |
| git config --global user.name 用户名 | 设置用户签名                 |
| git config --global user.email 邮箱  | 设置用户签名                 |
| git config --global -l               | 查看配置                     |
| git init                             | 初始化本地库                 |
| git status                           | 查看本地库状态               |
| git add 文件名                       | 添加到暂存区                 |
| git commit -m "日志信息" 文件名      | 提交到本地库                 |
| git reflog                           | 查看历史记录                 |
| git reset --hard 版本号              | 版本穿梭                     |
| git branch 分支名                    | 创建分支                     |
| git branch -v                        | 查看分支                     |
| git checkout 分支名                  | 切换分支                     |
| git merge 分支名                     | 把指定的分支合并到当前分支上 |

- 合并冲突后，添加到暂存区，再提交本地库，提交时不能带文件名



| 命令                      | 作用             |
| ------------------------- | ---------------- |
| git remote -v             | 查看远程库       |
| git remote add 别名 地址  | 添加远程库       |
| git remote rm 别名        | 删除远程库       |
| git push 别名/地址 分支名 | 添加分支到远程库 |
| git pull 别名/地址 分支名 | 从远程库拉取     |
| git clone 地址            | 克隆代码         |









# Html



## html

### 标签

- 排版标签
  - \<h1>：标题
  - \<p>：段落
  - \<br>：换行
  - \<hr>：一条水平线
- 文本格式化标签
  - \<b>、\<strong>：加粗
  - \<u>、\<ins>：下划线
  - \<i>、\<em>：倾斜
  - \<s>、\<del>：删除
- 媒体标签
  - \<img>：图片标签
  - \<audio>：音频标签
    - 目前仅支持：Mp3、Wav、Ogg
    - autopaly：自动播放（部分浏览器不允许）
    - loop：循环播放
    - controls：显示播放的控件
    - muted：静音
  - \<video>：视频标签
    - 目前仅支持：Mp4、WebM、Ogg
    - 属性与\<audio>标签相似
- 链接标签：\<a>
  - href：地址，#表示不跳转
  - target：
    - _self：默认值，在当前页面跳转
    - _blank：新页面
- 列表标签
  - \<ul>：无序列表
  - \<ol>：有序列表
  - \<dl>：自定义列表
    - \<dt>：主题
    - \<dd>：主题下的内容（内容展示默认缩进）
  - 属性list-style：none：可清除列表前的图标
- 表格标签：\<table>

  - \<caption>：表名

  - \<tr>：行
    - \<th>：表头单元格
    - \<td>：单元格

  - 结构标签
    - \<thead>：表格头部
    - \<tbody>：表格主体
    - \<tfoot>：表格底部

  - 合并单元格（单元格属性）
    - rowspan：跨行合并，值为合并单元格个数
    - colspan：跨列合并，值为合并单元格个数
- 表单标签

  - \<form>：表单域
    - action：提交的地址


  - \<input>

    - type属性
      - text：单行文本框
      - password：密码框
      - radio：单选框
      - checkbox：多选框
      - file：文件选择
      - date：日期
      - submit：提交按钮
      - reset：重置按钮
      - button：普通按钮

    - placeholder：占位文本
    - value：设置值
    - name：用于 设置参数名 和 选择框分组
    - checked：默认选中
    - multiple：可选择多个文件

  - \<button>：按钮标签

    - type：submit、reset、button

  - \<select>：下拉选择

    - \<option>：每一项
      - selected：默认选中

  - \<textarea>：文本域标签

    - cols：规定了文本域内的可见宽度
    - rows：规定了文本域内的可见行数

  - \<laber>：用于绑定内容与表单标签的关系

    ```html
    性别：
    <!--两种方式-->
    <input type="radio" name="gender" id="man"><label for="man">男</label>
    <label><input type="radio" name="gender" id="women">女</label>
    ```

- 语义化标签

  - 没有语义的标签：
    - \<div>：独占一行
    - \<span>：一行显示多个
  - 有语义的标签：
    - \<header>：网页头部
    - \<nav>：网页导航
    - \<footer>：网页底部
    - \<aside>：网页侧边栏
    - \<article>：网页文章
    - \<section>：网页文章中的段落



### H5标签

- IE 8 兼容 Html5

  html5shiv.js 是谷歌编写的，可解决 ie8 兼容 html5 **部分问题**的 js 脚本

  ```html
  <!-- 让 IE 浏览器处于一个最优的渲染模式 -->
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  
  <!-- 针对一些国产的 双核 浏览器，让浏览器优先使用 webkit 内核去渲染网页
       另一个内核是 ie 的 Trident 内核，进行支付和交易时可能用到 -->
  <meta name="renderer" content="webkit">
  
  <!-- ie 8 可以解析下面注释 -->
  <!--[if lt ie 9]>
  <script src="./html5shiv.js"></script>
  <![endif]-->
  ```

- 布局标签

  - header：整个页面，或者部分区域的头部

  - footer：整个页面，或者部分区域的底部

  - nav：导航

  - article：文章、帖子、评论、杂志、新闻、博客等

  - section：页面中的某段文字，或者文章中的某段文字（里面通常会包含标题）

  - aside：侧边栏

- 状态标签

  - meter 标签：三段式状态

    ```html
    <span>模拟手机电量：</span>
    <meter max="100" min="0" value="80" low="10" height="20" optimum="90"></meter>
    ```

  - progress 标签

    ```html
    <span>模拟进度条：</span>
    <progress max="100" value="20"></progress>
    ```

- 列表标签

  - datalist

    ```html
    <input type="text" list="mydata">
    <datalist id="mydata">
        <option value="上海">上海</option>
        <option value="北京">北京</option>
        <option value="深圳">深圳</option>
    </datalist>
    ```

  - details、summary

    ```html
    <details>
        <summary>王太太整天唠叨个不停，可她有一个月说话最少，是哪一个月？</summary>
        <p>二月</p>
    </details>
    ```

- 文本标签

  - ruby：可用于拼音 <ruby><span>标</span><rt>biāo</rt></ruby> <ruby><span>注</span><rt>zhù</rt></ruby>

    ```html
    <ruby>
        <span>魑</span>
        <rt>chī</rt>
    </ruby>
    ```

  - mark：用于标注一段文字中的<mark>重点</mark>，类似于 \<em>

- 表单中标签相关属性

  - novalidate：表单 form 属性，加上该属性的表单，提交时不进行验证

  ```html
  <input type="text" placeholder="请输入账号" required autofocus autocomplete="on"
         pattern="\w{6}">
  ```

  - placeholder：占位符，可用于提示
  - required：加了该属性的 input、textarea 必须填写，否则无法提交
  - disabled：禁用该元素
  - autofocus：自动获取焦点
  - autocomplete：自动提示以前填写过的内容
  - pattern：文本校验，无法校验 textarea，若无required属性，则即使为空也能提交

- input 新增 type 类型

  - email：进行邮箱格式校验
  - url：进行网址格式校验
  - number：进行数据格式校验，可添加属性 step（步长）、max（最大值）、min（最小值）
  - search：提供清空功能
  - tel：手机浏览器填写此input，会自动切换到 数字键盘
  - range：范围选择器，可添加属性 max、min
  - color：颜色选择器
  - date：日期选择器，类似的还有：month、week、datetime-local、time 等

- video、audio 标签

  属性有：

  - controls：显示控制模块

  - muted：默认静音

  - autoplay：自动播放（处于静音状态、高媒体参与度的网站、已有交互的网站 的视频/音频才能自动播放）

    媒体参与度查询: chrome://media-engagement

  - loop：循环播放

  - poster="./封面.png"：视频封面，audio标签没有该属性

  - preload="auto"：视频、音频预加载 （auto：自动加载；none：不加载；metadata：只加载视频基本信息，如时长等）

- 全局属性

  | 属性名          | 值         | 功能                                                 |
  | --------------- | ---------- | ---------------------------------------------------- |
  | contenteditable | true/false | 元素内容是否可编辑                                   |
  | draggable       | true/false | 元素是否可被拖动                                     |
  | hidden          |            | 隐藏元素                                             |
  | spellcheck      | true/false | 是否对元素进行拼写和语法检查，浏览器需要打开拼写检查 |
  | contextmenu     |            | 元素的上下文菜单，一般配合js使用                     |
  | data-*          | 数据值     | 用于存储页面的私有定制数据，一般配合js使用           |






## Css

### 基础

#### css的引入方式

- 内嵌式：css写在style标签中

  - style一般写在head标签中的title标签下

- 外联式：css写在一个单独的.css文件中

  - 需要通过link标签在网页中引入

  - ```html
    <link rel="stylesheet" href="./html.css"/>
    ```

- 行内式：css写在标签的style属性中

  - ```html
    <p style="color: yellow;">这是一段简介</p>
    ```

    

#### 基础选择器

- 标签选择器：`标签名  {css属性名: 属性值;}`
- 类选择器：`.类名 {css属性名: 属性值;}`
- 属性选择器：`[属性名] {css属性名: 属性值;}`
- 属性值选择器：`[属性名=属性值] {css属性名: 属性值;}`
- id选择器：`#id {css属性名: 属性值;}`
- 通配符选择器：`* {css属性名: 属性值;}`



#### 基础属性

- 字体样式
  - 字体大小：font-size，默认字号是16
  - 字体粗细：font-weight，取值 数值：100-900，正常：normal 400，加粗：bold 700
  - 字体样式：font-style，正常：normal，倾斜：italic
  - 字体类型：font-family，`font-family: 微软雅黑, 黑体, sans-serif;`
  - 字体类型：font复合属性，`font: style weight size family`（有序，style和weight可省略）
- 文本样式
  - 文本缩进：text-indent
    - 数字+px
    - 数字+em（1em = 当前标签的font-size的大小）

  - 文本水平对齐方式：text-align
    - left：左对齐
    - center：居中对齐
    - right：右对齐

  - 文本修饰：text-decoration
    - underline：下划线
    - line-through：删除线
    - overline：上划线
    - none：无装饰线，可用于去除a标签的下滑线

- 行样式
  - 行高：line-height
    - 数字+单位
    - 数字（当前标签的font-size的倍数）
    - font复合属性也可以设置行高：`font: style weight size/line-height family`


- 颜色
  - color、background-color
    - 关键词：red、green、yellow、blue
    - rgb表示法：rgb(r,g,b)：rgb(255,255,255)、rgb(200,0,180)
    - rgba表示法：rgb(r,g,b,a)：a表示透明度，取值范围0~1
    - 十六进制表示法：#000000、#ff0000，简写：#000、#f00
- 标签水平居中：`margin: 0 auto`



### 设计

#### 选择器拓展

- 后代选择器：`选择器1 选择器2 {属性: 值}`
- 子代选择器：`选择器1 > 选择器2 {属性: 值}`
- 并集选择器：`选择器1 , 选择器2 {属性: 值}`
- 交集选择器：`选择器1选择器2 {属性: 值}`
- 伪类选择器：`选择器1:状态 {属性: 值}`



#### 背景属性

- 背景颜色：background-color
  - 背景颜色默认值是透明的：rgb(0,0,0,0)
  - 背景颜色不会影响盒子的大小
- 背景图片：background-image
  - `background-image: url(图片的路径)`
  - 背景图片默认是在水平和垂直方向平铺的
  - 背景图片仅仅是指给盒子起到装饰效果，类似于背景颜色，不能撑开盒子
- 背景原点：background-origin
  - padding-box：默认值，从padding的左上角开始，平铺图片
  - content-box
  - border-box
  - -webkit-background-origin:text ：为**透明**文字添加背景蒙版

- 背景裁剪：background-clip
  - border-box：默认值，超过border以外的背景图、背景颜色不呈现
  - padding-box
  - content-box

- 背景平铺：background-repeat
  - repeat：（默认值）水平和垂直方向都平铺
  - no-repeat：不平铺
  - repeat-x：沿着水平方向（x轴）平铺
  - repeat-y：沿着垂直放行（y轴）平铺
- 背景缩放：background-size
  - background-size 宽度 高度
    - 数字+px
    - 百分比：相对于盒子
  - background-size 值
    - 宽
    - contain：缩放
    - cover：覆盖
- 背景位置：background-position
  - 方位名词
    - 水平方向：left、center、right
    - 垂直方向：top、center、bottom
  - 数字+单位
- 背景复合属性：`background：color url  repeat position / size origin clip`（部分有序）



#### 元素的显示模式

- 块
  - 独占一行
  - 宽度默认是父元素的宽度，高度由内容撑开
  - 可设置宽高
  - 代表标签：div、p、h系、ul、li、dl、dt、dd、form、header、nav、footer等
- 行内
  - 一行可以显示多个
  - 宽度和高度默认由内容撑开
  - 不可以设置宽高
  - 代表标签：a、span、b、u、i、s、strong、ins、em、del等
- 行内块
  - 一行可以显示多个
  - 可以设置宽高
  - 代表标签：input、textarea、button、select等
- 模式切换
  - 转换为块：display：block
  - 转换为行内块：display：inline-block
  - 转换为行内：display：inline



#### css特性

- 继承性：子元素默认继承父元素样式
  - 可继承的常见属性：文字控制属性都可以继承
  - 如果浏览器有默认样式，此时继承性依然存在，但不会使用继承的样式，如a标签的颜色，h系标签的字号
- 层叠性：同一优先级的，后配置的覆盖先配置的
- 优先级：
  - 继承 < 通配符选择器 < 标签选择器 < 类选择器 < 伪类选择器 < id选择器 < 行内样式 < !import
  - 权重叠加计算：复合选择器中比较 选择器的类型的权重



#### 盒子模型

- 内容：内容区域由 width 和 height 控制
- border：边框（会将盒子撑大）
  - 复合属性：border：width style color；
    - style：实线：solid；虚线：dashed；点线：dotted
- padding：内边距（会将盒子撑大）
- box-sizing：border-box；：自动设置内容的大小
- margin：外边距
  - 外边距折叠——合并现象
    - 两个盒子的margin会合并
    - 最终两者距离为margin的最大值

  - 外边距折叠——塌陷现象
    - 问题：互相嵌套的块级元素，子元素的margin会作用在父元素上
    - 解决方法：
      - 给父元素设置 border-top 或者 padding-top（分隔父子元素的margin-top）
      - 给父元素设置overflow：hidden
      - 转换成行内块元素
      - 设置浮动、定位
- 行内元素的 padding 和 margin 在垂直方向不生效，可以用 line-height

![盒子模型](D:\picture\typora\前端\盒子模型.png)



### 浮动

#### 伪元素

- 由css模拟的标签
- 种类
  - ::before：在父类内容的最前面添加一个伪元素
  - ::after：在父类内容的最后添加一个伪元素
- 注意
  - 必须设置content属性才能生效
  - 伪元素是行内元素



#### 浮动

- 浮动元素会脱离标准流，在标准流中不占位置
- 浮动元素比标准流高半个级别，可以覆盖标准流中的元素
- 下一个浮动元素会在上一个浮动元素后面左右浮动
- 浮动元素有特殊的显示效果



#### 清除浮动

- 清除浮动带来的影响

- 方法：

  - 直接加高度

  - 额外标签法

    - 在父元素内容的最后添加一个块级元素（class名一般取为clearfix）
    - 给添加的块级元素设置clear：both

  - 单伪元素清除法

    ```css
    .clearfix::after {
        content: "";
        display: block;
        clear: both;
        /*为了兼容性，可补充代码，在网页中看不到伪元素*/
        height: 0;
        visibility: hidden;
    }
    ```

  - 双伪元素清除法

    ```css
    /*用于解决塌陷问题*/
    .clearfix:before {
        content: "";
        display: table;
    }
    /*用于清除浮动*/
    .clearfix:after {
        clear: both;
    }
    ```

  - 给父元素设置overflow：hidden





### 定位

#### 使用步骤

- 设置定位方式（position属性）
  - 静态定位：static
  - 相对定位：relative
  - 绝对定位：absolute
  - 固定定位：fixed
- 设置偏移值
  - 偏移值设置分为两个方向，水平和垂直方向各使用一个
  - 选择原则一般是就近原则
  - left、right、top、bottom



#### 定位类型

- 静态定位：不改变位置（position：relative；）

- 相对定位：相对于原先的位置

  - position: relative;
  - 特点
    - 需要配合方位属性实现移动
    - 相对自己原来的位置移动
    - 在页面中占位置 -> 没有脱标

- 绝对定位：相对于**非静态定位**的父元素进行定位

  - position: absolute;

  - 特点

    - 脱标，不占位置
    - 就近找有定位的父级，如果逐级找不到就以浏览器的窗口进行定位
    - 具备行内块的特点，需要设置宽高

  - 绝对定位的盒子不能使用 margin：0 auto 来居中

    ```css
    /* 解决方法一：*/
    position: absolute;
    left: 50%;
    top: 50%;
    margin-left: -盒子宽的一半px;
    margin-top: -盒子高的一半px;
    /* 解决方法二：*/
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%,-50%);/*位移，宽高为盒子的一半*/
    ```

- 固定定位

  - position：fixed；
  - 特点：
    - 脱标，不占位置
    - 改变位置是参考浏览器窗口
    - 具备行内块的特点，需要设置宽高



#### 元素的层级关系

- 不同元素的层级关系
  - 标准流 < 浮动 < 定位
- 不同定位之间的层级关系
  - 相对、绝对、固定默认层级相同
  - 此时HTML书写在下面的元素层级更高，会覆盖上面的元素
  - 通过z-index可设置层级，默认层级为0



#### 装饰

- 基线：浏览器在解析处于一起的行内、行内块时，默认是当做文字按基线排列，可设置基线
  - 属性：vertical-align
  - 常见值：
    - baseline：默认，基线对齐
    - top：顶部对齐
    - middle：中部对齐
    - bottom：底部对齐
- 光标类型：设置鼠标光标在元素上时显示的效果
  - 属性：cursor
  - 常见值：
    - default：默认值，通常是箭头
    - pointer：提示可点击的图标
    - text：工字型
    - move：十字光标
- 边框圆角
  - 属性：border-fadius
  - 取值：数字+px、百分比
- 溢出部分显示效果
  - 属性：overflow
  - 常见值：
    - visiable：默认值，溢出部分可见
    - hidden：溢出部分影藏
    - scroll：无论是否溢出都显示滚动条
    - auto：根据是否溢出，自动显示或隐藏滚动条
- 元素本身的隐藏
  - visitibily：hidden（占位隐藏）
  - display：none（不占位隐藏）
- 元素整体透明度
  - 属性：opacity
  - 值：0~1
  - 整个标签连同内容全透明



### 开发

#### 精灵图

- 一张放了所需小图的大图

- 创建一个盒子，设置盒子的尺寸和小图尺寸相同
- 将精灵图设置为盒子的背景图片
- 修改背景图的位置



#### 盒子阴影

- 属性：box-shadow（复合属性）
- 常见取值：（有序）
  - h-shadow：必须，水平偏移量，允许负值
  - v-shadow：必须，垂直便宜量，允许负值
  - blur：可选，模糊度
  - spread：可选，阴影扩大尺寸
  - color：可选，阴影颜色
  - inset：可选，将阴影改为内部阴影（默认outset，该属性后 不能添加outset）
- <a href="https://getcssscan.com/css-box-shadow-examples?ref=producthunt">常用</a>
  - box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;
  - box-shadow: rgba(0, 0, 0, 0.25) 0px 54px 55px, rgba(0, 0, 0, 0.12) 0px -12px 30px, rgba(0, 0, 0, 0.12) 0px 4px 6px, rgba(0, 0, 0, 0.17) 0px 12px 13px, rgba(0, 0, 0, 0.09) 0px -3px 5px;




#### 过渡

- 属性：transition（复合属性）
- 常见取值：（有序）
  - 过渡属性：
    - all：所有属性
    - width：只过渡width属性
  - 过渡的时长：数字+s（秒）
- 注
  - 过渡多个属性和时长用逗号隔开
    - transition: width 0.5s,background-color 0.5s;
  - transition属性设置在不同状态中，效果不同
    - 给默认状态设置属性，鼠标移入移出都有过渡效果
    - 给hover状态设置属性，鼠标移入有，移出没有









# JavaScript



## ECMAScript

### ES6

#### 箭头函数

- 使用

  ```js
  // let fn = function(a, b) {return a+b}
  
  let fn = (a, b) => {return a+b}
  console.log(fn(1, 2))
  ```

- 特性

  - this 是静态的，this始终是执行函数声明时所在作用域下的 this 的值
  - 箭头函数不能作为构造器实例化对象
  - 不能使用 arguments 变量
  - 箭头函数的简写
    - 省略小括号：参数只有一个
    - 省略大括号：函数体只有一行，有return，return必须省略

- 使用

  ```js
  const arr = [1, 6, 9, 10, 11, 20, 100]
  //const result = arr.filter(function(item) {
  //    if(item % 2 === 0) {
  //        return true;
  //    }else{
  //        return false;
  //    }
  //})
  const result = arr.filter(item => item % 2 === 0)
  ```



#### 形参初始值

```js
function print(str = 'hello,world') {
    console.log(str)
}
print('你好，世界')
```

```js
function conn({host, username='root', passowrd, port='3306'}){
	// 假设下面的方法是连接数据库的方法
	connection(host, username, password, port);
}
conn({host: 'localhost', password= 'root'})
```



#### rest

- ES6 引入 rest 参数，用于获取函数的实参，用来代替 arguments

- 使用：必须放到参数的最后

  ```js
  //function data() {
  //    console.log(arguments) // __proto__: Object 对象
  //}
  function data(...args) {
      console.log(args); // Array数组
  }
  date('张三', '李四', '王五')
  ```



#### 扩展运算符

```js
function shopping() {
    console.log(arguments);
}
const customers = ['zhangsan', 'lisi', 'wangwu']

shopping(customers) // 接收到一个数组
shopping(...customers) // 接收到数组里的三个对象
```

- 其他使用场景
  - 数组的合并：const arr = [...arr1, ...arr2]
  - 数组的克隆：const copyArr = [...arr]
  - 伪数组转数组：let divs = [...document.querrySelectorAll('div')]





#### Symbol

- 新数据类型，表示独一无二的值，不能和其他值进行运算

- 创建方式

  ```js
  let s = Symbol();
  // console.log(s, typeof s) // symbol
  let s2 = Symbol("张三");
  let s3 = Symbol("张三");
  // console.log(s2 === s3) // false
  let s4 = Symbol.for("张三");
  let s5 = Symbol.for("张三");
  // console.log(s4 === s5) // true
  ```



#### 迭代器

- 使用迭代器

  ```js
  const customers = ['zhangsan', 'lisi', 'wangwu']
  
  // for(let c in customers)：c 是键或索引
  for(let c of customers) {
      console.log(c) // c 是值
  }
  ```

- 定义迭代器

  ```js
  const book = {
      name: '西游记',
      stus: ['唐僧', '猪悟能', '沙悟净', '孙悟空'],
      [Symbol.iterator]() {
          let index = 0;
          let _this = this;
          return {
              next: function() {
                  if(index < _this.stus.length) {
                      const result = {value: _this.stus[index], done: false};
                      index++;
                      return result;
                  }else {
                      return {value: undefined, done: true};
                  }
              }
          }
      }
  }
  ```



#### 生成器

- 使用方法

  ```js
  // yield是ES6的新关键字，使生成器函数执行暂停，yield关键字后面的表达式的值返回给生成器的调用者。
  function* gen() {
      console.log('111');
      let one = yield '这是第一部分';
      console.log('222');
      yield '这是第二部分';
  }
  let iterator = gen(); // 获得的是一个迭代器对象
  
  //for(let v of iterator){
  //    console.log(v);
  //}
  
  console.log(iterator.next());
  // next方法也可以传入实参，作为yield的返回值
  console.log(iterator.next("BBB"));
  console.log(iterator.next());
  ```

- 举例

  ```js
  function handle(data) {
      console.log("根据 "+data+" 执行操作")
  }
  function * gen(data) {
      handle(data)
      let data1 = yield first();
      handle(data1)
      let data2 = yield second();
      handle(data2)
  }
  let iterator = gen('初始数据');
  iterator.next()
  
  function first() {
      setTimeout(() => {
          let data = "第一步数据";
          iterator.next(data)
      }, 1000)
  }
  function second() {
      setTimeout(() => {
          let data = "第二步数据";
          iterator.next(data)
      }, 1000)
  }
  ```

  

#### Promise

- 快速使用

  ```js
  const p = new Promise((resolve, reject) => {
      setTimeout(() => {
          let data = "数据库返回的信息";
          //resolve(data); // 调用成功函数，p 的状态变为成功
          reject(data); // 调用失败函数，p的状态变为失败
      }, 100)
  })
  // 根据 p 的状态，若成功，调用前一个参数为value的函数，反之则调用后一个函数
  p.then(value => console.log("成功 "+value), reason => console.log("失败 "+reason))
  ```

- then方法的返回结果是Promise对象（下面简称为p）

  - 如果 then 方法参数中的函数 return 一个Promise对象，p的状态由该 Promise 的回调函数 resolve 和reject 决定
  - 如果 then 方法参数中的函数 return 一个非Promise对象，p的状态为成功
  - 如果 then 方法中抛出错误，p的状态为失败

  ```js
  new Promise(resolve => {resolve('第一步成功')})
      .then(value => {
          console.log(value) // 打印 “第一步成功”
          return new Promise((resolve, reject) => reject("第二步失败"))
      }, reason => console.log(reason))
  
      .then(value => console.log(value)) //虽然状态为失败，但不对失败进行处理
  
      .then(value => console.log(value), reason => {
          console.log(reason)// 依旧会打印 “第二步失败”，因为前面未对失败进行处理
          return false;
      })
      .catch(reason => console.log(reason))
  ```

- catch方法：失败时调用，与then类似，语法糖，省略第一个参数

- 发送ajax

  ```js
  function sendAjax(url) {
      return new Promise((resolve, reject) => {
          const xhr = new XMLHttpRequest()
          xhr.open("GET", url)
          xhr.send()
          xhr.onreadystatechange = () => {
              if (xhr.readyState === 4) {
                  if (xhr.status >= 200 && xhr.status < 300) {
                      resolve(xhr.response)
                  }else {
                      reject(xhr.status)
                  }
              }
          }
      })
  }
  sendAjax("http://39.101.75.72/weather/check?size=2")
  .then(value => console.log(value), reason => console.log("状态码码："+reason))
  ```

  



#### Set、Map

略



#### Class

```js
// 构造方法
function Phone(brand, price){
    this.brand = brand;
    this.price = price;
}

//添加方法
Phone.prototype.call = function() {console.log("打电话")}

//实例化对象
let huaWei = new Phone("华为", 6999);
huaWei.call();
```

```js
class Phone {
    constructor(brand, price) {
        this.brand = brand;
        this.price = price;
    }
    call() {
        console.log("打电话")
    }
}
let phone = new Phone("小米", 1999);
phone.call()


class SmartPhone extends Phone{
    constructor(brand, price, color, size) {
        super(brand, price);
        this.color = color
        this.size = size
    }
}

let smartPhone = new SmartPhone("华为", 5999, "blue", 5.5);
smartPhone.call()
```

```js
class Phone {
    get brand() {
        console.log('读取brand')
    }
    set brand(newBrand) {
        console.brand("设置brand为：" + newBrand)
    }
}
```



#### 模块化

- export

  - 分别暴露：export let name = "zhangsan"

    ```js
    export let name = 'zhangsan'
    export function f() {}
    ```

  - 统一暴露：export {name, ...}

    ```js
    let name = 'zhangsan'
    function f() {}
    exprot {name, f}
    ```

  - 默认暴露：

    ```js
    export default {
        name: 'zhangsan',
        change: function(){
            console.log("改变")
        }
    }
    ```

- import

  - 通用导入：import * as m1 form "../js/m1.js"
  - 结构赋值形式：
    - import {name, f as fun} from "../js/m1.js"
    - import {default as m3} from "../js/m3.js"
  - 简便导入：import m3 from "../js/m3.js"





### ES7

- includes

```js
const customers = ['zhangsan', 'lisi', 'wangwu']
console.log(customers.includes('zhangsan'))
```

- 幂运算

```js
console.log(2**10)
console.log(Math.pow(2, 10))
```





### ES8

#### async和await

- async

  ```js
  async function fun(data) {
      let result = await new Promise((resolve, reject) => reject(data))
          .catch(reason =>  new Promise((resolve, reject) => resolve(data)))
  }
  fun('abc').then(value => console.log("success:"+value), 
                  reason => console.log("fail:"+reason)) // success:abc
  ```

  - 方法内返回的不是一个Promise对象，则p的状态为成功

  - 方法内产生异常，则p的状态为失败

  - 方法内返回的是一个Promise对象，则p的状态与返回的Promise对象的状态保持一致

- await

  - await 必须写在 async 函数中
  - await 右侧的表达式一般为 promise 对象
  - await 返回的是 promise 成功的值
  - await 的 promise 失败了，就会抛出异常，需要通过 try..catch 捕获处理

  ```js
  async function fun(data) {
      let result = await new Promise((resolve, reject) => resolve(data))
          .then(value => new Promise((resolve, reject) => reject(value)))
          .catch(reason =>  new Promise((resolve, reject) => resolve(data)))
          .then(value => console.log("promise内部处理："+data))//
  
      console.log("被await接收："+result)
  }
  fun('abc')
  
  // 控制台输出
  promise内部处理：abc
  被await接收：undefined
  ```

- 发送Ajax

  ```js
  function sendAjax(url) {
      return new Promise((resolve, reject) => {
          const xhr = new XMLHttpRequest()
          xhr.open("GET", url)
          xhr.send()
          xhr.onreadystatechange = () => {
              if (xhr.readyState === 4) {
                  if (xhr.status >= 200 && xhr.status < 300) {
                      resolve(xhr.response)
                  }else {
                      reject(xhr.status)
                  }
              }
          }
      })
  }
  async function main() {
      let result = await sendAjax("http://39.101.75.72/weather/check?size=2");
      console.log(result)
  }
  ```




#### 对象方法

- Object.values()
  - 方法返回一个给定对象的所有可枚举属性值的数组
  - Object.keys()：获取对象的所有可枚举键
- Object.entries()
  - 方法返回一个给定对象自身可遍历的属性 [key, value] 的数组
  - const m = new Map(Object.entries(obj))
- Object.getOwnPropertyDescriptors()
  - 返回对象属性的描述对象





### ES9

#### rest升级

```js
function connect({host, port, ...user}) {
    console.log(user) // { username: 'root', password: 'root' }
}
connect({
    host: '127.0.0.1',
    port: '3306',
    username: 'root',
    password: 'root'
})
```

```js
const one = {first: 1}
const two = {second: 2}
const three = {three: 3}
const both = {...one, ...three}
const all = {...one, ...three, ...two} // 存在同一属性，后加入的覆盖先加入的
console.log(both) //{ second: 3, first: 1 }
console.log(all) //{ first: 1, second: 2 }
```



####         正则升级

- 命名捕获分组
- 反向断言
- dotAll模式





### ES10

- 字符串处理
  - trimStart()：处理头部空白
  - trimEnd()：处理尾部空白
- 数组方法
  - [1, 2, [3, [4, 5]]].flat(Number num)
    - 不传参：扁平化一次
    - 传递Number参数：扁平化具体次数
      - 传递 Infinity（无限）：全部展开
  - 映射：map()
    - [1, 2, 3].map(i => i**3)：所有数据变为其三次方
  - 映射并扁平：flatMap()
    - [1, 2, 3].flatMap(i => [i**3])





### ES11

#### 私有属性

- 定义类时，用 # 标注私有属性
- 私有属性无法在类的外部被直接调用

```js
class Persion {
    name;
    #age;
    constructor(name, age) {
        this.name = name
        this.#age = age
    }
}
```



#### Promise补充

- Promise.allSettled()：
  - 返回的是一个Promise对象
  - 状态：成功
  - 值：传入的所有promise的状态和值形成的对象
- Promise.all()：
  - 返回的是一个Promise对象
  - 状态：传入的所有promise都成功才为成功
  - 值：
    - 若状态为失败，则值为：所有失败的promise中的第一个promise值
    - 若状态为成功，则值为：所有promise的值

```js
let p1 = new Promise(resolve => resolve("p1成功"));
let p2 = new Promise((resolve, reject) => reject("p2失败"));
let p3 = new Promise((resolve, reject) => reject("p3失败"));
Promise.allSettled([p3, p2]).then(value => console.log(value))
Promise.all([p1, p3, p2]).then(value => console.log('成功'+value), 
                               reason => console.log("失败"+reason))
```



#### matchAll

- String.prototype.matchAll方法

```js
const regex = /<li>(.*?)<\/?li>/
const result = str.matchAll(regex)
```



#### 可选链操作符

```js
function main(config) {
    // config 或 config.db 为空时报错
    //const dbHost= config.db.host

    // 传统写法
    //const dbHost = config && config.db && config.db.host;

    const dbHost = config?.db?.host
    console.log(dbHost);
}
main({
    db: {
        host: "127.0.0.1"
    }
})
```



#### 动态import

- 静态引入

  ```js
  import * as module1 from './hello.js'
  ```

- 动态引入

  ```js
  window.onload = function() {
      // 返回的是一个Promise对象
      import('./hello.js').then(module1 => {
          
      })
  }
  ```

  

#### bigint

- 获得
  - let n = 521n;
  - let n = BigInt(521);
- 大数值运算
  - 不能用BigInt和Number进行运算
  - BigInt(13) \** BigInt(43)



#### globalThis

- 始终指向全局对象
  - 浏览器下是Window







## DOM

### 元素获取

```java
Element getElementById(String id);
Element[] getElementsByTagName(String tagname);
// 以下是html5新增的api
Element[] getElementsByClassName(String classname);
Element querySelector('选择器'); //返回指定选择器的第一个对象
Element[] querySelectorAll('选择器');
```

- 特殊元素
  - body：document.body
  - html：document.documentElement



### 事件基础

- 事件源：触发事件的元素对象
- 事件类型
  - onload：加载完毕
  - onclick：点击
  - onmouseover：鼠标经过，经过自身盒子和里面的子盒子都会触发
  - onmouseenter：鼠标经过，鼠标经过自身盒子才会触发
  - onmouseout：鼠标离开，包括自身和子盒
  - onmouseleave：鼠标离开，只有自身
  - onfocus：获得焦点
  - onblur：失去焦点
  - onmousemove：鼠标移动
  - onmouseup：鼠标弹起
  - onmousedown：鼠标按下
  - onscroll：滚动条滚动（区别于鼠标滚轮滚动）
  - onwheel：滚轮滚动

- 事件处理程序



### 元素的属性与属性值

- 属性

  - innerText（非标准）
    - 不识别里面的标签
    - 从起始位置到最终位置的内容，但它去除html标签，同时空格和换行也会去掉

  - innerHTML（W3C标准）
    - 识别里面的标签
    - 从起始位置到最终位置的内容，包括html标签，同时保留空格和换行

  - src、href、value、checked、selected、disabled

  - style：行内样式
  - className：类名称样式
  - H5规定自定义属性的属性名以data-开头
- 属性值

  - element.属性：可获得元素本身的属性
  - attribute：主要针对于自定义属性
    - element.getAttribute("属性")
    - element.setAttribute("属性"，"值")
    - element.removeAttribute("属性")
  - H5新增的获取自定义属性的方法
    - div.dataset：获取所有data-开头的属性（如div.dataset.index）
    - 如果自定义属性中有多个-连接的单词，在获取时，采取驼峰命名法
      - 属性：\<div class='data-last-name'>；获取：div.dataset['lastName']



### 节点操作

- 节点类型（属性：noteType）

  - 元素节点：1
  - 属性节点：2
  - 文本节点：3（包括文字、空格、换行）
- 查找

  - parentNode：直接父级

  - 子级

    - childNodes：直接子级

    - children：直接子元素

    - 单个子级
      - 节点
        - fistchild：第一个子元节点
        - lastchild：最后一个子节点
      - 元素（IE9以上）
        - firstElementChild：第一个子元素
        - lastElementChild：最后一个子元素

  - 同级

    - 节点

      - nextSibling：下一个同级节点

      - previousSibling：上一个同级节点

    - 元素（IE9以上）

      - nextElementSibling：下一个元素节点

      - previousElementSibling：上一个元素节点
- 添加

  - 创建元素节点：createElement("tagname")
  - 末尾追加节点：父级.appendChild(子级)
  - 添加到指定元素前：父级.insertBefore(子级, 指定元素)
- 删除

  - removeChild(元素)
- 复制

  - 复制：node.cloneNode(boolean)
  - 添加
  - 如果参数为空或false，表示浅拷贝，即只克隆复制节点本身，不克隆里面的子节点
- 三种元素创建
  - document.write()
    - 直接将内容写入页面的内容流，如果文档流已经执行完毕，此时调用该方法会导致页面重绘

  - element.innerHTML()
    - 是将内容写入某个DOM节点，不会导致页面重绘
    - 创建多个元素时，直接拼接字符串效率极低，采用数组拼接效率高

  - document.createElement()
    - 创建多个元素时，效率较低，但结构清晰



### 事件拓展

- 注册事件

  - 传统方式：注册事件唯一，后注册的事件会覆盖先注册的事件
  - 方法监听：W3C标准，IE9以上（IE9以前用attachEvent("**on**click",fun)注册事件）
    - addEventListener（type，listener，useCapture）
      - type：字符串，事件类型，比如"click"、"mouseover"
      - listener：事件处理函数，事件发生时，会调用该函数
      - useCapture：可选参数，是一个布尔值，默认是false（冒泡阶段）

- 解绑事件

  - 传统方式：element.onclick = null;
  - 方法监听：removeEventListener("click",fun-name)
  - IE9以前：detachEvent("onclick",fun-name)

- Dom事件流

  - 捕获阶段
  - 冒泡阶段
    - mouseover、mouseout：冒泡
    - mouseenter、mouseleave：不冒泡

- 事件对象：通过形参来获取，ie678通过windows.event来获取

  - 属性
    - target：触发事件的元素（this指代绑定事件的元素）
    - currentTarget：与this相似
    - srcElement：触发事件的元素（ie678使用）
    - type：事件的类型，如click、mouseover
    - cancelBubble：阻止冒泡（ie678使用）
    - returnValue：阻止默认事件（默认行为），如不让链接跳转，（ie678使用）
    - preventDefault()：方法，阻止默认事件
      - 事件方法体中return false；也可以阻止默认事件
    - stopPropagation()：方法，阻止冒泡

- 事件委托

  - 不是每个子节点单独设置事件监听，而是事件监听设置在父节点上，然后利用冒泡原理影响设置每一个字节点

- 补充事件

  - 禁用右键菜单

    ```javascript
    document.addEventListener('contextmenu',function(e){
    	e.preventDefault();
    })
    ```

  - 禁止鼠标选中

    ```JavaScript
    document.addEventListener('selectstart',function(e){
        e.preventDefault();
    })
    ```







## BOM

### 常见事件事件

- 页面加载事件

  - load：页面全部加载完

  - DOMContentLoaded：仅当DOM加载完，不包括样式表，图片，flash等等（IE9以上）

- 调成窗口大小事件

  - resize：窗口大小变化
  - 窗口大小的属性：
    - window.innerWidth
    - window.innerHeight



### 定时器

- window.setTimeout(调用函数，延时的毫秒数)
- window.clearTimeout(timeout-name)
- window.setInterval(回调函数，间隔的毫秒数)
- window.clearInterval(interval-name)



### 同步与异步

- 同步任务

- 异步任务

  - 普通事件，如click、resize等

  - 资源资源加载事件，如load、error等

  - 定时器，包括setInterval、setTimeout等



### 其他对象

- location

  - url
    - protocal：通信协议，常见http，ftp，maito等
    - host：主机（域名）
    - port：端口号，http默认为80
    - path：路径，由零或多个"/"符号分隔开的字符串
    - query：参数
    - fragment：锚点

  - location对象
    - 属性包括：href（整个url）、host（主机）、port（端口）、pathname（路径）、search（参数）、hash（锚点）
    - 方法：
      - assign(href)：和href相同，可用于页面的跳转
      - replace(href)：替换当前页面，因为不记录历史，所以不能后退
      - reload()：重新加载页面，相当于刷新按钮，如果参数为true，则强制刷新
- navigator

  - userAgent
- history

  - back()：后退
  - forward()：前进
  - go(int)：前进指定步数（负数为后退）





### WebStorage

- localStorage：永久保存数据
- sessionStorage：持久保存数据，浏览器关闭时清除
- api调用
  - 添加数据：`xxxStorage.setItem(String key, String value)`
  - 获取数据：`xxxStorage.getItem(String key)`
  - 移除数据：`xxxStorage.removeItem(String key)`
  - 清空数据：`xxxStorage.clear`




### 元素

- offset

  - 获得元素距离带有定位**父元素**的位置
  - 常用属性：
    - offsetParent：返回作为该元素带有定位的父级元素，如果父级元素都没有定位，返回body
    - offsetTop、offsetLeft：返回元素相对带有定位父元素上方（左方）的偏移
    - offsetWidth、offsetHeight：返回自身包括padding、边框、内容区的宽度（高度）

- client

  - 获得元素自身的属性

  - 常用属性：
    - clientTop、clientLeft：返回元素左边框的大小
    - clientWidth、clientHeight：返回自身包括padding、内容区的宽度（高度），不包含边框，返回数值不带单位

- scroll

  - 可用于得到该元素的滚动条的长度、滚动距离等

  - 常用元素

    - scrollTop、scrollLeft：返回被卷去的上侧（左侧）距离
    - scrollWidth、scrollHeight：返回自身实际宽度（高度），不包含边框

  - 补充：

    - 网页可见区域宽： document.body.clientWidth;
    - 网页可见区域高： document.body.clientHeight;
    - 网页可见区域宽： document.body.offsetWidth    (包括边线的宽);
    - 网页可见区域高： document.body.offsetHeight   (包括边线的宽);
    - 网页正文全文宽： document.body.scrollWidth;
    - 网页正文全文高： document.body.scrollHeight;
    - 网页被卷去的高： document.body.scrollTop;
    - 网页被卷去的左： document.body.scrollLeft;
    - 网页正文部分上： window.screenTop;
    - 网页正文部分左： window.screenLeft;
    - 屏幕分辨率的高： window.screen.height;
    - 屏幕分辨率的宽： window.screen.width;
    - 屏幕可用工作区高度： window.screen.availHeight;
    - 屏幕可用工作区宽度：window.screen.availWidth;
    
    



### 立即执行函数

- 不需要调用，立刻就能执行的函数
- 写法：
  - (function(指定参数) {方法体})(传递的参数);
  - (function(指定参数) {方法体}(传递的参数));



### 动画

- 可通过设定interval的定时器来达成动画的效果
- 封装函数简化代码，如function flash(obj, target)
- 用函数中所传递的元素对象的属性（一般为obj.timer）作为定时器的标识，可给不同的元素指定不同的定时器
- 执行定时器中的回调函数前先清除定时器（obj.timer）

- 缓动动画（步长公式：（目标值-现在的位置）/定值）

```javascript
function flash(obj, target, callback){
    clearInterval(obj.timer);
    obj.timer = setInterval(function(){
        var step = (target - obj.offsetLeft) / 10;
        step = step > 0 ? Math.ceil(step) : Math.floor(step);
        if(obj.offsetLeft == target){
            clearInterval(obj.timer);
            if(callback){
                callback();
            }
        }
        obj.style.left = obj.offsetLeft + step +"px";
    },15)
}
```









# CSS3



## css拓展

### 字体图标

- 字体图标下载 https://www.iconfont.cn/

- 使用

  ```html
  <html>
  <head>
      <title>字体图标</title>
      <!-- 引入字体图标样式 -->
      <link rel="stylesheet" href="./font_icon/iconfont.css">
  </head>
  <body>
      <!-- 使用字体图标 -->
      <span class="iconfont icon-RectangleCopy"></span>
  </body>
  </html>
  ```

- iconfont.css

  ```css
  /* 定义字体 */
  @font-face {
      font-family: "iconfont"; /* Project id 3663092 */
      src: url('iconfont.woff2?t=1663751590840') format('woff2'),
          url('iconfont.woff?t=1663751590840') format('woff'),
          url('iconfont.ttf?t=1663751590840') format('truetype');
  }
  /* iconfont样式 */
  .iconfont {
      /* 使用iconfont字体 */
      font-family: "iconfont" !important;
      font-size: 16px;
      font-style: normal;
      -webkit-font-smoothing: antialiased;
      -moz-osx-font-smoothing: grayscale;
  }
  
  /* 用伪元素加载字体 */
  .icon-RectangleCopy:before {
      content: "\e6a1";
  }
  ```

- 可上传图标（svg矢量图）



### 渐变

https://pxlab.cn/color/index.html

- 透明度：opacity: 0;

- 类型

  - 线性渐变
  - 径向渐变
  - 重复渐变

- 渐变：background-image: linear-gradient(方向, 颜色1, 颜色2, ...)

  - 线性渐变 - 从上到下（默认）

    ```css
    background-image: linear-gradient(red, yellow);
    ```

  - 线性渐变 - 从左到右

    ```css
    background-image: linear-gradient(to right, red , yellow);
    ```

  - 线性渐变 - 对角线

    ```css
    background-image: linear-gradient(to bottom right, red, yellow);
    ```

  - 使用角度

    ```css
    background-image: linear-gradient(-90deg, red, yellow);
    ```

  - 重复线性渐变

    ```css
    background-image: repeating-linear-gradient(red, yellow 10%, green 20%);
    ```



### 平面转换

- 变换

  - transform: translate(水平移动距离【, 垂直移动距离】)
    - 取值：具体值、百分比（参考自身盒子大小）
  - transform: translateY(垂直移动距离)
  - transform: rotate(角度)（单位deg）
    - 设置转动原点：transform-origin: 原点水平位置 原点垂直位置;
      - 取值：left、top、right、bottom、center
  - transform: scale(缩放倍数)（中心缩放）

- 变换原点：transform-origin

  对旋转、缩放有影响，对位移无影响

  - 横向：left、center、right、像素等
  - 纵向：top、center、bottom、像素等

- 使用

  ```html
  <html>
      <head>
          <title>平面移动</title>
          <style>
              .father {
                  width: 500px;
                  height: 300px;
                  border: 1px solid gray;
              }
              .son {
                  width: 100px;
                  height: 50px;
                  background-color: aqua;
                  /* 定义变化的时间 */
                  transition: all 1s;
              }
              .father:hover .son {
                  /* 定义位移距离 */
                  transform: translate(200px, 100px);
                  /* 定义旋转 */
                  transform: rotate(360deg);
                  /* 定义多重移动（旋转会改变坐标轴向，位移会受影响，所以一般放在最后） */
                  transform: translate(200px, 100px) rotate(360deg);
                  /* 缩放 */
                  transform: scale(3);
              }
          </style>
      </head>
      <body>
          <div class="father">
              <div class="son"></div>
          </div>
      </body>
  </html>
  ```



### 空间转换

- 3d 空间与景深
  - 开启 3d 空间：为 3d 元素的父元素添加属性：`transform-style: preserve-3d`（flat，默认，2d）
  - 景深：
    - `perspective：500px`：z=0 平面距观察者的距离
    - 开启景深后，会产生透视效果
    - 一般取值：800px~1200px
- 透视点的位置：perspective-origin: x y （默认在父元素的中心）

- 语法

  - transform: translate3d(x, y, z)
  - transform: rotateX(角度)：围绕X轴转动
  - transform: scale3d(x, y, z)：缩放

- 背部隐藏：backface-visibility：hidden

- 立方体盒子

  ```html
  <!DOCTYPE html>
  <html>
      <head>
          <title>立体导航栏</title>
          <style>
              .nav {
                  position: relative;
                  float: left;
                  width: 100px;
                  height: 50px;
                  line-height: 50px;
                  text-align: center;
                  /* 使子盒子处于真正的3d空间中*/
                  transform-style: preserve-3d;
                  transition: all .5s;
              }
              .nav .cube1 {
                  position: absolute;
                  width: 100px;
                  height: 50px;
                  background-color: yellow;
              }
              .nav .cube2 {
                  position: absolute;
                  width: 100px;
                  height: 50px;
                  background-color: aqua;
                  transform: translate3d(0, -25px, -25px) rotateX(90deg);
              }
              .nav:hover {
                  transform: translateY(25px) rotateX(-90deg);
              }
          </style>
      </head>
      <body>
          <div class="navigation">
          <div class="nav">
              <div class="cube1">登录</div>
              <div class="cube2">login</div>
          </div>
          <div class="nav">
              <div class="cube1">注册</div>
              <div class="cube2">register</div>
          </div>
      </div>
      </body>
  </html>
  ```



### 过渡

- transition-

  - property：需要过渡的属性

    常见支持过渡的属性有：颜色、长度值、百分比、z-index、opacity、2D变换属性、3D变换属性、阴影（如 width、height、bgc、box-shadow、rotate 等）

  - duration：过渡时间

  - delay：延时过渡

  - timing-function：过渡方式

    - <mark>ease</mark>：默认值，平滑过渡
    - <mark>linear</mark>：线性过渡，匀速过渡
    - ease-in：匀加速
    - ease-out：匀减速
    - ease-in-out：先匀减速，再匀减速（平滑过渡效果没有 ease 好）
    - <mark>steps(n)</mark>：分n步过渡
    - step-start：不考虑过渡时间，再直接从原点到达终点
    - step-end：达到过渡时间后，再直接从原点到达终点
    - <mark>cubic-bezier</mark>：贝塞尔曲线 [https://cubic-bezier.com](https://cubic-bezier.com)

- 复合属性

  transition: duration delay timing-function property1, property2（整体无序，delay需在duration后）


### 动画

- 语法

  - 定义动画

    ```css
    @keyframes 动画名称 {
        from {}
        to {}
    }
    ```

    ```css
    @keyframes 动画名称 {
        0% {}
        10% {}
        15% {}
        100% {}
    }
    ```

  - 使用动画

    ```css
    animation: 动画名称 动画花费时长, 第二个动画 时长, ......;
    
    /* 不区分顺序（第一个时间表示时长，第二个表示延时时间） */
    animation: 动画名称 动画花费时长 速度曲线 延时时间 重复次数 动画方向 执行完毕时状态;
    ```
    
  - 动画属性（配合js）
  
    - animation-name：动画名称
    
    - animation-duration：动画时长
    
    - animation-delay：延迟时间
    
    - animation-fill-mode：动画执行完毕时的状态
    
      - forwards：动画结束后停留在最终状态
      - backwards：动画结束后回到最初状态
    
    - animation-timing-function：速度曲线
    
      与 transation-timing-function 参数一样
    
    - animation-iteration-count：重复次数
    
      - infinite：无限循环
    
    - animation-direction：动画执行方向
    
      - normal：默认，正向
      - reserve：反向
      - alternate：先正向、后反向
      - alternate-reserve：先反向、后正向
    
    - animation-play-state：动画状态（取值paused为暂停）





## 移动端网页

### Flex布局

- 弹性容器
  - display: 
  
    - flex：设置弹性容器，其直接子盒子为弹性盒子
    - inline-flex：其直接子元素为行内弹性盒子
  
  - justify-content：调节元素在主轴的对齐方式
    - flex-start：默认值，起点开始依次排序
    - flex-end：终点开始依次排序
    - center：沿主轴居中排序
    - space-around：弹性盒子沿主轴均匀排序，空白间距均分在弹性盒子两侧
    - space-between：弹性盒子沿主轴均匀排序，空白间距均分在弹性盒子之间
    - space-evenly：弹性盒子沿主轴均匀排序，弹性盒子与容器之间间距相等
  
  - align-items：调节弹性盒子在侧轴的对齐方式（单行）
    - stretch：默认值，**在所有弹性盒子没设置高度时**，沿侧轴拉伸弹性盒子至与弹性容器相同
    - center：沿侧轴居中排列
    - flex-start：默认值，起点开始依次排列
    - flex-end：终点开始依次排列
  
  - align-content：调节弹性盒子在侧轴的对齐方式（多行）
  
    - flex-start
    - flex-end
    - center
    - space-around
    - space-between
    - stretch：默认值，**在所有弹性盒子没设置高度时**，沿侧轴拉伸弹性盒子至与弹性容器相同
  
  - flex-flow
  
    复合属性 `flex-flow: direction wrap`
  
    - flex-direction：主轴排列方向
      - row：默认，水平方向，从左往右
      - row-reserve：水平方向，从右往左
      - column
      - column-reserve
  
    - flex-wrap：换行
      - nowrap：默认值，不换行
      - wrap：换行
      - wrap-reserve：从左下角开始排列
  
- 弹性项
  
  - flex：复合属性
  
    `flex: grow shrink basis`
  
    - flex-basis：
      - 设置伸缩项在主轴上的基准长度，默认值auto（与width、height一致）
      - 若主轴横向 width 失效；若主轴纵向 height 失效
      - 浏览器根据某一轴上所有元素的基准长度和，判断该轴是否有剩余空间
    - flex-grow：
      - 占用弹性盒子**剩余空间**的份数，默认0，表示不拉伸
      - **同长拉伸**：若所有盒子收缩分数相同，拉伸尺寸都相同，与盒子大小无
    - flex-shrink：
      - 若某一轴长度不足，该轴上弹性盒子收缩的份数，默认0，表示不压缩
      - **不同长压缩**：还需考虑各个盒子的基准长度，即若所有盒子收缩分数相同，尺寸大的盒子收缩更多
      - 收缩的极限是保证盒子内所有的内容都呈现的最小大小
    - 简写
      - flex: 0 1 auto  =>  flex: 0 auto （默认值，可以压缩，不能拉伸）
      - flex: 1 1 0  =>  flex: 1（元素尺寸大小变为相同，同长拉伸，同长压缩）
      - flex: 1 1 auto  =>  flex: auto（同长拉伸，不同长压缩）
      - flex: 0 0 auto  =>  flex: none（不可拉伸，不可压缩）
  
  - order：排序，默认 0，负数会排在 0 前
  
  - align-self
  
    - 调节某个弹性盒子在侧轴的对齐方式
    - 取值与 align-items 属性相同
    - 默认值 auto，表示继承父元素的 align-items



### Grid布局 

- 网格容器
  - display:
    - grid：父元素为网格容器，其直接子元素为网格盒子（网格项）
    - inline-grid：其直接子元素为行内网格盒子
    
  - grid-template-columns: value1 value2 ...
  
    用来设置列，有几个值就有记录，每个值代表相应的宽度
  
    - 数值：200px、33%
    - 分数：1fr、3fr
    - auto：占满剩余空间
    - repeat(重复次数, 值1, 值2, ...)
  
  - grid-template-rows: value1 value2 ...
  
    用来设置行
  
  - grid-column-gap: 值
  
    设置列间距
  
  - grid-row-gap: 值
  
    设置行间距
  
  - place-items：设置网格项内容的对齐方式
  
    `place-items: <align-items> <justify-items>`
  
    - justify-items: start|center|end|stretch
  
      控制单元格在水平方向上的对齐方式
  
      - start：对齐单元格的起始边缘
  
      - end：对齐单元格的结束边缘
  
      - center：单元格内部居中
  
      - stretch：拉伸，占满单元格的整个宽度（默认值）
  
    - align-items: start|center|end|stretch
  
  - place-content：整个网格项内容在容器中的对齐方式
  
    `place-content: <align-content> <justify-content>`
  
    - align-content: start|center|end|stretch|space-around|space-between|space-evenly
    - justify-content: ...
  
  - grid-template-areas：区域命名
  
    ```css
    grid-template-areas: 'a a a'
                         'd e f'
                         'g h .'
    ```
  
    - 相同的命名，可将多个单元格组合成一块 **区域**（area）
    - 如果某些区域不需要使用，可以使用 . 来表示
    
  - 利用下面四个边框线，控制所有项的位置
  
    - template-row-start: 2
  
      2：表示该项从 行线2 开始排列
  
      a-start：表示从 区域a 的 start 处开始排列
  
    - template-row-end
  
    - template-column-start
  
    - template-column-end
  
- 网格项

  - 利用下面四个边框线，控制单个项的位置

    - 四个边框线控制

      - grid-row-start: 2|a-start
      - grid-column-start
      - grid-row-end
      - grid-column-end
  
    - 边框线简写
  
      grid-row: e-start/e-end => grid-row: e
  
      grid-column: e-start/e-end => grid-column: e
  
    - 区域简写
  
      grid-area: a|b|c
  
  - 控制单个网格项内容的纵向对齐方式：align-self
  
  - 控制单个网格项内容的横向对齐方式：justify-self










### 单位

- rem

  - rem单位

    - 相对单位
    - 1rem = 1HTML字号大小

- 移动适配

  - 媒体查询

    ```css
    @media (width:375px（媒体特性）){
        html {
            font-size: 30px;
        }
    }
    ```

  - 引入flexiable.js


- vh、vw、vmax、vmin

  - vh/vw
    - 1vh = 1/100视口高度
    - 1vw = 1/100视口宽度
  - vmax/vmin：浏览器视口的宽、高谁比较大/小，就基于谁



### less

```less
// out: ../css/  // 到处到指定位置
// out: false    // 禁止导出

// 导入其他样式文件
@import './test.less';

// 定义变量
@color: pink;

.father {
    width: 10+5px;
    // 除法需要加括号
    height: (300/37.5rem); 
    
    
    // 使用变量
    background: @color;
    
    .son {
        // 此处写子类样式表
        width: 5px;
    }
    
    // $：表示本选择器
    $:hover {
        background: red;
    }
}
```



### 媒体查询

- 语法

  ```css
  @media【关键词 媒体类型 and】(媒体选择器) {
      选择器 {
          样式
      }
  }
  
  // 屏幕大于 1024px 或小于 1440px 时应用该样式
  @media screen and (min-width: 1024px) and (max-width: 1440px) { 
    ...
  }
  ```

- 组成

  - 媒体选择器
    - 视口的宽和高：width、height
    - 视口最大宽或高：max-width、max-height
    - 视口最小宽或高：min-width、min-height
    - 屏幕方向：orientation=portrait（竖屏）、landscape（横屏）
  - 关键词
    - and
    - only
    - not
  - 媒体类型
    - screen：带屏幕的设备
    - print：打印预览模式
    - speech：屏幕阅读模式
    - all：默认值

- link引入

  ```html
  <link rel="stylesheet" media="逻辑符 媒体类型 and （媒体特性）" href="xx.h">
  ```

- 常用阈值
  - width < 768px：超小屏幕
  - 768px <= width < 992px：中等屏幕
  - 992px <= width < 1200px：大屏幕
  - width >= 1200px：超大屏幕




### BootStrap

- 栅格化是指将整个网页的宽度分成若干等分

- BootStrap默认将网页分成12等分

  |          | 超小屏幕 | 小屏幕   | 中等屏幕 | 大屏幕    |
  | -------- | -------- | -------- | -------- | --------- |
  | 响应段点 | < 768px  | >= 768px | >= 992px | >= 1200px |
  | 别名     | xs       | sm       | md       | lg        |
  | 容器宽度 | 100%     | 750px    | 970px    | 1170px    |
  | 前缀名   | col-xs-* | col-sm-* | col-md-* | col-lg-*  |
  | 列数     | 12       | 12       | 12       | 12        |
  | 列间宽   | 30px     | 30px     | 30px     | 30px      |

- 快速入门

  ```html
  <!-- 引入bootstrap样式表 -->
  <link rel="stylesheet" href="./bootstrap-3.4.1-dist/css/bootstrap.min.css">
  
  <!-- container是bootstrap中专门提供的版心类名，自带15px的padding -->
  <!-- row类自带-15px的外边距，与container结合使用可抵消container的15px的padding -->
  <div class="container">
      <div class="col-lg-3 col-md-2 col-sm-1 row">第一个模块</div>
      <div class="col-lg-3 col-md-2 col-sm-1 row">第二个模块</div>
      <div class="col-lg-3 col-md-2 col-sm-1 row">第三个模块</div>
      <div class="col-lg-3 col-md-2 col-sm-1 row">第四个模块</div>
  </div>
  
  <!-- container-fluid样式中宽度是100%，但存在15px的padding -->
  <div class="container-fluid"></div>
  ```

- 其他

  - [全局 CSS 样式](https://v3.bootcss.com/css)

    - 表格（table标签上）
      - table：表格基础类名
      - table-striped：增加隔行变色效果
      - table-bordered：为表格增加边框
      - table-hover：增加鼠标悬停效果
    - 按钮（a、button、input标签上）
      - btn：按钮基础类名
      - btn-default：默认样式
      - btn-primary：首选项
      - btn-success：成功

  - [组件](https://v3.bootcss.com/components/)

    - 使用步骤

      - 导入css样式
      - 复制结构，修改内容

    - 分页组件

      ```html
      <nav aria-label="Page navigation">
          <ul class="pagination">
              <li>
                  <a href="#" aria-label="Previous">
                      <span aria-hidden="true">&laquo;</span>
                  </a>
              </li>
              <li><a href="#">1</a></li>
              <li><a href="#">2</a></li>
              <li><a href="#">3</a></li>
              <li><a href="#">4</a></li>
              <li><a href="#">5</a></li>
              <li>
                  <a href="#" aria-label="Next">
                      <span aria-hidden="true">&raquo;</span>
                  </a>
              </li>
          </ul>
      </nav>
      ```

    - 字体图标

  - [JavaScript 插件](https://v3.bootcss.com/javascript/)

    - 使用步骤

      - 引入文件

        ```html
        <!-- 引入样式表 -->
        <link rel="stylesheet" href="./bootstrap-3.4.1-dist/css/bootstrap.min.css">
        
        <!-- 引入脚本 -->
        <script src="./js/jquery.js"></script>
        <script scr="./bootstrap-3.3.7-dist/js/bootstrap.min.js"></script>
        ```

      - 复制结构，调整内容

    - 下拉菜单

      ```html
      <div class="dropdown">
          <button id="dLabel" type="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
              Dropdown trigger
              <span class="caret"></span>
          </button>
          <ul class="dropdown-menu" aria-labelledby="dLabel">
              ...
          </ul>
      </div>
      ```

    - 轮播图

      ```html
      <div id="carousel-example-generic" class="carousel slide" data-ride="carousel">
          <!-- Indicators -->
          <ol class="carousel-indicators">
              <li data-target="#carousel-example-generic" data-slide-to="0" class="active"></li>
              <li data-target="#carousel-example-generic" data-slide-to="1"></li>
              <li data-target="#carousel-example-generic" data-slide-to="2"></li>
          </ol>
      
          <!-- Wrapper for slides -->
          <div class="carousel-inner" role="listbox">
              <div class="item active">
                  <img src="..." alt="...">
                  <div class="carousel-caption">
                      ...
                  </div>
              </div>
              <div class="item">
                  <img src="..." alt="...">
                  <div class="carousel-caption">
                      ...
                  </div>
              </div>
              
              ...
              
          </div>
      
          <!-- Controls -->
          <a class="left carousel-control" href="#carousel-example-generic" role="button" data-slide="prev">
              <span class="glyphicon glyphicon-chevron-left" aria-hidden="true"></span>
              <span class="sr-only">Previous</span>
          </a>
          <a class="right carousel-control" href="#carousel-example-generic" role="button" data-slide="next">
              <span class="glyphicon glyphicon-chevron-right" aria-hidden="true"></span>
              <span class="sr-only">Next</span>
          </a>
      </div>
      ```

  - [定制并下载](https://v3.bootcss.com/customize/)





## 补充

### 私有前缀

> W3C 标准所提出的某个 css 特性，在被浏览器正式支持前，浏览器厂商会根据浏览器的内核，使用私有前缀测试该 css 特性，在浏览器正式支持该 css 后，就不需要使用该私有前缀

- 查询css3兼容的网站：[https://caniuse.com/](https://caniuse.com/)

- 常用的浏览器前缀

  - Chrome 浏览器： `-webkit-`
  - Safari 浏览器：`-webkit-`
  - Firefox ：`-moz-`
  - Edge：`-webkit-`
  - <font color=#ccc>旧 Opera 浏览器：`-o-`</font>
  - <font color=#ccc>旧 IE 浏览器：`-ms-`</font>

  

### 伪类选择器

- 动态伪类

  - link：选中的是链接状态的元素，a 元素特有
  - visited：选中的是访问过的元素，a 元素特有
  - hover：选中的是鼠标悬浮上元素
  - active：选中的是激活状态的元素
  - focus：选中获得焦点的元素，表单类元素特有

- 结构伪类

  - E1 E2:xx-child：

    匹配 E1 元素下，**任意**父元素中**指定次序**的元素，若该元素不是E2，则不被选中

    - first-child：第一个
    - last-child：最后一个
    - nth-child(n)：第n个子元素
    - nth-last-child(n)：倒数第n个子元素
    - E:only-child：选中的是没有兄弟的E元素

    公式：2n、even（偶数）、2n+1、odd（奇数）、-n+5（前五个）、n+5（第五个往后）

  - E1 E2:xx-of-type

    匹配 E1 元素下，**任意**父元素中**与E2同类型中**、**指定次序**的元素，若该元素不是E2，则不被选中

    - first-of-type
    - last-of-type
    - nth-of-type(n)
    - nth-last-of-type(n)
    - E:only-of-type：选中的是没有同类兄弟的E元素

  - :root：选中的是html根元素

  - E:empty：空E元素

- 否定伪类

  - E:not(.fail)
  - E:not([title^="成绩不合格"])
  - E:not(:last-of-type)

- UI伪类

  - checked
  - disabed：选中被禁用的元素
  - enabled：选中的是可用的元素

- 目标伪类：target：选中的是地址栏 #id 表示的元素

- 语言伪类：lang(zh_CN)：选中含属性 lang="zh_CN" 的元素

- has伪类：`.outer:has(.inner:nth-child(1):hover)`



### 新增文本属性

- 文本阴影：text-shadow

- 文本换行：white-space

  - pre：按原文显示
  - pre-wrap：按原文显示；单行超出元素宽度，自动换行
  - pre-line：按原文显示；单行超出元素宽度，自动换行；单行始末两端空格去除
  - nowarp：全文不换行

- 文本溢出：text-overflow

  一般与 `overflow: hidden; white-space: nowarp;` 配合使用

  - clip：裁剪
  - ellipsis：省略号占位

- 文本修饰

  - text-decoration-line：装饰线位置
  - text-decoration-style：装饰线类型
  - text-decoration-color：装饰线颜色
  - 复合属性：`text-decoration: line style color`

- 文本描边

  - -webkit-text-stroke-color：描边颜色
  - -webkit-text-stroke-width：描边宽度



### 多列布局

- 多列布局

  - 直接指定列数

    column-count: 5

  - 指定每一列宽度，会自动计算出列数

    column-width: 200px

  - 调整列间距

    column-gap: 20px

  - 列分割线

    column-rule-color: 2px dashed red

  - 跨列（子元素属性）

    column-rule: none/all

- 布局图片

  ```html
  <style>
      .outer {
          column-count: 5;
      }
      img {
          /* 图片宽度设置为 100% 后会自动适应列宽 */
          width: 100%;
      }
  </style>
  <body>
      <div class="outer">
          <img src="./img1.png">
          <img src="./img2.png">
          ...
          <img src="./img100.png">
      </div>
  </body>
  ```



### BFC

> Block Formatting Context（块级格式化上下文）

- 开启 BFC 后能解决什么问题
  - 其子元素不会产生 margin 塌陷问题
  - 自己不会被其他浮动元素所遮挡
  - 就算其子元素浮动，元素自身高度也不会塌陷

- 开启 BFC
  - 根元素 html
  - 浮动元素：`float: left`
  - 绝对定位、固定定位的元素：`position: absolute`
  - 行内块元素：`display：inline-block`
  - 表格元素：table、thead、tbody、tfoot、th、td、tr、caption：`display: table`
  - overflow 的值不为 visible 的块元素：`overflow: auto`
  - 伸缩元素：`display: flex`
  - 多列容器：`column-count: 1`
  - column-span 为 all 的元素（即使该元素没有包裹在多列容器中）
  - display 为 flow-root







# Ajax

## XHR



### 基础

#### Http

- HTTP规定了浏览器和万维网服务器之间互相通信的规则。
- 请求报文
  - 行：GET	/test	HTTP/1.1
  - 头：（键值对形式）
    - Host: xxx.com
    - Cookie: name=admin
    - Content-type: （描述发送到服务器的数据类型）
      - application/x-www-form-urlencoded（默认）
      - multipart/form-data（表单有文件、图片）
      - application/json
    - Accept:（用来描述客户端能接受什么类型的返回值）
    - Accept-Language:（客户端期望接收的人类语言）
    - User-Agent:（说明当前是什么类型的浏览器）
  - 空行
  - 体：username=admin&password=admin（如果是get请求，请求体为空）
- 响应报文
  - 行：HTTP/1.1	200	OK
  - 头：（键值对形式）
    - Content-Type: text/html;charset=utf-8
    - Content-length: 2048
    - Access-Control-Allow-Headers: *：接收所有类型请求头
    - Access-Control-Allow-Origin: *：Ajax允许跨域
  - 空行
  - 体
    - html页面



#### XMLHttpRequest

- 方法

  | 方法                                | 描述                                                   |
  | ----------------------------------- | ------------------------------------------------------ |
  | abort()                             | 取消当前请求                                           |
  | getAllResponseHeader()              | 返回头部信息                                           |
  | getResponseHeader()                 | 返回特定的头部信息                                     |
  | open(method, url, async, user, psw) | 规定请求的方法、地址、是否异步、可选用户名称、可选密码 |
  | send()                              | 将请求发送到服务器，用于GET请求                        |
  | send(string)                        | 将请求发送到服务器，用于POST请求                       |
  | setRequestHeader()                  | 向要发送的报头添加键值对                               |

- 属性

  | 属性               | 描述                             |
  | ------------------ | -------------------------------- |
  | readyState         | 保存了XMLHttpRequest的状态       |
  | onreadystatechange | 事件：当readyState属性发生变化时 |
  | timeout            | 可设置请求超时时间               |
  | ontimeout          | 事件：当请求超时时               |
  | onerror            | 事件：当网络异常时               |
  | responseText       | 以字符串返回相应数据             |
  | responseXML        | 以XML数据返回相应数据            |
  | status             | 返回请求状态码                   |
  | statueText         | 返回请求文本                     |



#### 快速入门

```js
let xhr = null
let isSending = false
btn.onclick = function(){
    // 解决重复发送问题
    if(isSending){xhr.abort()}

    xhr = new XMLHttpRequest()
    // 自动转json，设置响应体数据类型（同步请求不能设置响应数据类型）
    //xhr.responseType = "json"
    // 网络异常处理
    xhr.timeout = 2000
    xhr.ontimeout = function(){alert("请求超时")} // 时间到后会自动取消请求
    xhr.onerror = function(){alert("网络异常")}

    //open(method, url, async, username, password)
    // async 参数指示请求使用应该异步地执行。
    // 	如果这个参数是 false，请求是同步的，后续对 send() 的调用将阻塞，直到响应完全接收。
    // 	如果这个参数是 true 或省略，请求是异步的
    // username 和 password 参数是可选的，为 url 所需的授权提供认证资格。
    xhr.open('GET',"http://localhost/test?username=admin", true)

    // 添加请求头，Content-Type是用来设置请求体类型的
    xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded')

    xhr.send() // POST方法在此处传参
    isSending = true

    // xhr.abort() // 取消当前请求

    xhr.onreadystatechange = () => {
        // readystate的值：
        // 0-未初始化 1-open方法调用完毕 2-send方法调用完毕 3-返回部分结果 4-返回全部结果
        if(xhr.readyState == 4){
            if(xhr.status >= 200 && xhr.status < 300){
                // 处理结果  行、头、体
                // 手动将响应转为json
                let data = JSON.parse(xhr.response)
                }
            isSending = false;
        }
    }
}
```





### 拓展

#### 数据解析

- 常用方法

  - encodeURI()：URL编码

  - decodeURI()：URL解码

  - JSON.stringify(Object obj)：对象转json字符串

  - JSON.parse(String json)：json字符串转对象

- json

  - 数据结构：{key: value, key: value, ...}

  - key：双引号包裹的字符串

  - value：数据类型可以是数字、字符串（双引号）、布尔值、null、数组、对象6种类型

  - 最外层必须是对象或数组格式

  - 例如：

    ```json
    {
        "name": "zhangsan",
        "age": 20,
        "address": null,
        "hobby": ["吃饭", "睡觉"],
        "friend": [
            {
                "name": "lisi"
            },
            {
                "name": "wangwu"
            }
        ]
    }
    ```

    

#### FormData

- 基本使用

  ```js
  // 1. 新建 FormDate 对象
  let fd = new FormData();
  
  // 2. 为 FormDate 添加表单项
  fd.append("username","zhangsan");
  fd.append("password","12345");
  
  // 3. 发送ajax请求
  const xhr = new XMLHtttpRequest();
  xhr.open("POST","http://www.test.com");
  xhr.send(fd)
  ```

- 从网页表单中获取数据

  ```js
  // 1. 获取form表单
  let form = document.querySelector("#form");
  
  // 2. 监听提交事件
  form.addEventListener("submit", function(e){
      // 3. 阻止默认事件
      e.preventDefault();
      // 4. 绑定
      let fd = new FormData(form);
      
      // 5. 发送ajax
  })
  ```

- 上传文件

  ```html
  <input id="file" type="file"/>
  <input id="btn" type="button"/>
  ```

  ```js
  let btn = document.querySelector("#btn")
  btn.addEventListener("click", function(){
      // 1. 检验是否选择了文件
      let files = document.querySelector("#file").files
      if(files.length <= 0){
          return alert("请选择要上传的文件")
      }
      
      // 2. 向FormData中追加文件
      let fd = new FormData()
      fd.append("file", files[0])
      
      // 细节：用formdata提交文件时不需要设置请求头
  })
  ```



#### 传输进度

- 上传

  ```js
  const xhr = new XMLHttpRequest()
  xhr.upload.onprogress = function(e){
      // e.lengthComputable 是一个布尔值，表示当前上传的资源是否具有可计算的长度
      if(e.lengthComputable){
          // e.loaded 已传输的字节
          // e.total 需要传输的总字节
          let percentComplete = Math.floor((e.loaded / e.total) * 100)
      }
  }
  // e.isTrusted为true时代表事件为用户触发的可信事件
  ```

- 下载：xhr.onprogress



#### Axios

- axios发送get/post请求

  ```js
  axios.get(url, {params: {/*参数对象*/}}).then(callback)
  axios.post(url, {/*数据对象*/}).then(callback)
  ```

  ```js
  // 请求的 URL 地址
  let url = "http://www.test.com"
  // 请求的参数对象
  let paramsObj = {name:'zs', age:20}
  // 调用 axios.get() 发送 GET 请求
  axios.get(url, {params: paramsObj}).then(function(res){
      // res.data 是服务器返回的数据
      let result = res.data
      // res中还包含请求的状态等信息
      console.log(res)
  })
  ```

- 直接发送axios请求

  ```js
  // axios(请求对象).then(callback)
  axios({
      method: "请求类型",
      url: "URL地址",
      data: {/*Post数据*/},
      params: {/*Get参数*/}
  }).then(callback)
  ```




#### 防抖与节流

- 防抖优化ajax请求

  ```js
  let input = doucment.getElementById('input');
  let timer = null
  // 定义防抖函数
  function debounceSearch(keywords){
      timer = setTimeout(function(){
          getSuggestList(keywords)
      },500)
  }
  // 在指定毫秒数内触发keyup事件时，清除上一个定时器，开启下一个定时器
  input.addEventListener("keyup", function() {
      clearTimeout(timer)
      // 重新获得keywords
      let kw = input.val().trim();
      debounceSearch(kw)
  })
  ```

- 节流优化鼠标跟随效果

  ```js
  let angle = document.getElementById("angel");
  let timer = null // 1. 预定义一个 timer 节流阀
  document.addEventListener("mousemove", function(e){
      if(timer){return} // 3. 判断节流阀是否为空，如果不为空，则证明距离上次执行间隔不足指定毫秒数
      timer = setTimeout(function() {
          angle.style.left = e.pagex+'px'
          angle.style.top = e.pageY+'px'
          timer = null // 2. 当设置了鼠标跟随效果后，清空 timer 节流阀，方便下次开启延时器
      },16)
  })
  ```

  



### 跨域

#### 同源策略

- 同源：协议、域名、端口相同，反之，则是跨域
- 同源策略：限制两个不同源的网页进行资源交互
- 处理时机：客户端可发送ajax到跨域的服务器，服务器也可以接收并返回数据，数据会在返回时被浏览器的同源策略拦截



#### JSONP

- 原理：由于浏览器的限制，网页中无法通过Ajax请求非同源的接口数据，但是\<script>标签的src属性不受浏览器的同源策略影响，可以同过src属性，请求非同源的js脚本。因此，JSONP的实现原理，就是同个\<script>标签的src属性，请求跨域的数据接口，并通过函数调用的形式，接收跨域接口响应回来的数据

- 步骤

  - 定义一个success的回调函数

    ```html
    <script>
    	function success(data) {
            console.log("获取到了data数据")
            console.log(data)
        }
    </script>
    ```

  - 通过\<script>标签，请求接口数据

    ```html
    <script src="http://www.test.com/jsonp?callback=success">
    </script>
    ```

  - 非同源的网页先获得后端服务器的数据

  - 再根据src中的callback参数，准备一个名为success的回调函数，参数为服务器的数据

    ```html
    <script>
        let data = res.data;
        success(data);
    </script>
    ```

- 兼容各个版本浏览器，但只支持get请求。也并不是ajax



#### CORS

- 服务端设置响应头

  - response.setHeader("Access-Control-Allow-Origin", "*");

    - 第二个参数：允许跨域的网页的 url，* 表示所有

  - 其他响应头

    - Access-Control-Expose-Headers
    - Access-Control-Max-Age
    - Access-Control-Allow-Credentials
    - Access-Control-Allow-Methods：设置允许跨域请求的方法，默认get、post
    - Access-Control-Allow-Headers：允许发送请求时携带自定义的请求头

  - 使用（HttpServletResponse）
  
    ```java
    response.setHeader("Access-Control-Allow-Origin", "*");
    response.setHeader("Access-Control-Allow-Methods", "*");
    response.setHeader("Access-Control-Allow-Headers", "*");
    ```
  
    
  
    

## fetch

> 下一代的 AJAX 技术，内部采用 Promise 方式处理数据

### 发送请求

- 快速入门

  - Promise

    ```js
    fetch('http://localhost/book/books')
        // res 是一个 Response 对象，res.json() 是一个 Promise
        .then(res => res.json())
        // 此处的 json 是 JSON 数据
        .then(json => console.log(json))
        .catch(err => console.log(err))
    ```

  - async、await 改写 promise

    ```js
    async function getData() {
        try {
            let res = await fetch('http://localhost/book/books')
            let json = await res.json()
        } catch(err) {
            console.log(err)
        }
    }
    getData()
    ```

- get 请求的参数传递：拼接字符串

  ```js
  fetch('http://localhost/book/books?id=1')
  ```

- Response 对象

  - 常用属性

    | 属性           | 说明                               |
    | -------------- | ---------------------------------- |
    | res.ok         | 返回一个布尔类型，表示请求是否成功 |
    | res.status     | 返回一个数字，表示HTTP响应状态码   |
    | res.statusText | 返回状态的文本信息，如 ok          |
    | res.url        | 返回请求的url路径                  |

  - 常用方法

    | 方法              | 说明                        |
    | ----------------- | --------------------------- |
    | res.json()        | 得到JSON对象                |
    | res.text()        | 得到文本字符串              |
    | res.blob()        | 得到二进制Blob对象          |
    | res.formData()    | 得到 FormData 表单对象      |
    | res.arrayBuffer() | 得到二进制 ArrayBuffer 对象 |



### Post请求

- 语法

  fetch(url [, options]) 的第一个参数是 url， 此外，还可以接收第二个参数，作为配置对象

  ```js
  fetch(url, {
      method: 'post', // 请求方式，如 delete、put 等
      headers: {
          'Content-Type': '请求头数据'
      },
      body: '请求体数据'
  })
  ```

- 请求体为json

  ```js
  let aBook = {
      bookname: '俘获富婆芳心的三百种手段',
      author: '小刘忙'
  }
  fetch("http://localhost/book/book", {
      method: 'post',
      headers: {
          'Content-Type': 'application/json'
      }
      body: JSON.stringify(aBook) 
  })
      .then(res => res.json())
      .then(json => console.log(json))
      .catch(err => console.log(err))
  ```

- 请求体为 x-www-form-urlencoded 格式

  ```js
  async function insertBook() {
      let res = await fetch(url, {
          method: 'post',
          headers: {
              'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
          },
          body: 'bookname=俘获富婆芳心的三百种手段'
      })
  }
  ```

- 请求体为 formData 格式

  ```js
  let form = document.querySelector('form')
  fetch(url, {
      method: 'post',
      body: new FormData(form)
  }) 
  ```

  













# Vue



## Vue2

### 入门

#### 快速入门

- 初始Vue
  - 创建一个Vue实例，并传入一个配置对象
  - root容器中的代码被称为【Vue模板】
  - 容器与Vue实例一一对应
  - 容器内部的{{内容}}（插值语法），内容为js表达式
  - 真实开发中只有一个Vue实例，并且会配合着组件一起使用

```html
<!--容器-->
<div id="root">
    <h1>Hello,{{name}}</h1>
    <h1>Hello,{{msg.age}}</h1>
</div>
<h1>Hello,{{name}}</h1>

<script type="text/javascript">
    Vue.config.productionTip = false; // 用于关闭页面加载之初的警告信息
    // 创建Vue实例
    const x = new Vue({

        // 指定容器（两种写法）
        el:'#root', // v.$mount("选择器")

        // 封装数据（两种写法）
        // 对象式
        data:{
            name:'razy',
            msg:{
                age:18
            }
        }
        /* 函数式
        data:function(){
        	// 返回一个对象
        	return{
            	name:'razy',
        	}
    	} */
    });
</script>
```



#### 语法

- 插值语法
  - 功能：用于解析标签体内容
  - 写法：{{xxx}}，xxx是js表达式，且可以直接读取到vm和data（代理）中的所有属性
- 指令语法
  - 功能：用于解析标签
  - 分类：
    - v-bind：放在属性前，可将属性的属性值变为js表达式，可简写为**:**，（单向绑定）
    - v-model：value属性前，双向绑定，只能应用在表单类元素上，可简写为 `v-model="js表达式"` 
    - v-show="boolean表达式"：用于显示或隐藏，用display:none进行隐藏，节点存在
    - v-if/v-else-if="boolean表达式"/v-else：条件渲染，节点不存在
    - v-for：遍历
    - v-text：向其所在的节点中渲染文本内容
      - 与插值语法的区别：v-text会替换掉节点中的内容，{{xx}}则不会
    - v-html：向其所在的节点中渲染节点内容（会解析html标签）
    - v-cloak：本质是一个特殊指令，Vue实例创建完毕并接管容器后，会删掉v-cloak属性
      - css中用属性选择器[v-clock]{ display: none;}
    - v-once：v-once所在的节点在初次动态渲染后，就变为静态内容，可用于优化性能
    - v-pre：跳过其所在节点的编译过程，可用于优化性能



#### 代理

> 如何让对象中的属性值发生变化时，创建一个切入点，用于更新内容？

- Object.defineProperty(对象，对象中的属性，{配置对象})

  ```javascript
  let number = 18;
  let person = {
      name:"张三",
      gender:"男",
      //age: number // 如果这样写，只会在person被定义时age属性被赋值一次，之后number变化，person不变
  }
  
  Object.defineProperty(person, 'age', {
      value: number, // 这里也是一次赋值
      enumerable: true, // 控制属性是否可以被枚举，默认值为false
      writable: true, // 控制属性是否可以被修改，默认值为flase
      configurable: true, // 控制属性是否可以被删除，默认值为flase
  
      // 当有人读取person中的age属性时，get函数就会被调用，且返回值就是age的值
      get:function(){
          return number
      },
      // 当修改person中的age属性时，set函数（setter）会被调用，形参为修改的具体值
      set(val){
      	number = val;
  	}
  })
  ```

- 错误写法

  ```js
  let data = { name: "张三" }
  Object.defineProperty(data, 'name', {
      get() { return data.name },
      set(newName) {
          data.name = newName
          // 切入点，拓展点：在此处可调用更新 Dom 的操作
      }
  })
  ```
  
  但当读取、修改 data.name 时，会触发 get、set 方法，而 get、set 方法又回去 读取、修改 data.name，从而再触发 get、set 方法 ... ，最终导致内存溢出
  
- 数据代理

  - 通过一个对象代理另一个对象中属性的操作（读/写）

    ```js
    let obj = {x:100}
    let obj2 = {}
    Object.defineProperty(obj2, 'x', {
        get(){ return obj.x },
        set(value){
        	obj.x = value;
            // 拓展点
    	}
    })
    ```
  
  - Vue对象（vm）则代理data中的数据
  
  - Vue对象（vm）中的_data就是data
  
- 上述数据代理的应用（如果按上面的写法，需要手动为每一个属性创建get、set方法，如何解耦合）：Observer（见后文）



#### 绑定样式

```html
<div id='root'>
    <div class='basic' :class='classArr'></div>
    <div class='basic' :class='classObj'></div>
    <div class='basic' :style='styleObj'></div>
    <div class='basic' :style='[styleObj]'></div>
</div>

<script>
    new Vue({
        el:'#root',
        data:{
            classArr:['class1','class2','class3'],
            classObj:{
                'class1': false,
                'class2': true
            },
            styleObj:{
                fontSize: '40px'
            }
        }
    })
</script>
```





### 属性

#### 事件处理

- 指令：v-on:click="方法名"，可简写为@click="方法名"

  ```html
  <div id="root">
      <button v-on:click="fun1">一个按钮</button>
      <button v-on:click="fun2(88)">另一个按钮</button>
      <button v-on:click="fun3(88,$event)">还有个按钮</button>
  </div>
  
  <script type="text/javascript">
      Vue vm = new Vue({
          el:"#root",
          // data 中的数据、方法会进行数据代理
          data:{},
          // methods中的方法也会放到Vue对象中，但 methods 中的数据、方法不会进行数据代理
          methods:{
              fun1(event){
                  console.log(event.target.innerText); // event.target是事件源
                  console.log(this === vm); // true
                  console.log("被点击了");
              },
              fun2(number){}, // event会丢失
              fun3(number,event){}
          }
      })
  </script>
  ```

- methods中配置的函数不要用箭头函数，否则 this 就不是 vm 了

- methods中配置的函数中，this 的指向是 vm 或 组件实例对象

- 事件修饰符（如@click.prevent.stop）

  - prevent：阻止默认事件
  - stop：阻止事件冒泡
  - once：事件只触发一次
  - capture：事件在捕获阶段就处理（默认在冒泡阶段处理）
  - self：只有event.target是当前操作的元素时才触发事件（也能阻止冒泡）
  - passive：事件的默认行为立即执行，无需等待事件回调执行完毕
    - 如鼠标滚轮滚动时（事件：wheel），默认会先处理监听器中的函数，再响应页面滚动的效果，加上passive，则立即执行页面滚动

- 键盘事件

  - 别名，如@keyup.enter（也可以用按键原本的名字，如@keyup.Enter）
    - 回车 => enter（按键名字：Enter，按键编码：13）
    - 删除 => delete
    - 退出 => esc
    - 空格 => space
    - 换行 => tab（Tab键在点击的时候会切走焦点，所以只能用keydown捕获）
    - 上、下、左、右 => up、down、left、right
  - @keyup.ctrl.y：按下ctrl+y键才执行



#### 计算属性

```js
new Vue({
    el: '#root',
    data: {
        firstName: '张',
        lastName: '三'
    },
    // 也在Vue的对象中
    computed: {
        fullName: {
            // get什么时候调用：1.初次读取fullName时。 2.所依赖的数据发生变化时
            get(){
                // this指向是Vue的对象
                return this.firstName + "-" +this.lastName;
            }，
            // 如果需要改fullName的值，则添加set方法
            set(value){}
        }
    }
})
```

- 简写（只能读取，不能修改）

```js
computed:{
    fullName(){
        return this.firstName + "-" +this.lastName;
    }
}
```



#### 监视属性

- 当被监视的属性（可以是计算属性）发生变化时，回调函数自动调用，进行相关操作

```js
new Vue({
    el:'#root',
    date:{
        temperature:30
    },
    watch:{
    	temperature:{
    		immediate:true, //初始化时让handler调用一下
    		// handler用于监视指定属性有没有变化
    		handler(newValue, oldValue){
    			console.log(newValue-oldValue)
			}
		}
	}
    // 第二种
    //vm.$watch('temperature',{handler(){}})
})
```

- 深度监视

```js
new Vue({
    el:'#root',
    date:{
        numbers:{
            a:1,
            b:2
        }
    },
    watch:{
        // 监视a
        'numbers.a':{handler(){}},
        // 监视numbers下的全部属性
        numbers:{
            // 开启深度监视
            deep:true,
            handler(){}
        }
    }
})
```

- 简写，不能配置deep、immediate等属性

```js
watch:{
    temperature(){/*相关操作*/}
}
```

```js
vm.$watch('temperature',{/*相关操作*/})
```



#### 计算与监视的区别

- 在一些问题中，computed所得到的计算属性比watch的直观

- computed能完成的功能，watch都能完成

- watch能完成的功能，computed不一定能完成，如watch能进行异步操作

  ```js
  // 一秒后再设置fullName
  // 用watch可行
  watch:{
      firstName(newValue){
          /* 
            1 使用 箭头函数 或 普通函数 的根本目的是让this指向Vue的对象vm
            2 计时器中回调函数的调用者是js引擎，并不是vm管理
          	2.1 用箭头函数，该函数没有自己的this，所以this是外部函数的this，即vm
          	2.2 用普通函数，函数的this指向window
          */
          setTimeout(() => {
              fullName = this.newValue+"-"+this.lastName;
          },1000)
      }
  }
  // 用computed不可行
  computed:{
      fullName(){
          /*
            return 所返回的值实际上返回给 计时器 中回调函数的调用者，而不是作为计算属性的值
          */
          setTimeout(() => {
              return this.firstName+"-"+this.lastName;
          },1000)
      }
  }
  ```




#### 过滤器

```html
<!-- 管道：|符号前面的结果作为后面的参数 -->
<h3>现在是：{{time | timeFormat("YYYY-MM-DD")}}</h3>

<script>
    // 全局的过滤器，在new Vue前，第一个参数是过滤器方法名，第二个是过滤器方法体
    Vue.filter(str,fun(){})
    
	new Vue({
        el: '#root',
        data: {
            time: new Date;
        },
        // 局部过滤器
        filters{
        	// 设定：该方法用以解析long型Data
        	// 参数设置默认值，若未传递该参数，则使用默认值
        	timeFormater(value,str="YYYY年MM月DD日"){
        		return dayjs(value).format(str);
    		}
    	}
    })
</script>
```



#### 自定义指令

```js
// 全局指令
Vue.directive("指令名",{bind(){},inserted(){},update(){}})
Vue.directive("指令名",fun(){简写})

new Vue({
    el: '#root',
    data: {
        n: 1
    },
    // 局部指令，指令中方法的this都是window
    directives:{
        // 指令何时调用？ 1.指令与元素成功绑定时（初始化）2.指令所在的模板被重新解析时
        // 自定义一个放大数据的指令，完整写法
        big: {
            // 该方法在指令与元素成功绑定时调用
            bind(element, binding){ //element是绑定的元素，binding是绑定的相关信息
                element.value = binding.value * 10
            },
            // 该方法在指令所在元素被插入页面时调用
            inserted(element, binding){},
            // 该方法在指令所在的模板被重新解析时调用
            update(element, binding){
                bind(element, binding)
            }
        },
        // 自定义一个缩小数据的指令，简写。只有bind()+update()的效果
        small(element,binding){}
        // 多个单词用-隔开（kebab-case 和 camelCase），定义方法时必须补上引号
        'small-num'(element,binding){}
    }
})
```





### 基础

#### 列表渲染

- \<li v-for="(a, b) in obj" :key="b">\</li>

  - 遍历数组：v-for="(person, index) in persons" :key="person.id/index"
  - 遍历对象：v-for="(value, key) in person" :key="key"
  - 遍历字符串：v-for="(char, index) in str" :key="index"
  - 遍历指定次数：v-for="(number, index) in 5" :key="index"

- 问题

  ![](D:\picture\typora\java\que.jpeg)

- 说明

  ![](D:\picture\typora\java\sifneiijidfie.jpeg)



#### 监测数据原理

- Vue实现监听数据的简单实现

  ```java
  let data = {
      name:'张三',
      age:18
  }
  // 创建一个监视的实例对象，用于监视data中属性的变化
  const obs = new Observer(data)
  console.log(obs)
  
  // 准备一个vm实例对象
  let vm = {}
  vm._data = obs
  data = obs
  
  // Observer 的构造函数
  function Observer(obj){
      // 汇总对象中所有的属性形成一个数组
      const keys = Object.keys(obj)
      keys.forEach(k => {
          // this 是Observer的实例对象
          Object.defineProperty(this , k, {
              get(){
                  return obj[k]
              },
              set(value){
                  obj[k] = value
                  // 切入点、拓展点：可在此处更新 DOM 树
              }
          })
      })
  }
  ```

  - 不完善点：
    - vue对象 vm 除了有 vm.\_data 属性外；还存在 vm.name、vm.age 可直接修改 vm.\_data 中的值
    - 上述案例只对第一层属性创建了代理，若 data 包含引用属性（引用属性中又可能包含引用属性），还需要对它们进行**深层代理**
    - 无法监听数组的数据的变化

- 后添加属性
  - 问题：**后添加的属性没有响应式的方法**
  - vm.$set(target, key, value)：为后添加的属性提供响应式方法（getter和setter）
  - Vue.set(target, key, value)
    - target：是添加的位置（往哪个对象身上加属性 或 修改哪个数组中的数据）
    - key：属性名 或 数组的索引
    - value：属性值
  - **上述方法不能给 vm 和 vm._data 添加数据**（target不能是Vue实例，或Vue实例的根数据对象）
  
- 监测数组改变
  - Vue数组的改变不能用索引值的方式（即：`arr[2] = "王五"`），因为无法生成响应式方法（getter和setter）
  
  - 方法一：可用上述的 **后添加属性** 的方法进行改变
  
  - 方法二：
  
    Vue对**被侦听的数组的变更方法进行了包裹**（增强了数组的部分原始方法），使其在实现原本效果的同时，更新DOM树
  
    - `push(item1, item2, ...)`：此方法用于将一个或多个元素添加到数组的末尾，并返回新的数组长度。
  
    - `pop()`：此方法用于删除并返回数组的最后一个元素。
  
    - `shift()`：此方法用于删除并返回数组的第一个元素。
  
    - `unshift(item1, item2, ...)`：此方法用于将一个或多个元素添加到数组的开头，并返回新的数组长度。
  
    - `splice()`：此方法用于通过删除、替换或添加元素来更改数组，并返回被删除的元素数组。
  
      - `start`：开始修改的位置。如果是负数，则从数组末尾开始计数。
      - `deleteCount`（可选）：要删除的元素数量。如果省略或大于 `start` 之后的元素数量，则删除 `start` 之后的所有元素。
      - `item1, item2, ...`（可选）：要添加到数组的元素。如果不指定，则 `splice()` 只删除元素。
  
      ```javascript
      let arr = [1, 2, 3, 4, 5];
      // 从索引 1 开始，删除 1 个元素，然后添加 'a' 和 'b'
      removed = arr.splice(1, 1, 'a', 'b');
      console.log(removed); // 输出：[2]
      console.log(arr); // 输出：[1, 'a', 'b', 3, 4, 5]
      ```
  
    - `sort()`：此方法用于对数组元素进行排序，并返回排序后的数组。
  
    - `reverse()`：此方法用于反转数组中元素的顺序，并返回反转后的数组。



#### v-model的使用

- 收集表单数据
  - \<input type="text"/>，则v-model收集的是value值，用户输入的就是value值
  - \<input type="radio" name="同一类" value="str"/>，则v-model收集的是value值
  - \<input type="checkbox">
    - 没有配置input的value属性，那么收集的就是checked状态（boolean）
    - 配置了value值
      - 初始值是非数组，那么收集的就是checked状态（boolean）
      - 初始值是数组，那么收集的就是value组成的数组
- v-model的三个修饰符
  - lazy：失去焦点时再收集数据
  - number：只能输入数字（字符串会强转为数值）
  - trim：过滤首位空格



#### 生命周期

```js
new Vue({
    el: '#root',
    data: {},
    // 生命周期函数的this指向vm
    // 常用的生命周期函数
    mounted(){
        // 发送ajax请求，启动定时器，绑定自定义事件，订阅消息等【初始化操作】
    },
    beforeDestroy(){
        // 清除定时器，解绑自定义事件，取消订阅消息等【收尾工作】
    }
})
```

![生命周期](D:\picture\typora\java\生命周期.png)





#### 组件定义

##### 快速入门

- 组件定义

```js
// 第一句可简写成 const stu = {...}
const stu = Vue.extend({
    // 可以定义组件在vue开发者工具中的名字
    name: 's',
    template:`<div>姓名：{{name}}<br>年龄：{{age}}<br>学校：<School></School></div>`,
    // 组件中data要用方法的格式，因为对象是传递地址值
    data(){
        return {
            name: '张三',
            age: 18
        }
    },
    // 这里可以注册已定义的组件，实现组件嵌套
    //components: {
    //    School: sch
    //}
})
```

- 添加组件

```js
// 全局注册
Vue.component("组件名",组件)
// 局部注册
new Vue({
    el: '#root',
    data(){return {}},
    components: {
        /* 命名风格：
           1.单个单词：默认报错
           2.多个单词：各字母小写，并用'-'连接（kebab-case）
         */
        Student: stu
    }
})
```

- 使用组件

```html
<Student></Student>
<Student/><!--需要Vue脚手架的支持-->
```



##### VueComponent

- student 组件本质是一个名为 VueComponent 的**构造函数**，且不是程序员定义的，是 `Vue.extend()` 生成的

- 我们只需要写 `<student/>` ,Vue解析时会帮我们创建student的实例对象，即Vue帮我们执行了：`new VueComponent(options)`

- **特别注意**：每次调用 `Vue.extend()`，返回的都是一个全新的 VueComponent

  以java类比，每次写 `<student/>`，都会执行 `new VueComponent`，但是这个 `VueComponent` 不是却同一个类

- **关于 `this` 的指向**

  - 组件配置中，this 的指向都是【VueComponent的实例对象】
  - `new Vue(options)` 中，this 的指向都是 【Vue实例对象】

- vc与vm类似，但vc没有el属性

  ```js
  function Demo() { // Demo 的构造函数
      this.a = 1
      this.b = 2
  }
  
  Demo.prototype          // 显示原型属性
  new Demo().__proto__    // 隐式原型属性
  Demo.prototype === new Demo().__proto__  // 显示/隐式原型属性 指向同一个对象（原型对象）
  ```
  
  ```java
  class Children extends Parent {
      public Children() {
          super(); // 创建子类对象时，会先创建父类对象。 js原型对象 <=> 父类对象
      }
  }
  ```
  
  ![vc与vm的关系](D:\picture\typora\java\vc与vm的关系.jpeg)





### VueCLI

http://cli.vuejs.org/zh

#### 快速入门

- 安装npm（安装 node.js 即可安装npm）

- 全局安装VueCLI 

  ```shell
  # 切换 npm 下载地址为国内地址
  npm config set registry https://registry.npmmirror.com
  
  npm install -g @vue/cli
  ```

- 切换到要创建项目的目录

  ```shell
  # -n 表示不用git管理
  vue create xxxx -n
  ```

- 启动项目（需先进入 vue 项目根目录）

  ```shell
  # 运行 vue 服务
  npm run serve
  # 生成 html
  npm run build
  ```

- 结构

  ```cmd
  ├── node_modules
  ├── public
  │   ├── favicon.ico: 页签图标
  │   └── index.html: 主页面
  ├── src
  │   ├── assets: 存放静态资源
  │   │   └── logo.png
  │   │── component: 存放组件
  │   │   └── HelloWorld.vue
  │   │── App.vue: 汇总所有组件
  │   └── main.js: 入口文件
  ├── .gitignore: git 版本管制忽略的配置
  ├── babel.config.js: babel 的配置文件
  ├── package.json: 应用包配置文件
  ├── README.md: 应用描述文件
  ├── package-lock.json：包版本控制文件
  └── vue.config.js：修改vue核心配置的文件
  ```

- render函数

  ```js
  // vue.js 是一个完整版的Vue，包含：核心功能 + 模板解析器
  // 此处引入的vue是一个精简的vue，没有模板解析器. vue/dist/vue.runtime.esm.js
  import Vue form 'vue' 
  import App from './App.vue'
  // 关闭 Vue 的生产提示
  vue.config.productionTip = false
  
  new Vue({
      el: '#root',
      // 因为vue.runtime.xxx.js没有解析器，所以不能使用template配置项，
      // 需要使用render函数接收到 createElement 函数去指定具体内容
      // render: h => h(App),
      render(createElement){
          return createElement(App)
      }
  })
  ```

- 修改vue的配置

  - 使用命令 `vue inspect > output.js` 可以且只能 **查看** vue脚手架的默认配置

  - 如何修改配置：在与src的同级目录中创建 vue.config.js 文件，在其中添加配置

    ```js
    // vue.config.js
    module.exports = {
        //关闭语法检查（检查内容包括，定义一个变量，未使用，报错）
        lintOnSave: false,
        
        /*
         * 该值用于配置，webpack打包后，资源访问的路径
         *   它也会影响到打包后生成的文件内资源的访问，如配置 "/assets/" ，
         *       css 就会变成 "background: url('/asserts/icon.png')"
         *   默认值是 ""，表示所有资源都放在根目录 / 下，
         *       这种方式适用于生产环境（生产环境静态资源会放在根目录），本地则一般无法正常显示
         *   因此，测试下，若想要打包后的 html 在本地运行，须配置成相对路径 "./"
         */
        publicPath: './'
    }
    ```
  
  - 具体配置见：https://cli.vuejs.org/zh/config/




#### ref

- 被用来给元素或子组件注册引用信息

- 应用在html标签上获取的是真实Dom元素，应用在组件标签上获取的是组件实例对象（vc）

  ```vue
  <template>
  <div>
      <h1 ref="test">测试src</h1>
      <time-table id="timetable" ref="timetable"></time-table>
      <button @click="showDom">点我输出上方元素</button>
  </div>
  </template>
  
  <script>
      import TimeTable from "@/components/timetable";
  
      export default {
          name: 'App',
          components: {
              TimeTable
          },
          methods: {
              showDom() {
                  console.log(this.$refs.test) // 输出的是Dom元素
                  console.log(this.$refs.timetable) // 输出的是vc
                  console.log(document.querySelector("#timetable")) // 该组件的根元素
              }
          }
      }
  </script>
  ```
  
  

#### props

> 如何让组件接收外部传入的数据

- 设置参数

  ```vue
  <template>
  <div>
      学生姓名：{{name}} <br/>
      学生年龄：{{age}}
  </div>
  </template>
  
  <script>
      export default{
          name: 'StudentComponent',
          props: ... // 设置需要的参数
      }
  </script>
  ```

  - 简单声明接收

    ```json
    props: ['name', 'age', 'getName']
    ```

  - 对传入该组件的参数进行**类型限制**

    ```json
    props: {
        name: String,
        age: Number,
        getName: Function
    }
    ```

  - 必要参数、默认值的设定

    ```json
    props: {
        name: {
            type: String,
            required: true // name是必要的
        },
        age: {
            type: Number,
            default: 99 // 默认值
        },
        getName: {
            default: () => console.log("App未获得姓名")
        }
    }
    ```

  - 集合数组类型需要利用方法返回

    ```json
    data() { return {...} }
    props: {
        items: {
            type: Array,
            // 需要利用方法返回的原因，和 data 数据用方法返回原因一样：防止传输地址值
            default: () => ['a', 'b']
        }
    }
    ```

- 传入参数

  ```vue
  <template>
  <div>
  <!-- 只需要在组件上添加同名属性，即可完成赋值 -->
  <!-- 通过v-bind引号中为js表达式这个特性，
       解决传递Number或其他对象变成字符串的问题 -->
  <student-component name="zhangsan" :age="18" :getName="getName"></student-component>
  </div>
  </template>
  
  <script>
      import StudentComponent from '@/components/student';
  
      export default {
          name: 'App',
          components: {
              StudentComponent
          },
          method: {
              getName(name) {
                  console.log("App收到学生姓名："+name)
              }
          }
      }
  </script>
  ```

- props中的属性 **优先** 被放到vm、vc上（优先级比data中定义的属性高），所以可以在data中通过 `this.属性` 获得

- 一般不会去修改 props 中传入到该组件的值，虽然可能会修改成功，但vue会警告，可通过以上特性：

  ```js
  export default {
      data() {
          return () {
              myAge: this.age // 一般不修改 this.age，可以修改 this.myAge
          }
      },
      props: ['age']
  }
  ```

  



#### mixin

> 多个组件共享一个配置

- 定义共享配置

  ```js
  // 定义文件如 mixin.js
  export const mixin =  {
      data() {
        return {
            name: "李四"
        }  
      },
      methods: {
          showName() {
              alert(this.name)
          }
      }
  }
  ```

- 使用

  ```js
  import {mixin} from "../mixin";
  
  export default {
    data() {
      return {
        name: '张三',
        age: 18
      }
    },
    mixins: [mixin]
  }
  ```

- data中的数据、methods中的方法，与vc中的发生冲突时，**以vc为主**。而**生命周期钩子函数都会执行**

- 全局混入：`Vue.mixin({...})`



#### 插件

- 定义插件

  ```js
  // 定义文件如 plugin.js
  export default {
      // 可以带其他参数
      install(Vue, [args...]) {
          // 给Vue原型上添加方法，vue实例可以使用方法
          Vue.prototype.oneMethod = () => {}
          // 定义全局过滤器
          Vue.filter()
          // 定义全局指令
          Vue.directive()
          // 定义全局混入
          Vue.mixin({})
      }
  }
  ```

- 使用插件

  ```js
  import Vue from 'vue'
  import plugin from './plugin'
  
  Vue.use(plugin)
  ```



#### 样式

```vue
<template></template>
<script></script>
<style scoped lang="less"></style>
```

- scoped：解决不同组件样式层叠问题

- lang：样式语言

  - css：默认值
  - less：默认不支持，需要下载 less-loader

- less-loader 下载（less-loader 与 vue中使用的webpack有版本对应关系）

  - 查看vue中使用的 webpack 版本

    - 查看脚手架中webpack的版本：node_modules/webpack/package.json 文件中配置了webpack版本信息
    - 查看某个框架的所有版本：`npm view xxx versions`

  - 查找 webpack 与 less-loader 的对应关系

  - 安装脚手架中webpack版本对应的less-loader版本：

    - 下载最新版本：`npm i less-loader`

    - 下载指定版本：`npm i less-loader@10`
      - `@10` 表示安装大版本10下的最新小版本





### 组件间通信

#### 自定义事件

> 父组件向子组件传输数据可以用 props
>
> **子组件如何向父组件传输数据呢？**

- 父组件向子组件传输回调函数，子组件将数据放到回调函数的参数中，实现数据传输

  - 父组件

    ```js
    export default = {
        name: 'ParentComponent',
        methods: {
            // 父组件定义回调函数
            getMsg(msg) {
                console.log("收到消息："+msg)
            }
        },
        components: {
            ChildComponent: child
        }
    }
    ```

    ```html
    <!-- 父组件将回调函数传给子组件 -->
    <child-component  :sendToParent="getMsg" />
    ```

  - 子组件

    ```js
    export default = {
        name: 'ChildComponent',
        porps: {
            // 子组件声明接收一个回调函数
            sendToParent: {
                type: Function,
                required: true
            }
        },
        methods: {
            send() {
                this.sendToParent("Hello") // 发送消息给父组件
            }
        }
    }
    ```

- 自定义事件

  - 在父组件中，为子组件绑定事件（在元素身上绑定）

    ```html
    <!-- 在子组件对象（vc）身上绑定事件 myEvent，还可以使用 .once 指定只触发一次 -->
    <child-component  v-on:myEvent='getMsg'/>
    ```

    ```js
    export default {
        name: 'ParentComponent',
        methods: {
            // 父组件定义事件触发后的回调函数，可以接受子组件触发事件是携带的参数
            getMsg(msg) {
                console.log("收到数据："+msg)
            }
        },
        components: {
            ChildComponent: child
        }
    }
    ```

  - 子组件触发事件

    ```js
    export default = {
        name: 'ChildComponent',
        methods: {
            send() {
                // 触发 Student 组件实例身上的 myEvent 事件，携带数据 msg
                this.$emit('myEvent', msg)
            }
        }
    }
    ```

  - 另一种绑定方式（编程式绑定，更加灵活）

    ```js
    export default {
        name: 'ParentComponent',
        methods: {
            // 父元素定义事件触发后的回调函数
            getMsg(msg) {
                console.log("收到数据："+msg)
            }
        },
        mounted() {
            // 先获取 子元素实例（vc），在通过 $on 方法绑定事件
            this.$ref.student.$on('myEvent', this.getMsg)
        }
    }
    ```

    - 注意事项：通过 `$on("事件名", 回调函数)` 绑定自定义事件时，为保证**回调函数**中的 `this` 指向的是当前 vc，要么使用 `methods` 中定义的函数（如上），要么使用 **箭头函数**
    
  - 解绑事件
  
    ```js
    // ChildComponent
    this.$off('myEvent')
    this.$off(['myEvent', 'myEvent2'])
    this.$off() // 解绑所有自定义事件
    
    // 调用生命周期函数，销毁当前组件的实例
    // 销毁后，所有当前组件实例的自定义事件和子组件全部销毁
    this.$destory()
    ```
  
- 原生事件

  - 错误写法

    ```html
    <!-- 虽然原生事件中有 click，但是vue依旧会认为这是自定义事件，需要用 $emit 触发 -->
    <child-component @click="show"></child-component>
    ```

  - 原生事件

    ```html
    <child-component @click.native="show"></child-component>
    ```






#### 全局事件总线

> 现在，可以实现下面两个功能：
>
> - 父组件给子组件传输数据：子组件设置 props
> - 子组件给父组件传输数据：
>   - 子组件 props 中要求一个父组件的回调函数
>   - 自定义事件
>
> 那是否可以利用 自定义事件 实现全局组件通信呢

- 原理
  - 找一个 <font color=blue>可以绑定自定义事件^[条件1]^</font> 且 <font color=blue>所有 VueComponent 实例都能访问到^[条件2]^</font> 的对象X
  
    - [条件1]
  
      可以绑定自定义事件，说明该对象 X 是 Vue 的实例（java类比：VueComponent 继承自 Vue）
  
      ```js
      let VueComponent = Vue.extend({})
      let x = new VueComponent()
      ```
  
    - [条件2]
  
      - 可以将对象绑定到 window 身上（即成为该对象的属性）
  
      - 可以将对象绑定到 `VueComponent.prototype`、`vc.__proto__` 身上么？
  
        **不行**，每一个 VueComponent 都是 `Vue.extend()` 生成的，并不是同一个对象
  
      - 可以将对象绑定到 `Vue.prototype` 身上
  
        `Vue.prototype == VueComponent.prototype.__proto__`
  
        ```js
        Vue.prototype.x = x
        ```
  
  - 若 A 需要 B 的信息
  
    - A 就可以为 X 绑定事件，并在 A 中指定回调函数
    - B 触发 X 的对应事件，并携带数据
  
- 实现

  - 创建对象 X（可在 main.js 中创建）

    ```js
    // 方法一：可以在这里按上面写法
    
    new Vue({
        el: "#app",
        render: h => h(App),
        beforeCreate() {
            Vue.prototype.$bus = this // 方法二：规范写法，vm 充当 X ，X 命名为 $bus
        }
    })
    ```

  - A 绑定事件

    ```js
    export default {
        name: "A",
        mounted() { // 挂载时绑定事件
            this.$bus.$on('myEvent', (data) => {
                console.log('我是 A，收到数据：', data)
            })
        },
        beforeDestory() { // 最好在销毁时，解绑事件
            this.$bus.$off('myEvent')
        }
    }
    ```

  - B 触发事件

    ```js
    export default {
        name: "B",
        methods: {
            sendMsgToA() {
                this.$bus.$emit("myEvent", "Hello, It is B.")
            }
        }
    }
    ```

    




#### pubsub-js

> 一个第三方消息发布订阅库

- 安装

  ```shell
  npm -i pubsub-js
  ```

- 引入

  ```js
  import pubsub from 'pubsub-js'
  ```

- 发布消息

  ```js
  export default {
      name: "A",
      mounted() {
          this.pubId = pubsub.subscribe('hello', (msgName, data) => {
              console.log("第一个参数是消息名：", msgName)
              console.log("第二个参数才是数据：", data)
          })
      },
      beforeDestory() {
          pubsub.unsubscribe(this.pubId) // 通过 id 取消订阅
      }
  }
  ```

- 订阅消息

  ```js
  methods: {
      sendMsg() {
          pubsub.publish('hello', '这是数据')
      }
  }
  ```



#### 插槽

> 也算一种特殊的父给子传输数据（结构）的通信

- 默认插槽

  - 子组件定义插槽

    ```vue
    <template>
    <div>
        <h3>{{title}}分类</h3>
        <!-- 使用者（父组件）可以将一些具体结构传入到 slot 插槽标签中 -->
        <slot>可以在这里设置默认值，当使用者没有传递具体结构时，会出现</slot>
    </div>
    </template>
    ```

  - 父组件 往子组件中传入具体结构

    ```vue
    <template>
    <child-component title="美食">
        <ul>
            <li v-for='(item, index) in foods' :key="index">{{item}}</li>
        </ul>
    </child-component>
    </template>
    
    <script>
        import child from './Child'
        export default {
            name: 'ParentComponent',
            data() {
                return {
                    foods: ['火锅', '烧烤', '牛排']
                }
            },
            components: { ChildComponent: child }
        }
    </script>
    ```

  - 对插槽内具体结构的css样式，既可以放在父组件中，也可以放在子组件中

- 具名插槽：用于区分多个插槽

  - 子组件定义具名插槽

    ```vue
    <template>
    <div>
        <h3>{{title}}分类</h3>
        <!-- 通过 name 属性，为插槽命名 -->
        <slot name="content">可以在这里设置默认值，当使用者没有传递具体结构时，会出现</slot>
        <slot name="footer"></slot>
    </div>
    </template>
    ```

  - 父组件传入结构

    ```vue
    <child-component title="美食">
        <!-- 通过 slot 指定往哪个插槽中传入结构 -->
        <ul slot="content">
            <li v-for='(item, index) in foods' :key="index">{{item}}</li>
        </ul>
        
        <!-- 为少些几遍slot，且不生成真实 DOM 元素，可使用 template 标签 -->
        <template slot="footer"> <!-- template+slot 可简写为 <template v-slot:footer> -->
            <a href="https://passport.meituan.com">去美团</a>
            <a href="https://www.ele.me">去饿了么</a>
        </template>
    </child-component>
    ```

- 作用域插槽：适用于父组件想要往子组件中插入结构，但数据在子组件中

  - 子组件将数据传给使用者

    ```vue
    <template>
    <div>
        <h3>{{title}}分类</h3>
        <!-- 在插槽身上加入属性，即可将数据传给使用者 -->
        <slot :foodList="foods" msg="可以传输多个数据" ></slot>
    </div>
    </template>
    
    <script>
        export default {
            name: 'ChildComponent',
            props: ['title'],
            data() {
                return {
                    foods: ['火锅', '烧烤', '牛排']
                }
            }
        }
    </script>
    ```

  - 父组件获取数据并应用

    ```vue
    <child-component title="美食">
        <!-- 接收数据时，必须使用 template 标签，通过 scope/slot-scope 属性接取数据对象 -->
        <!-- <template scope="{foodList, msg}">  解构赋值 -->
        <template scope="data">
            <ul slot="content">
                <!-- 数据是对象，使用时需要 对象.属性 -->
                <li v-for='(item, index) in data.foodList' :key="index">{{item}}</li>
            </ul>
        </template>
    </child-component>
    ```

    



### 补充

#### 动画与过渡

[略](https://www.bilibili.com/video/BV1Zy4y1K7SH/?p=91)



#### 零散

- $nextTick

  ```js
  vm.$nextTick(() => {
      // 在 DOM 更新结束后，再执行回调
  })
  ```






### Vuex

> Vue2 使用的是Vuex3
>
> Vue3 使用的是Vuex4
>
> > 安装指定版本 vuex：`npm i vuex@3`

#### 使用Vuex

- 安装 Vuex

- Vuex 三大组件

  ![vuex](D:\picture\typora\前端\vuex.png)

  - 理解：
    - Actions：对应后端的服务层（service）
    - Mutations：对应后端的持久层（Dao），直接操作数据库中的数据
    - State：对应后端的数据库，区别在于存在 State 中的数据，也会被进行数据代理（类似于data中的数据），变动时，页面后重新渲染

- 创建 store 对象，规范：在src目录下创建文件 src/store/index.js

  ```js
  import Vue from 'vue'
  import Vuex from 'vuex'
  Vue.use(Vuex) // 应用 Vuex 插件必须在创建 store 对象前
  
  const actions = {}
  const mutations = {}
  const state = {}
  const getters = {} // 也是一个组件，可以理解为是对 state 里的数据的映射（可以不使用）
  
  // 创建并保留 store
  export default new Vuex.Store({
      actions,
      mutations,
      state,
      getters
  })
  ```

- 应用 Vuex 插件，并添加 store（main.js中）

  ```js
  import Vue from 'vue'
  import App from './App.vue'
  import store from './store'
  
  Vue.config.productionTip = false
  // 应用 Vuex 插件必须在创建 store 对象前
  // 也不能将 'import store ....' 放到use后面，因为 import 会被提前
  //Vue.use(Vuex)
  
  new Vue({
      render: h => h(App),
      store
  }).$mount('#app')
  ```



#### 快速使用

- 定义各Vuex组件内容

  ```js
  const actions = {
      increase(miniStore, value) {
          // miniStore 是"简化版"的store，里面有 dispatch、commit 方法 和 getters，规范命名为：context
          miniStore.commit('INCREASE', value)
      }
  }
  
  const mutations = {
      INCREASE(state, value) { // mutations 中的方法一般大写
          state.sum += value
      }
  }
  
  const state = {
      sum: 0
  }
  
  // 也是一个组件，可以理解为是对 state 里的数据的映射（可以不使用）
  // 获取时： this.$store.getters.isOdd
  const getters = {
      isOdd(state) {
          return !!(state.sum % 2)
      }
  }
  ```

- 各组件中使用 Vuex

  ```js
  export default {
      name: "OneComponent",
      methods: {
          increment() {
              // 分发到 "业务层"，让 "业务层" 调 "持久层"
              this.$store.dispatch('increment', 1)
              // 也可以直接提交到 "持久层"
              this.$store.commit('INCREMENT', 1)
          }
      }
  }
  ```

- mapXxx

  - mapState：可直接读取 State 中的数据，可借助它简化开发

    ```js
    import {mapState} from 'vuex' // 导入 mapState
    export default {
        name: "OneComponent",
        computed: {
            // 如果想在使用 state 中数据，又不想每次都写 this.$store.state.xxx，
            // 可以将其放在计算属性中，但是放在计算属性中，也需要写一次 这个长串
            sum(){ return this.$store.state.sum },
            其他,
            
            // mapState 可以直接从 state 中读取值，借助 es6 语法，可以简化书写（对象写法）
            ...mapState({sum: 'sum', 其他}),
            
            // 若计算属性和 state 中的数据同名，可用 数组写法 进一步简化代码
            ...mapState(['sum', 其他])
        }
    }
    ```

  - mapGetters：可直接读取 Getters 中的数据，使用方法和 mapState 类似

  - mapMutations：简化 **commit** 方法的调用（**该方法一般在 actions 中使用，此处是在组件中使用**）

    ```js
    export default {
        name: "OneComponent",
        methods: {
            ...mapMutations({increment: 'INCREMENT', 其他}),
            ...mapMutations(['INCREMENT']) // 调用 methods 中的方法时，也需要使用大写
        }
    }
    ```

    - `...mapMutations({increment: 'increment', 其他})` 这种写法生成的方法是这样的：

      ```js
      increment(value) {
          this.$store.commit('INCREMENT', value)
      }
      ```

    - 但在上述案例中，我们需要的方法是这样的

      ```js
      increment() {
          this.$store.commit('INCREMENT', 1)
      }
      ```

    - 所以，如果使用 mapMutations 简化代码，可能需要改动部分逻辑，如

      ```html
      <button @click="increment">点我加1</button>
      ```

      改写为

      ```html
      <button @click="increment(1)">点我加1</button>
      ```

  - mapActions：简化 **dispatch** 方法的调用（**该方法一般在组件中使用**），使用方法与 mapMutations 类似





#### 模块化

- 独立出配置

  src/store/oneOptions.js

  ```js
  export default {
      actions: {},
      mutations: {},
      state: {
          x: 1 // 若 oneOption 的 state 中有一个数据 x=1
      },
      getters: {}
  }
  ```

- 加载配置

  ```js
  import oneOptions from ./oneOptions
  import anotherOptions from ./anotherOptions
  
  export default new Vuex.store({
      modules: {
          oneAbout: oneOptions,
          anotherAbout: anotherOptions
      }
  })
  ```

- 使用：模块化后，store 身上将不会直接携带数据，而是携带上面定义的两个模块对象，模块对象中封装着各模块的数据

  **可以在控制台打印 this.$store 来观察store中数据的存放位置**

  - 普通使用：

    - state: `this.$store.state.oneAbout.x`
    - dispatch: `this.$store.dispatch('anotherAbout/increment',  1)`（用 **/** 分隔模块与方法）
    - commit: `this.$store.commit('anotherAbout/INCREMENT',  1)`（用 **/** 分隔模块与方法）
    - getters
      - 错误写法：`this.$store.getters.oneAbout/bigX`（`oneAbout/bigX` 虽然是属性，但存在 `/` ，不能使用 `变量.属性`）
      - 正确写法：`this.$store.getters['oneAbout/bigX']`（读取属性的另一种方式 `变量['属性名']`）

  - 使用 mapXxx 简化书写

    ```js
    ... mapState['oneAbout', 'anotherAbout']
    ```

    - 使用时，仍需要通过 `oneAbout.x` 才能访问到对应数据

- namespace简化 `oneAbout.x` 中的 `oneAbout.`

  - 开启命名空间

    ```js
    const oneOption = {
        namespaced: true, // 开启命名空间
        actions: {},
        ...
    }
    ```

  - 使用 mapXxx

    ```js
    // mapState(模块名, 从该模块中取的数据)
    ...mapState('oneAbout', ['x'])
    ...mapMutations('anotherAbout', {increment: 'INCREMENT'})
    ```

    



### 路由
















# Canvas



## 基础使用

- 基础图形

  - 绘制矩形 并 填充

    ```js
    ctx.fillRect(100, 100, 100, 100)
    ```

  - 绘制矩形 并 显示路径

    ```js
    ctx.strokeRect(300, 100, 100, 100)
    ```

  - 逐渐擦除

    ```js
    ctx.fillRect(100, 250, 100, 100)
    let startPoint = {width: 105, height: 255}
    let clearRange = {width: 90, height: 90}
    let nowClear = {width: 0, height: 0}
    let WtoH = clearRange.width/clearRange.height
    ctx.timeout = setInterval(() => {
        if (nowClear.width>=clearRange.height) {
            clearInterval(ctx.timeout)
        }
        nowClear = {width: nowClear.width+=WtoH, height: nowClear.height+=1}
        ctx.clearRect(startPoint.width, startPoint.height, nowClear.width, nowClear.height)
    }, 10)
    ```

  - 绘制线段

    ```js
    ctx.beginPath()
    ctx.moveTo(500, 100)
    ctx.lineTo(600, 350)
    ctx.lineTo(500, 350);
    ctx.lineTo(500, 100)
    ctx.stroke()
    ctx.closePath()
    ```

- 绘制圆弧

  - 根据圆心

    ```js
    ctx.beginPath() // 下笔
    // 参数：圆心x，圆心y，半径，起始角度，终止角度，默认顺时针：false
    ctx.arc(350, 300, 50, 0, Math.PI/2, true) 
    ctx.stroke()
    ctx.closePath() // 提笔
    ```

  - 三点绘制圆弧

    ```js
    ctx.beginPath()
    ctx.strokeStyle = "red"
    ctx.moveTo(500, 100)
    // 参数：第二个点x，第二个点y，第三个点x，第三个点y，半径
    ctx.arcTo(600, 350, 500, 350, 40)
    ctx.stroke()
    ctx.closePath()
    ```

- svg、Path2D

  ```js
  // 方法一
  let polyline = new Path2D("M100 30 h500 v20 h-500 z")
  ctx.stroke(polyline)
  
  // 方法二
  let onePath = new Paht2D()
  onePath.moveTo(x, y)
  // 二次贝塞尔曲线（两个控制点，一个终点）
  onePath.bezierCureTo(x1, y1, x2, y2, x3, y3)
  ```
  





## SVG

### 基础使用

```html
<svg width="100" height="100" viewBox="25, 25, 50, 100" fill="skyblue"
     style="border:1px solid black">
    <circle r="50" cx="50" cy="50"/>
</svg>
```

- width、height="宽/高"：指定SVG图像在HTML中所占据的宽高。如果不指定这两个属性，默认未300像素（宽）*150像素（高）

- viewBox="坐标x, 坐标y, 宽, 高"

  ```html
  <svg width="300" height="300" viewBox="0, 0, 150, 75" fill="skyblue"
       style="border:5px solid red">
      <circle r="150" cx="150" cy="150"/>
  </svg>
  <svg width="300" height="300" viewBox="0, 0, 450, 600" fill="skyblue"
       style="border:5px solid blue">
      <circle r="150" cx="150" cy="150"/>
  </svg>
  <svg width="300" height="150" viewBox="225, 225, 25, 150" fill="skyblue"
       style="border:5px solid green">
      <circle r="150" cx="150" cy="150"/>
  </svg>
  ```

  ![SVG的viewBox属性](D:\picture\typora\前端\SVG的viewBox属性.png)

- css属性

  - fill=none ：填充颜色
  - stroke=rgb() ：描边色
  - stroke-width：边框宽度



### 常用标签及属性

- 通用属性
  - fill="none"：不填充颜色
  - stroke="black"：描边色为黑色
  - stroke-width="10"：边框宽度为10

- \<circle>：圆
  - cx="30"：圆心横坐标为30
  - cy="30"：圆心纵坐标为30
  - r="25"：半径为25
- \<line>：线段
  - x1="0" y1="0"：起点为坐标 (0, 0)
  - x2="200" y2="300"：终点坐标为 (200, 300)
- \<polyline>：折线
  - point="3, 3   30, 28   18, 55"：用多个点指定一条折线





## 填充与颜色

- 颜色线性渐变

  - ctx.createLinearGradient(左上角点x，左上角点y，右下角点x，右下角点y)

  - 实例

    ```js
    let i = 0
    let flag = true
    let render = () => {
        ctx.clearRect(100, 400, 100, 100)
        if (flag) i += .005
        else i-=.005
        if (i>.99) flag = false
        if (i<.01) flag = true
        // 参数：左上角点x，左上角点y，右下角点x，右下角点y
        let canvasGradient = ctx.createLinearGradient(100, 400, 200, 500)
        canvasGradient.addColorStop(0, "yellow")
        canvasGradient.addColorStop(i, "rgb(183,218,136)")
        canvasGradient.addColorStop(1, "aqua")
        ctx.fillStyle = canvasGradient
        ctx.fillRect(100, 400, 100, 100)
        requestAnimationFrame(render)
    }
    requestAnimationFrame(render)
    ```

- 颜色径向渐变

  - ctx.createRadialGradient(圆心1x, 圆心1y, 半径1, 圆心2x, 圆心2y, 半径2)

  - 实例

    ```js
    ctx.beginPath()
    let radialGradient = ctx.createRadialGradient(350, 450, 0, 350, 450, 100);
    radialGradient.addColorStop(0, "yellow")
    radialGradient.addColorStop(1, "aqua")
    ctx.fillStyle = radialGradient
    ctx.arc(350, 450, 50, 0, 2*Math.PI)
    ctx.fill()
    ctx.closePath()
    ```

- 圆锥渐变

- 印章

  - 实例

    ```js
    let img = new Image()
    img.src = "./images/oneImage.png"
    
    img.onload = () => {
        // img 可以是 Image对象 或 Canvas对象
        let pattern = ctx.createPattern(img, "repeat")
        ctx.fillSytle = pattern
        ctx.fillRect(0, 0, 600, 400)
    }
    ```

  - 填充方式

    - repeat：水平垂直重复
    - no-repeat：不重复
    - repeat-x、repeat-y：定向重复
  
- 合成：ctx.globalCompositeOperation

  - 步骤

    - 绘制图片1
    - 设置 globalCompositeOperation
    - 绘制图片2（图片2根据设置值与图片1进行合成）

  - 参数

  - 实例：刮刮乐

    ```html
    <div class="guaguale">
        感谢参与
        <canvas id="guaCanvas" width="400" height="200"></canvas>
    </div>
    ```

    ```css
    .guaguale {
        position: relative;
        width: 400px;
        height: 200px;
        font: normal bold 2em 楷体;
        line-height: 200px;
        text-align: center;
    }
    #guaCanvas {
        position: absolute;
        top: 0;
        left: 0;
    }
    ```

    ```js
    let guaCanvasEle = document.querySelector("#guaCanvas");
    let guaCtx = guaCanvasEle.getContext("2d");
    guaCtx.fillStyle = "rgb(204,204,204)"
    guaCtx.fillRect(0, 0, 400, 200)
    // 设置合成样式
    guaCtx.globalCompositeOperation = "destination-out"
    let canDraw = false
    guaCanvasEle.onmousedown = () => canDraw = true
    guaCanvasEle.onmouseleave = () => canDraw = false
    guaCanvasEle.onmouseup = () => canDraw = false
    guaCanvasEle.onmousemove = (event) => {
        if (!canDraw) return
        guaCtx.beginPath()
        guaCtx.arc(event.offsetX, event.offsetY, 30, 0, 2*Math.PI)
        guaCtx.fill()
        guaCtx.closePath()
    }
    ```






## 属性

- 线条宽度：lineWidth

- 线条端点样式：lineCap

  - butt：平齐（默认）
  - round：半圆突出
  - square：方形突出

- 线段连接处样式：lineJoin

  - miter：填充外侧连接（默认）
  - round：圆润连接
  - bevel：磨平

- 斜接延伸限制：miterLimit = 数值

- 虚线：ctx.setLineDash( [ 虚线点长度, 间隔长度 ] )

  - 偏移调整：ctx.lineDashOffset = 数值

  - 实例

    ```js
    ctx.setLineDash([10, 5])
    let offset = 0
    setInterval(() => {
        offset+=1
        ctx.lineDashOffset = offset
        ctx.clearRect(95, 95, 110, 110)
        ctx.strokeRect(100, 100, 100, 100)
    }, 20)
    ```

- 阴影

  - 阴影x轴偏移：ctx.shadowOffsetX = 数值

  - 阴影y轴偏移：ctx.shadowOffsetY = 数值

  - 阴影虚化度：ctx.shadowBlur = 数值

  - 阴影颜色：ctx.shadowColor = “rgba()”





## 图片与动画

- 绘制图片

  ```js
  let image = new Image()
  image.src = "./images/oneImage.png"
  image.onload = () => {
      ctx.draw(图片对象, 起始位置x, 起始位置y [, 终止位置x, 终止位置y])
      ctx.draw(图片对象, 图片的裁剪起始位置x, 图片的裁剪起始位置y, 图片裁剪宽度, 图片裁剪高度
               起始位置x, 起始位置y, 终止位置x, 终止位置y)
  }
  ```

- 绘制视频

  ```js
  let video = document.querySelector("video")
  let btn = document.querySelector("#btn")
  btn.onclick = () => {
      if (video.paused) {
          video.play()
          btn.innerHTML = "暂停"
      }else {
          video.pause()
          btn.innerHTML = "播放"
      }
      render()
  }
  let image = new Image()
  image.src = "./images/logo.png"
  let render = () => {
      ctx.drawImage(video, 0, 0, 600, 400)
      ctx.drawImage(image, 480, 320, 100, 70)
      requestAnimationFrame(render)
  }
  ```

- 绘制文字

  - 文字：ctx.font = "16px Microsoft YaHei"
  - 填充文字：fillText(text, x, y [, maxWidth])
  - 绘制文字轮廓：strokeText(text, x, y [, maxWidth])
  - 对齐方式
    - 水平：ctx.textAlign = "center"
      - start：默认
      - center
      - end
      - left
      - right
    - 垂直（基线对齐）：ctx.textBaseline = "middle"
      - middle
      - textBaseline
      - top
      - bottom
      - alphabetic：默认
    - 方向：ctx.direction = "rtl"（right to left）

- 坐标系变动

  - 位移：ctx.translate(x, y)
  - 缩放：ctx.scale(水平放大倍数, 竖直放大倍数)
  - 旋转：ctx.rotate(弧度制数值)
  - 矩阵变换：ctx.transform(a, b, c, d, e, f)
    - a、b：x轴单位向量对应的x，y
    - c、d：y轴单位向量对应的x，y
    - e、f：x、y轴位移量

- 裁剪：

  - ctx.clip()
    - 绘制路径
    - clip()
    - 绘制图片（自动根据路径裁剪图片）

  - ctx.clip(Path2D对象)

- 保存与恢复

  - ctx.save()
  - ctx.restore()
  - 可多次保存，保存的画笔数据储存在栈中，每一次读取的数据由中栈中取出，最后保存的数据最先取出


- 像素处理

  - 获得所有像素信息：ctx.getImageData(起点x, 起点y, 宽度, 高度)

    ``` js
    let imageData = ctx.getImageData(0, 0, 600, 400)
    
    // imageData.data 是一个数组，存储的是每个像素的rgba值
    for(let i = 0; i < imageData.data.length, i+=4) {
        console.log(image.data[i]) // r
        console.log(image.data[i+1]) // g
        console.log(image.data[i+2]) // b
        console.log(image.data[i+3]) // a
    }
    
    // 将修改后的像素数据重新渲染的画布上
    ctx.putImageData(imageData, 起点x, 起点y) // 全部渲染
    ctx.putImageData(imageData, 起点x, 起点y, 渲染起点x, 渲染起点y, 渲染宽度, 渲染高度)
    ```

- 判断是否在路径内：ctx.isPointInPath(路径对象(如Path2D对象), x, y)





## 封装

```js
let canvasEle = document.querySelector("#canvas1");
let ctx = canvasEle.getContext("2d");
class Heart {
    constructor(x, y, size) {
        this.x = x
        this.y = y
        this.size = size
        this.color = "blue"
        this.isIn = false
        this.eventMapList = {
            hover: [],
            leave: []
        }
        canvasEle.onmousemove = (e) => {
            let x = e.offsetX
            let y = e.offsetY
            this.isIn = ctx.isPointInPath(this.heartPath, x, y)
            if (this.isIn) this.eventMapList.hover.forEach(item => item())
            else this.eventMapList.leave.forEach(item => item())
        }
    }
    onHover(fn) {
        this.eventMapList.hover.push(fn)
    }
    onLeave(fn) {
        this.eventMapList.leave.push(fn)
    }
    setPosition(x, y) {
        this.x = x
        this.y = y
    }
    setSize(size) {
        this.size = size
    }
    setColor(color) {
        this.color = color
    }
    draw() {
        this.heartPath = new Path2D()
        this.heartPath.moveTo(this.x, this.y)
        this.heartPath.bezierCurveTo(this.x+this.size/2, this.y-this.size/2,
            this.x+this.size, this.y, this.x, this.y+this.size/2)
        this.heartPath.bezierCurveTo(this.x-this.size, this.y,
            this.x-this.size/2, this.y-this.size/2, this.x, this.y)
        ctx.save()
        ctx.fillStyle = this.color
        ctx.fill(this.heartPath)
        ctx.restore()
    }
}

let heart = new Heart(100, 100, 100)
heart.onHover(() => {
    heart.setColor("red")
    heart.setSize(120)
})
heart.onLeave(() => {
    heart.setSize(100)
    heart.setColor("blue")
})
function drawHeart() {
    ctx.clearRect(0, 0, canvasEle.width, canvasEle.height)
    heart.draw()
    requestAnimationFrame(drawHeart)
}
drawHeart()
```

