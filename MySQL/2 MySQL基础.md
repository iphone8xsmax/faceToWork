[TOC]

## MySQL 基础

### 一、基础

#### 基本概念

##### 1. 杂记

模式定义了数据如何存储、存储什么样的数据以及数据如何分解等信息，数据库和表都有模式。

主键的值不允许修改，也不允许复用（不能将已经删除的主键值赋给新数据行的主键）。

SQL（Structured Query Language)，标准 SQL 由 ANSI 标准委员会管理，从而称为 ANSI SQL。各个 DBMS 都有自己的实现，如 PL/SQL、Transact-SQL 等。

SQL 语句不区分大小写，但是数据库表名、列名和值是否区分依赖于具体的 DBMS 以及配置。

##### 2. 数据库存储数据的特点

- 将数据放到表中，表再放到库中
- 一个数据库中可以有多个表，每个表都有一个的名字，用来标识自己。表名具有**唯一性**。
- 表具有一些特性，这些特性定义了数据在表中如何存储，类似 Java 中 “**类**”的设计。
- 表由列组成，我们也称为**字段**。所有表都是由一个或多个列组成的，每一列类似 Java 中的”**属性**”。
- 表中的数据是**按行存储**的，每一行类似于 Java 中的“**对象**”。

##### 3. MySQL的语法规范

```sql
1.不区分大小写,但建议关键字大写，表名、列名小写
2.每条命令最好用分号结尾
3.每条命令根据需要，可以进行缩进 或换行，如：
	SELECT
	*
	FROM
	studentinfo;
4.注释
	单行注释：# 注释文字
	单行注释：-- 注释文字（--后面有空格）
	多行注释：/* 注释文字  */
```

##### 4. SQL的语言分类

- **DQL**（Data Query Language）：数据查询语言，如：select 。
- **DML**(Data Manipulate Language) : 数据操作语言，如：insert 、update、delete。
- **DDL**（Data Define Languge）: 数据定义语言，如：create、drop、alter。
- **TCL**（Transaction Control Language）：事务控制语言，如：commit、rollback。
- 后面的语句介绍即遵循分类介绍。

##### 5. 基本使用

###### ① 常用命令

```mysql
/* 创建Test数据库 */
CREATE DATABASE [IF NOT EXISTS] test;
/* 查看当前所有的数据库 默认有三个库不能动！里面是数据库信息！*/
show databases;   
/* 使用指定的库 */
use 库名;
/* 查看当前库的所有表 */
show tables;
/* 查看其它库的所有表,库的使用没变化，只是显示了其他库的表 */
show tables from 库名;
/* 创建表 */
create table 表名(
		列名 列类型,
		列名 列类型，
	  ...
);
/* 查看表结构 */
desc 表名;
/* 查看服务器的版本 */
select version();  # 方式一：登录到mysql服务端
mysql --version    # 方式二：没有登录到mysql服务端
mysql --V
```

###### ② 连接数据库

```mysql
mysql 【-h主机名 -P端口号 】-u用户名 -p密码
mysql -h localhost -P 3306 -u root -p 
mysql -h 120.79.59.125 -u root /*登录到服务器的MySQL*/
mysql -u root -p  # 然后输入密码登录
```



#### MySQL数据类型

##### 1. 整型

**TINYINT, SMALLINT, MEDIUMINT, INT, BIGINT** 分别使用 **8, 16, 24, 32, 64 位**存储空间，一般情况下**越小**的列越好。

INT(11) 中的数字只是规定了交互工具**显示字符**的个数，对于存储和计算来说是没有意义的。

##### 2. 浮点数

**FLOAT 和 DOUBLE 为浮点**类型，**DECIMAL 为高精度小数**类型。CPU 原生支持浮点运算，但是不支持 DECIMAl 类型的计算，因此 DECIMAL 的计算比浮点类型需要更高的代价。

FLOAT、DOUBLE 和 DECIMAL 都可以指定**列宽**，例如 **DECIMAL(18, 9)** 表示总共 18 位，取 9 位存储小数部分，剩下 9 位存储整数部分。

##### 3. 字符串

主要有 **CHAR（定长） 和 VARCHAR（变长）** 两种类型。

VARCHAR 这种**变长类型**能够**节省空间**，因为只需要存储必要的内容。但是在执行 UPDATE 时可能会使行变得比原来长，当超出一个页所能容纳的大小时，就要执行额外的操作。MyISAM 会将行拆成不同的片段存储，而 InnoDB 则需要分裂页来使行放进页内。

如果字符串长度比较确定且几乎一致，最好使用 CHAR 类型。

在进行存储和检索时，会**保留** VARCHAR 末尾的空格，而会**删除** CHAR 末尾的空格。

##### 4. 时间和日期

MySQL 提供了两种相似的日期时间类型：**DATETIME 和 TIMESTAMP**。

###### ① DATETIME

能够保存从 1001 年到 9999 年的日期和时间，精度为**秒**，使用 **8 字节**的存储空间。

它与**时区无关**。

默认情况下，MySQL 以一种可排序的、无歧义的格式显示 **DATETIME** 值，例如“2008-01-16 22:37:08”，这是 ANSI 标准定义的日期和时间表示方法。

阿里巴巴规范**建议**使用此类型。

###### ② TIMESTAMP

和 UNIX 时间戳相同，保存从 1970 年 1 月 1 日午夜（格林威治时间）以来的秒数，使用 **4 个字节**，只能表示从 1970 年到 2038 年。

它和**时区有关**，也就是说一个时间戳在不同的时区所代表的具体时间是不同的。

MySQL 提供了 FROM_UNIXTIME() 函数把 UNIX 时间戳转换为日期，并提供了 UNIX_TIMESTAMP() 函数把日期转换为 UNIX 时间戳。

默认情况下，如果插入时没有指定 TIMESTAMP 列的值，会将这个值设置为当前时间。

TIMESTAMP 比 DATETIME 空间效率更高。



#### MySQL运算符

主要分为几个大类，并进行优先级对比。

##### 1. 算术运算符

<img src="assets/image-20200531200834590.png" alt="image-20200531200834590" style="zoom: 67%;" />

##### 2. 比较运算符

<img src="assets/image-20200531200901951.png" alt="image-20200531200901951" style="zoom:67%;" />

##### 3. 逻辑运算符

<img src="assets/image-20200531200924576.png" alt="image-20200531200924576" style="zoom:67%;" />

“＆＆”或者“AND”是**“与”运算**的两种表达方式。如果所有数据不为0且不为空值（NULL），则结果返回 1；如果存在任何一个数据为 0，则结果返回 0；如果存在一个数据为 NULL 且没有数据为 0，则结果返回 NULL。“与”运算符支持多个数据同时进行运算。

“||”或者“OR”表示**“或”运算**。所有数据中存在任何一个数据为非 0 的数字时，结果返回 1；如果数据中不包含非 0 的数字，但包含 NULL 时，结果返回 NULL；如果操作数中只有 0 时，结果返回 0。“或”运算符“||”可以同时操作多个数据。

“！”或者NOT表示**“非”运算**。通过“非”运算，将返回与操作数据相反的结果。如果操作数据是非 0 的数字，结果返回0；如果操作数据是 0，结果返回 1；如果操作数据是 **NULL**，结果返回 **NULL**。

##### 4. 位运算符

<img src="assets/image-20200531201011015.png" alt="image-20200531201011015" style="zoom:67%;" />

##### 5. 优先级

<img src="assets/image-20200531201022804.png" alt="image-20200531201022804" style="zoom:67%;" />



### 二、DDL 数据定义语言

#### 库的管理

```mysql
# 创建库Books
CREATE DATABASE IF NOT EXISTS books ;
# 库的修改
RENAME DATABASE books TO 新库名;
# 更改库的字符集
ALTER DATABASE books CHARACTER SET gbk;
# 库的删除
DROP DATABASE IF EXISTS books;
```

#### 表的管理

##### 1. 创建表

```mysql
CREATE TABLE [IF NOT EXISTS] 表名(
	列名 列的类型【(长度) 约束】,
	列名 列的类型【(长度) 约束】,
	列名 列的类型【(长度) 约束】,
	...
	列名 列的类型【(长度) 约束】
);

CREATE TABLE [IF NOT EXISTS] stuinfo(
	stuId INT,
	stuName VARCHAR(20),
	gender CHAR,
	bornDate DATETIME
);
DESC studentinfo;
```

##### 2. 修改表

```mysql
ALTER TABLE 表名 ADD|MODIFY|DROP|CHANGE COLUMN 字段名 【字段类型】;
```

```mysql
# 修改列名
ALTER TABLE book CHANGE COLUMN publishdate pubDate DATETIME;
# 修改列的类型或约束
ALTER TABLE book MODIFY COLUMN pubdate TIMESTAMP;
# 添加新列
ALTER TABLE author ADD COLUMN annual DOUBLE; 
# 删除列
ALTER TABLE book_author DROP COLUMN  annual;
# 修改表名
ALTER TABLE author RENAME TO book_author;
DESC book;
```

##### 3. 删除表

```mysql
DROP TABLE IF EXISTS book_author;
SHOW TABLES;
```

通用的写法：

```mysql
DROP TABLE IF EXISTS 旧表名;
CREATE TABLE  表名();
```

##### 4. 表数据的复制

```mysql
INSERT INTO author VALUES
(1,'村上春树','日本'),
(2,'莫言','中国'),
(3,'冯唐','中国'),
(4,'金庸','中国');

SELECT * FROM Author;
SELECT * FROM copy2;

# 1.仅仅复制表的结构
CREATE TABLE copy LIKE author;

# 2.复制表的结构 + 数据
CREATE TABLE copy2 
SELECT * FROM author;

# 只复制部分数据
CREATE TABLE copy3
SELECT id, au_name
FROM author 
WHERE nation = '中国';

# 仅仅复制某些字段而不复制数据
CREATE TABLE copy4 
SELECT id, au_name
FROM author
WHERE 0;  # 不复制数据
```



#### 表的约束

约束：一种限制，用于限制表中的数据，为了保证表中的数据的**准确和可靠性**。

##### 1. 六大约束

