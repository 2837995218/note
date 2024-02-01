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

![盒子模型](D:\picture\typora\前端\盒子模型.png)



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

  ```java
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
          console.log(reason)// 依旧会打印 “第二步失败”，因为前面为对失败进行处理
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

  - ```js
    async function fun(data) {
        let result = await new Promise((resolve, reject) => reject(data))
            .catch(reason =>  new Promise((resolve, reject) => resolve(data)))
    }
    fun('abc').then(value => console.log("success:"+value), reason => console.log("fail:"+reason)) // success:abc
    ```

  - 方法内返回的不是一个Promise对象，则p的状态为成功

  - 方法内产生异常，则p的状态为失败

  - 方法内返回的是一个Promise对象，则p的状态与返回的Promise对象的状态保持一致

- await

  - await 必须写在 async 函数中

  - await 右侧的表达式一般为 promise 对象

  - await 返回的是 promise 成功的值

  - await 的 promise 失败了，就会抛出异常，需要通过 try..catch 捕获处理

  - ```js
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
Promise.all([p1, p3, p2]).then(value => console.log('成功'+value), reason => console.log("失败"+reason))
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

https://pxlab.cn/color/index.html

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
          let percentComplete = Math.floor((e.loaded / e.total) * 100)
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
// 第一句可简写成 const stu = {...}
const stu = Vue.extend({
    // 可以定义组件在vue开发者工具中的名字
    name: 's',
    template:`姓名：{{name}}<br>年龄：{{age}}<br>学校：<School></>`,
    // 组件中data要用方法的格式，因为对象是传递地址值
    data(){
        return {
            name: '张三',
            age: 18
        }
    },
    // 这里可以注册已定义的组件，实现组件嵌套
    components: {
        School: sch
    }
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



#### Component

- student组件本质是一个名为VueComponent的构造函数，且不是程序员定义的，是Vue.extend生成的

- 我们只需要写\<student/>,Vue解析时会帮我们创建student的实例对象，即Vue帮我们执行了：`new VueComponent(options)`

- 特别注意：每次调用Vue.extend，返回的都是一个全新的VueComponent

- 关于this的指向

  - 组件配置中，this的指向都是【VueComponent的实例对象】

- vc与vm类似，但vc没有el属性

  ![vc与vm的关系](D:\picture\typora\java\vc与vm的关系.jpeg)





### VueCLI

http://cli.vuejs.org/zh

#### 快速入门

- 全局安装VueCLI 

  ```shell
  npm install -g @vue/cli
  # 切换为淘宝镜像
  npm config set registry https://registry.npm.taobao.org
  ```

- 切换到要创建项目的目录

  ```shell
  # -n 表示不用git管理
  vue create xxxx -n
  ```

- 启动项目

  ```shell
  cd xxxx
  npm run serve
  # 生成
  npm run build
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
  ├── vue.config.js：修改vue核心配置的文件
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

- 修改vue的配置

  - 使用 vue inspect > output.js 可以 **查看** vue脚手架的默认配置

  - 在与src的同级目录中创建 vue.config.js 文件，在其中添加配置

    ```js
    // vue.config.js
    module.exports = {
        //关闭语法检查
        lintOnSave: false
    }
    ```

  - 具体配置见：https://cli.vuejs.org/zh/config/




#### ref

- 被用来给元素或子组件注册引用信息

- 应用在html标签上获取的是真实Dom元素，应用在组件标签上获取的是组件实例对象（vc）

  ```vue
  <template>
  <div>
      <h1 src="test">测试src</h1>
      <TimeTable src="timetable"></TimeTable>
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
              }
          }
      }
  </script>
  ```

  

#### props

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
          name: 'Student',
          //props: ['name', 'age', 'getName'] //简单声明接收
  
          // 限制类型
          /*props: {
              name: String,
              age: Number,
              getName: Function
          }*/
  
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
        			default: function () {
          			console.log("App未获得姓名")
        			}
      		}
      	}
      }
  </script>
  ```

- 传入参数

  ```vue
  <template>
  <div>
      <!-- 通过v-bind引号中为表达式的特性解决传递Number或其他对象的问题 -->
  <Student name="zhangsan" :age="18" :getName="getName"></Student>
  </div>
  </template>
  
  <script>
      import Student from '@/components/student';
  
      export default {
          name: 'App',
          components: {
              Student
          },
          method: {
              getName(name) {
                  console.log("App收到学生姓名："+name)
              }
          }
      }
  </script>
  ```

- props中的属性优先被放到vm上，所以可以在data中通过 this.属性 获得



#### mixin

- 定义

  ```js
  // 定义文件 mixin.js
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

- data中的数据、methods中的方法，与vc中的发生冲突时，以vc为主。而生命周期钩子函数都会执行

- 全局混入：Vue.mixin({})



#### 插件

- 定义插件

  ```js
  // 定义文件plugin.js
  export default {
      // 可以带其他参数
      install(Vue, [args...]) {
          // 定义全局过滤器
          Vue.filter()
          // 定义全局指令
          Vue.directive()
          // 定义全局混入
          Vue.mixin({})
          // 给Vue原型上添加方法，vue实例可以使用方法
          Vue.prototype.oneMethod = () => {}
      }
  }
  ```

- 使用插件

  ```js
  import Vue from 'vue'
  import plugin form './plugin'
  
  Vue.use(plugin)
  ```



#### 样式

- scoped：解决不同组件样式层叠问题
- lang：样式语言
  - 查看脚手架中webpack的版本：node_modules/webpack/package.json
  - 查看框架的所有版本：npm view xxx versions
  - 安装脚手架中webpack版本对应的less-loader版本：npm i less-loader@10









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
