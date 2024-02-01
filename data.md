# MySQL



## 基础配置

### 部署

#### 常规部署

- SQLyog连接mysql8报错

  - 原因：mysql8之前的版本中加密规则是mysql_native_password，而在mysql8之后，加密规则是caching_sha2_password

  - 解决方法：升级SQLyog  或  命令行登录mysql后，修改加密规则

    ```mysql
    # 修改本地登陆
    # 修改密码过期规则---->永不过期
    ALTER USER 'root'@'localhost' IDENTIFIED BY '密码' PASSWORD EXPIRE NEVER; 
    # 更新用户的密码修改加密规则
    ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '密码'; 
    # 刷新权限
    FLUSH PRIVILEGES;
    # 重置密码（==非必须==）
    ALTER USER 'root'@'localhost' IDENTIFIED BY '密码';
    
    # 修改远程登陆
    # 修改密码过期规则---->永不过期
    ALTER USER 'root'@'%' IDENTIFIED BY '密码' PASSWORD EXPIRE NEVER; 
    # 更新用户的密码修改加密规则
    ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '密码';
    # 刷新权限
    FLUSH PRIVILEGES;
    # 重置密码（==非必须==）
    ALTER USER 'root'@'%' IDENTIFIED BY '密码';
    ```
    
  - 文件目录
  
    - 数据库文件存放目录：/var/lib/mysql/
    - 相关命令目录：/usr/bin、/usr/sbin
    - 配置文件目录：/usr/share/mysql-8.0、/etc/mysql



#### docker部署

- 创建文件夹

  - mysql5
  
    ```shell
    mkdir -p /var/docker-volume/mysql/mysql5/log
    mkdir -p /var/docker-volume/mysql/mysql5/data
    mkdir -p /var/docker-volume/mysql/mysql5/conf
    ```
  
  - mysql8
  
    ```shell
    mkdir -p /var/docker-volume/mysql/mysql8/log
    mkdir -p /var/docker-volume/mysql/mysql8/data
    mkdir -p /var/docker-volume/mysql/mysql8/conf
    mkdir -p /var/docker-volume/mysql/mysql8/mysql-files
    ```
  


- 运行镜像

  
  - mysql5
  
    ```shell
    docker run -p 3306:3306 --name mysql5 \
    	-v /var/docker-volume/mysql/mysql5/log:/var/log/mysql \
    	-v /var/docker-volume/mysql/mysql5/data:/var/lib/mysql \
    	-v /var/docker-volume/mysql/mysql5/conf:/etc/mysql \
    	-e MYSQL_ROOT_PASSWORD=root \
    	-d mysql:5.7
    ```
  
  - mysql8
  
    ```shell
    docker run -p 3306:3306 --name mysql8 \
    	-v /var/docker-volume/mysql/mysql8/log:/var/log/mysql \
    	-v /var/docker-volume/mysql/mysql8/data:/var/lib/mysql \
    	-v /var/docker-volume/mysql/mysql8/conf:/etc/mysql \
    	-v /var/docker-volume/mysql/mysql8/mysql-files:/var/lib/mysql-files \
    	-e MYSQL_ROOT_PASSWORD=root \
    	-d mysql:8.0.23
    ```
  


- 添加配置文件conf目录下添加my.cnf文件【5.7版本】

  ```ini
  [mysqld]
  # 跳过域名解析
  skip-name-resolve
  character_set_server=utf8
  datadir=/var/lib/mysql
  server-id=1000
  ```





### 启动

- windows启动服务，需要管理员权限

  ```cmd
  # 启动 MySQL 服务命令
  net start [服务名]
  
  # 停止 MySQL 服务命令
  net stop [服务名]
  ```

- 登录

  ```cmd
  # 若 mysql 指令无法使用，需要配置环境bian'l
  mysql -uroot -p[密码]
  
  mysql -uroot -P [指定端口下的mysql] -p[密码]
  
  mysql -uroot -P [指定端口下的mysql] -h [指定ip地址下的mysql] -p[密码]
  ```

- 查看版本

  ```shell
  mysql -V;
  mysql --version;
  ```

- 退出

  ```shell
  quit 或 exit
  ```





### 编码问题

- 说明：
  - mysql 5.7 版本默认使用Latin编码，无法添加中文字符的内容
  - mysql 8+ 版本默认使用utf8mb4编码，兼容utf8（utf8mb3）编码

- 步骤1：查看编码命令

  ```bash
  show variables like 'character_%';
  show variables like 'collation_%';
  
  # 查看指定的数据库/表信息
  show create database [数据库名];
  show create table [表名];
  ```

- 步骤2：添加mysql的配置文件

  ```ini
  [mysql]
  default-character-set=utf8
  
  [mysql]
  character-set-server=utf8
  collation-server=utf8_general-ci
  ```

- 步骤3：重启服务





## 表语法

### SELECT

- 基本语法

  ```sql
  SELECT 1, 2+3;
  SELECT 1, 2+3 FROM DUAL; -- DUAL 伪表
  
  SELECT * FROM [表名];
  SELECT [字段], [字段], [...] FROM [表名];
  
  -- 为结果集中的字段取别名
  -- as 可以省略
  -- 列的别名可用用""引起来，不能用''
  SELECT [字段] AS [别名] FROM [表名];
  
  -- 结果集去重
  SELECT DISTINCT [字段] FROM [表名];
  
  -- 着重号 ``
  SELECT * FROM `order`;
  
  -- 常数
  SELECT 'abc', 2, [字段] FROM [表名];
  
  -- 显示表结构
  DESCRIBE [表名];
  DESC [表名];
  ```

- 使用 WHERE 过滤数据

  ```sql
  -- WHERE 紧挨在 FROM 后面
  SELECT * FROM [表名] WHERE [过滤条件];
  ```



#### 运算符

- 基本运算符
- 逻辑运算符
  - NOT 或 !：逻辑非
  - AND 或 &&：逻辑与
  - OR 或 ||：逻辑或
  - XOR：异或
- 比较运算符
  - 符号
  - 非符号
    - IS NULL：为空
    - ISNULL(字段)：为空
    - IS NOT NULL：不为空
    - LEAST(数字1, 数值2, ...)：最小值
    - GREATEST(数字1, 数值2, ...)：最大值
    - (NOT) BETWEEN 值1 AND 值2：(不在)两值之间，包含两值
    - (NOT) IN (值1, 值2, ...)：(不)属于
    - LIKE '_内容%'：模糊匹配，\_：代表1个字符；%：代表0或多个
    - REGEXP '正则'：正则表达式
    - RLIKE '正则'：正则表达式



#### 排序分页分组

- 排序

  ```sql
  -- 升序排序（默认，ASC可以省略）
  SELECT * FROM [表名] ORDER BY [salary] ASC;
  
  -- 降序排列
  SELECT * FROM [表名] ORDER BY [salary] DESC;
  
  -- 二级排序
  SELECT * FROM [表名] ORDER BY [salary] DESC, [age] ASC;
  ```

- 分页

  ```sql
  -- 偏移量 = (当前页数-1)*每页条数
  SELECT * FROM [表名] LIMIT [偏移量],[每页条数];
  
  -- mysql8+
  SELECT * FROM [表名] LIMIT [每页条数] OFFSET [偏移量];
  ```

- 分组

  ```sql
  -- SELECT 中出现的非聚合函数字段必须声明在GROUP BY中
  SELECT [查询字段] FROM [表名] GROUP BY [分组字段];
  ```

  - 如果==过滤条件==中使用了聚合函数，则必须使用HAVING来替换WHERE
  - 如果使用HAVING来描述过滤条件，则 滤条件用到的字段是分组字段中声明的 或 过滤条件用到的字段是聚合函数字段 
  - HAVING必须使用在GROUP BY后面
  - WHERE的执行效率更高



#### 多表查询

```sql
-- SQL92
SELECT [字段1], [表名].[字段2]， [...]
FROM [表1], [表2], [...]
WHERE [连接关系1] AND [连接关系2] AND [...]

-- SQL99
SELECT [字段1], [表名].[字段2]， [...]
FROM [表1] JOIN [表2]
ON [连接关系1]
JOIN [表2]
ON [连接关系2]
...

-- 可以在 FROM 中给表起别名，而在 SELECT 和 WHERE 中能且只能使用别名
```

- 多表查询的分类

  - 等值连接、非等值连接

  - 自连接、非自连接

    - 自连接：当做非自连接使用

  - 内连接、外连接

    - 内连接：合并具有同一列的两个以上的表的行，结果集中不包含一个表或另一个表匹配的行

    - 外连接：合并具有同一列的两个以上的表的行，结果集中除了包含一个表与另一个表匹配的行之外，还查询到了左表 或 右表中不匹配的行

      ```sql
      -- SQL92 外连接 (mysql不支持)
      SELECT e.employee_id, d.department_name
      FROM employees e, departments d
      WHERE e.department_id = d.department_id(+);
      
      -- SQL 99 外连接 (mysql不支持满外连接)
      -- 外连接 OUTER 可以省略，右外连接用 RIGHT，满外连接用 FULL
      SELECT e.employee_id, d.department_name
      FROM employees e LEFT OUTER JOIN departments d
      ON e.department_id = d.department_id;
      ```

    ![sqljoin](D:\picture\typora\mysql\sqljoin.jpeg)



- UNION 合并SELECT结果集

  - UNION：会执行去重操作
  - UNION ALL：不会执行去重操作
  - 结论：如果明确知道合并数据后的结果数据不存在重复数据，或者不需要去除重复数据，则尽量使用UNION ALL语句，以提高效率
  - 可以使用 UNION 或 UNION ALL 实现mysql的满外连接

- SQL 99 特性

  - 自然连接：自动查询两张表中所有相同的字段，然后进行等值连接

    ```sql
    SELECT [字段1], [表名].[字段2]， [...]
    FROM [表1] NATURAL JOIN [表2];
    ```

  - USING：填入需要指定的两张表的同名字段

    ```sql
    SELECT [字段1], [表名].[字段2]， [...]
    FROM [表1] JOIN [表2]
    USING ([两张表的同名字段名])
    ```



#### 函数

- 数值函数

  | 函数                  | 用法                           |
  | --------------------- | ------------------------------ |
  | ABS(X)                | 返回x的绝对值                  |
  | SIGN(X)               | 返回x的符号，正数为1，负数为-1 |
  | CEIL(X)               | 向上取整                       |
  | FLOOR(X)              | 向下取整                       |
  | LEAST(e1, e2, ...)    | 取最小值                       |
  | GREATEST(e1, e2, ...) | 取最大值                       |
  | MOD(x, y)             | 返回x除以y后的余数             |
  | RAND()                | 返回0-1的随机数                |
  | RAND(x)               | 返回0-1的随机数，x作为种子值   |
  | ROUND(x)              | 对x值四舍五入                  |
  | ROUND(x, y)           | 保留y位小数，四舍五入          |
  | TRUNCATE(x, y)        | 将x从小数点后y位开始截断       |

- 字符串函数

  | 函数                                                         | 用法                                                    |
  | ------------------------------------------------------------ | ------------------------------------------------------- |
  | CHAR_LENGTH(s)                                               | 返回字符串s的字符数                                     |
  | LENGTH(s)                                                    | 返回字符串s的字节数，与字符集有关                       |
  | CONCAT(s1, s2, ...)                                          | 连接字符串s1, s2, ...                                   |
  | INSERT(s, i, len, repalce)                                   | 将字符串s从第i位开始，len个字符替换为replace            |
  | REPLACE(str, a, b)                                           | 将字符串str中出现的所有a替换为b                         |
  | UCASE(s)                                                     | 所有字母转为大写                                        |
  | LCASE(s)                                                     | 所有字母转为小写                                        |
  | LEFT(str, n)                                                 | 返回字符串str最左边的n个字符                            |
  | RIGHT(str, n)                                                | 返回字符串str最右边的n个字符                            |
  | LPAD(str, len, pad)                                          | 用字符串pad对str最左边进行填充，直到字符串达到len个字符 |
  | RPAD(str, len, pad)                                          | 用字符串pad对str最右边进行填充，直到字符串达到len个字符 |
  | TRIM, LTRIM, RTRIM                                           | 去除左右、左、右的空格                                  |
  | TRIM(s1 FROM s)<br/>TRIM(LEADING s1 FROM s)<br/>TRIM(TRAILING s1 FROM s) | 去除首尾、首、尾处的字符串s1                            |

- 日期时间类函数

  | 函数                                         | 用法                       |
  | -------------------------------------------- | -------------------------- |
  | CURDATE()                                    | 返回当前日期，只包含年月日 |
  | CURTIME()                                    | 返回当前日期，只包含时分秒 |
  | NOW()                                        | 返回当前系统时间           |
  | UTC_DATE()                                   | 返回UTC日期                |
  | UTC_TIME()                                   | 返回UTC时间                |
  | YEAR(CURDATE())<br/>MONTH(CURDATE())<br/>... | 当前年份、月份...          |

- 流程控制函数

  | 函数                                                         | 用法                                           |
  | ------------------------------------------------------------ | ---------------------------------------------- |
  | IF(boolean, right, left)                                     | 如果boolean为true，则返回right，否则返回left   |
  | IFNULL(value1, value2)                                       | 如果value1不为null，返回value1，否则返回value2 |
  | CASE WHEN 条件1 THEN 结果1<br/>WHEN 条件2 THEN 结果2<br/>...<br/>ELSE 结果n END | 相当于if...else if...else                      |
  | CASE expr WHEN 常量1 THEN 值1<br/>WHEN 常量2 THEN 值2<br/>...<br/>ELSE 值n END | 相当于switch...case...default                  |

- 加密解密函数

- 聚合函数

  | 函数                 | 用法                                     |
  | -------------------- | ---------------------------------------- |
  | AVG(字段)            | 求平均值                                 |
  | SUM(字段)            | 求和                                     |
  | MAX(字段)、MIN(字段) | 最大值、最小值（适用于字符串和日期）     |
  | COUNT(字段)          | 指定字段在查询结构中的个数，不计算null值 |

  

#### 子查询

- 引入

  ```sql
  -- 谁的工资比 Abel 的工资高
  
  -- 两段查询
  SELECT salary from employees WHERE last_name='Abel'; -- 得到 Abel 工资为11000
  SELECT last_name, salary FROM employees WHERE salary>11000;
  
  -- 自连接查询
  SELECT c1.name, c1.money 
  FROM customer c1 
  JOIN customer c2 ON c2.money < c1.money AND c2.name = 'Abel'
  
  -- 子查询
  SELECT NAME,money 
  FROM customer 
  WHERE money > (SELECT money 
                 FROM customer 
                 WHERE NAME='Abel')
  ```

- 单行子查询

- 多行子查询

  - IN：在其中吗
  - ANY/SOME：比其中任意一个
  - ALL：比所有都要

- 相关子查询

  ```sql
  -- 查询员工的工资是否大于 所有员工 的平均员工工资
  SELECT NAME, money, gender, 
  CASE WHEN money >= (SELECT AVG(money) 
                      FROM customer) THEN '是'
       ELSE '否' END "是否多于所有员工的平均工资"
  FROM customer
  
  -- 查询员工的工资是否大于 本性别员工 的平均员工工资
  SELECT NAME, money, gender, 
  CASE WHEN money >= (SELECT AVG(money) 
                      FROM customer 
                      WHERE gender = c.gender 
                      GROUP BY gender) THEN '是'
       ELSE '否' END "是否多于该性别员工平均工资"
  FROM customer c
  
  -- 查询出员工的工资大于 本性别员工 的平均员工工资的员工的一种写法
  SELECT t1.name, t1.money, t1.gender
  FROM customer t1, (SELECT gender, AVG(money) avg_money 
                     FROM customer 
                     GROUP BY gender) t2
  WHERE t1.gender = t2.gender 
  AND t1.money >= t2.avg_money
  ```

  - EXISTS：关联子查询存在满足条件的行则返回true
  - NOT EXISTS：



#### 综合案例

- emp_sal表结构

  | id   | name | salary |
  | ---- | ---- | ------ |
  | 1    | 张三 | 4000   |

- emp_occ表结构

  | id   | name | occupation |
  | ---- | ---- | ---------- |
  | 1    | 张三 | 社畜       |

- 查询员工姓名、职位、工资、工资是否达到该员工对应职位的平均值

  ```sql
  SELECT  emp_sal.`name` "姓名", emp_occ.`occupation` "职位", salary "工资", 
  CASE WHEN salary >= 
  (SELECT 职业平均工资 FROM 
  	(SELECT occupation, AVG(salary) "职业平均工资" FROM 
  		(SELECT emp_sal.name, emp_occ.`occupation`, salary 
           FROM emp_sal JOIN emp_occ ON emp_sal.name = emp_occ.`name`) tb1
  	GROUP BY occupation) 
   tb2 WHERE occupation = 职位)  
  THEN '是'
  ELSE '否' END "是否达到该职位平均值"
  FROM emp_sal
  JOIN emp_occ
  ON emp_sal.`name` = emp_occ.`name`
  ```

  



### DDL

#### 操作数据库

- 创建数据库

  ```sql
  CREATE DATEBASE [数据库名];
  CREATE DATABASE [数据库名] CHARCTER SET '字符集';
  CREATE DATABASE IF NOT EXISTS [数据库名];
  ```

- 查看数据库

  ```sql
  -- 查看数据库的结构
  SHOW CRERATE DATABASE [数据库名];
  -- 查看所有数据库
  SHOW DATABASES;
  -- 切换数据库
  USE [数据库名];
  -- 查看当前数据库中所有的表
  SHOW TABLES;
  -- 查看指定数据库中所有的表
  SHOW TABLES FROM [数据库名];
  -- 查看当前使用的数据库
  SELECT DATABASE() FROM DUAL;
  ```

- 修改数据库

  ```sql
  -- 修改数据库字符集
  ALTER DATABASE [数据库名] CHARACTER SET '字符集'
  ```

- 删除数据库

  ```sql
  DROP DATABASE [数据库名];
  DROP DATABASE IF EXISTS [数据库名];
  ```

#### 操作表

- 创建表

  ```sql
  CREATE TABLE [表名] (
      字段1 类型1,
      字段2 类型2 (CHARACTER SET '字符集'), -- 可以指定字段的字符集
      ...
  ) (CHARACTER SET '字符集') -- 也可以在创建表时指定字符集
  
  -- 基于查询的结果集创建表
  CREATE TABLE [表名] AS SELECT ...
  ```