|      约束       |                             描述                             |
| :-------------: | :----------------------------------------------------------: |
|  **NOT NULL**   |             **非空**，用于保证该字段的值不能为空             |
|   **DEFAULT**   |               **默认**，用于保证该字段有默认值               |
| **PRIMARY KEY** |       **主键**，用于保证该字段的值具有唯一性，并且非空       |
|   **UNIQUE**    |       **唯一**，用于保证该字段的值具有唯一性，可以为空       |
|    **CHECK**    |    **检查约束**【mysql中不支持，语法上支持但是写了没用】     |
| **FOREIGN KEY** | 外键，用于限制**两个表**的关系，用于保证该**字段的值**必须来自于**主表的关联列的值**。在**==从表==添加外键**约束，用于引用**==主表==中某列**的值 |

添加约束的时机：

- **创建**表时
- **修改**表时

**约束的添加分类：**

- **列级**约束：六大约束语法上都支持，但外键约束没有效果。
- **表级**约束：除了非空、默认，其他的都支持。

```mysql
CREATE TABLE 表名(
	字段名 字段类型 列级约束,
	字段名 字段类型,
	表级约束
)
```

##### 2. 创建表时添加约束

###### ① 添加列级约束

- 语法：**直接在字段名和类型后面追加约束类型即可**。
- 只支持：默认、非空、主键、唯一

```mysql
/*
 添加列级约束实例
*/
USE students;
DROP TABLE stuinfo;
CREATE TABLE stuinfo(
	id INT PRIMARY KEY,  # 主键约束
	stuName VARCHAR(20) NOT NULL UNIQUE,  # 非空约束
	gender CHAR(1) CHECK(gender = '男' OR gender = '女'),	# 检查约束,其实没效果
	seat INT UNIQUE, # 唯一约束
	age INT DEFAULT 18, # 默认约束
	majorId INT
);

# 查看stuinfo中的所有索引，包括主键、外键、唯一
SHOW INDEX FROM stuinfo;
```

###### ② 添加表级约束

语法：在各个字段的最下面**【CONSTRAINT 约束名】 约束类型(字段名)** 

```mysql
/*
 添加表级约束实例
*/
DROP TABLE IF EXISTS stuinfo;
CREATE TABLE stuinfo(
	id INT,
	stuname VARCHAR(20),
	gender CHAR(1),
	seat INT,
	age INT,
	majorid INT,	# 下面加了外键的列
    # 下面添加表级约束
	CONSTRAINT pk PRIMARY KEY(id),  # 主键约束
	CONSTRAINT uq UNIQUE(seat),		# 唯一键约束
	CONSTRAINT ck CHECK(gender = '男' OR gender  = '女'), # 检查，其实无效果
	CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id) 	# 外键 majorid 与 major 表的 id
);
# 查看stuinfo中的所有索引，包括主键、外键、唯一
SHOW INDEX FROM stuinfo;



```

通用的写法：★

```mysql
CREATE TABLE IF NOT EXISTS stuinfo(
	id INT PRIMARY KEY,		# 主键
	stuname VARCHAR(20),
	sex CHAR(1),
	age INT DEFAULT 18,		# 默认值约束
	seat INT UNIQUE,		# 唯一键
	majorid INT,
	CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id) # 外键
);
```

> **主键与唯一键约束的对比**

|        | 保证唯一性 | 是否允许为空 | 一个表可以有多少个 | 是否允许组合 |
| :----: | :--------: | :----------: | :----------------: | :----------: |
|  主键  |     √      |      ×       |      至多一个      | √，但不推荐  |
| 唯一键 |     √      |      √       |      可以多个      | √，但不推荐  |

##### 3. 修改表时添加约束

1、添加**列级**约束

```mysql
ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型 新约束;
```

2、添加**表级**约束

```mysql
ALTER TABLE 表名 ADD 【constraint 约束名】 约束类型(字段名) 【外键的引用】;
```

```mysql
DROP TABLE IF EXISTS stuinfo;
CREATE TABLE stuinfo(
	id INT,
	stuname VARCHAR(20),
	gender CHAR(1),
	seat INT,
	age INT,
	majorid INT
)
DESC stuinfo;
# 1.添加非空约束
ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20) NOT NULL;
# 2.添加默认约束
ALTER TABLE stuinfo MODIFY COLUMN age INT DEFAULT 18;
# 3.添加主键
# ①列级约束
ALTER TABLE stuinfo MODIFY COLUMN id INT PRIMARY KEY;
# ②表级约束
ALTER TABLE stuinfo ADD PRIMARY KEY(id);
# 4.添加唯一
# ①列级约束
ALTER TABLE stuinfo MODIFY COLUMN seat INT UNIQUE;
# ②表级约束
ALTER TABLE stuinfo ADD UNIQUE(seat);
# 5.添加外键
ALTER TABLE stuinfo ADD CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id); 
```

##### 4. 修改表时删除约束

```mysql
# 1.删除非空约束
ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20) NULL;
# 2.删除默认约束
ALTER TABLE stuinfo MODIFY COLUMN age INT ;
# 3.删除主键
ALTER TABLE stuinfo DROP PRIMARY KEY;
# 4.删除唯一
ALTER TABLE stuinfo DROP INDEX seat;
# 5.删除外键
ALTER TABLE stuinfo DROP FOREIGN KEY fk_stuinfo_major;
SHOW INDEX FROM stuinfo;
```



#### 标识列

标识列又称为**自增长列**。可以不用手动的插入值，系统提供默认的**序列值**。

**特点：**
1、标识列必须和主键搭配吗？不一定，但要求是一个 key
2、一个表可以有几个标识列？**至多一个**！
3、标识列的类型只能是**数值型**
4、标识列可以通过 SET auto_increment_increment = 3; 设置**步长**
5、可以通过手动插入值，设置起始值

```mysql
# 一、创建表时设置标识列
DROP TABLE IF EXISTS tab_identity;
CREATE TABLE tab_identity(
	id INT  ,
	NAME FLOAT UNIQUE AUTO_INCREMENT,  # 自增长
	seat INT 
);
TRUNCATE TABLE tab_identity;
# 插入值 但是不插入Name字段的数据
INSERT INTO tab_identity(id,NAME) VALUES(NULL,'john');
INSERT INTO tab_identity(NAME) VALUES('lucy');
SELECT * FROM tab_identity;

SHOW VARIABLES LIKE '%auto_increment%';
SET auto_increment_increment = 3;
```





### 三、DQL 数据查询语言

#### 查询所用的数据库表格

先建几个表方便后续使用。

```mysql
DROP TABLE IF EXISTS `departments`;
CREATE TABLE `departments` (
  `department_id` int(4) NOT NULL AUTO_INCREMENT,
  `department_name` varchar(3) DEFAULT NULL,
  `manager_id` int(6) DEFAULT NULL,
  `location_id` int(4) DEFAULT NULL,
  PRIMARY KEY (`department_id`),
  KEY `loc_id_fk` (`location_id`),
  CONSTRAINT `loc_id_fk` FOREIGN KEY (`location_id`) REFERENCES `locations` (`location_id`)
) ENGINE=InnoDB AUTO_INCREMENT=271 DEFAULT CHARSET=gb2312;


/*Table structure for table `employees` */
DROP TABLE IF EXISTS `employees`;
CREATE TABLE `employees` (
  `employee_id` int(6) NOT NULL AUTO_INCREMENT,
  `first_name` varchar(20) DEFAULT NULL,
  `last_name` varchar(25) DEFAULT NULL,
  `email` varchar(25) DEFAULT NULL,
  `phone_number` varchar(20) DEFAULT NULL,
  `job_id` varchar(10) DEFAULT NULL,
  `salary` double(10,2) DEFAULT NULL,
  `commission_pct` double(4,2) DEFAULT NULL,
  `manager_id` int(6) DEFAULT NULL,
  `department_id` int(4) DEFAULT NULL,
  `hiredate` datetime DEFAULT NULL,
  PRIMARY KEY (`employee_id`),
  KEY `dept_id_fk` (`department_id`),
  KEY `job_id_fk` (`job_id`),
  CONSTRAINT `dept_id_fk` FOREIGN KEY (`department_id`) REFERENCES `departments` (`department_id`),
  CONSTRAINT `job_id_fk` FOREIGN KEY (`job_id`) REFERENCES `jobs` (`job_id`)
) ENGINE=InnoDB AUTO_INCREMENT=207 DEFAULT CHARSET=gb2312;

/*Table structure for table `jobs` */
DROP TABLE IF EXISTS `jobs`;
CREATE TABLE `jobs` (
  `job_id` varchar(10) NOT NULL,
  `job_title` varchar(35) DEFAULT NULL,
  `min_salary` int(6) DEFAULT NULL,
  `max_salary` int(6) DEFAULT NULL,
  PRIMARY KEY (`job_id`)
) ENGINE=InnoDB DEFAULT CHARSET=gb2312;

/*Table structure for table `locations` */
DROP TABLE IF EXISTS `locations`;
CREATE TABLE `locations` (
  `location_id` int(11) NOT NULL AUTO_INCREMENT,
  `street_address` varchar(40) DEFAULT NULL,
  `postal_code` varchar(12) DEFAULT NULL,
  `city` varchar(30) DEFAULT NULL,
  `state_province` varchar(25) DEFAULT NULL,
  `country_id` varchar(2) DEFAULT NULL,
  PRIMARY KEY (`location_id`)
) ENGINE=InnoDB AUTO_INCREMENT=3201 DEFAULT CHARSET=gb2312;
```



#### 基础查询

```sql
/* 语法 */
SELECT 查询列表 FROM 表名;

# 类似于Java中 :System.out.println(要打印的东西);
```

**特点：**

1. 通过 select 查询完的结果 ，是一个**虚拟的表格**，不是真实存在，临时性的。
2. 查询列表可以是**常量值、可以是表达式、可以是字段、可以是函数**。

##### 1. 一般查询

根据条件过滤原始表的数据，查询到想要的数据，顺序与表的顺序可以不一样。

```mysql
语法：
	SELECT 
	要查询的字段|表达式|常量值|函数
	FROM 
	表
	WHERE 
	条件 ;
```

```sql
# 查询单个字段
SELECT max_salary FROM jobs;
# 查询多个字段
SELECT max_salary, min_salary FROM jobs;
# 查询全部字段 显示顺序与表的定义一样
SELECT * FROM jobs; 
# 可以使用 `字段` 引号来包裹某字段来区分关键字	
SELECT `Jack`;	
```

##### 2. 查询常量值

```sql
SELECT 100;
```

##### 3. 查询表达式

```sql
SELECT 100%98;
```

##### 4. 查询函数

相当于调用函数并得到其返回值。

