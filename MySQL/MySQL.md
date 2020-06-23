# MySQL

## 一、概述

- 数据持久化

- 管理，便于查询

  DB： database 存储数据的仓库（容器）

  DBMS: database management system 数据库管理系统

  主要有:

  | DBMS       | 公司   |
  | ---------- | ------ |
  | MySQL      | oracle |
  | ORACLE     | oracle |
  | DB2        | IBM    |
  | SQL server | 微软   |

  DBMS分为两类：

  1. 基于共享文件系统，如access
  2. 基于c-s架构，需要安装client,server端

  SQL： structure query language

  特点：

  1. 几乎所有DBMS都支持SQL，也有特有的“方言“
  2. 简单易学
  3. 强有力，可以做复杂、高级的操作

  与JAVA类比

  1. 将数据存在表中，将表存在库中
  2. 一个库可以有多张表，但是表名唯一
  3. 表就类似于“类”
  4. 表由列组成，称为字段，类似于“属性”
  5. 每一行数据类似于“对象”

---

**MySQL优点：**

- 开源，免费，成本低
- 性能高，执行很快
- 简单，易安装使用

MySQL分为：

1. 社区版：免费
2. 企业版：收费

---

my.ini

```mysql
[mysqld]	//服务端配置
	basedir	//安装路径
	datadir //数据存储路径
	default-storage-engine = innodb	//存储引擎
```

修改配置文件需要重启服务

```dos
net stop 服务名(mysqlxxx)
net start 服务名(mysqlxxx)
```

登陆：

1. 使用MySQL shell client，可直接输入密码，但是只能使用root用户
2. 命令行输入`mysql -hxxx.xxx.xxx.xxx -P3306(port) -uroot(用户名) -p密码`前面参数后空格可加可不加，但是-p密码之间不能有空格
3. 若在命令行提示没有mysql命令，则需查看环境变量配置

常见命令：

- `show databases;`	 //(结尾使用分号或者/g)

- `use test;   `                 //进入test库

- `show tables; `           //查看test中的表

- `drop database test; `                           //删除test库

- `show tables from mysql; `                  //查看mysql库中的表

- `select database();  `                             //查看当前库

- `create table stuinfo(`

   			`id int,`

    		 `  name varchar(20));       `          //创建表stuinfo

- `desc stuinfo;`                                         //查看表结构

- `select * from stuinfo;`                          //查看表数据

- `insert into stuinfo (id, name) values (1, "John");`   //插入数据 

- `update stuinfo set name = 'Tom' where id = 1;`    //修改数据

- `delete from stuinfo where id = 1;  `                         //删除数据 

查看MySQL版本：

1. `MySQL>select version();`
2. `CMD:mysql --version 或者mysql -V`

MySQL语法规范：

1. 不区分大小写，建议关键字大写，表名、列名小写
2. 每条命令以分号结尾
3. 当一条命令比较长时，可以换行或者缩进
4. 注释：单行以#号开头、或者以--空格开头；多行以/*    注释    */的形式

---

## 二、DQL语言 =>data query language

### 2.1 基础查询 select

**语法：**

```mysql
select 查询列表   //查询列表包括表中字段、常量、函数、表达式
from 表名;
```

查询结果是一个虚拟的表格，使用时要注意前面加上`use 库名`

**起别名：**`select 100 as num;`好处是便于理解，同时如果有重名可以区分，as可以省略

**去重：**`select distinct 字段 from 表名;`

**+号：**sql中+号为加法运算符，两个操作数均为数值，如`select 100+90;`

​		`select '100' + 90;`若一方为字符，则试图转换为数值进行运算，若失败，则字符转换为0

​		`select null + 10;`只要一方为null，计算结果为null

**拼接函数 concat**

`select concat('a', 'b', 'c') as 结果;` 

> abc

`select concat(last_name, ' ', first_name) as 姓名 from employees;`

**ifnull(expr1, expr2)**

>  expr1如果为null，则显示expr2

**isnull(expr)**

> expr为null，返回1

---

### 2.2 条件查询

**语法：**

```mysql
select 查询列表            //3
from 表名                 //1
where 筛选条件;            //2 执行顺序1->2->3
```

**根据筛选条件分类**