- 修改表

  ```sql
  -- 添加字段
  ALTER TABLE [表名] ADD [字段名] [数据类型]; -- 默认添加到表的最后一个字段
  ALTER TABLE [表名] ADD [字段名] [数据类型] FIRST;
  ALTER TABLE [表名] ADD [字段名] [数据类型] AFTER [已存在的字段名];
  -- 修改字段
  ALERT TABLE [表名] MODIFY [字段] [类型];
  ALERT TABLE [表名] MODIFY [字段] [类型] [默认值];
  ALERT TABLE [表名] CHANGE [原字段] [新字段] [类型];
  -- 删除字段
  ALERT TABLE [表名] DROP COLUMN [字段];
  -- 重命名表
  ALTER TABLE [原表名] RENAME TO [新表名];
  RENAME TABLE [原表名] TO [新表名];
  -- 删除表
  DROP TABLE (IF EXISTS) [表名];
  -- 清空表
  TRUNCATE TABLE [表名];
  ```
  
  

### DCL

- COMMIT：提交数据。一旦执行，则数据就被永久保存到数据库中，不可回滚

- ROLLBACK：回滚数据

- 对比 TRUNCATE TABLE 和 DELETE FROM 的区别

  - 相同点：都可以清空表
  - 不同点：
    - TRUNCATE TABLE：DDL，数据不可回滚
    - DELETE FROM：DML，数据可以实现回滚
  - TRUNCAET TABLE 比 DELETE 速度快，使用的系统和日志资源少

- DDL 和 DML 的区别

  - DDL 的操作一旦执行，就不可以回滚
  - DML 的操作默认不可回滚，但是在执行 DML 之前，执行了 ==SET autocommit = FALSE==，则执行的DML操作就可以回滚

- MySQL8+的特性——DDL的原子性

  ```sql
  -- book1 表存在，book2 表不存在
  DROP TABLE book1, book2;
  
  -- mysql5+ 执行以上命令，删除book1，删除book2，报错。结果：book1删除成功，book2删除失败
  -- mysql8+ 执行以上命令，删除book1，删除book2，报错，回滚book1。结果：book1、book2删除失败
  ```



### DML

- 插入数据

  ```sql
  -- 按字段声明先后顺序添加
  INSERT INTO [表名] VALUES ([值1], [值2], [...]) ([值1], [值2], [...]) ...
  
  -- 按指定字段添加
  INSERT INTO [表名] ([字段2], [字段1], [...]) VALUES ([值2], [值1], [...]) ...
  
  -- 将查询的结果集插入到表中
  -- 注意表1与表2对应字段的数据类型和数据类型的范围
  INSERT INTO [表1] ([字段2], [字段1], [...])
  SELECT [字段2], [字段1], [...]
  FROM [表2]
  ...
  ```

- 修改数据

  ```sql
  UPDATE [表名] SET [字段1] = [值1], [字段2] = [值2], ... -- 批量修改
  UPDATE [表名] SET [字段] = [值] WHERE ...
  ```

- 删除数据

  ```sql
  DELETE FROM [表名] WHERE ...
  ```

- mysql8+ 新特性：计算列

  ```sql
  -- 创建表
  CREATE TABLE [](
  	a INT,
      b INT,
      c INT GENERATED ALWAYS AS (a+b) VIRTUAL
  )
  ```





### 数据类型

- 整型数据类型

  | 整型类型    | 字节 | 取值范围     | 无符号取值范围 |
  | ----------- | ---- | ------------ | -------------- |
  | TINYINT     | 1    | -128~127     | 0~2^8-1        |
  | SMALLINT    | 2    | -32768~32767 | 0~2^16-1       |
  | MEDIUMING   | 3    |              | 0~2^24-1       |
  | INT/INTEGER | 4    |              | 0~2^32-1       |
  | BIGINT      | 8    |              | 0~2^64-1       |

  - INT(M) 与 ZEROFILL

    - ZEROFILL 修饰的字段会自动添加 UNSIGNED(无符号取值) ，被其修饰只能添加正数

    ```sql
    CREATE TABLE [] (
    	f1 INT,
        f2 INT(5),
        f3 INT(5) ZEROFILL -- 插入的数据不足5位，前面补零填充
    )
    ```

- 浮点数和定点数

  - 浮点数

    | 类型   | 占用字符 | 有符号取值范围 | 无符号取值范围 |
    | ------ | -------- | -------------- | -------------- |
    | FLOAT  | 4        |                |                |
    | DOUBLE | 8        |                |                |
    | REAL   | 8/4      |                |                |

    - REAL 默认就是DOUBLE。如果把SQL模式设定为启用"REAL_AS_FLOAT"，那么MySQL的REAL就是FLOAT。可通过以下SQL启用

      ```sql
      SET sql_mode = "REAL_AS_FLOAT";
      ```

    - M,N：DOUBLE(M, N)，M指整数位+小数位，N指小数位，超出小数位默认四舍五入，超出整数位报错

  - 定点数：DECIMAL，默认DECIMAL(10, 0)

- 位类型：BIT，默认BIT(1)，可存0，1

  | 二进制字符串类型 | 长度 | 长度范围 | 取值范围 | 占用空间          |
  | ---------------- | ---- | -------- | -------- | ----------------- |
  | BIT(M)           | M    | 1<=M<=64 | 0~2^M-1  | 约为(M+7)/8个字节 |

- 日期时间类型

  - YEAR：年
  - TIME：时间，标准格式 HH:mm:ss
  - DATE：日期，标准格式 yyyy-MM-dd
  - DATETIME：日期时间
    - 占8个字节，取值范围大，开发中使用较多

  - TIMESTAMP：日期时间
    - 占4个字节，底层存储的是毫秒数，添加数据的时候对当前的时区转换成标准时间，查询的时候再将标准时间转换成所在时区的时间

- 文本字符串类型

  | 文本字符串类型 | 值的长度 | 长度范围       | 占用的存储空间 |
  | -------------- | -------- | -------------- | -------------- |
  | CHAR(M)        | M        | 0<=M<=255      | M个字节        |
  | VARCHAR(M)     | M        | 0<=M<=65535    | M+1个字节      |
  | TINYTEXT       | L        | 0<=L<=255      | L+1个字节      |
  | TEXT           | L        | 0<=L<=65535    | L+2个字节      |
  | MEDIUMTEXT     | L        | 0<=L<=16777215 | L+3个字节      |
  | LONGTEXT       | L        | 0<=L<=大约4GB  | L+4个字节      |

  - CHAR(M)：固定长度，若数据的实际长度比声明时的长度小，则会在右侧填充空格以达到指定长度。而检索该数据时，则会去除尾部的空格。适用于存储uuid等固定长度的内容

  - ENUM类型

    - 定义，字母忽略大小写

      ```sql
      CREATE TABLE test_enum(
      	season ENUM('春', '夏', '秋', '冬', 'unknow')
      );
      INSERT INTO test_enum VALUES ('春'); -- 只能存入设定值中的一个
      ```

  - SET类型

    - 定义

      ```sql
      CREATE TABLE test_set(
      	s SET('A', 'B', 'C')
      );
      ```

    - 可以存入多个设定值

    - 插入重复的SET类型成员时，MySQL会自动删除重复的成员 

  - 二进制字符串类型

    - BINARY 与 VARBINARY

      - BINARY(M)：固定长度，0<=M<=255，M个字节
      - VARBINARY(M)：可变长度，0<=M<=65535，M+1个字节

    - BLOB：可以存储大的二进制对象，如视频、音频和图片等

      | 二进制字符串类型 | 值的长度 | 长度范围   | 占用空间  |
      | ---------------- | -------- | ---------- | --------- |
      | TINYBLOB         | L        | 0<=L<=255  | L+1个字节 |
      | BLOB             | L        | 0<=L<=64KB | L+2个字节 |
      | MEDIUMBLOB       | L        | 0<=L<=16MB | L+3个字节 |
      | LONGBLOB         | L        | 0<=L<=4GB  | L+4个字节 |

  - JSON类型

    ```sql
    CREATE TABLE test_json(
    	js json
    );
    
    INSERT INTO test_json (js)
    VALUES ('{"name": "张三", "age": 18, "address": {"province": "安徽", "city": "合肥"}}')
    
    SELECT js -> '$.name' AS NAME, js -> "$.address.city" AS city
    FROM test_json;
    ```

- 选择建议

  - 整数一般用INT，小数一般用DECIMAL(M, D)，日期时间类型一般用DATETIME
  - 《java开发手册》
    - 任何字段如果为非负数，必须是 UNSIGNED
    - 小数类型必须使用DECIMAL
    - 如果存储的字符串长度几乎相等，使用CHAR
    - VARCHAR是可变字符串，不预先分配空间，长度不要超过5000。若超过，定义为TEXT类型，独立出来一张表，用主键来对应，避免影响其他字段的索引效率





### 约束

- 查看表中的约束

  ```sql
  SELECT * FROM information_schema.table_constraints
  WHERE table_name = '表名';
  
  SHOW INDEX FROM [表名]
  ```

- 添加约束

  - 创建表时添加

    ```sql
    CREATE TABLE empl(
    	id INT PRIMARY KEY AUTO_INCREMENT, -- 列级约束
        first_name varchar(30),
        last_name varchar(30),
        email varchar(30),
        -- 表级约束，CONSTRAINT uk_test_email 为约束起名，可以省略
        CONSTRAINT uk_test_email UNIQUE(email),
        -- 联立多个字段，执行唯一约束
        CONSTRAINT uk_test_first_last_name UNIQUE KEY (first_name, last_name)
    )
    ```

  - 使用 ==ALTER TABLE 表名 MODIFY 字段 类型 约束;==

- 删除约束

  ```sql
  ALTER TABLE [表名] DROP INDEX [约束名]
  ```

- 常用约束

  - not null（非空约束）：

  - unique（唯一性约束）：

  - primary key（主键约束）：

    - AUTO_INCREMENT：
      - mysql5+：mysql重启时，AUTO_INCREMENT会重置到 max(primary key)+1
      - mysql8+：会将自增主键的计数器持久化到==重做日志==中。每次计数器发生变化，都会将其写入重做日志中。如果数据库重启，会读取日志中的信息初始化计数器

  - check（检查约束，mysql5.7不支持，8+支持）：

    ```sql
    CREATE TABLE test_check(
    	id INT,
        name VARCHAR(30),
        gender CHAR(1) CHECK('男' or '女'),
        salary DECIMAL(10, 2) CHECK(salary > 3000)
    )
    ```

  - default（默认值约束）：

    - 字符串：not null default ''
    - 数值：not null default 0

- foreign key

  - 定义

    ```sql
    CREATE TABLE empl(
    	empl_id INT PRIMARY KEY AUTO_INCREMENT,
        empl_name varchar(30),
        department_id INT,
        
        -- 要求 dept 表中的 dept_id 必须是UNIQUE修饰的（primary key也可以）
        CONSTRAINT fk_empl_dept_id 
        FOREIGN KEY (department_id) REFERENCES dept(dept_id) [约束等级]
    )
    ```

    ```sql
    ALTER TABLE [表名]
    ADD CONSTRAINT [约束名]
    FOREIGN KEY (department_id) REFERENCES dept(dept_id) [约束等级]
    ```

  - 约束等级

    - Cascade：在父表上update/delete记录时，同步update/delete子表的匹配记录
    - Set null：在父表上update/delete记录时，将字表上匹配记录的列设为null，注意字表该列不能设为not null
    - Restrict/No action：如果子表中有匹配的记录，则不允许对父表对应候选键进行update/delete操作
    - Set default：父表有变更时，子表将外键列设置为一个默认的值，但Innodb不能识别
    - 对于外键约束，一般使用：ON UPDATE CASCADE ON DELETE RESTRICT

  - 阿里开发规范

    - 不得使用外键与级联，一切外键概念必须在应用层解决
    - 只适用于单机低并发，不适用于分布式、高并发集群
    - 父表改动导致子表改动为级联，级联更新是强阻塞，存在数据库更新风暴的风险
    - 外键影响数据库的插入速度





## 数据库对象

### 视图

- 创建视图

  ```sql
  CREATE [OR REPLACE] -- 创建或替换
  [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
  VIEW 视图名称 [(字段列表)]
  AS 查询语句
  [WITH [CASCADED|LOCAL] CHECK OPTION]
  ```

- 查看视图

  ```sql
  -- 查看表、视图对象
  SHOW TABLES;
  -- 查看视图结构
  DESC / DESCRIBE 视图名称;
  -- 查看视图的属性信息
  SHOW TABLE STATUS LIKE '视图名称'\G
  -- 查看视图的详细定义信息
  SHOW CREATE VIEW 视图名称
  ```

- 更新视图数据：更新视图数据会更新原表中的数据



### 存储过程

- 创建存储过程

  ```sql
  DELIMITER $
  CREATE PROCEDURE 存储过程名(IN | OUT | INOUT 参数名 参数类型, ...)
  [characterstic] [characterstic] [...]
  BEGIN
  	存储过程体; -- 分号
  END $
  DELIMITER ;
  ```

- characterstics：表示创建存储过程时指定的存储过程的约束条件，其取值信息如下

  ```sql
  -- 说明存储过程执行体是由SQL语句组成的
  LANGUAGE SQL
  
  -- 指明存储过程执行的结果是明确的，默认 not deterministic ，表示输入相同的值，可能得到不同的结果
  [NOT] DETERMINISTIC
  
  -- 指明子程序使用SQL语句的限制
  -- 默认，contains sql：包含sql，但不包含读写数据的sql
  -- no sql：不包含sql
  -- reads sql data：包含读数据的sql
  -- modifies sql data：包含写数据的sql
  { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }
  
  -- 执行当前存储过程的权限
  -- definer：该存储过程只能由创建者或定义者才能执行
  -- invoker：表示拥有当前存储过程的访问权限的用户可以执行当前存储过程
  SQL SECURITY {DEFINER | INVOKER}
  
  -- 注释信息，可用来描述存储过程
  COMMENT 'string'
  ```

- 调用

  ```sql
  -- 查看
  show create procedure 存储过程名;
  -- 删除
  drop procedure if exists 存储过程名;
  -- 调用
  CALL 存储过程名();
  ```

- 举例

  ```sql
  -- 定义
  delimiter $
  create procedure get_someone_salary_from_empl
  (in someone_name varchar(20), out one_salary decimal(10, 2))
  begin
  	select salary into one_salary
  	from empl where name = someone_name ;
  end $
  delimiter ;
  
  -- 调用
  set @name = '张三'
  call show_some_column_min(@name, @one_salary);
  select @one_salary;
  
  
  ================示例2================
  CREATE TABLE student(
  stu_id INT PRIMARY KEY AUTO_INCREMENT,
  math DEC(5, 2),
  chinese DEC(5,2),
  english DEC(5, 2),
  score DEC(5,2)) AUTO_INCREMENT = 1000001
  
  
  DELIMITER $
  CREATE PROCEDURE insert_student(IN number INT)
  BEGIN
  DECLARE i INT DEFAULT 0;
  DECLARE math_score DEC(5,2);
  DECLARE chinese_score DEC(5,2);
  DECLARE english_score DEC(5,2);
  SET autocommit = 0; # 设置手动提交事务
  REPEAT
  SET i = i+1;
  SET math_score = RAND()*150;
  SET chinese_score = RAND()*150;
  SET english_score = RAND()*150;
  INSERT INTO student(math, chinese, english, score)
  VALUES (math_score, chinese_score, english_score, ROUND(math_score+chinese_score+english_score));
  UNTIL i = number
  END REPEAT;
  COMMIT; # 提交事务
  END $
  DELIMITER ;
  ```
  
  

### 存储函数

- 创建存储函数

  ```sql
  DELIMITER $
  CREATE FUNCTION 函数名(参数名 参数类型, ...)
  RETURNS 返回值类型
  [characterstic] [characterstic] [...]
  BEGIN
  	函数体; -- 分号
  END $
  DELIMITER ;
  ```

- 调用

  ```sql
  -- 查看
  show create function 存储函数名;
  -- 删除
  drop function if exists 存储函数名;
  -- 调用
  SELECT 函数名(参数)
  ```

- 举例

  ```sql
  DELIMITER $
  use 'test' $
  drop function if exists `abc`$
  create function abc(admin_id INT)
  returns varchar(20)
  begin
  	return (select name from admin where id = admin_id);
  end $
  DELIMITER ;
  ```

  

### 补充

#### 变量

- 系统变量

  - 查看系统变量

    ```sql
    -- 查看全局系统变量
    SHOW GLOBAL VARIABLES;
    -- 查看会话系统变量
    SHOW SESSION VARIABLES; / SHOW VARIABLES;
    
    SHOW VARIABLES LIKE 'character_%';
    ```

  - 查看指定的系统变量

    ```sql
    -- 查看指定的系统变量的值
    SELECT @@global.变量名;
    
    -- 查看指定的会话便恋的值
    SELECT @@session.变量名;
    SELECT @@变量名;
    ```

  - 修改系统变量的值

    - 方法一：修改配置文件

    - 方法二：在服务器运行期间，使用set指令重新设置系统变量的值

      ```sql
      -- 为某个系统变量赋值
      SET @@global.变量名 = 变量值;
      SET GLOBAL 变量名 = 变量值;
      
      -- 为某个会话变量赋值
      SET @@session.变量名 = 变量值;
      SET SESSION 变量名 = 变量值;
      ```

- 用户变量

  - 会话用户变量定义

    ```sql
    -- 方法一：
    SET @用户变量 = 值;
    SET @用户变量 := 值;
    
    -- 方法二：
    SELECT @用户变量 := 表达式 [FROM 等子句];
    SELECT 表达式 INTO @用户变量 [FROM 等子句];
    ```

  - 局部变量定义（在存储函数或过程的最开始定义）

    ```sql
    -- 用declare关键词，定义在存储过程或函数中
    BEGIN
      -- 声明局部变量
      DECLARE 变量1 变量数据类型 [DEFAULT 变量默认值];
      DECLARE 变量2, 变量3, ... 变量数据类型 [DEFAULT 变量默认值];
      
      -- 为局部变量赋值
      SET 变量名1 = 值;
      SELECT 值 INTO 变量2 [FROM 子句];
      
      -- 查看局部变量的值
      SELECT 变量1, 变量2, 变量3;
    END
    ```