```mysql
SELECT VERSION();
```

##### 5. 字段起别名 AS

便于理解，如果要查询的字段有重名的情况，使用别名可以区分开来，显示的结果为别名值。也可以省略 AS。

```mysql
mysql> SELECT 100%98 AS RESULT;
mysql> SELECT 100%98 RESULT;
```

```mysql
SELECT last_name AS 姓, first_name AS 名 FROM employees;
```

```mysql
SELECT salary AS `OUT PUT` FROM employees;  # 使用引号区分关键词
```

起别名后输出字段名字变成指定的名称。

##### 6. 查询去重DISTINCT

```mysql
# 案例：查询员工表中所有的部门编号
SELECT department_id FROM employees;
# 会显示全部部门编号，很多重复。加上DISTINCT去重。
SELECT DISTINCT department_id FROM employees;
```

##### 7. 连接查询结果

有时候希望把查询结果组合起来。看下面例子，但是不对！

```mysql
/* 案例：查询员工名和姓连接成一个字段，并显示成  姓名 输出 (此法不对，输出为0) */
SELECT last_name + first_name AS 姓名 FROM employees;  
```

- Java 中的 + 号可当运算符或者连接符
- **MySQL 中的 + 号仅能当运算符**

① 两个操作数都为数值型，执行加法运算。

```mysql
SELECT 100 + 90;  # 190
```

② 其中一个为**字符型**时会视图将字符型数值转换成**数值型**，如果**转换成功**则继续做加法运算。如果转换**失败**则将字符型的**数值转换成 0**。

```mysql
SELECT '123' + 90;  # 213 转换成功
SELECT 'a123' + 90; # 90  转换失败
```

③ 只要其中一方为null，则结果为 null。

```mysql
SELECT null + 90;   # null
```

**那怎么办？？？**

在 MySQL 中**拼接用 ==CONCAT()== 函数**。

```mysql
# 将员工的姓名连接在一起输出
mysql> SELECT CONCAT(last_name, ', ', first_name) AS FullName FROM employees;
+--------------------+
| FullName           |
+--------------------+
| K_ing, Steven      |
| Kochhar, Neena     |
| De Haan, Lex       |
| Hunold, Alexander  |
| Ernst, Bruce       |
+--------------------+
```

查询结果将姓和名两个字段的**结果连接**在了一起。并且使用了“，”进行分隔。

**CONCAT()**  用于连接两个字段。许多数据库会使用空格把一个值填充为列宽，因此连接的结果会出现一些**不必要的空格**，使用 **TRIM()** 可以去除首尾空格。

```sql
SELECT CONCAT(TRIM(col1), '(', TRIM(col2), ')') AS concat_col
FROM mytable;
```

##### 8. 非空查询 ※

**NULL 结果和其他字段==拼接==查询结果就会为 NULL** 。

所以 **IFNULL 函数**可以**判断查询的字段是否为 NULL ，如果为 NULL   则返回**设置的默认值 ，如上述的 0。使用 IFNULL() 函数。

```mysql
# 设置 0 为max_salary的默认值，如果为NULL就设置为0
SELECT IFNULL(max_salary, 0) AS 最大薪水,
max_salary FROM employees;
```

##### 9. 限制行数 LIMIT

LeetCode 中经常使用。

限制返回的**行数**。可以有两个参数，第一个参数为**起始行**，从 ==**0 开始**==；第二个参数为返回的**总行数**。

返回前 5 行：

```sql
SELECT *
FROM mytable
LIMIT 5;  # 默认有第一行
```

```sql
SELECT *
FROM mytable
LIMIT 0, 5;
```

返回第 3 \~ 5 行：

```sql
SELECT *
FROM mytable
LIMIT 2, 3;  # 第三行开始，总共三行
```



#### 条件查询

语法：

```mysql
SELECT 查询列表 FROM 表名 WHERE 筛选条件；
```

判断筛选条件**是否成立**，成立则显示，否则不显示。**先筛选，再查询**显示结果。

##### 1. 按条件运算符筛选

条件运算符表

| 符合 |   释义   | 符号 |        释义        |
| :--: | :------: | :--: | :----------------: |
|  >   |   大于   |  <   |        小于        |
| \>=  | 大于等于 |  <=  |      小于等于      |
|  =   |   等于   |      |                    |
|  !=  |  不等于  |  <>  | 不等于（**推荐**） |

```mysql
# 案例1：查询工资>12000的员工信息
SELECT 
	*
FROM
	employees
WHERE
	salary > 12000;
```

```mysql
# 案例2：查询部门编号不等于90号的员工名和部门编号
SELECT 
	last_name,
	department_id
FROM
	employees
WHERE
	department_id <> 90;
```

##### 2. 按逻辑表达式分类

- 逻辑**运算符**: AND 与 OR 优先处理 AND。

|   运算符   |                         释义                          |
| :--------: | :---------------------------------------------------: |
| AND（&&）  |  两个条件如果**同时**成立，结果为 true，否则为 false  |
| OR（\|\|） | 两个条件只要有**一个**成立，结果为 true，否则为 false |
|  NOT（!）  |                       条件取反                        |

- 逻辑**表达式**：用于连接条件表达式 可以使用**多个**条件表达式

```mysql
# 案例1：查询工资在10000到20000之间的员工名、工资以及奖金
SELECT
	last_name,
	salary,
	commission_pct
FROM
	employees
WHERE
	salary >= 10000 AND salary <= 20000;
```

```mysql
# 案例2：查询部门编号不是在90到110之间，或者工资高于15000的员工信息
SELECT
	*
FROM
	employees
WHERE
	# 进行一波取反操作
	NOT (department_id >= 90 AND  department_id <= 110) OR salary > 15000;
```

##### 3. 模糊查询

###### ① LIKE

一般和**通配符**搭配使用

- **%** : 任意多个字符, 包含 0 个字符。
- **_** : 任意单个字符。
- (特殊情况通配符) 需要**转义**。

```mysql
# 案例1：查询员工名中包含字符a的员工信息
select 
	*
from
	employees
where
	last_name like '%a%';
```

```mysql
# 案例2：查询员工名中第三个字符为 e，第五个字符为 a 的员工名和工资
select
	last_name,
	salary
FROM
	employees
WHERE
	last_name LIKE '__e_a%';
```

```mysql
# 案例3：查询员工名中第二个字符为_的员工名(特殊情况通配符) 需要转义
SELECT
	last_name
FROM
	employees
WHERE
	last_name LIKE '_$_%' ESCAPE '$'; 	# 法1：使用ESCAPE指定转义字符
	last_name LIKE '_\_%';				# 法2：使用默认的转移字符 \
```

###### ② BETWEEN AND

- 使用 BETWEEN AND 可以提高语句的简洁度
- **包含**临界值，相当于大于等于和小于等于

```mysql
# 案例1：查询员工编号在100到120之间的员工信息
SELECT
	*
FROM
	employees
WHERE
	employee_id <= 120 AND employee_id >= 100;
# 简洁版本
SELECT
	*
FROM
	employees
WHERE
	employee_id BETWEEN 120 AND 100;
```

###### ③ IN

含义：判断某字段的值是否属于 **IN 列表**中的某一项
特点：

- 使用 IN 提高语句简洁度
- IN 列表的值类型**必须一致或兼容**
- IN 列表中**不支持**通配符

```mysql
# 案例：查询员工的工种编号是 IT_PROG、AD_VP、AD_PRES 中的一个员工名和工种编号
SELECT
	last_name,
	job_id
FROM
	employees
WHERE
	job_id = 'IT_PROT' OR job_id = 'AD_VP' OR JOB_ID ='AD_PRES';

# 改进版本
SELECT
	last_name,
	job_id
FROM
	employees
WHERE
	job_id IN('IT_PROT', 'AD_VP', 'AD_PRES');	# 有可能出现的值都在小括号内逗号隔开
```

###### ④ IS NULL

- ==**= 或 <> 不能用于判断 NULL值**==
- ==**IS NULL 或 IS NOT NULL可以判断 NULL值**==

但是 IS 不能查询具体的数值。

```mysql
# 案例1：查询没有奖金的员工名和奖金率
SELECT
	last_name,
	commission_pct
FROM
	employees
WHERE
	commission_pct IS NULL;
```

```mysql
# 案例2：查询有奖金的员工名和奖金率
SELECT
	last_name,
	commission_pct
FROM
	employees
WHERE
	commission_pct IS NOT NULL;	
```

> **安全等于 <=>** 

IS NULL : 仅仅可以判断 **NULL 值**，可读性较高，建议使用。
**<=>** : 既可以判断 NULL 值，又可以判断**普通**的数值，可读性较低, 用的较少。

```mysql
# 案例1：查询没有奖金的员工名和奖金率
SELECT
	last_name,
	commission_pct
FROM
	employees
WHERE
	commission_pct <=> NULL;
```

```mysql
# 案例2：查询工资为12000的员工信息
SELECT
	last_name,
	salary
FROM
	employees
WHERE 
	salary <=> 12000;
```

##### 4. 通配符

通配符也是用在过滤语句中，但它只能用于文本字段。

-  **%**  ：匹配 >=0 个任意字符；

-  **\_**    ：匹配 **==1** 个任意字符；

-  **[ ]**  ：可以匹配**集合内**的字符，例如 [ab] 将匹配字符 a 或者 b。用脱字符 ^ 可以对其进行**否定**，也就是不匹配集合内的字符。

使用 **Like** 来进行通配符**匹配**。

```sql
SELECT *
FROM mytable
WHERE col LIKE '[^AB]%'; # 不以 A 和 B 开头的任意文本
```

不要滥用通配符，通配符位于**开头处**匹配会**非常慢**。



#### 排序查询

基本语法

```mysql
SELECT
	要查询的列表
FROM
	表
WHERE 
	条件
ORDER BY 排序的字段|表达式|函数|别名 【ASC|DESC】;
```

特点：
1、**ASC** 代表的是**升序**，可以**省略**。**DESC** 代表的是**降序**。

2、ORDER BY 子句可以支持 **单个字段、别名、表达式、函数、多个字段**。

3、ORDER BY 子句在查询语句的**最后面**，除了 LIMIT 子句。

4、可以按**多个列**进行排序，并且为每个列指定**不同的排序方式**。

```mysql
# Case1: 查询员工信息，要求工资从高到低排序 按单个字段排序
SELECT * FROM employees ORDER BY salary DESC;
```

