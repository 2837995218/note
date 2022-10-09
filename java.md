# Git

## 常用命令

| 命令名称                             | 作用                         |
| ------------------------------------ | ---------------------------- |
| git config --global user.name 用户名 | 设置用户签名                 |
| git config --global user.email 邮箱  | 设置用户签名                 |
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
    - \<section>：网页区块
    - \<article>：网页文章




### 其他内容

- 字符实体
  - ' '：\&nbsp;
  - '&'：\&amp;
  - '©'：\&copy;





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
    - rgb表示法：rgb(r,g,b,a)：a表示透明度，取值范围0~1
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
    - contain：填充
    - cover：拉升

- 背景位置：background-position
  - 方位名词
    - 水平方向：left、center、right
    - 垂直方向：top、center、bottom
  - 数字+单位
- 背景复合属性：`background：color image repeat position`（无序）



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





### 浮动

#### 结构伪类选择器

- E:first-child{}：匹配父元素中第一个子元素，并且是E元素
- E:last-child{}：匹配父元素中最后一个子元素，并且是E元素
- E:nth-child(n){}：匹配父元素中第n个子元素，并且是E元素
- E:nth-last-child(n){}：匹配父元素中倒数第n个子元素，并且是E元素
- n的注意事项
  - n：为：1、2、3、4
  - 公式：2n、even（偶数）、2n+1、odd（奇数）、-n+5（前五个）、n+5（第五个往后）



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
    right: 50%;
    margin-left: -盒子宽的一半px;
    margin-top: -盒子高的一半px;
    /* 解决方法二：*/
    position: absolute;
    left: 50%;
    right: 50%;
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
-  window.setInterval(回调函数，间隔的毫秒数)
-  window.clearInterval(interval-name)



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




### 元素

- offset

  - 获得元素距离带有定位父元素的位置
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
    
  - 补：
  
    ```javascript
    网页可见区域宽： document.body.clientWidth;
    网页可见区域高： document.body.clientHeight;
    网页可见区域宽： document.body.offsetWidth    (包括边线的宽);
    网页可见区域高： document.body.offsetHeight   (包括边线的宽);
    网页正文全文宽： document.body.scrollWidth;
    网页正文全文高： document.body.scrollHeight;
    网页被卷去的高： document.body.scrollTop;
    网页被卷去的左： document.body.scrollLeft;
    网页正文部分上： window.screenTop;
    网页正文部分左： window.screenLeft;
    屏幕分辨率的高： window.screen.height;
    屏幕分辨率的宽： window.screen.width;
    屏幕可用工作区高度： window.screen.availHeight;
    屏幕可用工作区宽度：window.screen.availWidth;
    
    
    scrollHeight: 获取对象的滚动高度。  
    scrollLeft:设置或获取位于对象左边界和窗口中目前可见内容的最左端之间的距离
    scrollTop:设置或获取位于对象最顶端和窗口中可见内容的最顶端之间的距离
    scrollWidth:获取对象的滚动宽度
    offsetHeight:获取对象相对于版面或由父坐标 offsetParent 属性指定的父坐标的高度
    offsetLeft:获取对象相对于版面或由 offsetParent 属性指定的父坐标的计算左侧位置
    offsetTop:获取对象相对于版面或由 offsetTop 属性指定的父坐标的计算顶端位置  
    event.clientX 相对文档的水平座标
    event.clientY 相对文档的垂直座标
    
    event.offsetX 相对容器的水平坐标
    event.offsetY 相对容器的垂直坐标  
    document.documentElement.scrollTop 垂直方向滚动的值
    event.clientX+document.documentElement.scrollTop 相对文档的水平座标+垂直方向滚动的量
    
    要获取当前页面的滚动条纵坐标位置，用：
    document.documentElement.scrollTop;
    而不是：
    document.body.scrollTop;
    documentElement 对应的是 html 标签，而 body 对应的是 body 标签
    ```
  
    



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









# 移动端



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



### 平面转换

- 语法

  - transform: translate(水平移动距离【, 垂直移动距离】)
    - 取值：具体值、百分比（参考自身盒子大小）
  - transform: translateY(垂直移动距离)
  - transform: rotate(角度)（单位deg）
    - 设置转动原点：transform-origin: 原点水平位置 原点垂直位置;
      - 取值：left、top、right、bottom、center
  - transform: scale(缩放倍数)（中心缩放）

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
                  /* 定义多重移动（旋转会改变坐标轴向，位移会受影响） */
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
  
  

### 渐变

- 透明度：opacity: 0;

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



### 空间转换

- 使用 perspective 属性实现 透视 效果

  - 添加到使用z轴变换的盒子的父级上
  - 一般取值：800px~1200px