#### 定义条件与处理机制

- 定义在存储过程或存储函数中

- 定义条件

  ```sql
  -- DECLARE 错误名称 CONDITION FOR 错误码（或错误条件）
  DECLARE Field_Not_Be_Null CONDITION FOR 1048;
  DECLARE Field_Not_Be_Null CONDITION FOR SQLSTATE '23000';
  ```

- 定义处理程序

  ```sql
  DECLARE 处理方式 HANDLER FOR 错误类型 处理语句;
  ```

  - 处理方式：
    - CONTINUE：表示遇到错误不处理，继续执行
    - EXIT：表示遇到错误马上退出
    - UNDO：表示遇到错误后撤回之前的操作。mysql暂不支持
  - 错误类型（即条件）
    - SQLSTATE '字符串错误码'
    - MySQL_error_code
    - 错误名称
    - SQLWARNING：匹配所有以01开头的SQLSTATE错误代码
    - NOT FOUND：匹配所有以02开头的SQLSTATE错误代码
    - SQLEXCEPTION：匹配所有未被SQLWARNING和NOT FOUND捕获的SQLSTATE错误代码
  - 处理语句
    - 语句可以是`SET 变量 = 值`，也可以是`BEIGN ... END`



#### 流程控制

- 分支结构

  ```sql
  IF 表达式 THEN 操作1
  [ELSEIF 表达式2 THEN 操作2] ...
  [ELSE 操作N]
  END IF
  ```

  ```sql
  CASE 表达式
  WHEN 值1 TEHN 结果1或语句1（语句要加分号）
  WHEN 值2 THEN 结果2或语句2
  ...
  ELSE 结果n或语句n
  END [case]
  ```

  ```sql
  CASE
  WHEN 条件1 THEN 结果1或语句1（语句要加分号）
  WHEN 条件2 THEN 结果2或语句2
  ...
  ELSE 结果n或语句n
  END [case]
  ```

- 循环

  ```sql
  [loop_label:] LOOP
    循环执行的语句，包含跳出循环的条件
  END LOOP [loop_label]
  
  -- 举例
  declear id INT default 0;
  add_loop:LOOP
    SET id = id + 1;
    IF id >= 10 THEN LEAVE add_loop
    END IF;
  END LOOP add_loop;
  ```

  ```sql
  [while_label:] WHILE 循环条件 DO
    循环体;
  END WHILE [while_label];
  ```

  ```sql
  [repeat_label:] REPEAT
    循环体;
    UNTIL 结束循环的条件表达式
  END REPEAT [repeat_label]
  ```

- LEAVE：表示跳出循环或其他程序体，类似于其他语言的break

- ITERATE：表示再次循环，类似于其他语言的continue



#### 游标

- 第一步，声明游标（游标在存储函数或存储过程中使用，在变量后定义）

  - mysql，sql server，db2，mariadb

    ```sql
    DECLARE 游标名 CURSOR FOR select_statement;
    ```

  - oracle，postgreSQL

    ```sql
    DECLARE 游标名 CURSOR IS select_statement;
    ```

- 第二步，打开游标

  - 打开游标时，select语句的查询结果集就会送到游标工作区

  ```sql
  OPEN 游标名
  ```

- 第三步，使用游标（从游标中获取数据）

  ```sql
  FETCH 游标名 INTO var_name [, var_name ...]
  ```

  - var_name 的指代与select查询语句中查询的字段保持一致，因此数量要保持一致

- 第四步，关闭游标

  ```sql
  CLOSE cursor_name
  ```

  - 如果不及时关闭游标，游标会一直保持到存储过程结束，影响系统运行的效率。





### 触发器

- 创建触发器

  ```sql
  CREATE TRIGGER 触发器名称
  {BEFORE | AFTER} {INSERT | UPDATE | DELETE} ON 表名
  FOR EACH ROW
  触发器质性的语句块;
  ```

  - 表名：表示触发器监控的对象
  - BEFORE | AFTER：表示触发的时间
  - INSERT | UPDATE | DELETE：表示触发的事件

- 举例

  ```sql
  CREATE TABLE test_trigger(
  id INT PRIMARY KEY AUTO_INCREMENT,
  VALUE VARCHAR(30)
  );
  CREATE TABLE test_trigger_log(
  id INT PRIMARY KEY AUTO_INCREMENT,
  `log` VARCHAR(30),
  `time` TIMESTAMP
  );
  
  DELIMITER //
  CREATE TRIGGER before_insert_test_tri
  BEFORE INSERT ON test_trigger
  FOR EACH ROW
  BEGIN
    INSERT INTO test_trigger_log(`log`)
    VALUES('添加成功');
  END //
  
  CREATE TRIGGER before_delete_test_tri
  BEFORE DELETE ON test_trigger
  FOR EACH ROW
  BEGIN
    INSERT INTO test_trigger_log(`log`)
    VALUES('删除成功');
  END //
  DELIMITER ;
  ```

- 查看触发器

  ```sql
  -- 查看当前数据库的所有触发器
  SHOW TRIGGERS;
  
  -- 查看当前数据库中某个触发器的定义
  SHOW CREATE TRIGGER 触发器名;
  
  -- 查看所有数据库的触发器
  SELECT * FROM information_schema.TRIGGERS;
  ```

- 删除触发器

  ``` sql
  DROP TRIGGER [IF EXISTS] 触发器名;
  ```





## 其他新特性

### 窗口函数

| 函数分类 | 函数               | 函数说明                                |
| -------- | ------------------ | --------------------------------------- |
| 序号函数 | ROW_NUMBER()       | 顺序排序                                |
|          | RANK()             | 并列排序，会跳过重复的序号，比如1、1、3 |
|          | DENSE_RANK()       | 并列排序，不会跳过重复的序号            |
| 分布函数 | PERCENT_RANK()     | 等级值百分比                            |
|          | CUME_DIST()        | 累计分布值                              |
| 前后函数 | LAG(expr, n)       | 返回当前行的前n行的expr的值             |
|          | LEAD(expr, n)      | 返回当前行的后n行的expr的值             |
| 首尾函数 | FIRST_VALUE(expr)  | 返回第一个expr的值                      |
|          | LAST_VALUE(expr)   | 返回最后一个expr的值                    |
| 其他函数 | NTH_VALUE(expr, n) | 返回第n个expr的值                       |
|          | NTILE(n)           | 将分区中的有序数据分为n个桶，记录桶编号 |

- 表结构

  | id   | category | name | price |
  | ---- | -------- | ---- | ----- |
  | 1    | 服装     | 短裤 | 79    |

- 序号函数

  ```sql
  -- 按商品种类分组，并找出每组中最贵的两件商品
  SELECT * FROM 
  (SELECT row_number() OVER(PARTITION BY category ORDER BY price DESC) AS row_num,
   id, category, NAME, price FROM goods) t
   WHERE row_num < 3
  ```

- 分布函数

  ```sql
  -- 在同类别（服装）中，比短裤便宜的其他商品占该类别（服装）的比例；即百分之多少同类商品比短裤便宜
  SELECT cume_dist() over(PARTITION BY category ORDER BY price) AS 比例,
  id, category, NAME, price FROM goods WHERE NAME = '短裤'
  ```

- 前后函数

  ```sql
  -- 价格与该商品相隔两位，比该商品贵的同类商品的价格
  SELECT NAME, price, lead(price, 2)over(PARTITION BY category ORDER BY price) AS "相隔的价格" FROM goods
  ```

- 首尾函数

  ```sql
  -- 同类最便宜的商品价格
  SELECT NAME, price, first_value(price) over(PARTITION BY category ORDER BY price) AS "同类最贵" FROM goods
  ```

  

### 公用表表达式

公用表表达式简称为CTE。CTE是一个临时的结果集，作用范围是当前语句。可以理解为一个可以复用的子查询

```sql
WITH CTE 名称
AS (子查询)

SELECT|DELETE|UPDATE 语句;
```





## 高级篇

### 权限

#### 权限管理

- 查看用户

  ```sql
  use mysql
  select user, host from user;
  ```

- 创建用户

  ```sql
  -- 创建本地连接的用户
  create user 'zhangsan'@'localhost' identified by 'zhangsan123';
  -- 创建远程连接的用户
  create user 'zhangsan'@'%' identified by 'zhangsan123';
  ```

- 修改用户

  ```sql
  update mysql.user set user='li4' where user='wangwu' and host='%';
  flush privileges;
  ```

- 删除用户

  ```sql
  drop user 'wangwu'; -- 默认是%
  drop user 'wangwu'@'localhost';
  ```

- 修改密码

  - 修改当前用户密码

    ```sql
    alter user user() ideentified by 'new_pass';
    set password='new_pass';
    ```

  - 修改其他用户的密码

    ```sql
    alter user 用户 identified by 'new_pass';
    set password for 'zhangsan'@'%'='new_pass';
    ```

- 密码过期：可以正常登录，无法查询

  ```sql
  alter user 用户 password expire;
  ```

  - 过期时间设置

    - 全局设置

      - sql语句

        ```sql
        set persist default_password_lifetime = 180 -- 建立全局策略，180天过期
        ```

      - 配置文件

        ```sql
        [mysqld]
        default_password_lifetime=180
        ```

    - 单独设置

      ```sql
      create user 'zhangsan'@'%' password expire interval 90 day;
      alter user 'zhangsan'@'%' password expire interval 90 day;
      
      -- 永不过期
      create user 'zhangsan'@'%' password expire never;
      alter user 'zhangsan'@'%' password expire never;
      
      -- 沿用全局密码过期策略
      create user 'zhangsan'@'%' password expire default;
      alter user 'zhangsan'@'%' password expire default;
      ```

- 授予权限

  | 权限分布 | 权限                                                         |
  | -------- | ------------------------------------------------------------ |
  | 表权限   | select, insert, update, delete, create, drop, grant, references, index, alter |
  | 列权限   | select, insert, update, references                           |
  | 过程权限 | execute, alter routine, grant                                |

  ```sql
  -- 查看当前用户的权限
  show grants [for '用户名'/current_user()];
  
  -- 如果没有该用户，则自动创建
  grant 权限1, 权限2, ...
  on 数据库名.表名
  to 用户名@用户地址
  [identify by '密码'];
  
  grant all privileges on *.* to 用户名@用户地址 -- 授予所有表的所有操作权限
  with grant option -- 同时可以将自己的权限授予给其他用户
  ```

- 收回权限

  ```sql
  revoke 权限1, 权限2, 权限3, ...
  on 数据库名.表名
  from 用户名@用户地址;
  ```




#### 角色管理

将一些权限封装到一个角色中，再将角色赋予给某些人

- 创建角色

  ```sql
  create role 'role_name'[@'host_name'] [, 'role_name'[@'host_name'] ...]
  ```

- 给角色赋予权限

  ```sql
  grant 一些权限 on 表名 to 'role_name'[@'host_name'];
  ```

- 查看角色权限

  ```sql
  show grants for 'role_name';
  ```

- 收回权限

  ```sql
  revoke 一些权限 on 表名 from 'role_name'[@'host_name'];
  ```

- 角色赋予

  ```sql
  grant 'role_name'[@'host_name'] to 'user_name'[@'host_name'];
  ```

- 激活角色：角色赋予后，激活角色才能使用该角色的权限

  ```sql
  -- 查看当前用户的角色，未激活也显示none
  select current_role();
  
  set default role all to 'user_name'[@'host_name'] [, 多个用户];
  
  -- 添加该配置后，赋予角色后直接激活，默认未off
  set global activate_all_roles_on_login=on;
  ```

- 撤销用户的角色

  ```sql
  revoke role_name form user_name;
  ```

- 设置用户默认角色

  ```ini
  # 服务启动前配置
  [mysqld]
  mandatory_roles='role,role2@localhost,...'
  ```
  
  ```sql
  -- 服务启动时设置
  set persist mandatory_roles = 'role1, role2@localhost, ...'; # 系统重启后仍然有效
  set global mandatory_roles = 'role1, role2@localhost, ...'; # 系统重启后失效
  ```
  



#### 配置文件

与在命令行中指定启动选项不同的是，配置文件中的启动选项被划分为若干个组，每个组都有一个组名

- 以不同命令启动mysql会调用不同的组

  | 启动命令     | 类别       | 能读取到的组                       |
  | ------------ | ---------- | ---------------------------------- |
  | mysqld       | 启动服务器 | [mysqld]、[server]                 |
  | mysqld_safe  | 启动服务器 | [mysqld]、[server]、[mysqld_safe]  |
  | mysql.server | 启动服务器 | [mysqld]、[server]、[mysql.server] |
  | mysql        | 启动客户端 | [mysql]、[client]                  |
  | mysqladmin   | 启动客户端 | [mysqladmin]、[client]             |
  | mysqldump    | 启动客户端 | [mysqldump]、[client]              |

- 若不同组声明同一配置，后声明的覆盖前声明的

- 命令行中声明的配置优先级大于配置文件中的

  ```ini
  [server]
  default-storage-engine=InnoDB
  
  [mysql.server]
  default-storage-engine=MyISAM
  ```

  ```sql
  mysql.server start --default-storage-engine=MyISAM
  ```



### 架构

#### 存储引擎

- 创建指定引擎的表

  ```sql
  create table 表名(字段 ...) engine=存储引擎;
  ```

- InnoDB存储引擎

  - /var/lib/mysql/ 目录下

    - ibdata1/：系统表空间，大小12m，自动拓展
    - 对应数据库名/：独立表空间
      - 对应数据库名/db.opt：存储数据库的信息如字符集等
      - 对应数据库名/对应表名.frm：存储表结构信息如表中字段的约束
      - 对应数据库名/对应表名.idb：存放表中的数据内容
    - 5.6.6前，表中的数据默认存放在系统表空间中；5.6.6后，表中的数据默认存放在独立表空间中

    ```sql
    -- 配置数据文件存放的位置
    -- 0：代表使用系统表空间 1：代表使用独立表空间
    [server]
    innodb_file_per_table=1
    ```

    ```sql
    -- 查询数据存储位置，on表示独立表空间开启
    show variables like 'innodb_file_per_table';
    +-----------------------+-------+
    | Variable_name         | Value |
    +-----------------------+-------+
    | innodb_file_per_table | ON    |
    +-----------------------+-------+
    1 row in set (0.01 sec)
    ```

    - 8.0，独立表空间中只有 对应表名.idb 文件，数据库信息，字段约束，数据全存放在该文件中

- MyISAM存储引擎

  - /var/lib/mysql/对应数据库名/ 目录下
    - 对应表名.frm：表结构
    - 对应表名.MYD：数据内容
    - 对应表名.MYI：数据索引

- 其他对比

  | 对比项 | MyISAM                     | InnoDB                           |
  | ------ | -------------------------- | -------------------------------- |
  | 外键   | 不支持                     | 支持                             |
  | 事务   | 不支持                     | 支持                             |
  | 行表锁 | 表锁                       | 行锁                             |
  | 缓存   | 只缓存索引                 | 缓存索引和真实数据，对内存要求高 |
  | 关注点 | 节省资源、消耗少、简单业务 | 并发写、事务、更大资源           |

- Archive引擎

  - archive表示归档，仅仅支持插入和查询两种功能。适用于日志和档案的记录，拥有很高的插入所读，对查询支持较差
  - 行级锁
  - 拥有很好的压缩机制

- blackhole引擎：丢弃所有插入的数据，因此无法查询到数据，但服务器会记录操作日志

- csv引擎：存储数据时，用逗号分隔各个字段的数据

  - 不支持允许空值的字段，即创建表是每个字段要用 not null 约束
  - 用csv引擎存储的表会生成两个文件
    - 表名.csm：元文件，存储表的状态，行数等信息
    - 表名.csv：存储真实数据的文件，可以用notepad或excel打开

- memory引擎：将数据存储到内存中

- NDB引擎：主要用于MySQL Cluster分布式集群环境

- Merge引擎：管理多个MyISAM表构成的表集合

- Federated引擎：访问其他MySQL服务器的远程表，存在一些问题，默认禁用

- InnoDB和MyISAM的区别

  - InnoDB支持外键，MyISAM不支持
  - InnoDB支持事务，MyISAM不支持
  - InnoDB默认行级锁，MyISAM只支持表锁
  - InnoDB有一个聚簇索引
  - InnoDB必须要有唯一索引，若用户未指定，会产生一个隐藏的列row-id来充当主键，MyISAM可以没有
  - 存储的文件结构区别
  - MyISAM保存表的行数





#### 整体架构

- 服务器处理客户端请求
  - 外部请求：如JDBC、PHP等
  - 连接层
    - 连接池
  - 服务层
    - SQL接口：接收SQL指令，返回查询结果
    - 缓存：查询缓存是否命中
    - 解析器：解析语法，生成语法树
    - 优化器：核心组件，对SQL进行优化，并制定执行计
  - 引擎层
    - 存储引擎：与底层的文件系统进行交互
  - 文件系统 及 日志文件





#### 逻辑结构

- 常见的索引

  - 聚簇索引：存储索引和真实数据
  - 二级索引：存储索引和数据的地址
  - 联合索引：联合索引，是一种二级索引

- MyISAM引擎和InnoDB引擎

  - MyISAM引擎：只使用二级索引
  - InnoDB引擎：使用聚簇索引和二级索引

- 数据结构

  - Hash结构：

    - 等值查找速度快，但范围查找速度慢

    - 自适应hash索引

      - InnoDB本身不支持hash索引，但提供自适应hash索引。当某个数据经常被访问，就会将这个页的地址存放的hash表中。

      - 默认开启

        ```sql
        show variables like 'innodb_adaptive_hash_index';
        ```

  - 树型结构：

    - 二叉搜索树：左子节点比本节点小，右子节点比本节点大

    - AVL树，平衡二叉搜索树：左右两个子树的高度绝对值不超过1，且左右子树也是AVL树

    - B树，多路平衡查找树：关键字集合分布在整棵树中，即叶子节点和非叶子节点都能存放数据

      ![B树](D:\picture\typora\mysql\B树.png)

    - B+树：数据存放在叶子节点中，目录节点不存放数据，因此可以存放更多目录

      ![B+树](D:\picture\typora\mysql\B+树.png)