```mysql
# Case2：查询部门编号大于等于90的员工信息，按入职时间先后排序
SELECT * FROM employees WHERE department_id >= 90 OEDER BY hiredate ASC;
```

```mysql
# Case3：按年薪的高低显示员工的信息和年薪（按表达式排序）奖金commission_pct可能为NULL，防止出错使用IFNULL为其设置默认值0，否则整个表达式都是NULL
SELECT *, salary * 12 * (1 + IFNULL(commission_pct, 0)) 年薪 FROM employees ORDER BY salary * 12 * (1 + IFNULL(commission_pct, 0)) DESC;

SELECT *, salary * 12 * (1 + IFNULL(commission_pct, 0)) 年薪 FROM employees ORDER BY 年薪 DESC;  # 也支持别名
```

```mysql
# Case4: 按姓名的长度显示员工的姓名和工资（按函数排序）
SELECT LENGTH(last_name) 名字长度, last_name, salary FROM employees ORDER BY LENGTH(last_name) DESC;
```

```mysql
# Case5: 查询员工信息，要求先按工资排序，再按员工编号排序（按多个字段排序）
SELECT * FROM employees OREDER BY salary ASC, employee_id DESC; # 整体的salary是升序，如果有几个工资相同的情况，则员工编号按降序排列。
```



#### 常见函数

基本使用。

```mysql
SELECT 函数名（实参列表） [ FROM 表 ];
```

##### 1. 单行函数

###### ① 字符函数

```mysql
CONCAT 拼接
SUBSTR 截取子串
UPPER 转换成大写
LOWER 转换成小写
TRIM 去前后指定的空格和字符
LTRIM 去左边空格
RTRIM 去右边空格
REPLACE 替换
LPAD 左填充
RPAD 右填充
INSTR 返回子串第一次出现的索引
LENGTH 获取字节个数
```

```mysql
# 1.LENGTH 获取参数值的字节个数
SELECT LENGTH('john');			
SELECT LENGTH('张三丰hahaha');
SHOW VARIABLES LIKE '%char%';	# 查看字符集
```

```mysql
# 2.concat 拼接字符串 使用_隔开
SELECT CONCAT(last_name, '_', first_name) 姓名 FROM employees;
```

```mysql
# 3.upper、lower
SELECT UPPER('john');
SELECT LOWER('joHn');
# 示例：将姓变大写，名变小写，然后以'_'隔开拼接
SELECT CONCAT(UPPER(last_name), '_', LOWER(first_name))  姓名 FROM employees;
```

```mysql
# 4.SUBSTR、SUBSTRING
注意：索引从1开始
# 截取从指定索引处后面所有字符 索引从1开始
SELECT SUBSTR('李莫愁爱上了陆展元', 7)  out_put;	 # out_put = ‘陆展元’
# 截取从指定索引处指定字符长度的字符
SELECT SUBSTR('李莫愁爱上了陆展元', 1, 3) out_put;  # out_put = '李莫愁'
# 案例：姓名中首字符大写，其他字符小写然后用_拼接，显示出来
SELECT CONCAT(UPPER(SUBSTR(last_name,1,1)),'_',LOWER(SUBSTR(last_name,2)))  out_put
FROM employees;
```

```mysql
# 5.INSTR 返回子串第一次出现的索引，如果找不到返回 0
SELECT INSTR('杨不殷六侠悔爱上了殷六侠', '殷八侠') AS out_put;  # 7
```

```mysql
# 6.TRIM
# 去掉前后空格
SELECT LENGTH(TRIM('    张翠山    ')) AS out_put;	# 张翠山
# 指定a为去掉的字符
SELECT TRIM('a' FROM 'aaaaaaaaa张aaaaaaaaaaaa翠山aaaaaaaaaaaaaaa')  AS out_put;	# 张aaaaaaaaaaaa翠山
```

```mysql
# 7.LAPD 用指定的字符实现左填充指定长度
SELECT LPAD('殷素素', 10, '*') AS out_put;  # *******殷素素
```

```mysql
# 8.RPAD 用指定的字符实现右填充指定长度
SELECT RPAD('殷素素',12,'ab') AS out_put;   # 殷素素ababababa
```

```mysql
# 9.REPLACE 替换
SELECT REPLACE('周芷若周芷若张无忌爱上了周芷若', '周芷若', '赵敏') AS out_put;	# 赵敏赵敏张无忌爱上了赵敏
```

###### ② 数学函数

```mysql
ROUND 四舍五入
RAND 随机数
FLOOR 向下取整
CEIL 向上取整
MOD 取余
TRUNCATE 截断
```

```mysql
# ROUND 四舍五入
SELECT ROUND(-1.55);		# 2
# 重载 第二位是位数
SELECT ROUND(1.567, 2);		# 1.57
# CEIL 向上取整,返回 >= 该参数的最小整数
SELECT CEIL(-1.02);			# -1
# FLOOR 向下取整，返回 <= 该参数的最大整数
SELECT FLOOR(-9.99); 		# -10
# TRUNCATE 截断 第二个参数是小数位数
SELECT TRUNCATE(1.69999, 1);# 1.6
# MOD取余
/*
mod(a,b) ：  a-a/b*b
mod(-10,-3):-10- (-10)/(-3)*（-3）=-1
*/
SELECT MOD(10, -3);	# -1
SELECT 10 % 3;
```

###### ③ 日期函数

```mysql
NOW() 当前系统日期 + 时间
CURDATE() 当前系统日期
CURTIME() 当前系统时间
STR_TO_DATE() 将字符转换成日期
DTA_FORMAT() 将日期转换成字符
```

```mysql
# NOW 返回当前系统日期+时间
SELECT NOW();		# 2019-07-22 14:45:33
# CURDATE 返回当前系统日期，不包含时间
SELECT CURDATE();	# 2019-07-22
# CURTIME 返回当前时间，不包含日期
SELECT CURTIME();	# 14:46:14
# 可以获取指定的部分，年、月、日、小时、分钟、秒
SELECT YEAR(NOW()) 年;
SELECT YEAR('1998-1-1') 年;	# 年 1998
SELECT  YEAR(hiredate) 年 FROM employees;
SELECT MONTH(NOW()) 月;
SELECT MONTHNAME(NOW()) 月;	# September
```

```mysql
# STR_TO_DATE 将字符通过指定的格式转换成日期
SELECT STR_TO_DATE('1998-3-2', '%Y-%c-%d') AS out_put;

# 查询入职日期为1992-4-3的员工信息 这是默认的日期格式 是可以查询的
SELECT * FROM employees WHERE hiredate = '1992-4-3';

# 非标准格式的字符串查询日期需要转换一下
SELECT * FROM employees WHERE hiredate = STR_TO_DATE('4-3 1992','%c-%d %Y');
```

**字符串转时间格式表格**

| 序号 | 格式符 |        功能         |
| :--: | :----: | :-----------------: |
|  1   |   %Y   |     四位的年份      |
|  2   |   %y   |      2位的年份      |
|  3   |   %m   | 月份（01,02…11,12） |
|  4   |   %c   | 月份（1,2,…11,12）  |
|  5   |   %d   |    日（01,02,…）    |
|  6   |   %H   |  小时（24小时制）   |
|  7   |   %h   |  小时（12小时制）   |
|  8   |   %i   |  分钟（00,01…59）   |
|  9   |   %s   |   秒（00,01,…59）   |

```mysql
# DATE_FORMAT 将日期转换成字符
SELECT DATE_FORMAT(NOW(), '%y年%m月%d日') AS out_put;# 19年07月22日
# 查询有奖金的员工名和入职日期(xx月/xx日 xx年)
SELECT last_name, DATE_FORMAT(hiredate, '%m月/%d日 %y年') 入职日期
FROM employees
WHERE commission_pct IS NOT NULL;
```

![1563779280284](assets/1563779280284-1590927050999.png)

###### ④ 流程控制函数

```mysql
if 处理双分支
case 语句 处理多分支
	 情况1：处理等值判断
	 情况2：处理条件判断
```

```mysql
SELECT IF(10 < 5,'大','小');

SELECT last_name, commission_pct, IF(commission_pct IS NULL, 'No bonus', 'Have bonus') AS Notes FROM employees;
+-------------+----------------+------------+
| last_name   | commission_pct | Notes      |
+-------------+----------------+------------+
| Davies      |           NULL | No bonus   |
| Matos       |           NULL | No bonus   |
| Vargas      |           NULL | No bonus   |
| Russell     |           0.40 | Have bonus |
| Partners    |           0.30 | Have bonus |
+-------------+----------------+------------+
```

```mysql
# MySQL 中的Case结构1 类似于 switch 语句
CASE 要判断的字段或表达式
	WHEN 常量1 THEN 要显示的值1或语句1;
	WHEN 常量2 THEN 要显示的值2或语句2;
	...
	ELSE 要显示的值 n 或语句 n;
END
```

```mysql
/* 案例：查询员工的工资，要求
部门号=30，显示的工资为1.1倍
部门号=40，显示的工资为1.2倍
部门号=50，显示的工资为1.3倍
其他部门，显示的工资为原工资 */
SELECT salary 原始工资,department_id,
CASE department_id
WHEN 30 THEN salary * 1.1
WHEN 40 THEN salary * 1.2
WHEN 50 THEN salary * 1.3
ELSE salary
END AS 新工资
FROM employees;  # 语句中不要放分号
```

```mysql
# MySQL 中的Case结构2 类似于多重 if 区间判断
CASE 
	WHEN 条件1 THEN 要显示的值1或语句1
	WHEN 条件2 THEN 要显示的值2或语句2
	...
	ELSE 要显示的值n或语句n
END
```

```mysql
/* 案例：查询员工的工资的情况
如果工资>20000,显示A级别
如果工资>15000,显示B级别
如果工资>10000，显示C级别
否则，显示D级别 */
SELECT salary,
CASE 
WHEN salary > 20000 THEN 'A'
WHEN salary > 15000 THEN 'B'
WHEN salary > 10000 THEN 'C'
ELSE 'D'
END AS 工资级别
FROM employees;
```

###### ⑤ 其他函数

```mysql
version() 版本
database() 当前库
user() 当前连接用户
```

##### 2. 分组函数

一般用作**统计功能**，又叫统计函数。

LEAVE 类似于 Java 中的 break 语句，跳出所在循环！！！