1. 条件运算符：>, <, =, >=, <=, !=或者<>, 不等于推荐使用<>
2. 逻辑表达式：&&, ||, !, 或者and, or, not，推荐使用后者
3. 模糊查询：like, between and, in, is null

- like

​	与通配符搭配使用，主要适合字符型，数值型也可以

​	%	任意多个字符，包含0个字符`like '%a%'`

​	_	任意单个字符`like '_a_'`

- between and

  包含临界值，完全等价>= <=，临界值不能颠倒

- in

  查询字段在一组中的一个

  `select * from 表名 where 字段 in (str1, str2, ...);`

  使用in比or提高语句简洁度

  in列表值类型需一致

  in本质是若干个条件表达式的或运算，即`expr1 or expr2 or expr3...`

  因为expr包含'='号，故in不支持通配符，使用通配符需用like

- is null / is not null

  因为不能使用`= null`，所以使用`is null`来判断`null`值

  但是可以使用安全等于`<=> null`，安全等于可以判断`null`值，也可以用于一般数值

---

### 2.3 排序查询

**语法：**`order by 排序列表 asc | desc`

**特点：**asc升序，desc降序，默认升序

​			支持表达式，别名，函数

​			可以按多个字段排序

​			一般放在查询语句最后，limit子句除外

---

### 2.4 常见函数

隐藏了实现细节，提高了重用性

#### 2.4.1单行函数

1. 字符函数

- length() 获取参数字节长度

  使用`show variables like '%char%';`可以查看字符集，每种字符集所表示的汉字字节数不同

  utf8为3个字节，gbk为2个字节

- concat() 拼接字符串

- upper()/lower() 大小写

- substr()/substring() 截取字符，这里索引从1开始计算，如

  `substr(str, pos);`截取指定索引后面的所有字符

  `substr(str, pos, len);`截取指定索引后面指定长度的字符

- instr() 返回字符串中第一个位置索引，如果找不到则返回0

  `instr(str, substr);`

- trim() 去掉前后的字符，默认不加参数是去掉空格

  `trim(remstr from str);`

  eg:`trim('a' from 'aaatheaaa');` => the

- lpad()/rpad() 左右填充，用指定字符填充指定长度，这里是指填充到长度，如下

  `lpad('lothar', 10, 'x');` => ‘xxxxlohtar'，总长度为10

- replace() 替换

  `replace(str, from_str, to_str);`

2. 数学函数

- round() 四舍五入

- ceil() 向上取整，返回大于等于该参数的最小整数

- floor() 向下取整

- truncate() 截断

  `truncate(1.69, 1);` =>1.6

- mod() 取余 `mod(10, 3);` => 1

- rand() 随机数，返回(0, 1)之间的小数

3. 日期函数

- now() 返回当前日期，时间

- curdate() 当前日期

- curtime() 当前时间

- 可以获取一个日期的指定部分，如`year,month,day,hour,minite,second,monthname等`

  `select monthname(now());` 显示当前月份，June

- str_to_date() 字符串转日期，通过指定格式解析

  `str_to_date('09-12-1999', '%m-%d-%Y');`

  | 符号 | 转换含义        |
  | ---- | --------------- |
  | %Y   | 4位年           |
  | %y   | 2位年           |
  | %m   | 月份，01，02... |
  | %c   | 月份，1，2...   |
  | %d   | 日              |
  | %H   | 24小时制        |
  | %h   | 12小时制        |
  | i%   | 分钟            |
  | %s   | 秒              |

- datediff(日期1，日期2)

- date_format() 将日期转为字符串

  `date_format(curdate(), '%y年%c月%d日');`

4. 其他函数

- version() 版本
- database() 当前库
- user() 当前用户
- password('字符串') 返回字符串的密码形式，自动加密
- md5('字符串') 返回md5格式加密

5. 流程控制函数

- if()

  `select if(10 >5, '大', '小');`

- case()

  使用一：类似switch case效果

  ```mysql
  case 要判断的字段或者表达式
  when 常量1 then 语句/值
  when 常量2 then 语句/值
  ...
  else 语句/值(默认)
  end
  ```

  使用二：类似多重if

  ```mysql
  case
  when 条件1 then 语句/值
  when 条件2 then 语句/值
  ...
  else 语句/值(默认)
  end
  ```

  

#### 2.4.2分组函数

用于统计使用，又称聚合、组、统计函数

​	