#### 存储结构

InnoDB的底层存储结构是页，InnoDB将数据划分为若干个页，每个页的默认大小是16KB。

- 页的上层结构

  - 区：一个区分配64个连续的页，所以一个区的大小是1MB
  - 段：由一个或多个区组成。段是数据库中分配单位，不同类型的数据库对象以不同的段形式存在
  - 表空间：是一个逻辑容器，表空间的存储对象是段

- 页

  - 结构

    | 名称               | 占用大小 | 说明                               |
    | ------------------ | -------- | ---------------------------------- |
    | file header        | 38字节   | 文件头，描述页的信息               |
    | page header        | 56字节   | 页头，页的状态信息                 |
    | infimum + supremum | 26字节   | 最大最小记录，这是两个虚拟的行记录 |
    | user records       | 不确定   | 用户记录，储存行记录内容           |
    | free space         | 不确定   | 空余记录，页中还没有被使用的空间   |
    | page directory     | 不确定   | 页目录，存储用户记录的相对位置     |
    | file trailer       | 8字节    | 文件尾，校验页是否完整             |

- 为什么要有区：如果查询一个范围的记录，可能需要读取多个页。这些双向链表上的页虽然逻辑上是连续的，但实际的物理地址可能非常远，因此需要判断很多页是否是要找的页，也就是随机IO。引入区后，每个区中的页都是相连的，查找页的速度加快，也就是顺序IO。同时，每个区只有1m大小，避免了系统无法划分出过大连续空间问题。

- 为什么要有段：B+树中，有叶子节点和非叶子节点，对应的是数据页和目录页。而存放数据页的区的集合就算作一个段，存放目录页的区的集合算作一个段，即一个索引会生成两个段，一个叶子节点段，一个非叶子节点段

- 为什么要有碎片区：一个存储数据非常少的表，一个索引也会生成两个区，对应叶子节点和非叶子节点；同时若再每为一个字段添加一个索引，则又会多申请2m空间。因此提出碎片区，碎片区包括叶子节点段和非叶子节点段，当某段已经占用32个页面时，则申请完整的区。





### 调优

#### 索引

- 索引的分类

  - 功能逻辑上：普通索引、全文索引、单列索引、多列索引、空间索引
  - 物理实现方式上：聚簇索引、非聚簇索引
  - 作用字段个数：单列索引、联合索引

- 创建索引

  - 隐式：在声明有主键约束、唯一性约束、外键约束的字段上，会自动添加索引

  - 显示

    ```sql
    -- 创建表时添加
    create table 表名 (
    [字段名 数据类型, ...]
    [空 | unique | fulltext | spatial]
    [index | key]
    [索引的名字]
    (作用的字段 [(取该字段多长的数据作为索引)]
    [ASC | DESC])
    [visible | invisible]
    )
    ```

    ```sql
    alter table 表名 add 索引类型 索引名(字段) [visible | invisible]
    ```

    ```sql
    create 索引类型 索引名 on 表名(字段名) [visible | invisible]
    ```

- 索引的删除

  ```sql
  alter table 表名 drop index 索引名
  drop index 索引名 on 表名
  
  -- 查看索引
  show index from 表名;
  ```

- 创建索引的注意事项

  - 字段的数值有唯一性的逻辑限制
  - 频繁作为 WHERE 查询条件的字段
  - 经常 GROUP BY 和 ORDER BY 的列
  - UPDATE、DELETE 的 WHERE 条件列
  - 需要DISTINCT（去重）的字段可以创建索引
  - 多表 JOIN 连接操作时，创建索引的注意事项
    - 连接的表数量不要超过3张
    - 对 WHERE 条件创建索引
    - 对用于连接的字段创建索引

  - 尽量针对列对应的类型小的字段创建索引
  - 使用字符串前缀创建索引
  - 区分度高（散列性高）的列适合作为索引：如gender中只分男、女，不适合建立索引
  - 使用最频繁的列建议放到联合索引的左侧
  - 在多个字段都要创建索引的情况下，联合索引优于单值索引

- 不适合添加索引

  - 数据量小的表
  - 经常更新的表






#### 性能分析

- 慢查询日志

  - 一些变量

    - slow_query_log=OFF：慢日志查询开关
    - slow_queries：慢查询条数
    - slow_query_log_file=/var/lib/mysql/文件名-slow.log：日志文件地址
    - long_query_time=10：时间阈值
    - log_output=FILE

  - 解析文件

    ```shell
    # 本地命令行下执行，并非在mysql命令行下执行
    Usage: mysqldumpslow [ OPTS... ] [ LOGS... ]
    
    Parse and summarize the MySQL slow query log. Options are
    
      --verbose    verbose
      --debug      debug
      --help       write this text to standard output
    
      -v           verbose
      -d           debug
      -s ORDER     what to sort by (al, at, ar, c, l, r, t), 'at' is default
                    al: average lock time
                    ar: average rows sent
                    at: average query time
                     c: count
                     l: lock time
                     r: rows sent
                     t: query time  
      -r           reverse the sort order (largest last instead of first)
      -t NUM       just show the top n queries
      -a           don't abstract all numbers to N and strings to 'S'
      -n NUM       abstract numbers with at least n digits within names
      -g PATTERN   grep: only consider stmts that include this string
      -h HOSTNAME  hostname of db server for *-slow.log filename (can be wildcard),
                   default is '*', i.e. match all
      -i NAME      name of server instance (if using mysql.server startup script)
      -l           don't subtract lock time from total time
    ```

  - 初始化慢查询日志

    ```shell
    # 初始化所有 [慢查询] 日志文件
    mysqladmin -uroot -p密码 flush-logs [slow]
    ```

- 性能分析 profile

  ```sql
  -- 开启profile
  set profiling = on;
  
  show profiles;
  +----------+------------+----------------------------------------+
  | Query_ID | Duration   | Query                                  |
  +----------+------------+----------------------------------------+
  |        1 | 0.17563750 | select * from student where id = 10001 |
  +----------+------------+----------------------------------------+
  
  -- 查看某条语句的具体执行信息
  show profile cpu,block io for query 1;
  +--------------------------------+----------+----------+------------+
  | Status                         | Duration | CPU_user | CPU_system |
  +--------------------------------+----------+----------+------------+
  | starting                       | 0.000125 | 0.000114 |   0.000000 |
  | Executing hook on transaction  | 0.000024 | 0.000007 |   0.000000 |
  | starting                       | 0.000040 | 0.000040 |   0.000000 |
  。。。
  ```

- 性能分析 explain

  - 各列的作用
    - id：在一个大的查询语句中每一个select关键字都对应一个唯一的id
      - id如果相同，可以认为是一组，从上往下顺序执行
      - 在所有组中，id值越大，优先级越高，越先执行
      - id号每个号码，表示一趟独立的查询，一个sql的查询趟数越少越好
    - select_type：select关键字对应的那个查询的类型
      - simple：简单查询
      - primary：此查询在整个查询中占主要地位
      - union：此查询是联合查询
    - table：表名
    - partitions：匹配的分区信息
    - type：针对单表的访问方法
      - System：查询结果由系统直接给出，性能最高
      - const：根据主键（或普通键+唯一键）对应的列与常数等值匹配得出结果，性能很高
      - ref：根据普通索引对应的列与常数等值匹配得出结果，性能较高
      - all：全文遍历，性能极低
    - possible_keys：可能用到的索引
    - key：实际上使用的索引
    - key_len：实际使用的索引的长度
    - ref：当使用索引列等值查询时，与索引列进行等值匹配的对象信息
    - rows：预估的需要读取的记录条数
    - filtered：某个表经过搜索条件过滤后剩余记录条数的百分比
    - extra：一些额外信息

- Sys schema视图使用

  - 索引情况

    ```sql
    -- 查询冗余索引
    select * from sys.schema_redundant_indexes;
    
    -- 查询未使用过的索引
    select * from sys.schema_unused_indexes;
    
    -- 查询索引的使用情况
    select index_name, rows_selected, rows_inserted, rows_updated, rows_deleted
    from sys.schema_index_statistics where table_schema = '数据库名';
    ```

  - 表相关

    ```sql
    -- 查询表的访问量
    select table_schema, table_name, sum(io_read_requests+io_write_requests) as io
    from sys.schema_table_statistics group by table_schema, table_name
    order by io desc;
    
    -- 查询占用 bufferpool 较多的表
    select object_schema, object_name, allocated, `data`
    from sys.innodb_buffer_stats_by_table
    order by allocated
    limit 10;
    
    -- 查看表的全表扫描情况
    select * from sys.statements_with_full_table_scans where db='数据库名';
    ```

  - 语句相关

    ```sql
    -- 监控 sql 执行的频率
    SELECT db, exec_count, `query`
    FROM sys.`statement_analysis`
    ORDER BY exec_count DESC;
    
    -- 监控使用了排序的 sql
    SELECT db, exec_count, first_seen, last_seen, `query`
    FROM sys.`statements_with_sorting`
    LIMIT 1;
    
    -- 监控使用了临时表或者磁盘临时表的 sql
    SELECT db, exec_count, tmp_tables, tmp_disk_tables, `query`
    FROM sys.`statement_analysis`
    WHERE tmp_tables > 0 OR tmp_disk_tables > 0
    ORDER BY (tmp_tables+tmp_disk_tables) DESC;
    ```

  - IO相关

    ```sql
    -- 查看消耗磁盘IO的文件
    SELECT FILE, avg_read, avg_write, avg_read+avg_write AS avg_io
    FROM sys.io_global_by_file_by_bytes
    ORDER BY avg_read
    LIMIT 10;
    ```

  - Innodb 相关

    ```sql
    -- 行锁阻塞情况
    SELECT * FROM sys.innodb_lock_waits;
    ```

  

  

#### 查询优化

- 索引失效

  - 计算、函数、类型转化（自动或手动）导致索引失效

    ```sql
    -- 可以使用索引
    explain select sql_no_cache * from student where stuent.score = 600;
    explain select sql_no_cache * from student where student.name like 'abc%';
    explain select sql_no_cache * from student where student.name = '123';
    
    -- 无法使用索引
    explain select sql_no_cache * from student where student.score+1 = 601;
    explain select sql_no_cache * from student where left(student.name) = 'abc';
    -- 先将 name 字段上的数据作一个隐式转换成 int，再与 123 比较
    explain select sql_no_cache * from student where student.name = 123;
    ```

  - 范围条件右侧的列失效（看定义索引时的顺序）

    ```sql
    -- 若对 age 进行范围查询，score 和 name 的索引失效
    create index stu_age_score_name on student(age, score, name);
    
    explain select sql_no_cache * from student
    where student.age=18 and student.score > 500 and student.name = 'abc';
    ```

    ```sql
    -- 不失效，a b c都使用到
    create index a_b_c on test_table(a, b, c);
    
    explain select sql_no_cache * from test_table
    where a=3 and b like 'bb%' and c=1;
    ```

  - 不等于索引失效

    ```sql
    -- 主键索引不失效
    explain select * from student where stu_id <> 101;
    
    -- 普通索引失效
    explain select * from student where score <> 400;
    ```

  - is null 可以使用索引，is not null 无法使用索引

    - 在设计表时，尽量将字段设置为 not null 约束

  - like 以通配符 % 开头索引失效

  - or 前后存在非索引（索引失效）的列，索引失效

  - 统一字符集，不同字符集的数据比较时会发生转换，会导致索引失效

- 多表查询优化

  - 多表查询原理
    - 简单循环嵌套：驱动表将数据一个个传递到被驱动表，被驱动表一个个对比
    - 被驱动表添加索引：驱动表将数据一个个传递到被驱动表，被驱动表按索引查找
    - 块嵌套循环连接：驱动表将数据分为若干块，再依次放入 join buffer 中，被驱动表再整体查找
      - 通过`show variables like '%optimizer_switch%'`可查看 block_nested_loop 是否开启
      - join buffer 默认大小256k，可通过`show variables like '%join_buffer%'`查看
  - 驱动表：在 explain 的结果集中，相同 id 下的不同 table 中，上方的是驱动表，下方的是被驱动表
  - 内连接：优化器可以决定谁作为驱动表，谁作为被驱动表
    - 相同条件下，能查询到索引的会被作为被驱动表
    - 相同条件下，结果集大的会被作为被驱动表
  - 外连接：优化器可以将外连接优化为内连接
    - 左外连接：在没有转化为内连接时，左边的表是驱动表
    - 右外连接：在没有转化为内连接时，右边的表是驱动表

- 子查询优化：不使用子查询

- order by 优化

  - 尽量使用 index 排序，避免使用 filesort 排序

    ```sql
    -- 建立score索引后，执行下面两条sql
    create index score on student(score);
    
    -- extra：using index
    EXPLAIN SELECT score FROM student ORDER BY score;
    -- extra：using filesort  因为使用索引后要回表查询所有信息，并且回表数据量大，所以不使用索引
    EXPLAIN SELECT * FROM student ORDER BY score;
    -- type：index  添加 limit 后，回表数据若不多，会使用索引
    EXPLAIN SELECT * FROM student ORDER BY score LIMIT 100, 20;
    ```

  - 如果 where 和 order by 后面是相同的字段列，考虑使用单索引；如果不同，考虑使用联合索引

  - 无法使用 index 时，需对 filesort 方式进行调优

    - filesort 有两种算法
      - 双路排序：mysql 4.1 前使用，两次扫描数据
        - 第一次：将读取行指针和 order by 列，对它们进行排序
        - 第二次：扫描已经排好的列表，按列表中的值重新从列表中读取对应数据输出
      - 单路排序：从磁盘中读取需要查询的所有列，按 order by 列在 buffer 中对它们进行排序输出，只需扫描一次，单占用内存空间增加
    - 优化策略
      - 尝试提高 sort_buffer_size 大小
      - 尝试提高 max_length_for_sort_data 大小：如果返回的列总长度大于该值，则会使用双路排序
      - order by 时 select * 是一个大忌

- group by优化：有 order by 类似

- 分页查询优化

  ```sql
  -- 性能低
  SELECT * FROM student LIMIT 999900,10;
  
  -- 尽量利用主键，用普通键还要回表
  SELECT * FROM student WHERE stu_id > 1999900 LIMIT 10;
  
  -- 若主键不自增或自增被打乱
  SELECT * FROM student t1, 
  (SELECT stu_id FROM student ORDER BY stu_id LIMIT 999900, 10) t2
  WHERE  t1.`stu_id` = t2.stu_id;
  ```






#### 其他优化

- 覆盖索引：一个索引包含了满足查询结果的数据就叫做覆盖索引；如创建某一字段索引时，非聚簇索引中还包含了一个主键用于回表，所以在只查询该字段和主键时，能使用该字段的索引

  ```sql
  create index stu_age_name on student(age, name);
  
  -- 不使用索引（索引失效）
  explain select * from student where age <> 20;
  explain select * from student where name like '%abc';
  
  -- 使用索引
  explain select age, name from student where age <> 20;
  explain select id, age, name from student where name like '%abc';
  ```

  - 优点：

    - 避免了回表后对主键进行二次查询
    - 由随机IO变为顺序IO：覆盖索引可以直接从非聚簇索引中获取数据，在范围查询等查询中，这些数据可能在一张表中，而回表后，数据可能存放在多张表中

  - 弊端：

    ```sql
    -- 本来只想建立 age 索引，但考虑到查询时要查询 age、name 两个字段，所以要建立联合索引，加大开销
    select age, name from student where age <> 20;
    ```

- 索引下推：ICP

  ```sql
  create index stu_age_name on student(age, name);
  
  -- 先利用索引找出符合age的过滤结果1，而判断name的时候虽然索引失效，但仍会在过滤结果1中判断是否符合name条件再得到过滤结果2，再由过滤结果2进行回表
  explain select * from student
  where age = 20
  and name like '%张%';
  ```

  - 默认开启，可通过`set optimizer_switch = 'index_condition_pushdown=off;'`关闭

- EXISTS 和 IN

  ```sql
  -- 建议A为大表，B为小表时使用
  select * from A where cc in (select cc from B)
  
  -- 建议A为小表，B为大表时使用
  select * from A where exists (select cc from B where B.cc=A.cc)
  ```

- 统计数据时，MyISAM引擎时间复杂度是O(1)（其存储了row_count值），在InnoDB引擎中，count(字段) 的字段一般选普通索引字段，因为主键索引字段是聚簇索引，携带数据量大。count(1) 和 count(*) 会自动找普通索引字段进行统计

- select(*)一般不用，因为既要查数据字典将 \* 转化为列名，又可能无法使用覆盖索引

- 查询时加LIMIT 1，再查出一条数据就会停止，不会继续往下查，若该字段已被唯一约束，则不需要加

- 多使用COMMIT





#### 主键设计

- 自增

  - 可靠性不高：存在自增ID回溯问题，直到mysql8才修复
  - 安全性不高
  - 性能差：需要在数据库服务器端生成
  - 局部唯一性：自增ID是局部唯一的，需要数据库实例唯一

- uuid

  - 无序uuid 组成部分
    - 时间+UUID版本（16字节） - 时钟序列（4字节） - MAC地址（12字节）
      - 时间+UUID版本
        1. 时间低位：32位
        2. 时间中位：16位
        3. 时间高位：12位
        4. UUID版本：4位
  - 有序UUID：将时间低位和时间高位互换，时间就是单调递增了

  ```sql
  -- uuid_to_bin() mysql8 提供
  SELECT UUID(), uuid_to_bin(UUID()), uuid_to_bin(UUID(), TRUE)
  ```

- 自定义：如 订单ID = 时间 + 去重字段 + 用户ID后6位尾号