```mysql
1、以上五个分组函数都忽略 NULL 值，除了COUNT(*)
2、SUM 和 AVG 一般用于处理数值型, MAX、MIN、COUNT 可以处理任何数据类型
3、都可以搭配 DISTINCT 使用，用于统计去重后的结果
4、COUNT 的参数可以支持：字段、*、常量值，一般放 1
   建议使用 COUNT(*)
5、效率：
  MYISAM 存储引擎下，COUNT(*)的效率高
  INNODB 存储引擎下，COUNT(*)和COUNT(1) 的效率差不多，比COUNT(字段)要高一些   
6、和分组函数一同查询的字段要求是 GROUP BY 后的字段  
```

```mysql
# 1、基础查询
SELECT SUM(salary) FROM employees;
SELECT AVG(salary) FROM employees;
SELECT MIN(salary) FROM employees;
SELECT MAX(salary) FROM employees;
SELECT COUNT(salary) FROM employees;
# 可以搭配其他函数使用
SELECT SUM(salary),AVG(salary),MAX(salary),MIN(salary),COUNT(salary) FROM employees;
SELECT SUM(salary),ROUND(AVG(salary),2),MAX(salary),MIN(salary),COUNT(salary) FROM employees;
```

```mysql
# 2、参数支持哪些类型
SELECT SUM(last_name), AVG(last_name) FROM employees;
SELECT SUM(hiredate), AVG(hiredate) FROM employees;
SELECT MAX(last_name), MIN(last_name) FROM employees;
SELECT MAX(hiredate), MIN(hiredate) FROM employees;
SELECT COUNT(commission_pct) FROM employees;
SELECT COUNT(last_name) FROM employees;
```

```mysql
# 3、是否忽略NULL
SELECT SUM(commission_pct), AVG(commission_pct), SUM(commission_pct)/35, SUM(commission_pct)/107 FROM employees;
SELECT MAX(commission_pct), MIN(commission_pct) FROM employees;
SELECT COUNT(commission_pct) FROM employees;
SELECT commission_pct FROM employees;
```

```mysql
# 4、和DISTINCT搭配
SELECT SUM(DISTINCT salary), SUM(salary) FROM employees;
SELECT COUNT(DISTINCT salary), COUNT(salary) FROM employees;
```

```mysql
# 5、CUONT函数的详细介绍
SELECT COUNT(salary) FROM employees;
SELECT COUNT(*) FROM employees;
SELECT COUNT(1) FROM employees;
```

```mysql
# 6、和分组函数一同查询的字段有限制
SELECT AVG(salary), employee_id  FROM employees;
```



#### 分组查询

分组就是把具有相同的数据值的行放在**同一组**中。

可以对同一分组数据使用**汇总函数**进行处理，例如求分组数据的平均值等。

指定的分组字段除了能按该字段进行分组，也会自动按该字段进行**排序**。

**基本语法**

```mysql
SELECT 查询的字段, 分组函数
FROM 表
[WHERE 筛选条件]
GROUP BY 分组的字段
[ORDER BY 子句]
# 查询列表要求是分组函数和GROUP BY后出现的字段
```

**GROUP BY** 自动按分组字段进行排序，**ORDER BY** 也可以按**汇总**字段来进行**排序**。

**==WHERE 过滤行，HAVING 过滤分组，行过滤应当先于分组过滤==**。

**分组规定**

- **GROUP BY 子句出现在 WHERE 子句之后，ORDER BY 子句之前**；
- 除了汇总字段外，SELECT 语句中的**每一字段**都必须在 **GROUP BY** 子句中**给出**；
- **NULL 的行会单独分为一组**；
- 大多数 SQL 实现不支持 GROUP BY 列具有可变长度的数据类型。

**特点：**

1. 和分组函数一同查询的字段必须是 GROUP BY **后**出现的字段。
2. 筛选分为两类：**分组前筛选和分组后筛选**。一般来讲，能用分组前筛选的，尽量使用**分组前筛选**，提高效率。

3. 分组可以按单个字段也可以按多个字段。
4. 可以搭配着排序使用，排序放在整个分组查询最后。
5. **分组函数**做条件肯定是放在 **HAVING** 子句中。
6. HAVING 后可以支持**别名**。
7. **GROUP BY** 子句支持单个字段分组，多个字段分组，多个字段之间用**逗号隔开没有顺序**要求。

|            |       数据源       |          位置           |     关键字     |
| :--------: | :----------------: | :---------------------: | :------------: |
| 分组前删选 |     **原始表**     | GROUP BY 子句的**前面** |   **WHERE**    |
| 分组后筛选 | **分组后**的结果集 | GROUP BY 子句的**后面** | **==HAVING==** |

**简单分组查询**

```mysql
# 案例：查询每个工种的最高工资 
SELECT MAX(salary), job_id FROM employees GROUP BY job_id;
```

```mysql
# 案例：查询每个位置上的部门个数
SELECT COUNT(*), location_id FROM departments GROUP BY location_id;
```

**添加条件查询**

```mysql
# 案例1：查询邮箱中包含a字符的 每个部门的最高工资
SELECT MAX(salary), department_id
FROM employees
WHERE email LIKE '%a%'
GROUP BY department_id;
```

```mysql
# 案例2：查询有奖金的每个领导手下员工的平均工资
SELECT AVG(salary), manager_id
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY manager_id;
```

**分组后筛选**

```mysql
# 案例1：查询哪个部门的员工个数 > 5
# 查询每个部门的员工个数，再筛选结果
SELECT COUNT(*),department_id
FROM employees
GROUP BY department_id
HAVING COUNT(*) > 5;
```

```mysql
# 案例2：每个工种有奖金的员工的最高工资>12000的工种编号和最高工资
SELECT job_id,MAX(salary)
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY job_id
HAVING MAX(salary) > 12000;
```

```mysql
# 案例3：领导编号>102的每个领导手下的最低工资大于5000的领导编号和最低工资
SELECT manager_id, MIN(salary)
FROM employees
WHERE manager_id > 102
GROUP BY manager_id
HAVING MIN(salary) > 5000;
```

**添加排序**

```mysql
# 案例：每个工种有奖金的员工的最高工资>6000的工种编号和最高工资,按最高工资升序
SELECT job_id, MAX(salary) m
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY job_id
HAVING m > 6000
ORDER BY m;

+--------+----------+
| job_id | m        |
+--------+----------+
| SA_REP | 11500.00 |
| SA_MAN | 14000.00 |
+--------+----------+
```

**按多个字段分组**

```mysql
# 案例：查询每个工种每个部门的最低工资,并按最低工资降序
SELECT MIN(salary), job_id, department_id
FROM employees
GROUP BY department_id, job_id
ORDER BY MIN(salary) DESC;
```



#### 连接查询

又称**多表查询**，用于连接多个表，使用 **JOIN** 关键字，并且条件语句是 **ON** 而不是 WHERE。

##### 1. 连接查询分类

###### ① 按标准分

- SQL92标准：仅仅支持**内连接**(即等值连接、非等值连接和自连接)
- SQL99标准【推荐】：支持**内连接 + 外连接**（左外和右外）+ **交叉连接**

###### ② 按功能分

① **交叉连接**：不适用任何匹配条件,生成**笛卡尔积**。

② **内连接**：只连接**匹配**的行。

- **等值连接**：连接条件中**使用等值方法**连接两个表。
- **非等值连接**：连接条件中**使用非等值方法**连接两个表。
- **自连接**：自己和自己做**笛卡尔积**。

③ **外连接**：

- **左外连接**：**左表(A)**的记录将会**全部**表示出来，而**右表**(B)只会**显示符合搜索条件**的记录。右表记录不足的地方均为**NULL**。
- **右外连接**：与左外连接**相反**。
- **全外连接**：实现左外和右外连接的效果。

##### 2. SQL92标准语法

SQL92标准仅仅支持**内连接**(即等值连接、非等值连接和自连接)。

###### ① 等值连接(SQL92标准)（不太建议）

等值的含义就是连接条件中**使用等于号**(=)运算符比较被连接列的列值。

1. 写好 WHERE 中的**连接条件**。
2. 等值连接的结果 = 多个表的**交集**。
3. n 表连接，至少需要 n-1 个连接条件。
4. 多个表不分主次，没有顺序要求。
5. 一般为表起别名，提高阅读性和性能。

```mysql
# 案例1：查询女神名和对应的男神名
SELECT NAME, boyName 
FROM boys, beauty
WHERE beauty.boyfriend_id = boys.id; 	# 使用表名.字段格式
```

```mysql
# 案例2：查询员工名和对应的部门名
SELECT last_name, department_name
FROM employees, departments
WHERE employees.`department_id` = departments.`department_id`;
```

**为表起别名**

```mysql
/*
1.提高语句的简洁度
2.区分多个重名的字段
注意：如果为表起了别名，则查询的字段就不能使用原来的表名去限定
*/
# 查询员工名、工种号、工种名
SELECT e.last_name, e.job_id, j.job_title
FROM employees AS e, jobs j
WHERE e.`job_id` = j.`job_id`;
```

**添加筛选条件**

```mysql
# 案例：查询有奖金的员工名、部门名
SELECT last_name, department_name, commission_pct
FROM employees e, departments d		# 起别名
WHERE e.`department_id` = d.`department_id`
AND e.`commission_pct` IS NOT NULL;	# 筛选条件
```

```mysql
# 案例2：查询城市名中第二个字符为o的部门名和城市名
SELECT department_name, city
FROM departments d, locations l
WHERE d.`location_id` = l.`location_id`
AND city LIKE '_o%';	# 筛选条件
```

**添加分组**

```mysql
# 案例1：查询每个城市的部门个数
SELECT COUNT(*) number, city
FROM departments d, locations l
WHERE d.`location_id` = l.`location_id`
GROUP BY city;		# 添加分组
+--------+---------------------+
| number | city                |
+--------+---------------------+
|      1 | London              |
|      1 | Munich              |
|      1 | Oxford              |
|     21 | Seattle             |
|      1 | South San Francisco |
|      1 | Southlake           |
|      1 | Toronto             |
+--------+---------------------+
```

```mysql
# 案例2：查询有奖金的每个部门的部门名和部门的领导编号和该部门的最低工资
SELECT department_name, d.`manager_id`, MIN(salary)
FROM departments d, employees e
WHERE d.`department_id` = e.`department_id`
AND commission_pct IS NOT NULL  # 筛选条件
GROUP BY department_name, d.`manager_id`;	# 添加分组
+-----------------+------------+-------------+
| department_name | manager_id | MIN(salary) |
+-----------------+------------+-------------+
| Sal             |        145 |     6100.00 |
+-----------------+------------+-------------+
```