- 语法

  - transform: translate3d(x, y, z)
  - transform: rotateX(角度)：围绕X轴转动
  - transform: scale3d(x, y, z)：缩放

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
    /*
    	steps(n)：分三次执行
    	infinite：无限循环
    	alternate：返回
    	forwards：动画停留在最终状态 backwards：动画停留在最初状态
    */
    ```

  - 动画属性（配合js）
    - animation-name：动画名称
    - animation-duration：动画时长
    - animation-delay：延迟时间
    - animation-fill-mode：动画执行完毕时的状态
    - animation-timing-function：速度曲线
    - animation-iteration-count：重复次数
    - animation-direction：动画执行方向
    - animation-play-state：动画状态（取值paused为暂停）





## 移动端网页

### Flex布局

- 弹性容器
  - display: flex：设置弹性容器，其直接子盒子为弹性盒子
  - justify-content：调节元素在主轴的对齐方式
    - flex-start：默认值，起点开始依次排序
    - flex-ent：终点开始依次排序
    - center：沿主轴居中排序
    - space-around：弹性盒子沿主轴均匀排序，空白间距均分在弹性盒子两侧
    - space-between：弹性盒子沿主轴均匀排序，空白间距均分在弹性盒子之间
    - space-evenly：弹性盒子沿主轴均匀排序，弹性盒子与容器之间间距相等
  - align-items：调节弹性盒子在侧轴的对齐方式
    - stretch：默认值，在弹性盒子没设置高度时，沿侧轴拉伸弹性盒子至与弹性容器相同
    - center：沿侧轴居中排列
    - flex-start：默认值，起点开始依次排列
    - flex-end：终点开始依次排列
  - flex-direction：改变主轴方向
    - row：默认值：行，水平
    - column：列，垂直
    - row-reverse：行，从右向左
    - column-reverse：列，从下向上
  - flex-wrap：换行
    - nowrap：默认值，不换行
    - wrap：换行
  - align-content：flex布局下，行与行间有默认间距
    - 取值与justify-content属性相似，无space-evenly
- 弹性盒子
  - align-self：调节某个弹性盒子在侧轴的对齐方式
    - 取值与flex-direction属性相同
  - flex：占用弹性盒子剩余尺寸的分数



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
  


- vh/vm

  - vh/vm单位
    - 1vh = 1/100视口高度
    - 1vw = 1/100视口宽度
  



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









# Ajax



## 基础

### Http

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



### XMLHttpRequest

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



### 快速入门

```js
const xhr = null
let isSending = false
btn.onclick = function(){
    // 解决重复发送问题
    if(isSending){xhr.abort()}
    
    xhr = new XMLHttpRequest()
    // 自动转json，设置响应体数据类型
    xhr.responseType = "json"
    // 网络异常处理
    xhr.timeout = 2000
    xhr.ontimeout = function(){alert("请求超时")} // 时间到后会自动取消请求
    xhr.onerror = function(){alert("网络异常")}

    xhr.open('GET',"http://localhost/test?username=admin",false)

    // 添加请求头，Content-Type是用来设置请求体类型的
    xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded')

    xhr.send() // POST方法在此处传参
    isSending = true

    // xhr.abort() // 取消当前请求

    xhr.onreadystatechange = function(){
        // readystate的值：
        // 0-未初始化 1-open方法调用完毕 2-send方法调用完毕 3-返回部分结果 4-返回全部结果
        if(xhr.readyState == 4){
            if(xhr.status >= 200 && xhr.statud < 300){
                // 处理结果  行、头、体
                // 手动将响应转为json
                let data = JSON.parse(xhr.response)
                }
            isSending = false;
        }
    }
}
```





## 拓展

### 数据解析

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

    

### FormData

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



### 传输进度

- 上传

  ```js
  const xhr = new XMLHttpRequest()
  xhr.upload.onprogress = function(e){
      // e.lengthComputable 是一个布尔值，表示当前上传的资源是否具有可计算的长度
      if(e.lengthComputable){
          // e.loaded 已传输的字节
          // e.total 需要传输的总字节
          let percentComplete = Math.ceil((e.loaded / e.total) * 100)
      }
  }
  // e.isTrusted为true时代表事件为用户触发的可信事件
  ```

- 下载：xhr.onprogress



### Axios

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




### 防抖与节流

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

  



## 跨域

### 同源策略

- 同源：协议、域名、端口相同，反之，则是跨域
- 同源策略：限制两个不同源的网页进行资源交互
- 处理时机：客户端可发送ajax到跨域的服务器，服务器也可以接收并返回数据，数据会在返回时被浏览器的同源策略拦截



### JSONP

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



### CORS











# Vue



## Vue2

### 基础

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
    - v-bind：放在属性前，可将属性的属性值变为js表达式，可简写为”**:**“，（单向绑定）
    - v-model：value属性前，双向绑定，只能应用在表单类元素上，可简写为“ v-model="js表达式" ”
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

- 数据代理

  - 通过一个对象代理另一个对象中属性的操作（读/写）

    ```js
    let obj = {x:100}
    let obj2 = {y:200}
    Object.defineProperty(obj2, 'x', {
        get(){
            return obj.x;
        },
        set(value){
        	obj.x = value;
    	}
    })
    ```

  - Vue对象（vm）则代理data中的数据

  - Vue对象（vm）中的_data就是data



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
                class1:false,
                class2:true
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

- 指令：v-on:click="方法名"，可简写为@:click="方法名"

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





### 拓展

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



#### 监测数据

- Vue实现监听数据的简单实现

```js
let data = {
    name:'张三',
    age:18
}
// 创建一个监视的实例对象，用于监视data中属性的变化
const obs = new Observer(data)
console.log(obs)

// 准备一个vm实例对象
let vm = {}
vm._data = data = obs

// Observer 的构造函数
function Observer(obj){
    // 汇总对象中所有的属性形成一个数组
    const keys = Object.keys(obj)
    keys.forEach((k)=>{
        // this 是Observer的实例对象
        Object.defineProperty(this,k,{
            get(){
                return obj[k]
            },
            set(value){
                obj[k] = value
            }
        })
    })
}
```

- 后添加属性
  - 问题：后添加的属性没有响应式的方法
  - vm.$set(target, key, value)：为后添加的属性提供响应式方法（getter和setter）
  - Vue.set(target, key, value)
    - target：是添加的位置
    - key：属性名 或 数组的索引
    - value：属性值
  - 上述方法不能给 vm 和 vm._data 添加数据
- Vue监测数组
  - Vue数组的改变不能用索引值的方式，否则无法生成响应式方法（getter和setter）
  - 可用上述的 后添加属性 的方法进行改变
  - Vue对被侦听的数组的变更方法提供了包装，使其在实现原本效果的同时，生成响应式方法
    - push(items[])：添加一个数组到末尾
    - pop()：取出数组中的最后一项
    - shift()
    - unshift()
    - splice()
    - sort()
    - reverse()



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
  - number：只能输入数字
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





### 组件

#### 快速入门

- 组件定义

```js
const stu = Vue.extend({
    template:`姓名：{{name}}<br>年龄：{{age}}`,
    // 组件中data要用方法的格式，因为对象是传递地址值
    data(){
        return {
            name: '张三',
            age: 18
        }
    }
})
```

- 添加组件

```js
// 全局
Vue.component("组件名",组件)