- 雪花算法

  - 64位的长整型，组成部分：

    - 符号位：1bit
    - 时间戳：41bit
    - 工作机器ID：10bit
    - 序列号：12bit

  - 使用

    - 工具类

      ```java
      public class SnowFlakeUtil {
      
          // 起始时间戳
          private final static long START_STMP = 1480166465631L;
      
          // 每部分的位数
          private final static long SEQUENCE_BIT = 12; // 序列号占用位数
          private final static long MACHINE_BIT = 5; // 机器id占用位数
          private final static long DATACENTER_BIT = 5; // 机房id占用位数
      
          // 每部分最大值
          private final static long MAX_DATACENTER_NUM = -1L ^ (-1L << DATACENTER_BIT);
          private final static long MAX_MACHINE_NUM = -1L ^ (-1L << MACHINE_BIT);
          private final static long MAX_SEQUENCE = -1L ^ (-1L << SEQUENCE_BIT);
      
          // 每部分向左的位移
          private final static long MACHINE_LEFT = SEQUENCE_BIT;
          private final static long DATACENTER_LEFT = SEQUENCE_BIT + MACHINE_BIT;
          private final static long TIMESTMP_LEFT = DATACENTER_LEFT + DATACENTER_BIT;
      
          private long datacenterId; // 机房id
          private long machineId; // 机器id
          private long sequence = 0L; // 序列号
          private long lastStmp = -1L; // 上次的时间戳
      
          public SnowFlakeUtil(long datacenterId, long machineId) {
              if (datacenterId > MAX_DATACENTER_NUM || datacenterId < 0){
                  throw new IllegalArgumentException("datacenterId can't be greater than MAX_DATACENTER_NUM or less than 0");
              }
              if (machineId > MAX_MACHINE_NUM || machineId < 0){
                  throw new IllegalArgumentException("machineId can't be greater than MAX_MACHINE_NUM or less than 0");
              }
              this.datacenterId = datacenterId;
              this.machineId = machineId;
          }
      
          // 产生下一个ID
          public synchronized long getNextId() {
              long currStmp = getNewstmp();
              if (currStmp < lastStmp) {
                  throw new RuntimeException("Clock moved backwards.Refusing to generate id");
              }
              if (currStmp == lastStmp) {
                  // 若在相同毫秒内 序列号自增
                  sequence = (sequence + 1) & MAX_SEQUENCE;
                  // 同一毫秒的序列数已达到最大
                  if (sequence == 0L)
                  {
                      currStmp = getNextMill();
                  }
              } else {
                  // 若在不同毫秒内 则序列号置为0
                  sequence = 0L;
              }
              lastStmp = currStmp;
      
              return (currStmp - START_STMP) << TIMESTMP_LEFT // 时间戳部分
                      | datacenterId << DATACENTER_LEFT // 机房id部分
                      | machineId << MACHINE_LEFT // 机器id部分
                      | sequence; // 序列号部分
          }
      
          // 获取新的毫秒数
          private long getNextMill()
          {
              long mill = getNewstmp();
              while (mill <= lastStmp)
              {
                  mill = getNewstmp();
              }
              return mill;
          }
      
          // 获取当前的毫秒数
          private long getNewstmp()
          {
              return System.currentTimeMillis();
          }
      }
      ```

    - 使用

      ```java
      SnowFlakeUtil snowFlakeUtil = new SnowFlakeSnowFlakeUtil (12,13);
      System.out.println(snowFlakeUtil.nextId());
      ```





### 事务

#### 基础

- 事务的三个特性

  - 原子性
  - 一致性
  - 隔离性

- 显示事务

  ```sql
  -- 开启事务
  start transaction / begin
  read only / read write (默认) -- read only 也可以修改零时表
  with consistent snapshot;
  
  -- 一系列事务中的操作（主要是DML，不包含DDL）
  
  -- 保存点
  savepoint 保存点名称;  -- 设置保存点
  release savepoint 保存点名称;  -- 删除保存点
  
  -- 结束
  commit;  -- 提交事务
  rollback;  -- 回滚事务
  rollback to 保存点名称;  -- 回滚到某一保存点
  ```

- 隐式事务

  - DML 语句是一个事务

  - 隐式事务自动提交

    ```sql
    show variables like 'autocommit'; -- 默认是ON
    ```

  - commit; 手动提交事务

    ```sql
    set autocommit = false; -- 关闭自动提交
    update ...  -- 一系列 DML 语句
    commit;
    ```

  - 在 autocommit 为 true 的情况下，使用 start transaction / begin 开启事务后不会自动提交数据



#### 隔离级别

- 并发问题（严重程度：脏写 > 脏读 > 不可重复读 > 幻读） 

  - 脏写：

    | 顺序 | Session A         | Session B         |
    | ---- | ----------------- | ----------------- |
    | 1    | begin             |                   |
    | 2    |                   | begin             |
    | 3    |                   | update money -=10 |
    | 4    | update money -=20 |                   |
    | 5    | commit            |                   |
    | 6    |                   | rollback          |

  - 脏读

    | 顺序 | Session A    | Session B         |
    | ---- | ------------ | ----------------- |
    | 1    | begin        |                   |
    | 2    |              | begin             |
    | 3    |              | update money -=10 |
    | 4    | select money |                   |
    | 5    | commit       |                   |
    | 6    |              | rollback          |

  - 不可重复度

    | 顺序 | Session A    | Session B                  |
    | ---- | ------------ | -------------------------- |
    | 1    | begin        |                            |
    | 2    | select money |                            |
    | 3    |              | update money -=10; commit; |
    | 4    | select money |                            |

  - 幻读：一个事务中，后一次查询到的内容比前一次多，叫做幻读。因此删除语句不属于幻读

    | 顺序 | Session A             | Session B   |
    | ---- | --------------------- | ----------- |
    | 1    | begin                 |             |
    | 2    | select * where id > 0 |             |
    | 3    |                       | insert into |
    | 4    | select * where id > 0 |             |

- 隔离级别

  | 隔离级别                     | 解决问题         | 说明                       |
  | ---------------------------- | ---------------- | -------------------------- |
  | read uncommitted             | 解决到脏写       |                            |
  | read committed               | 解决到脏读       | 只能读取已提交的           |
  | repeatable read（Mysql默认） | 解决到不可重复读 | 读取到的仍然是提交前的数据 |
  | serializable                 | 解决到幻读       | 串行化                     |

- 查看隔离级别

  ```sql
  show variables like 'tx_isolation'; -- 5.7.20 版本前
  
  show variables like 'transaction_isolation'; -- 5.7.20 版本后
  ```

- 修改隔离级别

  ```sql
  set [global | session] transaction_isolation = '隔离级别'
  -- 隔离级别有：read-uncommitted  read-committed  repeatable-read  serializable
  ```






### 并发

#### 锁划分

- 按数据操作类型划分

  - 共享锁（S锁）

    ```sql
    -- 对读取的记录加 S锁
    select ... lock in share mode;
    
    select ... for share; -- 8.0后
    ```

  - 排它锁（X锁）

    ```sql
    -- 对读取的记录加 X锁
    select ... for update;
    ```

  - 两个S锁不会阻塞，包含X锁会阻塞

  - 5.7及之前的版本中，如果获取不到锁，会一直等待，直到<font color='red'>**`innodb_lock_wait_timeout`**</font>超时。8.0版本中可以添加 <font color='green'>**`NOWAIT`、`SKIP LOCKED`**</font> 跳过锁等待，前者遇到锁直接报错返回，后者遇到锁也会直接返回，只是返回的结果中不包含被锁定行

- 按数据操作粒度划分：每一层级锁空间有大小，超过大小阈值，会导致<font color=green>**锁升级**</font>

  - 表锁
  - 行锁
  - 页锁

- 按对待并发态度划分

  - 乐观锁

    - 版本号：<font color=green>**`update ... set version = version+1 where version=version`**</font>
    - CAS

  - 悲观锁

    ```sql
    -- 查询库存时可通过设置悲观锁防止多卖
    select count from goods where id = 10010 for update;
    ```
    
    <font color=red>select ... for update 语句执行过程中会把所有**扫描**的行都锁上，因此在MySQL中用悲观锁必须确定使用了索引，而不是全表扫描，否则会把整个表都锁上</font>

- 加锁方式

  - 显示锁
  - 隐式锁：为防止别的事务在当前事务insert完数据但尚未提交时访问或修改该条数据从而可能导致数据的脏读脏写问题
  
- 全局锁

  - 全局锁就是对<font color=red>**整个数据库实例**</font>加锁。当你需要让整个库处于<font color=red>**只读状态**</font>的时候，可以使用这个命令，之后的其他线程的以下语句会被阻塞：数据更新语句（数据的增删改）、数据定义语句（表的修改）和更新类的事务的提交语句。适用场景：<font color=green>**全库逻辑备份**</font>

  - 命令：

    ```sql
    flush tables with read lock
    ```

- 死锁

  - 两个事务各自持有对方事务所需要是锁
  - 处理方法
    - 等待，直到超时（innodb_lock_wait_timeout=50s）
    - 使用死锁检测进行死锁处理：绘制出wait-for graph（等待图），一旦检测到回路、有死锁，这时候InnoDB存储引擎会选择<font color=red>回滚undo量最小的事务</font>




#### 表锁

- 意向锁

  - <font color=red>**如果我们给某一行数据加上排他锁，数据库会自动给更大一级的空间，比如数据页或数据表加上意向锁，告诉其他人这个数据页或数据表已经有人上过排他锁了**</font>,这样当其他人想要获取数据表排他锁的时候，只需要了解是否有人已经获取了这个数据表的意向锁

  - 意向共享锁

    ```sql
    -- 事务要获取某些行的 S 锁，必须先获得表的 IS 锁
    select column from table ... lock in share mode;
    ```

  - 意向排它锁

    ```sql
    -- 事务要获取某些行的 X 锁，必须先获得表的 IX 锁
    select column from table ... for update;
    ```

  - 意向共享锁和意向排它锁都不互斥

- 自增锁

- 元数据锁（MDL锁）：<font color=red>**当对一个表做增删改查操作时，会加一个MDL读书；当对表的结构作变更操作时，会加MDL写锁，写锁排它**</font>



#### 行锁

- 记录锁：X锁为排它锁

- 间隙锁（gap锁）

  - 一张表

    | id   | name | 其他字段 |
    | ---- | ---- | -------- |
    | 1    | 张三 | 。。。   |
    | 3    | 李四 | 。。。   |
    | 7    | 王五 | 。。。   |

  - 在一个会话中开启事务，并查询id为5的数据

    ```sql
    begin;
    select * from student where id = 5 for update;
    ```

  - id为3至7之间（开区间）的间隙将被锁，另一个事务中，无法插入3至7的数据

- 临键锁：既锁某行数据，也锁区间（也包含gap锁）

  ```sql
  begin;
  select * from student where id<=7 and id>3 for update;
  ```

- 插入意向锁：一个事务在插入一条记录时会判断插入的位置是否被其他事务加了gap锁，如果有，则处于等待阶段直到gap锁释放。并且InnoDB规定事务在等待的时候也需要在内存中生成一个锁结构，表明有事务想在某个间隙中插入新记录，这个锁叫做插入意向锁。



#### MVCC

MVCC（Multiversion Concurrency Control），多版本并发控制。MVCC是通过利用UndoLog提供的多个版本管理来实现数据库的并发控制。在查询一些正在被另一个事务更新的行时，可以看到他们被更新之前的值，这样在做查询的时候就不用等待另一个事务释放锁。只有InnDB支持MVCC实现机制

mysql中 repeatable read（可重复读）隔离级别解决了幻读问题，因为其使用了MVCC







### 日志

- 慢查询日志
- 通用查询日志
- 错误日志
- 二进制日志
- 中继日志
- 数据定义语句日志



#### 通用查询日志

- 查看

  ```sql
  show variables like '%general%';
  +------------------+---------------------------------+
  | Variable_name    | Value                           |
  +------------------+---------------------------------+
  | general_log      | OFF                             |
  | general_log_file | /var/lib/mysql/81923cd0cc5c.log |
  +------------------+---------------------------------+
  ```

- 开启

  - 配置文件

    ```ini
    [mysqld]
    general_log=ON
    general_log_file=[path/[filename]]
    ```

  - 命令行

    ```sql
    set global general_log=on;
    set global general_log_file='path/filename';
    ```



#### 错误日志

- 在mysql数据库中，错误日志功能是默认开启的，且无法关闭

  ```ini
  [mysqld]
  log-err=[path/[filename]]
  ```

- 初始化日志

  ```sql
  -- 改名
  mv /var/log/mysqld.log /var/log/mysqld.log.old;
  -- 创建文件
  install -omysql -gmysql -m0644 /dev/null /var/log/mysqld.log;
  -- 刷新
  mysqladmin -uroot -p flush-logs;
  ```

  - mysql 5.5.7之前的版本，flush-logs将错误日志文件重命名为filename.err_old，并创建新的日志文件
  - mysql 5.5.7开始，flush-logs只是重新打开日志文件，并不做日志备份和创建的操作，因此初始化日志操作的命令其实是 install

  

#### 二进制日志

- 查看设置

  ```sql
  show variables like '%log_bin%';
  +---------------------------------+-----------------------------+
  | Variable_name                   | Value                       |
  +---------------------------------+-----------------------------+
  | log_bin                         | ON                          |
  | log_bin_basename                | /var/lib/mysql/binlog       |
  | log_bin_index                   | /var/lib/mysql/binlog.index |
  | log_bin_trust_function_creators | OFF                         |
  | log_bin_use_v1_row_events       | OFF                         |
  | sql_log_bin                     | ON                          |
  +---------------------------------+-----------------------------+
  
  +---------------------------------+-----------------------------+
  | binlog_expire_logs_seconds      | 2592000                     |
  | max_binlog_size                 | 1073741824                  |
  +---------------------------------+-----------------------------+
  ```

- 更改bin-log日志存放目录

  - 配置目录

    ```ini
    [mysqld]
    log-bin='/var/lib/mysql/binlog/abc-bin'
    ```

  - 使用mysql用户创建目录

    ```shell
    # 先创建文件夹
    mkdir /var/lib/mysql/binlog
    # 非必要：changed ownership of '/var/lib/mysql/binlog' from root:root to mysql:mysql
    chown -R -v mysql:mysql /var/lib/mysql/binlog
    ```

  - 重启后，新的文件将出现在该目录下

- 二进制日志的查看与数据的恢复：略

- 删除二进制日志

  - 删除指定日志文件：purge master logs

    ```sql
    purge {master | binary} logs to '指定日志文件名'
    purge {master | binary} logs before '指定日期如 20230101'
    ```

  - 删除所有：<font color=red>`reset master;`</font>





## MyCat2

### 配置文件

- 修改配置文件创建数据源、用户等操作需要重启mycat才能应用，也可以通过mycat命令修改

  ```mycat
  查看数据源
  /*+ mycat:showDataSources{} */
  
  创建数据源
  /*+ mycat:createDataSource{
  "name":"slave2",
  "password":"123456",
  "type":"JDBC",
  "url":"jdbc:mysql://...",
  "user":"root",
  "weight":0
  } */
  
  删除数据源
  /*+ mycat:dropDataSource{
  "name":"slave2",
  } */
  
  查看用户
  /*+ mycat:showUsers{} */
  
  创建/删除集群
  /*!  ......  */
  
  重置配置
  /*+ mycat:resetConfig{} */
  ```

- 数据源名.datasource.json

  ```json
  {
  	"dbType":"mysql",
  	"idleTimeout":60000,
  	"initSqls":[],
  	"initSqlsGetConnection":true,
  	"instanceType":"READ_WRITE",
  	"maxCon":1000,
  	"maxConnectTimeout":3000,
  	"maxRetryCount":5,
  	"minCon":1,
  	"name":"prototypeDs",
  	"password":"root",
  	"type":"JDBC",
  	"url":"jdbc:mysql://192.168.88.129:3306?useUnicode=true&serverTimezone=Asia/Shanghai&characterEncoding=UTF-8",
  	"user":"root",
  	"weight":0
  }
  ```

  - instanceType：实例的类型，READ_WRITE、READ、WRITE
  - weight：访问的权重，0最大

- 用户名.user.json

  ```json
  {
  	"dialect":"mysql",
  	"ip":null,
  	"password":"123456",
  	"transactionType":"proxy",
  	"username":"root",
      "isolation":3
  }
  ```

  - transactionType
    - 默认值：proxy（本地事务，在涉及大于1个数据库的事务，commit阶段失败会导致不一致，但是兼容性最好
    - 可选值：xa（要求数据库支持xa）
  - isolation：设置初始化的事务隔离级别
    - READ_UNCOMMITTED：1
    - READ_COMMITTED：2
    - REPEATED_READ：3
    - SERIALIZABLE：4

- 集群名.cluster.json

  ```json
  {
  	"clusterType":"MASTER_SLAVE",
  	"heartbeat":{
  		"heartbeatTimeout":1000,
  		"maxRetry":3,
  		"minSwitchTimeInterval":300,
  		"slaveThreshold":0
  	},
  	"masters":[
  		"prototypeDs"
  	],
  	"maxCon":200,
  	"name":"prototype",
  	"readBalanceType":"BALANCE_ALL",
      "replicas":[
          
      ],
  	"switchType":"SWITCH"
  }
  ```

  

### mysql主从

- mysql主从原理

  - MySQL主从是基于binlog，主节点需开启binlog才能进行主从
  - 主从复制步骤：
    - 主节点记录binlog日志，并生成log dump线程，将binlog日志传递到从节点。
    - 从节点生成两个线程：I/O线程和SQL线程
      - I/O线程请求并接收主节点的binlog日志，并将其写入relay log中
      - SQL线程读取relay log，并解析执行具体操作

- 步骤

  - 搭建两个mysql实例

  - 两个mysql实例给予不同的server-id

    ```ini
    [mysqld]
    server-id=1
    # 可将master节点的binlog日志更名为master-bin
    log-bin=master-bin
    ```

  - 主节点创建一个权限小的用户供从节点使用
  
    ```sql
    -- 创建用户：mysql5
    create user 'slave'@'%' identified by 'slave';
    -- mysql8 由于加密规则改变，需要还原成 mysql_native_password 加密规则
    ALTER USER 'slave'@'%' IDENTIFIED WITH mysql_native_password BY 'slave';
    
    -- 分配权限
    grant replication slave on *.* to 'slave'@'%';
    flush privileges;
    
    -- 查看主节点binlog日志的日志名和记录位置
    show master status;
    -- 重置 reset master;
    ```
  
  - 从节点
  
    ```sql
    change master to master_host="192.168.88.129",master_port=3306,master_user="slave",master_password="slave",master_log_file="master-bin.000001",master_log_pos=156;
    
    -- 启动
    start slave;
    -- 关闭  stop slave;
    -- 重置  reset slave;
    
    -- 查看从机状态
    show slave status \G;
    ```
    