**添加排序**

```mysql
# 案例：查询每个工种的工种名和员工的个数，并且按员工个数降序
SELECT job_title, COUNT(*)
FROM employees e, jobs j
WHERE e.`job_id` = j.`job_id`
GROUP BY job_title
ORDER BY COUNT(*) DESC;
+---------------------------------+----------+
| job_title                       | COUNT(*) |
+---------------------------------+----------+
| Sales Representative            |       30 |
| Shipping Clerk                  |       20 |
| Stock Clerk                     |       20 |
| Purchasing Clerk                |        5 |
| Stock Manager                   |        5 |
| Accountant                      |        5 |
+---------------------------------+----------+
```

**实现三表连接**

阿里开发手册建议别三表查询。。。

```mysql
# 案例：查询员工名、部门名和所在的城市
SELECT last_name, department_name, city
FROM employees e, departments d,locations l
WHERE e.`department_id` = d.`department_id`
AND d.`location_id` = l.`location_id`
AND city LIKE 's%'
ORDER BY department_name DESC;
```

###### ② 非等值连接(SQL92标准)

非等值的含义就是连接条件中**不使用等于号**(=)运算符比较被连接列的列值。

```mysql
# 案例1：查询员工的工资和工资级别
SELECT salary, grade_level
FROM employees e, job_grades g
WHERE salary BETWEEN g.`lowest_sal` AND g.`highest_sal`
AND g.`grade_level` = 'A';
```

###### ③ 自连接(SQL92标准)

自连接可以看成**内连接**的一种，只是连接的表是自身而已。就是自己和自己做**笛卡尔积**。

一张员工表，包含员工**姓名**和员工**所属部门**，要找出与 Jim 处在**同一部门**的所有**员工姓名**。

**子查询版本**

```sql
# 先查询 Jim 所在的部门，然后用 WHERE 筛选
SELECT name
FROM employee
WHERE department = (
      SELECT department
      FROM employee
      WHERE name = "Jim");
```

**自连接版本**

```sql
SELECT e1.name
FROM employee AS e1 INNER JOIN employee AS e2
ON e1.department = e2.department
      AND e2.name = "Jim";
```

```mysql
# 案例：查询 员工名和上级的名称
SELECT e.employee_id, e.last_name, m.employee_id, m.last_name
FROM employees e, employees m
WHERE e.`manager_id` = m.`employee_id`;
```

##### 3. SQL99语法（重要）

SQL99标准支持**内连接**(即等值连接、非等值连接和自连接)、外连接和交叉连接。

通过 **JOIN** 关键字实现连接。

```mysql
SELECT 字段，...
FROM 表1
【INNER|INNER INNER|INNER OUTER|OUTER】OUTER 表2 ON 连接条件
【INNER|INNER INNER|INNER OUTER|OUTER】OUTER 表3 ON 连接条件
【WHERE 筛选条件】
【GROUP BY 分组字段】
【HAVING 分组后的筛选条件】
【ORDER BY 排序的字段或表达式】
```

好处：语句上，**连接条件和筛选条件实现了分离**，简洁明了！

###### ① 内连接

**语法**

```mysql
SELECT 查询列表
FROM 表1 别名
INNER JOIN 表2 别名
ON 连接条件;
```

① **等值连接**

1. 添加排序、分组、筛选
2. INNER 可以省略
3. 筛选条件放在 WHERE 后面，连接条件放在 ON 后面，提高分离性，便于阅读
4. INNER  JOIN 连接和sql92语法中的等值连接效果是一样的，都是查询多表的交集

```mysql
# 案例1.查询员工名、部门名
SELECT last_name,department_name
FROM departments d
JOIN employees e
ON e.`department_id` = d.`department_id`;	# 连接条件
```

```mysql
# 案例2.查询名字中包含e的员工名和工种名（添加筛选）
SELECT last_name, job_title
FROM employees e
INNER JOIN jobs j
ON e.`job_id`=  j.`job_id`
WHERE e.`last_name` LIKE '%e%';
```

```mysql
# 案例3.查询部门个数 >3 的城市名和部门个数（添加分组+筛选）
#1.查询每个城市的部门个数
#2.在1结果上筛选满足条件的
SELECT city, COUNT(*) 部门个数
FROM departments d
INNER JOIN locations l
ON d.`location_id` = l.`location_id`
GROUP BY city			# 先分组
HAVING COUNT(*) > 3;	# 在之前的结果之上进行删选
```

```mysql
# 案例4.查询哪个部门的员工个数 >3 的部门名和员工个数，并按个数降序（添加排序）
# 1.查询每个部门的员工个数
SELECT COUNT(*), department_name
FROM employees e
INNER JOIN departments d
ON e.`department_id` = d.`department_id`
GROUP BY department_name

# 2.在1结果上筛选员工个数 >3 的记录，并排序
SELECT COUNT(*) 个数,department_name
FROM employees e
INNER JOIN departments d
ON e.`department_id` = d.`department_id`
GROUP BY department_name
HAVING COUNT(*) > 3
ORDER BY COUNT(*) DESC;
```

```mysql
# 案例5.查询员工名、部门名、工种名，并按部门名降序（添加三表连接）
SELECT last_name, department_name, job_title
FROM employees e
INNER JOIN departments d ON e.`department_id` = d.`department_id`
INNER JOIN jobs j ON e.`job_id` = j.`job_id`
ORDER BY department_name DESC;
```

② **非等值连接**

也是使用 JOIN 关键字。

```mysql
# 查询员工的工资级别
SELECT salary, grade_level
FROM employees e
JOIN job_grades g
ON e.`salary` BETWEEN g.`lowest_sal` AND g.`highest_sal`;
```

```mysql
# 查询工资级别的个数 >20 的个数，并且按工资级别降序
SELECT COUNT(*), grade_level
FROM employees e
JOIN job_grades g
ON e.`salary` BETWEEN g.`lowest_sal` AND g.`highest_sal`
GROUP BY grade_level
HAVING COUNT(*) > 20
ORDER BY grade_level DESC;
```

③ **自连接**

```mysql
# 查询员工的名字、上级的名字
SELECT e.last_name, m.last_name
FROM employees e
JOIN employees m
ON e.`manager_id`= m.`employee_id`;
```

```mysql
# 查询姓名中包含字符k的员工的名字、上级的名字
SELECT e.last_name,m.last_name
FROM employees e
JOIN employees m
ON e.`manager_id`= m.`employee_id`
WHERE e.`last_name` LIKE '%k%';		# 添加筛选条件
```

###### ② 外连接 

应用场景：用于查询一个表**中有**，另一个表**没有**的记录。主要分为左外连接、右外连接、全外连接。

 特点：

1. 外连接的查询结果为**主表**中的**所有记录**。如果从表中**有和它匹配**的，则显示**匹配的值**。如果从表中**没有和它匹配**的，则显示**NULL**。外连接查询结果 = **内连接结果 + 主表中有而从表没有的记录**。
2. **左外**连接，LEFT JOIN **左边的是主表**
   **右外**连接，RIGHT JOIN **右边的是主表**
3. 左外和右外交换两个表的**顺序**，可以实现同样的效果 
4. **全外连接** = 内连接的结果 + 表1中有但表2没有的 + 表2中有但表1没有的

```mysql
# 案例1：查询哪个部门没有员工
# 左外
SELECT d.*, e.employee_id
FROM departments d
LEFT OUTER JOIN employees e
ON d.`department_id` = e.`department_id`
WHERE e.`employee_id` IS NULL;	# 筛选条件
+---------------+-----------------+------------+-------------+-------------+
| department_id | department_name | manager_id | location_id | employee_id |
+---------------+-----------------+------------+-------------+-------------+
|           120 | Tre             |       NULL |        1700 |        NULL |
|           130 | Cor             |       NULL |        1700 |        NULL |
|           140 | Con             |       NULL |        1700 |        NULL |
|           150 | Sha             |       NULL |        1700 |        NULL |
+---------------+-----------------+------------+-------------+-------------+

# 右外
SELECT d.*, e.employee_id
FROM employees e
RIGHT OUTER JOIN departments d
ON d.`department_id` = e.`department_id`
WHERE e.`employee_id` IS NULL;
```

###### ③ 交叉连接

产生笛卡尔乘积：如果连接条件省略或无效则会出现，所有行都会互相连接。

现象：表1 有 m 行，表 2 有 n 行，结果 = **m * n** 行。

```mysql
select * from person ,dept;
```

用处不大。解决办法：添加上连接条件。

###### ④ 自然连接

自然连接是把**同名列**通过**等值测试**连接起来的，同名列可以**有多个**。

内连接和自然连接的区别：内连接**提供**连接的列，而自然连接**自动连接**所有**同名列**。

```sql
SELECT A.value, B.value
FROM tablea AS A NATURAL JOIN tableb AS B;
```

##### 4. INNER JOIN、LEFT JOIN、RIGHT JOIN、FULL JOIN总结

SQL 中的连接查询有四种方式，它们之间其实并没有太大区别，仅仅是查询出来的结果有所不同。

- inner join（内连接）
- left join（左连接）
- right join（右连接）
- full join（全连接）

例如我们有两张表： 

![20150603222647340](assets/20150603222647340-2.png)

**Orders** 表通过外键 **Id_P** 和 **Persons** 表进行关联。

###### 1. inner join

**在两张表进行连接查询时，只保留两张表中完全匹配的结果集。**

我们使用 inner join 对两张表进行连接查询，sql如下：

```mysql
SELECT Persons.LastName, Persons.FirstName, Orders.OrderNo
FROM Persons
INNER JOIN Orders
ON Persons.Id_P=Orders.Id_P
ORDER BY Persons.LastName
```

查询结果集： 

![20150603222827804](assets/20150603222827804.png)

此种连接方式 Orders 表中 Id_P 字段在 Persons 表中找不到匹配的，则不会列出来。



###### 2. left join

**在两张表进行连接查询时，会返回左表所有的行，即使在右表中没有匹配的记录**

我们使用 left join 对两张表进行连接查询，sql如下：

```mysql
SELECT Persons.LastName, Persons.FirstName, Orders.OrderNo
FROM Persons
LEFT JOIN Orders
ON Persons.Id_P=Orders.Id_P
ORDER BY Persons.LastName
```