new Vue({
    el: '#root',
    data(){return {}},
    components: {
        /* 命名风格：
           1.单个单词：首字母大写；或首字母小写
           2.多个单词：
           		1.首字母小写，并用'-'连接（kebab-case）
           		2.首字母大写（CamelCase），需要Vue脚手架支持）
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



#### VueComponent

- student组件本质是一个名为VueComponent的构造函数，且不是程序员定义的，是Vue.extend生成的

- 我们只需要写\<student/>,Vue解析时会帮我们创建student的实例对象，即Vue帮我们执行了：`new VueComponent(options)`

- 特别注意：每次调用Vue.extend，返回的都是一个全新的VueComponent

- 关于this的指向

  - 组件配置中，this的指向都是【VueComponent的实例对象】

- vc与vm类似，但vc没有el属性

  ![vc与vm的关系](D:\picture\typora\java\vc与vm的关系.jpeg)



#### VueCLI

- 全局安装VueCLI 

  ```shell
  npm install -g @vue/cli
  # 切换为淘宝镜像
  npm config set registry https://registry.npm.taobao.org
  ```

- 切换到要创建项目的目录

  ```shell
  vue create xxxx
  ```

- 启动项目

  ```shell
  npm run serve
  ```

- 结构

  ```
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
  │   │── main.js: 入口文件
  ├── .gitignore: git 版本管制忽略的配置
  ├── babel.config.js: babel 的配置文件
  ├── package.json: 应用包配置文件
  ├── README.md: 应用描述文件
  ├── package-lock.json：包版本控制文件
  ```

- render函数

  ```js
  // vue.js 是一个完整版的Vue，包含：核心功能 + 模板解析器
  // 此处引入的vue是一个精简的vue，没有模板解析器. vue/dist/vue.runtime.esm.js
  import Vue form 'vue' 
  import App from './App.vue'
  
  new Vue({
      el: '#root',
      // 因为vue.runtime.xxx.js没有解析器，所以不能使用template配置项，需要使用render函数接收到		// createElement函数去指定具体内容
      // render: h => h(App),
      render(createElement){
          return createElement(App)
      }
  })
  ```

- vue.config.js配置文件
  - 使用 vue inspect > output.js 可以查看vue脚手架的默认配置
  - 使用vue.config可以对脚手架进行个性化定制，详情见：http://cli.vuejs.org/zh







































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
    - `export JAVA_HOME=/usr/local/java/jdk1.8.0_333`
    - `export PATH=$JAVA_HOME/bin:$PATH`
    - `source /ect/profile` （刷新环境变量）
    - `echo $PATH`（查看环境变量




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

- ln指令
  - 基本语法：ln -s 源文件或目录 软连接名
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

| 排序法 | 平均时间        | 最差情况        | 稳定度 | 额外空间 | 备注                   |
| ------ | --------------- | --------------- | ------ | -------- | ---------------------- |
| 冒泡   | O(n²)           | O(n²)           | 稳定   | O(1)     | n较小时好              |
| 交换   | O(n²)           | O(n²)           | 不稳定 | O(1)     | n较小时好              |
| 选择   | O(n²)           | O(n²)           | 不稳定 | O(1)     | n较小时好              |
| 插入   | O(n²)           | O(n²)           | 稳定   | O(1)     | 大部分已排序时较好     |
| 基数   | O(log*r* **B**) | O(log*r* **B**) | 稳定   | O(n)     | B（0~9）、r（个十百）  |
| Shell  | O(logn)         | O(n)~O(n²)      | 不稳定 | O(1)     | 指数大小取决于所选分区 |
| 快速   | O(logn)         | O(n²)           | 不稳定 | O(nlog)  | n较大时好              |
| 归并   | O(logn)         | O(nlogn)        | 稳定   | O(1)     | n较大时好              |
| 堆     | O(logn)         | O(nlogn)        | 不稳定 | O(1)     | n较大时好              |

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
  - 一个软件实体如类、模块和函数应该对扩展开放，对修改关闭。也就是说一个软件实体应该通过扩展来实现变化，而不是通过修改已有的代码来实现变化
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
      
      public static synchronized Singleton getInstance(){
          if(instance == null){
              instance = new Singleton();
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
              return new  Laptop(this);
          }
      }
  }
  ```

  





### 结构型模式

#### 适配器模式

#### 桥接模式

#### 装饰模式

#### 组合模式

#### 外观模式

#### 享元模式

#### 代理模式





### 行为型模式

#### 模板方式模式

#### 命令模式

#### 访问者模式

#### 迭代器模式

#### 观察者模式

#### 中介者模式

#### 备忘录模式

#### 解释器模式

#### 状态模式

#### 策略模式

#### 责任链模式















# MyBatis



## 快速入门

- mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
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
    <mapper resource="\SQL映射文件位置\"/>
  </mappers>
</configuration>
```

- XXXMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="\命名空间\">
  <select id="\sql的id\" resultType="返回的值的类型">
    select * from Blog where id = #{id}
  </select>
</mapper>
```

- 构建SqlSessionFactory

```java
String resource = "mybatis-config.xml的相对位置";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
```

- 获取SqlSession对象，以此来执行sql（参数表明是否自动提交事务，不写默认false）

```java
SqlSession sqlSession = sqlSessionFactory.openSession(\布尔值\);
```

- 执行sql

```java
List<POJO> pojo = sqlSession.selectList("\名称空间\.\sql的id\")
```

- 释放资源

```java
sqlsession.close();
```



## Mapper代理

- 根据sql语句的返回内容 创建对应的mapper类

```xml
<select id="selectAll" resultType="cn.langh.POJO.User">
    select * from mybatis_01;
</select>
```

```java
public interface UserMapper {
    List<User> selectAll();//方法名为SQL语句的id
}
```

- 设置SQL映射文件的namespace属性为Mapper接口的全限定名

```xml
<mapper namespace="cn.langh.mapper.UserMapper">
    <select id="selectAll" resultType="cn.langh.POJO.User">
        select * from mybatis_01;
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

- 如果Mapper接口的名称和sql映射文件的名称相同，并在同一目录下，则可以使用包扫描的方式简化sql映射文件的加载

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
- **typeAliases**：可指定pojo包，会为包中的实体类自动取类型别名，别名不区分大小写，在mapper配置中的resultType中可使用该别名
- 配置标签是需要遵循一定的顺序



## 其他问题

- 数据库的字段名称 和 实体类的属性名称 不一样时，不能自动封装。

  - 查询时取别名：对不一样的列名取别名，让别名与实体类属性名一致

  ```xml
  <select id="selectAll" resultTyep="User">
  	select id, user_name(数据库字段名称) as userName(别名) from user(表);
  </select>
  ```

  - 将sql片段抽取出来

  ```xml
  <sql id="sql_01">id, user_name as userName</sql>
  
  <select id="selectAll" resultTyep="User">
  	select
      	<include regid="sql_01"/>
      from user(表);
  </select>
  ```

  - 映射
    - id：唯一标识
    - type：放回的值的类型，也支持别名
    - 子标签
      - id：完成主键字段的映射
      - column：完成一般字段的映射

  ```xml
  <resultMap id="resultMap_01" type="user">
  	<result column="\列名称\" property="\实体类的属性名\"/>
      <result id="\列名称\" property="\实体类的属性名\"/>
  </resultMap>
  
  <select id="selectAll" resultTyep="reusltMap_01">
  	select 条件 from user(表);
  </select>
  ```

- 参数占位符

  - #{\参数\\}：会将其替换为"**?**"。为了防止sql注入
  - ${\参数\\}：拼接sql，会存在sql注入问题

- 参数类型：select标签中的**parameterType**可以设置所传递的参数类型，可以省略

- 特殊字符的处理：

  - 转义字符：<：\&lt;
  - cData区：将字符写在\<![CDATA[ 内容 ]]>中
  
- 添加语句中的主键返回

  \<insert>标签中useGeneratedKeys属性属性设置为true，keyProperty设置为对象的主键



## 参数接收

- 单个参数

  - pojo类型：直接使用，对象的属性名 和 参数占位符名 一致

  - map集合：直接使用，键名 和 参数占位符名 一致

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

- 多个参数：封装为Map集合，可以使用@Param注解，替换集合中默认的arg键名

  - 如果参数设置为`User select(String 参数1,String 参数2)`

    ```java
    map.put("arg0",参数1);
    map.put("param1",参数1);
    map.put("arg1",参数2);
    map.put("param2",参数2);
    ```



## 动态sql

- \<if>标签

```xml
<if test="\布尔表达式\"> 内容 </if>
```

- \<choose>标签

```xml
<choose>
	<when test=""></when>
    <when test=""></when>
    <otherwise></otherwise>
</choose>
```

- \<where>标签

  可自动检测动态sql是否存在由于拼接导致的一部分问题



## 注解开发

- @Select

```java
@Select("select * from user where id = #{id}}")
public User selectById(int id);
```

- @Insert
- @Update
- @Delete









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
  
  

### 依赖注入

- setter注入

  - 简单注入

  ```java
  public class UserDao implements UserDaoImpl{
      private int connectionNum;
      private String databaseName;
      
      public void setConnectionNum(int connectionNum){
          this.connectionNum = connectionNum;
      }
      public void setDatabaseName(String databaseName){
          this.databaseName = databaseName;
      }
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
  public class BookServiceImpl implements BookService{
      private UserDao userDao;
      
  	public void setUserDao(UserDao userDao){
      	this.userDao = userDao;
  	}
  }
  ```

  ```xml
  <bean id="userDao" class="cn.langh.dao.impl.UserDaoImpl"/>
  <bean id="userService" class="cn.langh.service.impl.UserServiceImpl">
  	<property name="bookDao" ref="bookDao"/>
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
      <constructor-arg name="databaseName" value="mysql"/>
      <constructor-arg name="connectionNum" value="10"/>
  </bean>
  ```

  ​		注：注入时

  ​			标准注入：\<constructor-arg name="connectionNum" value="10"/>

  ​			用参数类型解耦合注入：\<constructor-arg type="int" value="10"/>

  ​			用参数位置解耦合注入：\<constructor-arg index="0" value="10"/>

  

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
  <property name="array">
      <array>
          <value>1</value>
          <value>2</value>
          <value>3</value>
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

  

### 数据源对象管理

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/test"/>
    <property name="username" value="root"/>
    <property name="password" value="root"/>
</bean>
```

​		加载外部properties

		1. 开启context命名空间
		2. 使用context空间加载properties文件

```xml
<context:property-placeholder location="jdbc.properties"/>

<bean id="dataSource2" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="${jdbc.driver}"/>
    <property name="jdbcUrl" value="${jdbc.url}"/>
    <property name="user" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>
```

注：

1. 命名空间默认为优先加载系统属性，可在context标签中设置 system-properties-mode="NEVER"\
2. context标签中 location 属性设置为“**\*.properties**”表示加载所有后缀为properties的文件
2. 加载properties的标准格式为 **location="classpath:*.properties"**
2. 从类路径或jar包中搜索并加载properties文件格式为 **location="classpath\*:\*.properties"**



### 容器

- 获取容器 

```java
new ClassPathXmlApplicationContext("applicationContext.xml");
```

```java
new FileSystemXmlApplicationContext("\绝对路径\");
```

- 获取bean

```java
UserDao userDao = (UserDao) app.getBean("userDao");
```

```java
UserDao userDao = app.getBean("userDao",userDao.class);
```

```java
UserDao userDao = app.getBean(UserDao.class);
```

注：

- BeanFactory 是 IoC 容器的顶层接口，初始化BeanFactory对象时，加载的bean延迟加载
- ApplicationContext 接口是 spring 容器的核心接口，初始化时bean立即加载，可用 lazy-init 属性控制
- ApplicationContext 接口提供的 bean 操作相关方法，通过替他接口拓展其功能
- ApplicationContext 接口常用的初始化类
  - ClassPathXmlApplicationContext
  - FileSystemXmlApplicationContext



### 总结

- bean相关

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

### 定义bean

- 定义bean

  - @Component("\名称\\")（注解在类上）
    - 业务层：@Service
    - 数据层：@Repository
    - 表现层：@Controller

- bean的管理

  - @Scope("singleton/prototype")（注解在类上）

  - 生命周期（注解在方法上）
    - 初始化方法：@PostConstruct（构造方法后运行）
    - 销毁方法：@PreDestory（销毁方法前运行）

- 组件扫描

  - xml

  ```xml
  <context:component-scan base-package="cn.langh"/>
  ```

  - 配置类

  @ComponentScan("cn.langh")

  @ComponentScan({"cn.langh.dao","cn.langh.service})

- 配置类
  - 注解：@Configuration
  - 添加外部配置：@ImportResource("applicationContext.xml")
  - 组件扫描：@ComponentScan
  - 导入其他配置类：@Import({jdbcConfig.class,...})
  - 获取容器：new AnnotationConfigApplicationContext(SpringConfig.class)



### 依赖注入

- 配置文件
  - 加载：@propertySource({"\文件名1\\","\文件名2\\",...})
  - 使用：${\键\\}
- 简单类型注入
  - @Value("\值\\")

- 自动装配

  - @Autowired（注解在成员变量上）

    按类型注入，若容器中含相同类型的bean，则按成员变量对应的名称注入

  - @Qualifier("\名称\\")（注解在成员变量上）

    注入指定名称的bean，依赖于@Autowired



### 数据源对象管理

第三方bean的管理

- 定义一个方法获得要管理的对象,参数传递需要的引用对象
- 添加 @Bean 表示方法的返回值为bean
- 第三方bean的依赖注入
  - 引用类型：方法形参
  - 简单类型：成员变量

```java
@PropertySource("jdbc.properties")
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
    public DataSource dataSource(\形参\){
        DruidDataSource ds = new DruidDataSource();
        ds.setDriverClassName(driver);
        ds.setUrl(url);
        ds.setUsername(username);
        ds.setPassword(url);
        return ds;
    }
}
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
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.2.10.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.10</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.1</version>
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
    </dependencies>
```

- MybaitsConfig

```java
public class MybatisConfig {
    @Bean
    SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource){
        SqlSessionFactoryBean ssfb = new SqlSessionFactoryBean();
        ssfb.setTypeAliasesPackage("cn.langh.domain");
        ssfb.setDataSource(dataSource);
        return ssfb;
    }

    @Bean
    MapperScannerConfigurer mapperScannerConfigurer(){
        MapperScannerConfigurer msc = new MapperScannerConfigurer();
        msc.setBasePackage("cn.langh.dao");
        return msc;
    }
}
```





## Spring整合Junit

- 位置坐标

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

- 例：

```java
@RunWith(SpringJUnit4ClassRunner.class)//设置类运行器
@ContextConfiguration(classes = SpringConfig.class)//指定spring容器
public class UserServiceTest {
    @Autowired
    private UserService userService;

    @Test
    public void testGetById(){
        System.out.println(userService.getById(1));
    }
}
```





## AOP面向切面编程

### 快速入门

- 导入坐标

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>5.2.10.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.9.1</version>
    <scope>runtime</scope>
</dependency>
```

- 制作连接点方法（原始造作，Dao接口与实现类）
- 制作共性功能（通知类与通知）

- 定义切入点

```java
@Pointcut("execution(切入点表达式)")
```

- 绑定切入点与通知关系（切面）
- 例：

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
public class MyAdvice {
    @Pointcut("execution(void cn.langh.dao.UserDao.save())")
    private void pt(){}

    @After("pt()")
    public void method(){
        System.out.println(System.currentTimeMillis());
    }
}
```



### 切入点表达式

- 动作关键字：描述切入点的行为动作，例如execution表示执行到指定切入点

- 访问修饰符：public，private等，可以省略

- 可以使用通配符描述切入点，快速描述

  - \* ：单个独立的任意符号，可以独立出现，也可以作为前缀或后缀的匹配符出现

  ```java
  execution(public * cn.langh.*.*Service.find*(*))
  ```

  - ..  ：多个连续的任意符号，可以独立出现，常用于简化包名与参数的书写

  ```java
  execution(public User com..UserService.findById(..))
  ```

  - \+ ：专用于匹配子类类型

  ```java
  execution(* *..*Service+.*(..))
  ```



### 通知类型

- @Before
- @After
- @Around
  - 需要依赖形参
  - 方法的返回值为Object，方法执行完后要有返回值

```java
@Around("切入点方法")
public Object around(ProceedingJoinPoint pjp) throws Throwable{
    System.out.println("原始方法前");
    Object o = pjp.proceed();//表示对原始方法的调用
    System.out.println("原始方法后");
    return o;
}
```

- @AfterReturning	原始方法成功执行完以后执行此方法
- @AfterThrowing     原始方法抛出异常后执行此方法



### 通知获取数据

- 获取参数
  - 通知方法中加形参 JoinPoint，调用 JoinPoint 中的方法Object[ ] getArgs()，获得参数
  - proceedingJoinPoint 是 JoinPoint 的实现类
  - JoinPoint 必须是形参的第一个

- 获取返回值

  - @Around 获取返回值

  ```java
  Object o = pjp.proceed();
  ```

  - @AfterReturning 获取返回值

  ```java
  @AfterReturning(value="切入点方法",returning="ret")//将原始方法的返回值注入到形参ret中
  public void afterReturning(Object ret){
      方法体;
  }
  ```

- 获取异常

  - @Around 获取异常

  ```java
  try{
  	pjp.proceed();
  }catch(Throwable e){}
  ```

  - @AfterThrowing 获取异常

  ```java
  @AfterReturning(value="切入点方法",throwing="thr")//将原始方法抛出的异常注入到形参thr中
  public void afterThrowing(Throwable thr){
      方法体;
  }
  ```





## Spring事务

### 快速入门

- 一般在业务层接口（Service接口）的方法上添加标注 @Transactional
  - @Transactional 可标注在类或接口上，表示该类或接口中所有方法都添加事务

- 添加事务管理器


```java
@Bean
public PlatformTransactionManager transactionManager(DataSource dataSource){
    DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
    transactionManager.setDataSource(dataSource);
    return transactionManager;
}
```

- SpringConfig 上添加标注 @EnableTransactionManagement




### Spring事务角色

- 事务角色

  - 事务管理员：发起事务方，在Spring中通常指代业务层开启事务的方法
  - 事务协调员：加入事务方，在Spring中通常指代数据层方法，也可以是业务层方法

- 事务传播行为

  - 事务协调员对管理员的态度
  - 通过@Transactional中的propagation属性设置（Propagation.属性）

  | 传播属性      | 事务管理员 | 事务协调员态度 |
  | ------------- | ---------- | -------------- |
  | REQUIRED      | 已开启事务 | 加入该事务     |
  |               | 无         | 新建事务       |
  | REQUIRES_NEW  | 已开启事务 | 新建事务       |
  |               | 无         | 新建事务       |
  | SUPPORTS      | 已开启事务 | 加入该事务     |
  |               | 无         | 无             |
  | NOT_SUPPORTED | 已开启事务 | 无             |
  |               | 无         | 无             |
  | MANDATORY     | 已开启事务 | 加入该事务     |
  |               | 无         | ERROR          |
  | NEVER         | 已开启事务 | ERROR          |
  |               | 无         | 无             |
  | NESTED        |                            |



### Spring事务的其他配置

@Transactional注解源码

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

    //默认遇到Error或运行是异常时回滚数据，其他异常需要是要手动设置
    Class<? extends Throwable>[] rollbackFor() default {};

    String[] rollbackForClassName() default {};

    //默认遇到Error或运行是异常时回滚数据，其中不需要回滚的异常需要手动设置
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
            <version>2.1</version>
            <configuration>
                <port>80</port>
                <path>/</path>
            </configuration>
        </plugin>
    </plugins>
</build>
```



### Config

- 创建spring的配置文件

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
      
      //设置哪些请求归属springMVC处理
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
        - 负数：默认值
        - 零：删除cookie信息

  - cookie存储中文

    - 在tomcat 8之前 cookie不能直接存储中文数据，需要转码，一般用URL编码

    - 在tomcat 8之后，cookie可以直接存储中文，但仍不支持特殊字符

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

  - 响应页面
    - 方法返回值设定位要跳转的页面


  - 响应json
    - 方法返回值设置为pojo类型，并返回pojo对象


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



### 异常处理器的定义

- 出现异常后，将异常全部抛到表现层进行处理

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
            throw new SystemException(Code.SYSTEM_TIMEOUT_ERR, "出异常啦");
        }
    }
    ```

    

- 在表现层定义异常处理器，异常处理器会自动拦截异常

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

- 拦截器的定义

```java
@Component
public class ProjectInterceptor implements HandlerInterceptor {
    // 在原始操作前运行
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle...");
        // 是否放行
        return true;
    }

    // 原始操作后运行
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle...");
    }
    
	// 在postHandle操作后运行
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion...");
    }
}
```

- 拦截器的加载

```java
@Configuration
public class SpringmvcSupport extends WebMvcConfigurationSupport {
    // 过滤访问的静态资源
    @Override
    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
        // 访问 /page/** 时让他定位到 /page/ 目录
        registry.addResourceHandler("/page/**").addResourceLocations("/page/");
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

- SpringmvcSupport 也要在SpringmvcConfig中扫描到

-  简化配置

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









# Maven拓展



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
    <packaging>war</packaging>
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
    <packaging>war</packaging>

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
- 依赖冲突所执行的顺序
  - 路径优先：当依赖中出现相同的资源时，层级越深，优先级越低
  - 声明优先：当资源在相同层级被依赖时，配置顺序靠前的覆盖配置顺序靠后的
  - 特殊优先：当同级配置了相同资源的不同版本，后配置的覆盖先配置的

- 可选依赖：隐藏当前工程所依赖的资源，隐藏后，对应资源将不具备依赖的传递性

```xml
<dependency>
    <groupId>XXX</groupId>
    <artifactId>XXX</artifactId>
    <version>XXX</version>
    <optional>true</optional>
</dependency>
```

- 排除依赖：隐藏当前依赖所对应的部分依赖关系

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

  - directory可设置为 \<directory>${project.basedir}/src/main/resources\</directory>
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
            <jdbc.url>jdbc:mysql://127.0.0.1:3306/ssm（开发环境地址）</jdbc.url>
        </properties>
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



## 私服

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

<!-- 将tomcat服务器嵌入项目中 -->
<plugin>
    <groupId>org.apache.tomcat.maven</groupId>
    <artifactId>tomcat7-maven-plugin</artifactId>
    <version>2.1</version>
    <configuration>
        <port>80</port>
        <path>/</path>
    </configuration>
</plugin>
```









# MyBatisPlus

https://baomidou.com/

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

- 配置文件

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <configuration>
      <property name="pattern" value="[%-5level] %d{yyyy-MM-dd HH:mm:ss:SSS} %msg --------- %c %M %thread%n"></property>
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
    @Component
    @ConfigurationProperties(prefix = "OnePerson")
    @Date
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
   
  # \t是转义字符，使用引号包裹转义可生效
  tempDir: ${location.baseDir}\temp
  ```
  
  - 自定义对象封装的警告解决方案
  
  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-configuration-processor</artifactId>
      <optional>true</optional>
  </dependency>
  ```
  



### 整合junit

- 因为maven在打包时默认会测试，所以springboot默认整合了junit
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

- 问题
  - spring在整合junit时，需要加注解@RunWith（设置类运行器）和@SpringContext（指定spring容器）。
  - springboot会在测试类所在的包中找SpringBootConfiguration
  - 如果测试类和引导类在同一包下，则不需要指定spring容器位置。
  - 如果不在同一包下，则需用@SpringBootTest(classes = 引导类的class) 或 @ContextConfiguration(classes = 引导类的class)



### 整合mybatis

- 勾选MyBatis技术和数据库连接技术，也就是导入MyBatis对应的starter和数据库连接

- 配置连接

```yaml
spring:
  datasource:
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





## 运维篇

### 服务器启动

#### windows

- 对springboot项目打包

```maven
mvn package
```

- 运行项目

```cmd
java -jar springboot.jar
```

- 注：jar支持命令行启动需要依赖maven插件

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

```cmd
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

- springboot中的4级配置文件（复制叠加）

  - 1级：file：application.yml
  - 2级：file：config/application.yml
  - 3级：classpath：application.yml
  - 4级：classpath：config/application.yml
  - 注：文件优先级：properties > yml > yaml

- spring配置文件的设置

  - 添加临时属性

  ```cmd
  --spring.config.name=newApplication
  --spring.config.location=classpath:/newApplication1.yml/newApplication2.yml
  ```


#### 属性层级

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

  

  

## 开发篇

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

      



































































## 原理篇



### spring补充

#### bean 的加载方式

##### 第一种

- xml + \<bean/>



##### 第二种

- xml:context + 注解（@Component+4个@Bean）



##### 第三种

- 配置类+扫描+注解（@Component+4个@Bean）
  - @Bean定义FactoryBean接口
  - @ImportResource
  - @Configuration 注解的 proxyBeanMethod 属性
    - ProxyBeanMethods = true（默认），表示使用配置类的代理对象
    - 使用代理对象时，通过配置类调用方法方法时创建对象时，先检查容器中有没有相同实体的对象，若有，则使用容器内的对象



##### 第四种

- @ImportResource(String[] files)：可导入外部配置

- @Import(Class<?>[] classes)
  - 可导入实体类从而创建bean
  - 可导入配置类

```java
@Configuration
@ImportResource({"applicationContext1.xml","applicationContext2.xml"})
@Import({SpringConfig2.class,SpringConfig3.class})
public class SpringConfig1 {
    @Bean
    public DogFactroyBean dog(){
        return new DogFactroyBean();
    }
}
// 所有配置文件 都配置了名为dog的bean
// 顺序：	2.xml -> 1.xml -> SpringConfig1 -> SpringConfig3 -> SpringConfig2
```



##### 第五种

- 可以在上下文对象初始化完毕后，手动添加bean

```java
AnnotationConfigApplication ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
// registerBean(String beanName,Class<?> class,construtorArgs:......)
// 后添加的覆盖先添加的
ctx.registerBean("cat",Cat.class);
ctx.registerBean("cat",Cat.class);
// register(Class<?> class)
ctx.register(Dog.class);
```



##### 第六种

- 实现ImportSelector接口

```java
public class MyImportSelector implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata metadata) {
        return new String[]{"cn.langh.bean.Dog"};
    }
}
```

```java
@Import(MyImportSelector.class)
```

- AnnotationMetadata：通过条件选择可用来控制加载bean


```
// @Import(MyImportSelector.class)注解所在的类的信息
metadata.getClassName(); // 类名
metadata.hasAnnotation("注解的全类名") // 是否有该注解
metadata.getAnnotationAttributes("注解的全类名") // 注解中的属性值
// ...
```



##### 第七种

- 实现ImportBeanDefinitionRegister


```java
public class MyRegister implements ImportBeanDefinitionRegistrar {
    @Override
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
        
    }
}
```

```java
@Import(MyRegister)
```

- AnnotationMetadata：通过条件选择可用来控制加载bean
- BeanDefinitionRegistry：用来构建bean

```java
BeanDefinition beanDefinition = BeanDefinitionBuilder.rootBeanDefinition(Dog.class).getBeanDefinition();
beanDefinition.setScope();
// ...
        registry.registerBeanDefinition("name",beanDefinition);
```



##### 第八种

- 实现BeanDefinitionRegistryPostProcessor

```java
public class MyPostProcessor implements BeanDefinitionRegistryPostProcessor {
    @Override
    public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry beanDefinitionRegistry) throws BeansException {
        
    }

    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory configurableListableBeanFactory) throws BeansException {

    }
}
```

```
@Import(MyPostProcessor.class)
```

- 等到系统的bean全部定义完，再定义此处的bean，若与之前的bean重复，便可覆盖





#### bean 的加载控制

##### 编程式

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

##### 注解式

- springboot提供的注解，定义在bean上或bean对应的类上

- @ConditionalOnClass(name = "全类名")：存在这个类时
- @ConditionalMissingClass(“全类名”)：不存在这个类时
- @ConditionalOnBean(......)：存在这个bean时
- @ConditionalMissingBean(......)：不存在这个bean时
- @ConditionalOnNotWebApplication：不是web项目时
-  。。。。。。





#### bean 依赖的属性配置

- 将业务功能bean运行需要的资源抽取成独立的属性类（***Properties），设置读取配置文件的信息

```java
@Data
@ConfigurationProperties(prefix = "cartoon") // 读取yml配置文件
public class CartoonProperties{
    private Cat cat;
    private Mouse mouse;
}
```

```java
@Component // 最好在spring容器上用@Import导入
@EnableConfigurationProperties(CartoonProperties.class)
public class CartoonCatAndMouse{
    private CartoonProperties = properties;
    
    public CartoonAndMouse(CartoonProperties properties){
        this.properties = properties;
        Cat cat = new Cat();
        Cat catp = properties.getCat();
        cat.setName(catp!=null && StringUtils.hasText(catp.getName()) ? catp.getName() , "tom");
    }
}
```





### 自动配置原理























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









# Android



## 快速入门

### 布局

- 在layout目录中创建xml文件

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical">

    <TextView
        android:id="@+id/tv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello,world!!!" />

</LinearLayout>
```



### MainActivity

```java
import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;
import android.widget.TextView;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main2);
        TextView tv = findViewById(R.id.tv);
        tv.setText("你好，世界！！！");
    }
}
```



### 容器

- 在AndroidManifest.xml中注册页面配置

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="cn.langh.myapplication">

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@drawable/ic_launcher_foreground"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.MyApplication"
        tools:targetApi="31">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```





## 简单控件

### 文本显示

- 设置文本内容的两种方式
  - 在 xml 文件中通过属性 android:text 设置文本
  - 在 java 代码中调用文本视图对象的 setText 方法设置文本
  - 在values包中的 string.xml 文件可定义字符串，引用："@string/(name)"
- 设置文本的大小
  - 在 java 代码中调用 setTextSize 方法
  - 在xml中通过属性 android:textSize 指定文本大小，此时需要指定字号单位
    - px：是手机屏幕的最小显示单位，与设备的显示屏有关
    - dp：与设备无关的显示单位，之与屏幕的尺寸有关
    - sp：专门用来设置字体大小，在系统设置中可以调整字体的大小
- 颜色设置
  - 在xml中设置属性 android:textColor="#00ff00"
  - 在java中调用 setTextColor(颜色参数) ，参数可以是
    - Color类中的枚举
    - 0xff00ff00l：以此为 进制、透明度、红、绿、蓝

  - 在values包中的 colors.xml 文件中可定义颜色，引用："@color/(name)"




### 视图基础

- 宽高属性值
  - match_parent：表示与上级视图保持一致
  - wrap_content：表示与内容自适应
  - 以dp、px、sp等为单位的具体尺寸
- 在java中设置视图宽高
  - 确保xml中宽高的属性值为wrap_content，接着打开该页面对应的java代码
  - 调用控件对象的 getLayoutParams 方法，获取该控件的布局参数
  - 布局参数的width、height表示宽高（单位为像素）
  - 调用控件对象的 setLayoutParams 方法，填入修改后的布局参数使之生效
- 视图间距
  - layout_margin：外间距
  - layout_padding：内间距
- 视图的对齐方式
  - layout_gravity：指定当前视图相对上一级视图的对齐方式
  - gravity：指定了下级视图相对于当前视图的对齐方式
  - 取值：
    - right、left、top、bottom、center
    - right|bottom
- 滚动视图：
  - scrollView：垂直方向的滚动视图
    - layout_height要设置为wrap_content

  - HorizontalScrollView：水平方向的滚动视图
    - layout_width要设置为wrap_content




### 常用布局

- 线性布局：LinearLayout
  - orientation属性
    - horizontal：内部视图在水平方向从左往右排列（默认）
    - certical：内部视图在垂直方向从上往下排列
  - 宽高方向上的权重
    - 如果宽或高设置为0，layout_weight设置的值为该方向上的权重
- 相对布局：RelativeLayout
  - 指定视图（属性值为某一视图）
    - layout_toLeft/RightOf：当前视图在指定视图的左/右边
    - layout_above/below：当前视图在指定视图的上/下方
    - layout_alignLeft/Right/Top/Bottom：当前视图与指定视图的左/右/上/下测对齐

  - 上级视图（属性值为true/flase）
    - layout_centerInParent：当前视图在上级视图的中间
    - layout_centerHorizontal/Vertical：当前视图在上级视图的水平/垂直方向居中
    - layout_alignParentLeft/Right/Top/Bottom：当前视图与上级视图的左/右/上/下测对齐

- 网格布局：GridLayout
  - 支持多行多列的表格排列
  - 属性
    - columnCount：指定了网格的列数
    - rowCount：指定了网格的行数




### 按钮触控

- Button继承TextView
  - Button拥有默认的按钮背景，内部文本默人对齐，默认将英文字母专为大写
  - Button新增属性
    - textAllCaps：是否将英文字母转大写
    - onClick：值为java代码中的方法名

- onClick

```xml
<Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Hello,world!"
        android:onClick="doClick"/>
<TextView
          android:id="@+id/msg"
          android:layout_width="match_parent"
          android:layout_height="wrap_content"/>
```

```java
public void doClick(View view){
    Button button = (Button) view;
    CharSequence text = button.getText();
    setContentView(R.layout.activity_main2);
    TextView tv = findViewById(R.id.msg);
    tv.setText(text+LocalTime.now().toString());
}
```

- 监听器
  - 点击监听器：setOnClickListener(OnClickListener接口的实现类对象)
  - 长按监听器：setOnLongClickListener(OnClickListener接口的实现类对象)

```java
public class MainActivity extends AppCompatActivity {
    private TextView tv;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main2);
        
        Button button = findViewById(R.id.btn);
        tv = findViewById(R.id.msg2);
        button.setOnClickListener(new OnClickListenerImpl(tv));
    }
    
    // 使用静态的匿名内部类可防止内存的泄露
    static class OnClickListenerImpl implements View.OnClickListener{
        private final TextView tv;
        public OnClickListenerImpl(TextView tv){
            this.tv = tv;
        }

        @Override
        public void onClick(View v) {
            tv.setText(LocalTime.now().toString());
        }
    }
}
```



### 图像显示











# SpringCloud



## 实用篇

### 微服务治理

#### 快速入门

- 注册 RestTemplate

  ```java
  // RestTemplete可以发送http请求
  @Bean
  public RestTemplate restTemplate(){
      return new RestTemplate;
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

- 搭建Eureka服务

  - 父工程设置依赖管理

    ```xml
    <dependencyManagement>
        <dependencies>
            <!-- eureka的管理依赖 -->
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
  
  - 编写启动类，添加@EnableEurekaServer注解
  
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

  - 在order-service项目的启动类OrderApplication中的RestTemplate中添加负载均衡注解

    ```java
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
    ```



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

    - 方式一：注册一个IRule接口的bean，返回对应的实现类

      ```java
      @Bean
      public IRule randomRule(){
          return new RandomRule();
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

    

#### Nacos

- 使用步骤

  - 启动nacos的注册中心

    ```cmd
    startup.cmd -m standalone
    ```

  - 在父工程中添加spring-cloud-alibaba的管理依赖

    ```xml
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        <version>2.2.5.RELEASE</version>
        <type>pom</type>
        <scope>import</scope>
    </dependency>
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

- 根据集群负载均衡（在本地集群中采用随机策略）

  - 服务的提供者和消费者都设置集群

  - 在服务消费者的application.yml中配置

    ```yml
    userService:
     ribbon:
      NFLoadBalancerRuleClassName: com.alibaba.cloud.nacos.ribbon.NacosRule
    ```

- 环境隔离

  - 创建命名空间

  - 添加服务到该命名空间

    ```yml
    spring:
     cloud:
      nacos:
       server-addr: localhost:8848
       discovery:
        cluster-name: HF # 集群名称
        namespace: 123 # 此处填写id名
    ```

- 配置非临时实例

  - 临时实例每个一段时间会向nacos发送一次心跳，心跳停止时，nacos会直接移出该服务

  - 非临时实例是由nacos主动询问，服务停止时，会标记健康状态为false，不会移出该服务

  - application.yml

    ```yml
    spring:
     cloud:
      nacos:
       server-addr: localhost:8848
       discovery:
        cluster-name: HF # 集群名称
        namespace: 123 # 此处填写id名
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
    - Nacos集群默认采用AP方式，当集群中存在非临时实例时，采用CP模式；Eureka采用AP方式





### 微服务框架

#### Nacos

- 统一配置管理

  - 新建配置

    - Data Id：【服务器名】-【profile】.【后缀名】
    - Group：分组
    - 配置格式：目前支持yaml和properties

  - 读取nacos配置文件

    - 项目启动后会先读取nacos中的配置文件，再读取application.yml中的配置，因此需要在bootstrap.yml中配置nacos的地址信息，才能拿到nacos的配置文件信息

    - 引入Nacos的配置管理客户端依赖

      ```xml
      <dependency>
          <groupId>com.alibaba.cloud</groupId>
          <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
      </dependency>
      ```

    - 在customerService中的resource目录添加一个bootstrap.yml文件，这个文件是引导文件，优先级高于application.yml

      ```yml
      # 微服务启动时，会读取[spring.application.name]-[spring.profiles.active].yaml，作为文件名，向Nacos查找文件
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

- 配置热更新

  - 方式一：在用@Value读取nacos配置文件的类上加上@RefreshScope

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

    - 新建配置，命名为[spring.application.name]如customeService.yaml

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

  - 配置文件优先级：customer-dev.yaml > customer.yaml > 本地配置

- Nacos集群



#### Feign

- 基本使用

  - 引入依赖

    ```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
        <version>3.1.4</version>
    </dependency>
    ```

  - 启动类加@EnableFeighClients

  - 定义和使用Feign客户端

    ```java
    @FeignClient("customerService(服务器名)")
    public interface CustomerClient {
        @GetMapping("/customer/{id}")
        Customer findById(@PathVariable("id") Long id);
    }
    ```

    ```java
    Customer customer = CustomerClient.findById({id});
    ```

- 配置

  | 类型                | 作用             | 说明                                             |
  | ------------------- | ---------------- | ------------------------------------------------ |
  | feign.Logger.Level  | 修改日志级别     | 四种级别：NONE、BASIC、HEADERS、FULL             |
  | feign.codec.Decoder | 响应结果的解析器 | 解析http远程调用结果，如解析json字符串为java对象 |
  | feign.coder.Encoder | 请求参数编码     | 将请求参数编码，便于通过http发送请求             |
  | feign.Contract      | 支出的注解格式   | 默认是SpringMVC的注解                            |
  | feign.Retryer       | 失败重试机制     | 请求失败的重试机制，默认无，但会使用Ribbon的重试 |

  













### Docker

### 异步通信

### 分布式搜索





























