### mycat主从

- 创建数据源

  - 主节点数据源可读可写
  - 从节点数据源可读

- 创建集群

- 修改逻辑库数据源

  - 创建逻辑库

    ```sql
    CREATE DATABASE db1 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
    ```

  - 修改配置文件：conf/schemas/db1.schema.json

    ```json
    {
    	"customTables":{},
    	"globalTables":{},
    	"normalProcedures":{},
    	"normalTables":{},
    	"schemaName":"db1",
    	"targetName":"test",
    	"shardingTables":{},
    	"views":{}
    }
    ```



### 分库分表

略















# Redis



## 基础篇

### 安装redis

#### 配置文件

```ini
# 开始启动时必须如下指定配置文件
# ./redis-server /path/to/redis.conf


# 存储单位如下所示
# 1k => 1000 bytes
# 1kb => 1024 bytes
# 1m => 1000000 bytes
# 1mb => 1024*1024 bytes
# 1g => 1000000000 bytes
# 1gb => 1024*1024*1024 bytes

################################## INCLUDES ###################################

# 如果需要使用多配置文件配置redis，请用include
# include /path/to/local.conf
# include /path/to/other.conf

################################## MODULES ##################################### modules

# 手动设置加载模块（当服务无法自动加载时设置）
# loadmodule /path/to/my_module.so
# loadmodule /path/to/other_module.so

################################## NETWORK #####################################

# Examples:
# bind 192.168.1.100 10.0.0.1
# bind 127.0.0.1 ::1
# 设置绑定的ip
bind 127.0.0.1

# 保护模式：不允许外部网络连接redis服务
protected-mode yes

# 设置端口号
port 6379

# TCP listen() backlog.
# 
# TCP 连接数，此参数确定了TCP连接中已完成队列(完成三次握手之后)的长度
tcp-backlog 511

# Unix socket.
# 
# 通信协议设置，本机通信使用此协议不适用tcp协议可大大提升性能
# unixsocket /tmp/redis.sock
# unixsocketperm 700

# 定期检测cli连接是否存活
tcp-keepalive 300

################################## 权限 ###################################

# 设置连接时密码
# requirepass 123456

################################# GENERAL #####################################

# 是否守护进程运行（后台运行）
# 与docker的-d冲突，docker部署时daemonize设为no
daemonize yes

# 是否通过upstart和systemd管理Redis守护进程
supervised no

# 以后台进程方式运行redis，则需要指定pid 文件
pidfile /var/run/redis_6379.pid

# 日志级别
# 可选项有： # debug（记录大量日志信息，适用于开发、测试阶段）； # verbose（较多日志信息）； # notice（适量日志信息，使用于生产环境）； 
# warning（仅有部分重要、关键信息才会被记录）。
loglevel notice

# 日志文件的位置
logfile ""

# 数据库的个数
databases 16

# 是否显示logo
always-show-logo yes

################################ RDB 持久化  ################################
# 900秒内触发一次请求进行持久化，300秒内触发10次请求进行持久化操作，60s内触发10000次请求进行持久化操作
# save "" # 禁用RDB
save 900 1
save 300 10
save 60 10000

# 持久化出现错误后，是否依然进行继续进行工作
stop-writes-on-bgsave-error yes

# 使用压缩rdb文件 yes：压缩，但是需要一些cpu的消耗。no：不压缩，需要更多的磁盘空间
rdbcompression yes

# 是否校验rdb文件，更有利于文件的容错性，但是在保存rdb文件的时候，会有大概10%的性能损耗
rdbchecksum yes

# dbfilename的文件名
dbfilename dump.rdb

# dbfilename文件的存放位置
dir ./

############################## AOF持久化 ###############################

# 是否使用AOF持久化方式
appendonly no

# appendfilename的文件名
appendfilename "appendonly.aof"

# 持久化策略
# 表示每执行一次写命令，立即记录到AOF文件
# appendfsync always
# 写命令执行完先放到AOF缓冲区，然后每隔1秒将缓冲区数据写到AOF文件
appendfsync everysec
# 写命令执行完先放入AOF缓冲区，由操作系统决定何时将缓冲区内容写入磁盘
# appendfsync no

# 持久化时（RDB的save | aof重写）是否可以运用Appendfsync，用默认no即可，保证数据安全性
no-appendfsync-on-rewrite no

# 触发重写的基准值
auto-aof-rewrite-percentage 100 # 相比上一次增大100%
auto-aof-rewrite-min-size 64mb # 文件大小为64mb

# 指定当发生AOF文件末尾截断时，加载文件还是报错退出
aof-load-truncated yes

# 开启混合持久化，更快的AOF重写和启动时数据恢复
aof-use-rdb-preamble yes

################################# 主从关系 #################################

# replicaof 即slaveof 设置主结点的ip和端口
# replicaof <masterip> <masterport>

# 集群节点访问密码
# masterauth <master-password>

# 从结点断开后是否仍然提供数据
replica-serve-stale-data yes

# 设置从节点是否只读
replica-read-only yes

# 是否启用无磁盘复制（不先写入磁盘IO流，而直接写入网络IO流，网络快磁盘慢时可使用）
repl-diskless-sync no

# master节点创建子进程前等待的时间
repl-diskless-sync-delay 5

# Replicas发送PING到master的间隔，默认值为10秒。
# repl-ping-replica-period 10

# repl-timeout 60
 
repl-disable-tcp-nodelay no

# repl-backlog-size 1mb
# repl-backlog-ttl 3600

replica-priority 100
 
# min-replicas-to-write 3
# min-replicas-max-lag 10
#
# replica-announce-ip 5.5.5.5
# replica-announce-port 1234

################################ Redis 集群  ###############################

# 是否开启集群
# cluster-enabled yes

# 集群结点信息文件
# cluster-config-file nodes-6379.conf

# 等待节点回复的时限
# cluster-node-timeout 5000

# 结点重连规则参数
# cluster-replica-validity-factor 10
# cluster-migration-barrier 1
# cluster-require-full-coverage yes
# cluster-replica-no-failover no

################################### CLIENTS ####################################

# 最大连接数
# maxclients 10000

############################## MEMORY MANAGEMENT ################################

# redis配置的最大内存容量
# maxmemory <bytes>

# 内存达到上限的处理策略
# maxmemory-policy noeviction

# 处理策略设置的采样值
# maxmemory-samples 5

# 是否开启 replica 最大内存限制
# replica-ignore-maxmemory yes

############################# LAZY FREEING ####################################

# 惰性删除或延迟释放
lazyfree-lazy-eviction no
lazyfree-lazy-expire no
lazyfree-lazy-server-del no
replica-lazy-flush no
```



#### docker部署

- 创建/var/docker-volume/redis/conf文件，以便之后的文件挂载

  ```shell
  docker run \
  --privileged=true \
  -p 6379:6379 \
  --name redis \
  -v /var/docker-volume/redis/conf:/etc/redis/conf \
  -v /var/docker-volume/redis/data:/data \
  -d redis \
  redis-server /etc/redis/conf/redis.conf \
  --requirepass 123456
  ```

  - -p 6379:6379 端口映射：前表示主机部分，：后表示容器部分。
  - --name myredis  指定该容器名称，查看和进行操作都比较方便。
  - -v 挂载目录，规则与端口映射相同。
  - -d redis 表示后台启动redis（与配置文件中的`daemonize yes`选用）
  - --privileged=true 这个参数，容器内的root拥有真正root权限，否则容器内root只是外部普通用户权限
  - redis-server /etc/redis/conf/redis.conf  以配置文件启动redis，加载容器内的conf文件，最终找到的是挂载的目录
  - appendonly yes 开启redis 持久化
  - 解决：WARNING overcommit_memory is set to 0!（容器内执行）
    - 命令：echo 1 > /proc/sys/vm/overcommit_memory
    - To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot
    - run the command 'sysctl vm.overcommit_memory=1' for this to take effect.





### [Redis命令](https://redis.io/commands)

#### 使用命令行

- redis-cli [options] [commands]
  - 常用options
    - -h 127.0.0.1：指定redis节点的ip，默认为127.0.0.1
    - -p 6379：指定连接的redis节点的端口，默认是6379
    - -a redis：指定redis的访问密码
  - commands是redis的操作命令
    - ping：与redis服务做心跳测试，服务正常返回pong
    - AUTH redis：指定redis的访问密码
    - select (index)：切换库
  - 不指定commands，会进入redis-cli的交互控制台



#### [通用命令](https://redis.io/commands#generic)

- 查看帮助文档：help @generic
- 常用命令：
  - `KEYS 表达式`：查看复合模板的所有key
    - `keys *`：查询所有键
    - `keys a*`：查询以a开头的键
  - `DEL key ...`：删除一个或多个key，返回值为删除的key的个数
  - `EXISTS key ...`：判断key是否存在，返回值为存在的key的个数
  - `EXPIRE key seconds`：设置一个key的存活时间
  - `TTL key`：查看一个key的剩余存活时间，返回值-2为不存在，-1为永久有效
  - `MEMORY USAGE key`：查看一对键值占用内存的大小



#### String类型

- string类型的value是字符串，不过根据字符串的格式不同，又可分为3类
  - string：普通字符串
  - int：整数类型，可以做自增、自减操作
  - float：浮点类型，可以做自增、自减操作
- 不管哪种格式，底层都是字节数组格式存储，只不过数字类型为减小存储大小而采用不同编码方式。字符串类型的最大空间不能超过512m
- 常用命令
  - SET key value [EX seconds|PX milliseconds|EXAT timestamp|PXAT milliseconds-timestamp|KEEPTTL] [NX|XX] [GET]
  - GET key：由key获得value
  - MSET key value ...：创建多个key和value
  - MGET key ...：获得多个value
  - INCR key：让一个整型的key自增1
  - INCRBY key increment：让一个整型的key自增指定步长
  - INCRBYFLOAT key increment：让一个浮点型的key自增指定步长
  - SETNX key value：添加一个string类型的键值对，前提是这个key不存在，否则不执行
  - SETEX key seconds value：添加一个指定类型的键值对，并指定有效期



#### Hash类型

- 类型

  <table>
      <tr><th rowspan="2">key</th><th colspan="2">value</th></tr>
      <tr><th>field</th><th>value</th></tr>
      <tr><td rowspan="2">user:1</td><td>name</td><td>张三</td></tr>
      <tr><td>age</td><td>21</td></tr>
      <tr><td rowspan="2">user:2</td><td>name</td><td>李四</td></tr>
      <tr><td>age</td><td>23</td></tr>
    </table>

- 常用指令

  - HSET key field value：添加或修改hash类型key的field的值

  - HGET key field：获取一个hash类型key的field的值

  - HMSET key field value：批量添加多个hash类型的key的field和值

  - HMGET：批量获取多个hash类型的key的field的值

  - HGETALL：获取一个hash类型的key中的所有field和value

  - HKEYS：获取一个hash类型的key中的所有的field

  - HVALS：获取一个hash类型的key中的所有的value

  - HINCRBY：让一个hash类型key的字段自增并指定步长

  - HSETNX：添加一个hash类型的key的field值，前提field不存在，否则不执行



#### List类型

- 常用指令
  - LPUSH key element ...：向key对应的列表左侧插入一个或多个元素
  - LPOP key [count]：移出并返回key对应的列表左侧前n个元素，没有则返回nil
  - RPUSH key element ...：向key对应的列表右侧插入一个或多个元素
  - RPOP key [count]：移出并返回key对应的列表右侧前n个元素，没有则返回nil
  - LRANGE key start end：返回一个角段内的所有元素
  - BLPOP和BRPOP key ... timeout：与LPOP和RPOP类似，不过在没有元素时，等待指定时间，而不是直接返回nil



#### Set类型

- 常用指令
  - SADD key member ... ：向set中添加一个或多个元素
  - SREM key member ... ：移出set中的指定元素
  - SCARD key：返回set中元素的个数
  - SISMEMBER key member：判断一个元素是否存在于set中
  - SMEMBERS：获取set中的所有元素
  - SINTER key1 key2 ...：求key1与key2的交集
  - SDIFF key1 key2 ...：求key1和key2的差集（key1中有key2中没有的）
  - SUNION key1 key2 ...：求key1和key2的并集



#### SortedSet类型

- 常用指令
  - ZADD key score member：添加一个或多个元素到sorted set ，如果已经存在则更新其score
  - ZREM key member：删除sorted set的一个指定元素
  - ZSORE key member：获取sorted set中的指定元素的score值
  - ZRANK key member：获取sorted set中的指定元素的排名
  - ZCARD key：获取sorted set中的元素个数
  - ZCOUNT key min max：统计score值在给定范围内的所有元素的个数
  - ZINCRBY key increment member：让sorted set中的指定元素的score值自增指定步长
  - ZRANGE key min max：按照score排序后，获取指定排名范围内的元素
  - ZRANGEBYSCORE key min max：按照socre排序后，获取指定score范围内的元素
  - ZDIFF、ZINTER、ZUNION：求差集、交集、并集





### Java