查询结果如下： 

![20150603223638605](assets/20150603223638605.png)

可以看到，左表（Persons表）中 LastName 为 Bush 的行的 Id_P 字段在右表（Orders表）中没有匹配，但查询结果仍然保留该行。



###### 3. right join

**在两张表进行连接查询时，会返回右表所有的行，即使在左表中没有匹配的记录**

我们使用right join对两张表进行连接查询，sql如下：

```mysql
SELECT Persons.LastName, Persons.FirstName, Orders.OrderNo
FROM Persons
RIGHT JOIN Orders
ON Persons.Id_P=Orders.Id_P
ORDER BY Persons.LastName
```

 查询结果如下：

![20150603224352995](assets/20150603224352995.png)

Orders 表中最后一条记录 Id_P 字段值为 65，在左表中没有记录与之匹配，但依然保留。

###### 4. full join

**在两张表进行连接查询时，返回左表和右表中所有没有匹配的行。**

我们使用 full join 对两张表进行连接查询，sql如下：

```mysql
SELECT Persons.LastName, Persons.FirstName, Orders.OrderNo
FROM Persons
FULL JOIN Orders
ON Persons.Id_P=Orders.Id_P
ORDER BY Persons.LastName
```

查询结果如下： 

![20150603224604636](assets/20150603224604636.png)

查询结果是 left join 和 right join 的并集。

这些连接查询的区别也仅此而已。



参考来源：

- [SQL中INNER JOIN、LEFT JOIN、RIGHT JOIN、FULL JOIN区别 - 杨浪 - 博客园](https://www.cnblogs.com/yanglang/p/8780722.html)





#### 子查询

含义：

一条查询语句中又嵌套了另一条**完整的 SELECT 语句**，其中**被嵌套**的SELECT语句，称为子查询或**内查询**。

**分类：**

按结果集的**行列数**不同：

- 标量子查询（结果集只有**一行一列**）※
- 列子查询（结果集只有**一列多行**）※	
- 行子查询（结果集有**一行多列**）
- 表子查询（结果集一般为**多行多列**）

子查询可以放在不同的**位置**：

- WHERE 或 HAVING 后面
- SELECT 后面
- FROM 后面
- EXISTS 后面

##### 1. WHERE 或 HAVING 后面

特点：

1、子查询都放在**小括号**内
2、子查询可以放在 FROM 后面、SELECT 后面、WHERE 后面、HAVING 后面，但一般放在**条件的右侧**
3、子查询**优先**于主查询执行，主查询使用了子查询的执行结果
4、子查询根据查询结果的**行数不同**分为以下两类：
**① 单行子查询**
	结果集只有一行
	一般搭配单行操作符使用：>  <  =  <>  >=  <= 
	非法使用子查询的情况：
		a、子查询的结果为一组值
		b、子查询的结果为空
**② 多行子查询**
	结果集有多行
	一般搭配多行操作符使用：ANY、ALL、IN、NOT IN
	IN：属于子查询结果中的任意一个就行
	ANY 和 ALL 往往可以用其他查询代替

###### ① 标量子查询★

子查询的结果为**一行一列**。

```mysql
# 案例1：谁的工资比 Abel 高?
# 1.查询Abel的工资
SELECT salary
FROM employees
WHERE last_name = 'Abel'
# 2.查询员工的信息，满足 salary > 1中结果
SELECT *
FROM employees
WHERE salary>(
	SELECT salary
	FROM employees
	WHERE last_name = 'Abel'
);
```

```mysql
# 案例2：返回job_id与141号员工相同，salary比143号员工多的员工姓名，job_id 和工资
# 1.查询141号员工的job_id
SELECT job_id
FROM employees
WHERE employee_id = 141

# 2.查询143号员工的salary
SELECT salary
FROM employees
WHERE employee_id = 143

# 3.查询员工的姓名，job_id 和工资，要求job_id = 1并且salary > 2中
SELECT last_name,job_id,salary
FROM employees
WHERE job_id = (
	SELECT job_id
	FROM employees
	WHERE employee_id = 141
) AND salary>(
	SELECT salary
	FROM employees
	WHERE employee_id = 143
);
```

```mysql
# 案例3：返回公司工资最少的员工的last_name,job_id和salary
# 1.查询公司的 最低工资
SELECT MIN(salary)
FROM employees

# 2.查询last_name, job_id和salary，要求salary = 1中
SELECT last_name,job_id,salary
FROM employees
WHERE salary=(
	SELECT MIN(salary)
	FROM employees
);
```

```mysql
# 案例4：查询最低工资大于50号部门最低工资的部门id和其最低工资
# 1.查询50号部门的最低工资
SELECT  MIN(salary)
FROM employees
WHERE department_id = 50
# 2.查询每个部门的最低工资
SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
# 3.在2基础上筛选，满足min(salary) > 1结果
SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
HAVING MIN(salary)>(
	SELECT  MIN(salary)
	FROM employees
	WHERE department_id = 50
);
```

```mysql
# 非法使用标量子查询
SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
HAVING MIN(salary)>(
	SELECT  salary
	FROM employees
	WHERE department_id = 250
);
```

###### ② 列子查询★

子查询结果为**一列多行**。

```mysql
# 案例1：返回location_id是1400或1700的部门中的所有员工姓名
# 1.查询location_id是1400或1700的部门编号
SELECT DISTINCT department_id
FROM departments
WHERE location_id IN(1400,1700)
# 2.查询员工姓名，要求部门号是①列表中的某一个
SELECT last_name
FROM employees
WHERE department_id  <>ALL(
	SELECT DISTINCT department_id
	FROM departments
	WHERE location_id IN(1400,1700)
);
```

```mysql
# 案例2：返回其它工种中比job_id为‘IT_PROG’工种任一工资低的员工的员工号、姓名、job_id 以及salary
# 1.查询job_id为‘IT_PROG’部门任一工资
SELECT DISTINCT salary
FROM employees
WHERE job_id = 'IT_PROG'
# 2.查询员工号、姓名、job_id 以及salary，salary<(1)的任意一个
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<ANY(
	SELECT DISTINCT salary
	FROM employees
	WHERE job_id = 'IT_PROG'
) AND job_id<>'IT_PROG';

#或
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<(
	SELECT MAX(salary)
	FROM employees
	WHERE job_id = 'IT_PROG'
) AND job_id<>'IT_PROG';
```

```mysql
# 案例3：返回其它部门中比job_id为‘IT_PROG’部门所有工资都低的员工   的员工号、姓名、job_id 以及salary
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<ALL(
	SELECT DISTINCT salary
	FROM employees
	WHERE job_id = 'IT_PROG'
) AND job_id<>'IT_PROG';

#或
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<(
	SELECT MIN( salary)
	FROM employees
	WHERE job_id = 'IT_PROG'
) AND job_id<>'IT_PROG';
```

###### ③ 行子查询

子查询结果集**一行多列**或**多行多列**。

```mysql
# 案例：查询员工编号最小并且工资最高的员工信息
SELECT * 
FROM employees
WHERE (employee_id,salary)=(
	SELECT MIN(employee_id),MAX(salary)
	FROM employees
);
# 1.查询最小的员工编号
SELECT MIN(employee_id)
FROM employees

# 2.查询最高工资
SELECT MAX(salary)
FROM employees

# 3.查询员工信息
SELECT *
FROM employees
WHERE employee_id=(
	SELECT MIN(employee_id)
	FROM employees
)AND salary=(
	SELECT MAX(salary)
	FROM employees
);
```

##### 2. SELECT后面

仅仅支持**标量子查询**。

```mysql
# 案例1：查询每个部门的员工个数
SELECT d.*, (
	SELECT COUNT(*)
	FROM employees e
	WHERE e.department_id = d.`department_id`
 ) 个数
 FROM departments d;
```

```mysql
# 案例2：查询员工号=102的部门名
SELECT (
	SELECT department_name,e.department_id
	FROM departments d
	INNER JOIN employees e
	ON d.department_id=e.department_id
	WHERE e.employee_id=102
) 部门名;
```

##### 3. FROM 后面

将子查询结果充当**一张表**，要求**必须起别名**。

```mysql
# 案例：查询每个部门的平均工资的工资等级
# 1.查询每个部门的平均工资
SELECT AVG(salary), department_id
FROM employees
GROUP BY department_id

SELECT * FROM job_grades;
# 2.连接1的结果集和job_grades表，筛选条件平均工资 between lowest_sal and highest_sal
SELECT ag_dep.*,g.`grade_level`
FROM (
	SELECT AVG(salary) ag, department_id
	FROM employees
	GROUP BY department_id
) ag_dep
INNER JOIN job_grades g
ON ag_dep.ag BETWEEN lowest_sal AND highest_sal;
```

##### 4. EXISTS 后面（相关子查询）

语法：exists (完整的查询语句)
结果：1 或 0

```mysql
# 案例1：查询有员工的部门名
# IN
SELECT department_name
FROM departments d
WHERE d.`department_id` IN(
	SELECT department_id
	FROM employees
)

# EXISTS
SELECT department_name
FROM departments d
WHERE EXISTS(
	SELECT *
	FROM employees e
	WHERE d.`department_id`=e.`department_id`
);
```

```mysql
# 案例2：查询没有女朋友的男神信息
# IN
SELECT bo.*
FROM boys bo
WHERE bo.id NOT IN(
	SELECT boyfriend_id
	FROM beauty
)

# EXISTS
SELECT bo.*
FROM boys bo
WHERE NOT EXISTS(
	SELECT boyfriend_id
	FROM beauty b
	WHERE bo.`id` = b.`boyfriend_id`
);
```



#### 分页查询

结果很多可以使用分页查询。如果有 100000 条数据，不是说一次全部查出来，而是**分页**去提交请求查询少量数据。

语法：

```mysql
SELECT 字段|表达式,...
FROM 表
【WHERE 条件】
【GROUP BY 分组字段】
【HAVING 条件】
【ORDER BY 排序的字段】
LIMIT 【要显示条目的起始索引（起始索引从0开始），要显示的条目个数】;
```

特点：

```mysql
1.起始条目索引从0开始
2.LIMIT子句放在查询语句的最后
3.公式：SELECT * FROM 表 LIMIT （page - 1）* sizePerPage, sizePerPage
此处:
sizePerPage 每页显示条目数
page 要显示的页数
```

```mysql
# 案例1：查询前五条员工信息
SELECT * FROM  employees LIMIT 0, 5;
SELECT * FROM  employees LIMIT 5;

# 案例2：查询第11条——第25条
SELECT * FROM  employees LIMIT 10,15;

# 案例3：有奖金的员工信息，并且工资较高的前10名显示出来
SELECT 
    * 
FROM
    employees 
WHERE commission_pct IS NOT NULL 
ORDER BY salary DESC 
LIMIT 10;
```



#### 组合查询

将**多条**查询语句的结果**合并成一个结果**。

应用场景：
要查询的结果来自于**多个表**，且多个表**没有直接的连接关系**，但查询的信息一致时。

语法：

```mysql
SELECT 字段|常量|表达式|函数 【FROM 表】 【WHERE 条件】 UNION 【ALL】
SELECT 字段|常量|表达式|函数 【FROM 表】 【WHERE 条件】 UNION 【ALL】
SELECT 字段|常量|表达式|函数 【FROM 表】 【WHERE 条件】 UNION 【ALL】
.....
SELECT 字段|常量|表达式|函数 【FROM 表】 【WHERE 条件】
```

特点：

- 多条查询语句的查询的**列数**必须是**一致**的
- 多条查询语句的查询的**列的类型几乎相同**
- **UNION** 代表**去重**，**UNION ALL**代表**不去重**
- 只能包含**一个 OEDER BY** 子句，并且必须位于语句的最后。

```mysql
# 案例：查询部门编号>90或邮箱包含a的员工信息
SELECT * FROM employees WHERE email LIKE '%a%' OR department_id > 90;

SELECT * FROM employees WHERE email LIKE '%a%'
UNION
SELECT * FROM employees WHERE department_id > 90;
```

```mysql
# 案例：查询中国用户中男性的信息以及外国用户中年男性的用户信息
SELECT id, cname FROM t_ca WHERE csex='男'
UNION ALL
SELECT t_id, tname FROM t_ua WHERE tGender = 'male';
```



### 四、DML数据操作语言

#### 插入

##### 1. 语法1

```mysql
INSERT INTO 表名(字段名，...) VALUES(值1，...);
```

特点：

- 字段类型和值类型一致或兼容，而且**一一对应**
- 可以为空的字段，可以**不用**插入值，或用**NULL**填充
- **不可以为空**的字段，**必须**插入值
- 字段个数和值的个数必须**一致**
- 字段可以省略，但默认所有字段，并且顺序和表中的存储**顺序一致**

```mysql
# 1.插入的值的类型要与列的类型一致或兼容 没有的写为NULL
INSERT INTO beauty(id, NAME, sex, borndate, phone, photo, boyfriend_id)
VALUES(13, '唐艺昕', '女', '1990-4-23', '1898888888', NULL, 2);
```

```mysql
# 2.不可以为NULL的列必须插入值。可以为NULL的列如何插入值？
# 方式一：
INSERT INTO beauty(id, NAME, sex, borndate, phone, photo, boyfriend_id)
VALUES(13, '唐艺昕', '女', '1990-4-23', '1898888888', NULL, 2);

# 方式二：数据库会加默认值或者NULL填充未指定的字段
INSERT INTO beauty(id, NAME, sex, phone)
VALUES(15, '娜扎', '女', '1388888888');
```

```mysql
# 3.列的顺序可以调换，一一对应即可
INSERT INTO beauty(NAME, sex, id, phone)
VALUES('蒋欣', '女', 16, '110');
```

```mysql
# 4.列数和值的个数必须一致
INSERT INTO beauty(NAME, sex, id, phone)
VALUES('关晓彤', '女', 17, '110');
```

```mysql
# 5.可以省略列名，默认所有列，而且列的顺序和表中列的顺序一致
INSERT INTO beauty
VALUES(18, '张飞', '男', NULL, '119', NULL, NULL);
```

##### 2. 语法2(用得少)

```mysql
INSERT INTO 表名 SET 列名 = 值, 列名 = 值, ...
```

```mysql
INSERT INTO beauty SET id=19,NAME='刘涛',phone='999';
```

两种语法PK：

```mysql
# 1、方式一支持插入多行,方式二不支持
INSERT INTO beauty
VALUES(23, '唐艺昕1', '女', '1990-4-23', '1898888888', NULL, 2)
,(24, '唐艺昕2', '女', '1990-4-23', '1898888888', NULL, 2)
,(25, '唐艺昕3', '女', '1990-4-23', '1898888888', NULL, 2);

# 2、方式一支持子查询，方式二不支持
INSERT INTO beauty(id, NAME, phone)
SELECT 26, '宋茜', '11809866';  # 后面是一个完整的语句，相当于一个子查询

INSERT INTO beauty(id, NAME, phone)
SELECT id, boyname, '1234567'
FROM boys WHERE id < 3;
```



#### 修改

##### 1. 修改**单表**语法

```mysql
UPDATE 表名 SET 字段 = 新值, 字段 = 新值
【WHERE 条件】
```

一般都要加 **WHERE** 条件，否则会把所有的都修改了。

```mysql
# 案例1：修改beauty表中姓唐的女神的电话为13899888899
UPDATE beauty SET phone = '13899888899'
WHERE NAME LIKE '唐%';
```

```mysql
# 案例2：修改boys表中id好为2的名称为张飞，魅力值 10
UPDATE boys SET boyname= '张飞', usercp = 10
WHERE id = 2;
```

##### 2. 修改**多表**语法

SQL92语法：

```mysql
UPDATE 表1 别名1, 表2 别名2
SET 字段 = 新值，字段 = 新值
WHERE 连接条件
AND 筛选条件
```

SQL99语法：

```mysql
UPDATE 表1 别名
INNER|LEFT|RIGHT JOIN 表2 别名
ON 连接条件
SET 列 = 值, ...
WHERE 筛选条件;
```

```mysql
# 案例 1：修改张无忌的女朋友的手机号为114
UPDATE boys bo
INNER JOIN beauty b ON bo.`id` = b.`boyfriend_id`
SET b.`phone` = '119', bo.`userCP` = 1000
WHERE bo.`boyName` = '张无忌';
```

```mysql
# 案例2：修改没有男朋友的女神的男朋友编号都为2号
UPDATE boys bo
RIGHT JOIN beauty b ON bo.`id` = b.`boyfriend_id`
SET b.`boyfriend_id` = 2
WHERE bo.`id` IS NULL;
```



#### 删除

##### 1. DELETE语句

###### ① 单表的删除 ★

```mysql
DELETE FROM 表名 【WHERE筛选条件】;
```

一般需要加 **WHERE** 条件，否则**全删**了。使用更新和删除操作时一定要用 **WHERE** 子句，不然会把**整张表的数据都破坏**。可以先用 SELECT 语句进行测试，防止错误删除。

```mysql
# 1.单表的删除
# 案例：删除手机号以9结尾的女神信息
DELETE FROM beauty WHERE phone LIKE '%9';
```

######  ② 多表的删除

需要用到**连接**

SQL92语法：

```mysql
DELETE 别名1，别名2
FROM 表1 别名1，表2 别名2
WHERE 连接条件
AND 筛选条件;
```

SQL99语法：

```mysql
DELETE 表1的别名, 表2的别名
FROM 表1 别名
INNER|LEFT|RIGHT JOIN 表2 别名 ON 连接条件
WHERE 筛选条件;
```

```mysql
# 案例：删除张无忌的女朋友的信息
DELETE b
FROM beauty b
INNER JOIN boys bo ON b.`boyfriend_id` = bo.`id`
WHERE bo.`boyName` = '张无忌';
```

```mysql
# 案例：删除黄晓明的信息以及他女朋友的信息
DELETE b,bo
FROM beauty b
INNER JOIN boys bo ON b.`boyfriend_id` = bo.`id`
WHERE bo.`boyName` = '黄晓明';
```

##### 2. TRUNCATE语句

相当于**清空数据、全删**。

```mysql
TRUNCATE TABLE 表名
```

```mysql
# 删除boys表的内容
TRUNCATE TABLE boys ;
```

DELETE 与 TRUNCATE 两种方式的区别

- TRUNCATE 不能加 WHERE 条件，而 DELETE 可以加 WHERE 条件。
- TRUNCATE 的效率高一丢丢。
- TRUNCATE 删除带自增长的列的表后，如果再插入数据，数据**从1开始。**
- DELETE 删除带自增长列的表后，如果再插入数据，数据从上一次的**断点**处开始。
- TRUNCATE 删除**不能回滚**，DELETE 删除**可以回滚。**



### 五、其他

#### 字符集

基本术语：

- **字符集**为**字母和符号**的集合；
- **编码**为某个字符集成员的内部表示；
- **校对字符**指定**如何比较**，主要用于**排序和分组**。

除了给表指定字符集和校对外，也可以给列指定：

```sql
CREATE TABLE mytable
(col VARCHAR(10) CHARACTER SET latin COLLATE latin1_general_ci)
DEFAULT CHARACTER SET hebrew COLLATE hebrew_general_ci;
```

可以在**排序、分组时指定校对**：

```sql
SELECT *
FROM mytable
ORDER BY col COLLATE latin1_general_ci;
```



#### 权限管理

MySQL 的**账户信息**保存在 **mysql** 这个数据库中。

```sql
USE mysql;
SELECT user FROM user;
```

**创建账户** 

新创建的账户**没有任何**权限。

```sql
CREATE USER myuser IDENTIFIED BY 'mypassword';
```

**修改账户名** 

```sql
RENAME myuser TO newuser;
```

**删除账户** 

```sql
DROP USER myuser;
```

**查看权限★** 

```sql
SHOW GRANTS FOR myuser;
```

**授予权限★** 

账户用 **username@host** 的形式定义，**username@%** 使用的是**默认主机名**。

```sql
GRANT SELECT, INSERT ON mydatabase.* TO myuser;
```

**删除权限** 

**GRANT** 和 **REVOKE** 可在几个层次上控制访问权限：

- 整个服务器，使用 GRANT ALL 和 REVOKE ALL；
- 整个数据库，使用 **ON database.\*；**
- 特定的表，使用 ON database.table；
- 特定的列；
- 特定的**存储过程**。

```sql
REVOKE SELECT, INSERT ON mydatabase.* FROM myuser;
```

**更改密码** 

必须使用 Password() 函数进行**加密**。

```sql
SET PASSWROD FOR myuser = Password('new_password');
```