- Redis 的java客户端
  - Jedis：以Redis命令作为方法名，学习难度低，简单实用。但是Jedis实例是线程不安全的，多线程环境下需要基于连接池来使用
  - Lettuce：Lettuce是基于Netty实现的，支持同步、异步和响应式编程方式，并且线程是安全的。支持Redis的哨兵模式、集群模式和管道模式
  - [Redisson](#Redisson)：Redisson是一个基于Redis实现的分布式、可伸缩的Java数据结构集合。包含了诸多Map、Queue、Lock、Semaphore、AtomicLong等强大功能

#### Jedis

##### 基本使用

- 引入依赖

  ```xml
  <dependency>
      <groupId>redis.clients</groupId>
      <artifactId>jedis</artifactId>
      <version>3.7.1</version>
  </dependency>
  <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <version>5.8.2</version>
      <scope>test</scope>
  </dependency>
  ```

- 建立连接

  ```java
  private Jedis jedis;
  
  @BeforeEach
  void setUp(){
      jedis = new Jedis("192.168.36.131", 6379);
      jedis.auth("redis");
      jedis.select(0);
  }
  ```

- 测试

  ```java
  @Test
  void testString(){
      String result = jedis.set("name", "zhangsan"); // OK
      String name = jedis.get("name"); // 
  }
  ```

- 释放资源

  ```java
  @AfterEach
  void tearDown(){
      if(jedis != null){
          jedis.close();
      }
  }
  ```



##### jedis连接池

- Jedis 本身是线程不安全的，并且在频繁的创建和销毁连接会有性能损耗，因此推荐使用Jedis连接池代替Jedis的直连方式

```java
// 自定义一个工具类，在静态代码块中设置连接池参数
public class JedisConnectionFactory {
    private static final JedisPool jedisPool;

    static {
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
        // 最大连接
        jedisPoolConfig.setMaxTotal(8);
        // 最大空闲连接
        jedisPoolConfig.setMaxIdle(8);
        // 最小空闲连接
        jedisPoolConfig.setMinIdle(0);
        // 连接池中没有连接时的最长等待时间，默认-1表示一直等待
        jedisPoolConfig.setMaxWaitMillis(200);
        jedisPool = new JedisPool(jedisPoolConfig, "192.168.36.131", 6379, 1000, "redis");
    }
    
    // 获取Jedis对象
    public static Jedis getJedis(){
        return jedisPool.getResource();
    }
}
```





#### SpringDataRedis

- 说明

  - **整合了Jedis和Lettuce**

  - 支持Redis的发布订阅模型

  - 支持Redis的哨兵和Redis集群

  - **支持Lettuce的响应式编程**

  - 支持基于JDK、JSON、字符串、Spring对象的数据序列化和反序列化

  - 支持基于Redis的JDKCollection实现

- 自动配置

  - RedisAutoConfiguration

    ```java
    @AutoConfiguration
    @ConditionalOnClass({RedisOperations.class})
    @EnableConfigurationProperties({RedisProperties.class})
    @Import({LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class})
    public class RedisAutoConfiguration { ... }
    ```

  - LettuceConnectionConfiguration

    ```java
    @Configuration(
        proxyBeanMethods = false
    )
    @ConditionalOnClass({RedisClient.class})
    @ConditionalOnProperty(
        name = {"spring.data.redis.client-type"},
        havingValue = "lettuce",
        matchIfMissing = true
    )
    class LettuceConnectionConfiguration extends RedisConnectionConfiguration { ... }
    ```

  - JedisConnectionConfiguration

    ```java
    @Configuration(
        proxyBeanMethods = false
    )
    @ConditionalOnClass({GenericObjectPool.class, JedisConnection.class, Jedis.class})
    @ConditionalOnMissingBean({RedisConnectionFactory.class})
    @ConditionalOnProperty(
        name = {"spring.data.redis.client-type"},
        havingValue = "jedis",
        matchIfMissing = true
    )
    class JedisConnectionConfiguration extends RedisConnectionConfiguration { ... }
    ```

    

##### RedisTemplate

| API              | 返回值类型      | 说明                  |
| ---------------- | --------------- | --------------------- |
| rt.opsForValue() | ValueOperations | 操作String类型数据    |
| rt.opsForHash()  | HashOperations  | 操作Hash类型数据      |
| rt.opsForList()  | ListOperations  | 操作List类型数据      |
| rt.opsForSet()   | SetOperations   | 操作Set类型数据       |
| rt.opsForZSet()  | ZSETOperations  | 操作SortedSet类型数据 |
| rt               |                 | 通用的命令            |



##### 快速入门

- 引入依赖（基于boot）

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-redis</artifactId>
  </dependency>
  <dependency>
      <groupId>org.apache.commons</groupId>
      <artifactId>commons-pool2</artifactId>
  </dependency>
  ```

- 配置文件

  ```yml
  spring:
    redis:
      host: 192.168.36.131
      port: 6379
      password: redis
      database: 0
      # spring默认使用lettuce，若用jedis，须导入jedis依赖
      lettuce:
        pool:
          max-active: 8
          max-idle: 8
          min-idle: 0
          max-wait: 1000ms
  ```

- 注入RedisTemplate

  ```java
  @Autowired
  private RedisTemplate redisTemplate;
  ```



##### 序列化

- 自动序列化

  - redisTemplate默认使用java的序列化方式

  - 导入jackson-databind依赖

  - 重写RedisTemplate的Bean

    ```java
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory){
        // 创建 RedisTemplate 对象
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        // 设置连接工厂
        template.setConnectionFactory(factory);
        // 创建JSON序列化工具
        GenericJackson2JsonRedisSerializer jackson2JsonRedisSerializer = new GenericJackson2JsonRedisSerializer();
        // 设置Key的序列化
        template.setKeySerializer(RedisSerializer.string());
        template.setHashKeySerializer(RedisSerializer.string());
        // 设置Value的序列化
        template.setValueSerializer(jackson2JsonRedisSerializer);
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        // 返回
        return template;
    }
    ```

  - 问题：存对象时，会存入类字节码路径

- 手动序列化

  - Spring默认提供了一个StringRedisTemplate类，它的key和value的序列化方式默认是String方式。省去了自定义RedisTemplate的过程

  ```java
  private static final ObjectMapper mapper = new ObjectMapper();
  @Autowired
  private StringRedisTemplate stringRedisTemplate;
  
  @Test
  void testStringTemplate() throws JsonProcessingException {
      User temp = new User("zhangsan", 18);
      // 存入数据
      String json = mapper.writeValueAsString(temp);
      stringRedisTemplate.opsForValue().set("user", json);
      // 读取数据
      String jsonFromRedis = stringRedisTemplate.opsForValue().get("user");
      User user = mapper.readValue(jsonFromRedis, User.class);
      System.out.println(user);
  }
  ```








## 实战篇

### hutool

- json与对象的转换

  - 普通对象

    ```java
    String shopJson = JSONUtil.toJsonStr(shop);
    ```

    ```java
    Shop shop = JSONUtil.toBean(shopJson, Shop.class);
    ```

  - list集合

    ```java
    String shopTypeJson = JSONUtil.toJsonStr(typeList);
    ```

    ```java
    JSONArray jsonArray = JSONUtil.parseArray(shopTypeJson);
    List<ShopType> shopTypes = JSONUtil.toList(jsonArray, ShopType.class);
    ```

- 拷贝属性

  ```java
  UserDTO userDTO = BeanUtil.copyProperties(user, UserDTO.class);
  ```

- 对象转map

  ```java
  Map<String, Object> userMap = null;
  userMap = BeanUtil.beanToMap(userDTO, new HashMap<>(),
                               // 可定义转换规则
                               CopyOptions.create()
                               .setIgnoreNullValue(true)
                               .setFieldValueEditor((fieldName, fieldValue) -> fieldValue.toString()));
  ```

- 生成是否不带中划线的uuid

  ```java
  String token = UUID.randomUUID().toString(false); // 生成带中划线的uuid
  ```

  

  

### 缓存

#### 预热

- 项目启动时，就向redis中缓存部分热点数据

  ```java
  @Component
  public class RedisHandler implements InitializingBean {
      @Autowired
      private StringRedisTemplate redisTemplate;
      
      @Override
      public void afterPropertiesSet() throws Exception {
          // 初始化缓存：查询热点信息，放入缓存
          // 设置随机有效时间可有效解决缓存雪崩问题
      }
  }
  ```

  

#### 使用

- 缓存穿透

  - 问题：指请求的数据在**缓存中和数据库中**都不存在，这样的缓存永远也不会生效，每次请求都会打到数据库

  - 解决方案

    - 向redis中缓存空对象
      - 实现简单，维护方便
      - 存在额外的内存消耗（请求可能发送大量不同的垃圾参数。一般设置一个极短的有效时间）
      - 可能造成短期的不一致（redis先缓存null，用户新建数据，用户再查询返回却返回缓存中的null）

    - 布隆过滤：存在误判（一般过滤器拒绝的请求，一定不存在该数据，而放行的请求，不一定存在该数据）


  ![缓存穿透](D:\picture\typora\java\缓存穿透.png)

- 缓存雪崩

  - 问题：多项缓存同时失效或redis宕机
  - 解决
    - 多级缓存
    - 随机有效时间
    - redis集群

- 缓存击穿

  - 问题：缓存击穿问题也加热点key问题，就是一个高并发访问或缓存重构业务负载的key突然失效了，无数的请求访问会在瞬间给数据库带来巨大的冲击（所有请求都会去查询数据库并重建耗时量长的缓存，理论上只需一个）

    ![缓存击穿](D:\picture\typora\后端\redis\缓存击穿.png)

  - 解决

    - 互斥锁：**保证一致性**，但一方面所有查询请求需要等待，另一方面，可能**存在死锁风险**（如：方法A、B都有各自的锁，若A、B并行查找**同一用户/数据源**的两项数据，再同时都上了自己的锁**后**，又互相调用对方获得数据，此时一般会导致死锁）
      - 同一用户/数据源 ? ：上锁时，一般锁名为 `String lockKey = "lock:业务名:"+查询数据的id;`，同一数据源很可能导致id一致

    - 逻辑过期：发现缓存逻辑失效，开启一个线程去更新缓存，自己则返回逻辑过期数据；不需要等待，但**不保证一致性**，有额外内存消耗

    ![缓存击穿解决方法](D:\picture\typora\后端\redis\缓存击穿解决方法.png)

  - 如何定义互斥锁

    - 可以利用redis的 `SETNX key value`（如果key不存在，则添加键值；如果存在，则添加键值失败）指令

    - 问题：

      - 依赖于redis的高可用（应配置集群），若redis宕机，无论是从无法删除锁的角度，亦或是这个方法业务的角度，都会使整个方法业务不可用，若不断重试，甚至出现更糟糕的局面
      - **已经配置集群，真的就能直接这样定义redis锁吗？**如果增加slave从节点，因为Redis的主从同步通常是异步的，不能实现资源的独享（此处忽略了这个问题，详细可见[红锁](#红锁)）

      ```java
      private boolean tryLock(String key) {
          Boolean flag = stringRedis.opsValue().setIfAbsent(key, "1", 10, TimeUnit.SECONDS);
          
          // 不直接返回 flag 是因为，Boolean 拆箱成 boolean 可能存在空指针
          return BooleanUtils.isTrue(flag);
      }
      
      private void unlock(String key) {
          stringRedisTemplate.delete(key);
      }
      
      public Pojo 业务方法(参数) {
          String lockKey = "lock:业务名:"+查询数据的id;
          boolean isLock = tryLock(lockKey);
          失败则休眠后重试;
          成功则查询再缓存;
          最后释放互斥锁;
      }
      ```

      


​    




#### 秒杀

- redis缓存商品信息和库存
- 可用redis的set集合







### Redisson

#### 基础配置

- 引入依赖

  ```xml
  <dependency>
  	<groupId>org.redisson</groupId>
      <artifactId>redisson</artifactId>
      <version>3.13.6</version>
  </dependency>
  ```

- 不建议使用redisson的starter，应为会替代spring官方的redis-data-starter

- 添加redisson实例

  ```java
  @Configuration
  public class RedisConfig {
      @Bean
      public RedissonClient redissonClient() {
          // 配置类
          Config config = new Config();
          // 添加单点的redis地址，也可以使用config.userClusterServers()添加集群地址
          config.useSingleServer()
              .setAddress("redis://192.168.36.131:6379")
              .setPassword("redis");
          // 创建客户端并返回
          return Redisson.create(config);
      }
  }
  ```

  



#### 分布式锁

- 基于redis自定义分布式锁

  - 以 lock:order:userId 作为键，以 uuid等 作为值，设置合适的存活时间，以免死锁
  - 执行业务时，判断该键是否存在
  - 若键不存在，生成键值，执行业务
  - 执行完业务后，删除该键用来代表归还锁
  - 若键存在，说明用户在不同服务端下重复申请
  - 细节
    - 可通过 uuid 判断 生成键与将要销毁键 的是否是同一线程，若不是，则不允许该线程销毁
    - 是否是可重入的锁（同一个线程可以多次获得同一把锁，如：递归调用设置锁的线程时，不可重入会在第二次执行本方法时失败）

- <span id="Redisson">Redisson</span>

  - 说明：实现了可重入锁、公平锁、联锁、红锁、信号量、可过期性信号量、闭锁

  - 信号量：

    - 业务描述：根据服务器的实际承受能力，后台允许同时制作10份月结报表。如果服务器正在同时制作10份月结报表，那么其他制作月结报表的请求只能排队，直到有任务完成让出一个信号量，才能处理一个新的请求。
    - 实现原理：其实redisson分布锁信号量的实现原理非常简单，就是在redis中的一个整数，当有制作报表的请求时，我们首先尝试获得一个信号量，如果能够获得(也就是信号量的整数大于0)就执行制作报表的任务，然后让信号量总数减1。制作报表的任务完成之后，再调用release()方法释放一个信号量，信号总量+1。

  - <span id="红锁">红锁</span>

    - 问题

      - 实现Redis分布式锁的最简单的方法就是在Redis中创建一个key，这个key有一个失效时间（TTL)，以保证锁最终会被自动释放掉。当客户端释放资源(解锁）的时候，会删除掉这个key。

      - 从表面上看似乎效果不错，但有一个严重的单点失败问题：如果Redis挂了怎么办？你可能会说，可以通过增加一个slave节点解决这个问题。但这通常是行不通的。这样做，我们不能实现资源的独享，因为Redis的主从同步通常是异步的。

        在这种场景（主从结构）中存在明显的竞态：

        1. 客户端A从master获取到锁
        2. 在master将锁同步到slave之前，master宕掉了。
        3. slave节点被晋级为master节点
        4. 客户端B从新的master获取到锁，但是这个锁对应的资源之前已经被客户端A已经获取到了。安全失效！

    - Redis中针对此种情况，引入了[红锁](https://redis.io/topics/distlock)的概念。红锁采用主节点**过半机制**，即获取锁或者释放锁成功的标志为：在过半的节点上操作成功。

  - 闭锁：类似于CountDownLatch

    ```java
    @Autowired
    private RedissonClient redisson;
    
    //锁门
    @GetMapping("/lockdoor")
    public String lockdoor() throws InterruptedException {
        RCountDownLatch door = redisson.getCountDownLatch("door");
        //设置一个班级有20个同学
        door.trySetCount(20);
        //需要等到20个同学全部离开，才锁门
        door.await();
        return "锁门了";
    }
    //同学离开教室
    @GetMapping("/leave/{id}")
    public String leave(@PathVariable("id")Long id) throws InterruptedException {
        RCountDownLatch door = redisson.getCountDownLatch("door");
        //表示一个同学离开
        door.countDown();
        return ""+id+"号同学离开了";
    }
    ```

  - 可重入锁

    ```java
    @Resource
    private RedissonClient redissonClient;
    
    // 此方法用于模拟分布式下一人一单的 锁 操作
    @Test
    void testRedisson(int userId) throws InterruptedException {
        // 获取锁（可重入），指定锁的名称
        RLock lock = redissonClient.getLock("lock:order:" + userId);
        // 尝试获取锁，参数分别是：获取锁的最大等待时间（期间会重试），锁自动释放时间，时间单位
        // 默认值：(-1, 30*1000, TimeUnit.毫秒); -1 表示不等待
        boolean isLock = lock.tryLock(2, 10, TimeUnit.SECONDS);
        // 判断是否获取成功
        if (isLock) {
            try {
                System.out.println("执行业务");
            }finally {
                lock.unlock();
            }
        }
    }
    ```

  - 联锁（不使用redis集群，单纯在三个redis数据库间建立连锁。联锁：所有的锁都拿到，才能真正获得锁）

    ```java
    @Resource
    private RedissonClient redissonClient1;
    @Resource
    private RedissonClient redissonClient2;
    @Resource
    private RedissonClient redissonClient3;
    
    private RLock lock;
    
    @BeforeEach
    void setUp() {
        RLock lock1 = redissonClient1.getLock("lock:order:" + userId);
        RLock lock2 = redissonClient2.getLock("lock:order:" + userId);
        RLock lock3 = redissonClient3.getLock("lock:order:" + userId);
        
        lock = redissonClient.getMultiLock(lock1, lock2, lock3);
    }
    ```

    

  

  





## 高级篇

- 数据丢失问题：实现Redis数据持久化
- 并发能力问题：搭建主从集群，实现读写分离
- 存储能力问题：利用redis哨兵，实现健康检测和自动恢复
- 故障恢复问题：搭建分片集群，利用插槽机制实现动态扩容



### redis持久化

- RDB持久化（Redis Database Backup file）

  - Redis在正常关机时会触发一次数据持久化

  - Redis内部有触发RDB的机制，可以在redis.conf文件中找到

    ```ini
    # 900 秒内，如果有 1 个key被修改，则执行bgsave
    # 如果使用 save "" 则表示禁用RDB
    save 900 1 
    save 300 10
    save 60 10000
    
    # 是否压缩，建议不开启，压缩会消耗cpu，磁盘空间不值钱
    rdbcompression no
    # RDB文件名称
    dbfilename dump.rdb
    # 文件保存的路径目录
    dir ./
    ```

  - RDB方式 `save` 指令持久化：由主进程来执行RDB，会阻塞所有命令

  - RDB方式 `bgsave` 指令持久化的基本流程（整体是异步的）

    - fork主进程得到一个子进程，共享内存数据
      - fork过程是**同步阻塞**的
      - fork并非拷贝数据，而是让子进程获得与物理内存中的数据的**映射**关系
    - 子进程读取内存数据并写入新的RDB文件
      - 在持久化数据的过程中，若有数据修改，为避免脏数据，会复制该部分数据，在此基础上查询修改，最后合并
    - 用新的RDB文件替换旧的RDB文件

- AOF持久化：记录指令，类似于指令的日志，恢复时重新调用这些指令即可恢复数据

  - 如 `set number 1234` 指令在AOF中的记录形式

    ```aof
    $3
    set
    $6
    number
    $4
    1234
    ```

  - 配置文件

    ```ini
    # 是否开启AOF功能，默认no
    appendonly yes
    # AOF 文件的名称
    appendfilename "appendonly.aof"
    
    ####### 记录频率 #######
    # 表示每次执行一次写命令，立即记录到AOF文件
    appendfsync always
    # 写命令执行完先放到AOF缓冲区，然后每隔1秒将缓冲区数据写到AOF文件，是默认方案
    appendfsync everysec
    # 写命令执行完先放入AOF缓冲区，由操作系统决定何时将缓冲区内容写入磁盘
    appendfsync no
    ```

- RDB 和 AOF 的比较

  - 持久化方式：RDB -> 定时对整个内存做快照；AOF -> 记录每条指令

  - 文件大小：RDB < AOF
  - 恢复速度：RDB > AOF，RDB和AOF都开启，恢复数据时会用AOF恢复
  - 系统占用：RDB > AOF，AOF在重写时会占用大量cpu和内存资源
  - 使用场景：RDB -> 可容忍数分钟的数据丢失，追求更快的启动速度；AOF -> 对数据安全性要求高



### redis主从

- 搭建主从架构（从节点：slave/replica）一般主节点写，从节点读
- 数据同步步骤
  - **全量同步**：首次连接时执行
    - 第一阶段
      - slave节点执行`replicaof`命令，建立连接
      - slave节点 携带replid和offset 请求数据同步
      - master根据 replid与自己是否相同 判断是否是第一次同步
      - 是第一次同步，返回master的replid和offset

    - 第二阶段
      - master执行bgsave，生成RDB
      - 发送RDB到salve节点
      - salve节点清空本地数据，加载RDB文件
      - 对master执行bgsave的过程中接收到的修改数据库指令进行记录，形成repl_baklog文件

    - 第三阶段
      - master发送repl_backlog中的命令，salve执行接收到的命令

  - **增量同步**：后续数据同步
    - master持续记录，发送repl_baklog中的命令，salve执行接收到的命令
    - 增量同步失败
      - repl_backlog大小有上限，写满后会覆盖最早的数据。如果slave长时间不进行同步，repl_backlog写满后覆盖了slave尚未同步的数据，则无法做增量同步
- 数据同步原理
  - master如何判断slave是不是第一次来同步数据？这里会用到两个概念
    - Replication Id：简称replid，是数据集的标记，id一致则说明是同一数据集。每一个master都有唯一的replid，slave则会继承master节点的replid
    - offset：偏移量，随着记录在repl_backlog中的数据的增多而逐渐增大。slave完成同步时会记录当前同步的offset，如果slave的offset小于master的offset，说明slave数据落后于master，需要更新




### redis哨兵

- 原理

  - 服务状态监控
    - Sentinel基于心跳机制监测服务状态，每隔1秒向集群的每个实例发送ping命令
      - 主观下线：有sentinel节点发现某redis实例未在规定时间内响应，则认为该实例主观下线
      - 客观下线：超过指定数量（quorum）的sentinel都认为该实例主观下线，则该实例客观下线。quorum值最好超过sentinel实例数量的一半

  - 选举新的master
    - 首先会判断slave节点与master节点断开时间长短，如果时间超过指定值（down-after-milliseconds*10）则会排除该slave节点
    - 然后判断slave节点的slave-priority值（配置文件配置），越小优先级越高，如果是0则永不参与选举
    - 如果slave-priority一样，则判断slave节点的offset值，越大说明数据越新，优先级越高
    - 最后判断slave节点的运行id大小，越小优先级越高

  - 故障转移
    - 当选中一个slave成为新的master后
      - sentinel 给该slave节点发送 `slaveof no one` 命令，让该节点成为master
      - sentinel给所有其他slave发送 `slaveof <host> <port>` 命令，让其他节点成为新master的从节点
      - 最后，sentinel在故障节点的配置文件中添加slaveof指令，当故障节点恢复后会自动成为新的master的slave节点

- [搭建sentinel集群（此处搭建3个sentinel实例）](https://www.bilibili.com/video/BV1cr4y1671t?p=106&vd_source=25ad2de4838bd28372a4956bac63c618)

  - 先创建三个文件夹作为各个sentinel的工作目录，将配置文件放在其中

  - 编写sentinel实例1的配置文件，文件名：sentinel.conf

    ```ini
    # 当前sentinel（此处是sentinel实例1）的ip、端口信息
    port 27001
    sentinel announce-ip 192.168.36.132
    # 为当前sentinel指定工作目录
    dir "/tmp/sentinel1"
    
    # 配置redis主节点信息
    	# mymaster：主节点名称，自定义
    	# 192.168.36.132 7001：主节点的ip和端口（主节点中保存了从节点的信息，所以只用填主节点）
    	# 2：选举master时的quorum
    sentinel monitor mymaster 192.168.36.132 7001 2
    sentinel down-after-millisenconds mymaster 5000
    sentinel failover-timeout mymaster 60000
    ```

  - 编写剩余两个sentinel实例的配置文件：sentinel.conf

  - 分别执行指令 `redis-sentinel <配置文件的路径>`

- redisTemplate的哨兵模式

  - 引入依赖

    ```xml
    <dependency>
    	<groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis</artifactId>
    </dependency>
    ```

  - 因为master节点是变动的，所有不需要配置master地址，只需要配置sentinel信息

    ```yaml
    spring:
      redis:
        sentinel:
          master: mymaster # 指定master名称
          nodes: # 指定redis-sentinel集群信息
            - 192.168.36.131:27001
            - 192.168.36.131:27002
            - 192.168.36.131:27003
    ```

  - 配置读写分离

    ```java
    @Bean
    public LettuceClientConfigurationBuilderCustomizer configurationBuilderCustomizer() {
        return configBuilder -> configBuilder.readFrom(ReadFrom.REPLICA_PREFERRED);
    }
    ```

    - ReadFrom是配置Redis的读取策略，是一个枚举，包括下面选择
      - MASTER：从主节点读取
      - MASTER_PREFERRED：优先从主节点读取，主节点不可用才读取slave(replica)
      - REPLICA：从slave节点读取
      - REPLICA_PREFERRED：优先从slave节点读取，所有slave不可用才读取master



### redis分片

- 说明
  - 主从和哨兵可以解决高可用、高并发读的问题。但无法解决海量数据存储、高并发写的问题
  - 使用分片集群可以解决上述所有问题，其特征：
    - 集群中有多个master，每个master保存不同的数据
    - 每个master都可以有多个slave节点
    - **master之间通过ping检测彼此的健康状态**
    - **客户端请求可以访问集群中的任一节点，最终都会被路由转发到正确的节点**

- 搭建集群

  - 以 redis.conf 获取redis实例

  - redis5.0后，集群管理集成到redis-cli中，格式如下

    ```shell
    redis-cli --cluster \ 
    create --cluster-replicas 1 \ 
    192.168.150.101:7001 192.168.150.101:7002 192.168.150.101:7003 192.168.150.101:8001 192.168.150.101:8002 192.168.150.101:8003
    ```

    - redis-cli --cluster：代表集群操作命令
    - create：表示创建集群
    - --replicas 1 或 --cluster-replicas 1：指定集群中每个master的副本个数为1，此时`节点总数 / (replicas + 1)`得到的就是master的数量。因此节点列表中的**前n个就是master**，其他节点都是slave节点，随机分配到不同的master上

- **散列插槽**

  - 所有master分配 0-16383 共16384个插槽，存入数据时，由key经一定算法得到hash值，再对16384取余，将数据存储到该余数对应的插槽上
    - 若key中没有 {} ，根据整个key计算hash
    - 若key中有 {}， 根据 {} 内的部分计算hash
  - 相比于数据和节点绑定，数据和插槽绑定后，若该master宕机，可将插槽和数据一并交由下一个master管理

- 集群伸缩

  - 添加节点：新增的节点没有插槽，需要分配

    ```shell
    redis-cli \ 
    --cluster add-node <new_host>:<new_port> <existing_host>:<existing_port> \ 
    --cluster-slave \ # 表示添加的是从节点
    --cluster-master-id  <arg> # 指定该节点的master 
    ```

  - 分配插槽：reshard命令

    - 先压缩某master上的插槽

      ```shell
      redis-cli --cluster reshard <existing_host>:<existing_port>
      ```

    - How many slots do you want to move (from 1 to 16384)?

    - What is the receiving node ID?

    - Please enter all the source node IDS.

      - 填写插槽来源的masterID
      - 填写done表示完成

- 故障转移

  - 默认故障转移：有master节点宕机后，选举出新的master
  - 指定故障转移：在从节点上执行 CLUSTER FAILOVER 可让该从节点替换master节点

- RedisTemplate访问分片集群

  - 引入redis的starter依赖

  - 配置分片集群地址

    ```yaml
    spring:
      redis:
        cluster:
          nodes: # 指定分片集群的每个节点信息
            - 192.168.36.132:7001
            - 192.168.36.132:7002
            - 192.168.36.132:7003
            - 192.168.36.132:8001
            - 192.168.36.132:8002
            - 192.168.36.132:8003
    ```

  - 配置读写分离：与哨兵模式配置读写分离一致









# PostgreSQL



## 部署

### docker部署

- 拉去镜像

  ```shell
  docker pull postgres
  ```

- 创建挂载目录

  ```shell
  mkdir -p  /var/docker-volume/postgres/data
  ```

- 构建容器

  ```shell
  docker run -it \
  --name postgres \
  --restart always \
  -e POSTGRES_PASSWORD='root' \
  -e ALLOW_IP_RANGE=0.0.0.0/0 \
  -v /var/docker-volume/postgres/data:/var/lib/postgresql/data \
  -p 5432:5432 \
  -d postgres
  ```

  - `-e ALLOW_IP_RANGE=0.0.0.0/0`：表示允许所有IP访问，如果不加，则非本机IP访问不了

- 进入容器

  ```java
  docker exec -it postgres bash
  ```



### 登录

- 切换用户

  ```shell
  su postgres
  ```

- 登录

  ```shell
  psql -h 服务器 -U 用户名 -d 数据库 -p 端口
  
  # 登录到 postgres 用户下的 postgres 数据库
  psql 
  ```

  - `-d`：登录后默认进入的数据库。如果不指定数据库，以什么用户登录，就会进入与该用户名相同的数据库，若没有与该用户名相同的数据库，则报错 `FATAL:  database "用户名" does not exist`

- 退出登录

  ```shell
  \q
  ```

- 常用命令
  - `\password`：设置密码
  - `\q`：退出
  - `\h`：查看SQL帮助文档
  - `\?`：查看命令帮助文档
  - `\l`：列出所有数据库
  - `\c <database_name>`：连接其他数据库
  - `\d`：列出当前数据库的所有表格
  - `\d <table_name>`：列出某一张表格的结构
  - `\du`：列出所有用户



### 配置文件

- pg_hba.config：该配置文件用于配置允许访问的ip地址，访问的方式

  ```ini
  # TYPE  DATABASE        USER          ADDRESS             METHOD
  
  # "local" is for Unix domain socket connections only
  local   all             all                               trust
  # IPv4 local connections:
  host    all             all           127.0.0.1/32        trust
  # IPv6 local connections:
  host    all             all           ::1/128             trust
  # Allow replication connections from localhost, by a user with the
  # replication privilege.
  local   replication     all                               trust
  host    replication     all           127.0.0.1/32        trust
  host    replication     all           ::1/128             trust
  host    all             all           all                 scram-sha-256
  ```

  - TYPE
    - local：本地访问
    - host：主机访问
  - DATABASE：访问登录的数据库
  - USER：访问登录的用户
  - ADDRESS：访问的ip地址
  - METHOD：访问的类型
    - peer：用linux用户验证登录（type是local的那条可能使用）
    - trust：不需要输入密码
    - scram-sha-256、md5：需要输入密码（会把密码加密）



## 基本使用

### 数据库操作

- 数据库操作
  - 查看所有数据库：`\l`
  - 查看数据库下所有的表：`\d 数据库`（`\d`：查看当前表）
  - 创建数据库：`create database 数据库名;`
  - 切换数据库：`\c 数据库名`
  - 删除数据库：`drop database 数据库名;`



### 数据类型

- 数值类型

  | 名       | 存储长度 | 描述       | 范围                                    |
  | -------- | -------- | ---------- | --------------------------------------- |
  | smallint | 2byte    | 小范围整数 | -2^15^ 到 2^15^-1（3.2万）              |
  | interger | 4byte    | 常用整数   | -2^31^ 到 2^31^-1（21.47亿）            |
  | bigint   | 8byte    | 大范围整数 | -2^61^ 到 2^61^-1（2.3*10^18^）         |
  | decimal  | 可变长度 |            | 小数点前 2^17^ 位<br/>小数点后 2^14^ 位 |
  | numeric  | 可变长度 |            | 小数点前 2^17^ 位<br/>小数点后 2^14^ 位 |
  | real     | 4byte    | 可变精度   | 6 位十进制数字精度                      |
  | double   | 8byte    | 可变精度   | 15 位十进制数字精度                     |
  
- 序列（可用于id）：

  - smallserial：1-2^15^
  - serial：1-2^31^
  - bigserial：1-2^63^

- 字符串类型

  - char(size)，character(size)：固定长度字符串，size规定了存储的字符数，由右边的空格补齐
  - varchar(size)，character varying(size)：可变长度字符串，size规定了需要存储的字符数
  - text：可变长度字符串

- 日期

  - timestamp：日期和时间
  - data：日期
  - time：时间

- 布尔值：boolean（true 或 false）

- 货币数额：money

- 其他：json、集合数据等



### 表操作

- 实例

  ```sql
  CREATE TABLE student(
      id     SERIAL   PRIMARY KEY,
      name   TEXT     NOT NULL,
      age    INT      NOT NULL
  );
  
  -- 注意：字段用双引号，字符串值用单引号
  INSERT INTO student (name, age)
  VALUES ('张三', 32);
  
  SELECT * FROM student;
  
  UPDATE student 
  SET name='李四' 
  WHERE id = 1; 
  
  DELETE FROM student
  WHERE id = 1;
  ```





### Schema

- 说明：PostgreSQL模式（Schema）可以看做是一个表的集合（又或者说是一个数据库下的文件夹）。一个模式可以包含视图、索引、数据类型、函数和操作符等。

  相同的对象名可以被用于不同的模式中而不会出现冲突，例如在数据库mydb下创建了schema1和schema2两个模式，两个模式中，都可以创建mytable的表

- 创建表时，不指定schema，默认在 public 这个模式中

- 实例

  ```sql
  -- 创建schema
  create schema myschema;
  
  -- 在schema下创建表
  create table myschema.student (
      id serial primary key,
      name varchar(30) not null
  );
  
  -- 删除schema
  drop schema myschema;
  
  -- 删除一个模式，及其里面所有对象（表、视图、函数等）
  drop schema myschema cascade;
  ```





### 备份数据

- 单数据库

  - PostgreSQL提供了 `pg_dump` 实用程序来简化备份单个数据库的过程。必须对要备份数据库具有读取权限的用户才能运行此命令

  - 流程

    - 以 postgres 用户身份登录

      ```shell
      su postgres
      ```

    - 通过运行以下命令将数据库中的内容转储到文件中。

      ```shell
      pg_dump 数据库名 > 数据库名.bak
      ```

    - 生成的备份文件 `数据库名.bak` 可以使用 scp 传输到另一台主机

    - 恢复数据前须先创建一个新的数据库

      ```sql
      create database 数据库名;
      ```

    - 使用 `psql` 恢复数据

      ```shell
      psql test < 数据库名.bak
      ```

  - 备份数据格式的几种选择

    - `*.bak`：压缩二进制格式
    - `*.sql`：明文转储
    - `*.tar`：tarball

  - 注意：**默认情况下，PostgreSQL将忽略备份过程中发生的任何错误。**这可能导致备份不完整。要防止这种情况，可以使用 `-l` 选项运行 `pd_dump` 命令，这会将整个备份过程视为单个事务，这将在发生错误时阻止部分备份。

- 所有数据库

  - 由于 `pg dump` 一次只能备份一个数据库，因此它不会存储有关数据库角色或其他集群范围配置信息。要备份所有数据库，并存储此信息，可以使用 `pg_dumpall`

    ```shell
    pd_dumpall > pg_backup.bak
    ```

  - 还原

    ```shell
    psql -f pg_backup.bak postgres
    ```





### 用户

- 创建用户

  ```sql
  create user 'username' with password 'password';
  create user username with password 'password';
  ```

- 查看所有用户

  ```shell
  \du  # 查看所有用户
  
  # 登录时可使用
  psql -U username -d 登录后进入的数据库
  ```

  ```sql
  select * from pg_user; -- 查看所有的用户
  #   usename  | usesysid | usecreatedb | usesuper | userepl | usebypassrls | passwd| ...
  #  ----------+----------+-------------+----------+---------+--------------+-------+-----
  #   postgres | 10       | t           | t        | t       | t            | ***** | ...
  #   ......
  
  select * from pg_roles; -- 查看所有的角色，也可以查看到用户
  ```

- 修改用户密码

  ```sql
  alter user username with password 'password';
  ```

- 数据库授权

  ```sql
  -- 授予了对数据库的所有权限，无法操作数据库里原有的表
     -- ？不知道有哪些权限。不授权，用户也可以连接，创建新表；授权了，用户也不能查看数据库中原有的表
  grant all privileges on database dbname to username;
  
  -- 注意，该SQL语句必须在所要操作的数据库里执行
  grant all privileges
  on all tables
  in schema public -- 默认创建的表都在public中
  to username;
  ```

- 移出指定账户指定数据库的权限

  ```sql
  revoke all privileges
  on database mydb
  from username;
  
  revoke all privileges
  on all tables
  in schema public
  from username;
  ```

- 删除用户（删除前可能需要移出该用户所有权限 和 该用户创建的所有的表）

  ```sql
  drop user username;
  ```



### 角色

- 创建角色

  ```sql
  CREATE ROLE rolename;                  -- 默认不带有登录属性（不能登录）
  create role eli with login cretedb;    -- 创建带属性的角色，多个权限空格分隔
  alter role eli with password 'eli';    -- 添加属性
  drop role eli;                         -- 删除角色
  
  CREATE USER username;                  -- 默认带有登录属性
  ```

- 查看

  ```sql
  create role eli;
  
  \du  # 查看用户及角色（pg并没有过于区分用户和角色）
  
  #  Role name |                 List of roles Attributes                   | Member of
  # -----------+------------------------------------------------------------+----------
  #   postgres | superuser, Create role, Create DB, Replication, Bypass RLS | {}
  #   eli      | Cantnot login                                              | {}
  
  
  select * from pg_roles; -- 可以查看到用户，也可以查看到角色
  ```

- 角色属性

  | 属性            | 说明                                                     |
  | --------------- | -------------------------------------------------------- |
  | login           | 只有具有LOGIN属性的角色可以用做数据库连接的初始角色名    |
  | superuser       | 超级用户                                                 |
  | createdb        | 创建数据库的权力                                         |
  | createrole      | 允许其创建和删除其他普通的用户角色（超级用户除外）       |
  | replication     | 做流复制时用到了一个用户属性，一般单独设定               |
  | password '密码' | 在登录时要求指定密码才会起作用（登录时需要输密码）       |
  | iherit          | 用户组对组员的一个继承标志，成员可以继承用户组的权限特性 |

  







































# Hadoop



## HDFS

### linux

- 修改主机名

  ```shell
  hostnamectl set-hostname 主机名
  ```

- 修改IP地址

  ```shell
  vim /etc/sysconfig/network-scripts/ifcfg-ens33
  ```

  ```ini
  # dhcp 自动获取id，static固定
  BOOTPROTO="static"
  IPADDR="IP地址"
  NETMASK="子网掩码"
  GATEWAY="网关"
  DNS1="DNS"
  ```

  ```shell
  systemctl stop network
  systemctl start network
  systemctl restart network
  ```

- 环境变量：在 /etc/profile 文件末添加

  ```ini
  export HADOOP_HOME=/export/server/hadoop
  export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
  ```

  ```shell
  source /etc/profile
  ```

- 创建用户

  ```shell
  # 创建用户
  useradd 用户名
  # 为用户添加密码
  passwd 用户名
  # 切换到该用户
  su 用户名
  # 更改文件权限
  chown -R 用户名:用户名 目录
  ```



### 部署

- hadoop文件结构

  - bin：存放Hadoop的各类命令程序
  - etc：存放Hadoop的配置文件
  - sbin：管理员命令程序
  - include：c语言的一些头文件
  - lib：存放Linux系统的动态链接库（.so文件）
  - libexec：存放配置Hadoop系统的脚本文件（.sh和.cmd）
  - licenses-binary：存放许可证文件
  - share：存放二进制源码（.jar等）

- 配置

  - 配置 <font color=blue>workers</font> 文件

    ```ini
    192.168.88.129:8020
    192.168.88.129:8021
    192.168.88.129:8022
    ```

  - 配置 <font color=blue>hadoop-env.sh</font> 文件

    ```ini
    export JAVA_HOME=/export/server/jdk
    export HADOOP_HOME=/export/server/hadoop
    export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
    export HADOOP_LOG_DIR=$HADOOP_HOME/logs
    ```

  - 配置 <font color=blue>core-site.xml</font> 文件

    ```xml
    <configuration>
    	<property>
        	<name>fs.defaultFS</name>
            <value>hdfs://192.168.88.129:8020</value>
        </property>
        
        <property>
        	<name>io.file.buffer.size</name>
            <value>13107</value>
        </property>
    </configuration>
    ```

  - 配置 <font color=blue>hdfs-site.xml</font> 文件

    ```xml
    <configuration>
        <!-- hdfs文件系统中，新建文件的默认权限是700 -->
    	<property>
        	<name>dfs.datanode.data.dir.perm</name>
            <value>700</value>
        </property>
        <!-- NameNode 元数据的存储位置 -->
        <property>
        	<name>dfs.namenode.name.dir</name>
            <value>/data/nn</value>
        </property>
        <!-- NameNode 允许哪些节点的 DataNode 连接 -->
        <property>
        	<name>dfs.namenode.hosts</name>
            <value>192.168.88.129</value>
        </property>
        <!-- hdfs 默认块大小 -->
        <property>
        	<name>dfs.blocksize</name>
            <value>268435456</value>
        </property>
        <!-- NameNode 处理的并发线程数 -->
        <property>
            <name>dfs.namenode.handler.count</name>
            <value>100</value>
        </property>
        <!-- DataNode 的数据存储目录 -->
        <proerty>
            <name>dfs.datanode.data.dir</name>
            <value>/data/dn</value>
        </proerty>
    </configuration>
    ```

  - 创建文件夹 /data/nn、/data/dn

































