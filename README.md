## mysql 必知必会

### 第一章：基本操作

1. mysql -u root -p  登录
2. exit 退出
3. show databases; 查看所有表格
4. use A 使用某个表格
5. show tables;  查看所选中的表个的所有列
6. show columns from customers;   展示customers表的具体信息



### 第二章：select检索数据

1. select **distintc** booksName from book;  检索出不同的name, 没有重复
2. select * from books;   选择所有的列
3. select booksName from book **limit 5, 5**;  限制在第5行，并且只取后续的5行

4. 表名和列名可以加以限定。a.b的形式



### 第三章：排序检索数据

#### 3.1 学会使用order by

如依靠name对数据进行排序

```mysql
select booksName
from book
order by booksName;
```

其中order by所选中的列是表示列，但实际中用非检索的列表也可以。

如

```mysql
select booksName
from book
order by booksPrice;
```

显示的依旧时booksName列，但排序顺序是按booksPrice列。



#### 3.2 多列排序

```mysql
select booksId, booksName, booksPrice
from books
order by booksPrice, booksName;
```

多列排序，先对booksPrice排序，相同时在看booksName。

重点理解多列的顺序即可，**按从左到右**



#### 3.3 指定排序方向

order by  默认升序，如价格从低到高，字母从A~Z

但我们可以规定使用降序

关键字，**DESC**

```mysql
select booksName, booksPrice
from books
order by booksPrice DESC;
```

但是，多列排序呢

```mysql
select booksName, booksPrice
from books
order by booksPrice DESC, booksName;
```

发现booksPirce按降序，但booksName依旧时升序

所以，DESC只作用于**关键字前**的一列，其余列依旧是默认升序

若要多列降序，必须每一列都加上DESC

与DESC，相反的是ASC升序排序，但不用记，因为默认是升序

```mysql
 select booksName, booksPrice
    -> from books
    -> order by booksPrice
    -> limit 1;
```

配合limit限制语句，可以找出你需要的数据。



### 第四章：过滤数据（where)

#### 4.1 使用where子句

数据库通常包含大量的数据，我们往往只需要少数的子集。

select检索出超出需求的数据，大量的数据发送到客户端会造成带宽的浪费。

客户端在进行检索也浪费时间。

**因此过滤/筛选数据很重要。**

where + 搜索条件可以解决需求

```mysql
 select booksName
    -> from books
    -> where booksPrice = 79;
```

只返回价格为79的书名



当where和order by连用时，顺序非常重要

注意**order by必须在where之后**，否则将出现错误

原因如下：（先记住这个规则，反例暂时想不出来）

1. 条件影响排序结果
2. 结果集不一致
3. where写在order by之前还能减少排序所占用的时间，空间复杂度。



#### 4.2 where可以使用的操作符

| 操作符  | 说明               |
| ------- | ------------------ |
| =       | 等于               |
| <>      | 不等于             |
| !=      | 不等于             |
| <       | 小于               |
| <=      | 小于等于           |
| >       | 大于               |
| >=      | 大于等于           |
| BETWEEN | 在指定的两个值之间 |



##### 4.2.1检查单个值

```mysql
select prod_name, prod_price
from products
where prod_name = 'fuses';
```

注意mysql在检索时不区分大小写，因此，fuses和Fuses一样。

注意，fuses时字符串，因此需要限定符单引号 ‘ ’。



##### 4.2.2检查不匹配

```mysql
 select booksName
    -> from books
    -> where booksId <> 2;
```



##### 4.2.3检查范围值

```mysql
 select booksName
    -> from books
    -> where booksPrice BETWEEN 40 AND 90;
```

注意，betwee在使用时，必须要加上下界，因此需要AND关键字连接。



#### 4.3 空值匹配

创建表时，我们可以自己指定是否包含值。

在一个列不包含值时，称其为空指NULL。



**NULL 它与字段包含0、空字符串或仅仅包含空格不同。**

特殊的where子句——IS NULL，来检测具有NULL的列。

```mysql
select booksName
    -> from books
    -> where booksPrice is null;
```



### 第五章：高级数据过滤

#### 5.1 组合where子句

##### 5.1.1 AND操作符

```mysql
select booksName
    -> from books
    -> where booksPrice <= 50 AND booksTypeId = 1;
```

AND和高级语言中的用法一样，注意**mysql没有 == 符号**，切记

##### 5.1.2 OR操作符

where 写法上与AND子句基本一样，用法参考高级语言如C。这里不写例子了。

##### 5.1.3 计算次序

跟大多数高级语言一样，AND的优先级高于OR。可用括号来自由的满足你需要的顺序。

如

```mysql
where (a > 10 OR b < 100) AND c <> 1000;
```

写法上，我们推崇加括号，可读性更强，且不易出错



#### 5.2 IN操作符

```mysql
select booksName
    -> from books
    -> where booksTypeId IN (1, 5);
```

IN操作符取，满足括号内条件的值。

上述例子中，去出booksTypeId 等于1或者5的书籍

注意，**不是**类型编号从1 到 5 的书籍。

**看到IN往往容易想道引申的含义，在什么范围内，错误的。**

IN的用法与OR相当。但有更多的优点。

1. 长选项清单时，IN操作符的语法更加清楚且直观。
2. IN在使用时，计算的次序更容易管理
3. IN比OR运行得更快
4. **最大有点**：可以包含其他select语句，可以动态建立where语句。具体见14章



#### 5.3 NOT操作符

NOT只有一个功能，否定它之后的任何条件。

```mysql
select booksName
    -> from books
    -> where booksTypeId NOT IN (1, 5);
```

否定ID为1或者5的书，找其他的。

MySQL支持NOT对IN、BETWEEN、EXISTS子句取反，这与很多的DBMS允许对各种条件取反有很大的差别。



### 第六章：用通配符进行过滤

#### 6.1 LIKE 操作符

适用于特定条件的搜素模式。例如找出名字中包含“帅哥”的书籍。

**通配符** 用来匹配值的一部分的特殊字符

**搜索模式** 由字面值、通配符或两者组合构成的搜索条件

SQL支持通配符，但必须搭配LIKE使用。

LIKE指示后跟的搜索模式利用通配符匹配而不是直接相等匹配进行比较。

从技术上说，LIKE是谓词而不是通配符



##### 6.1.1 百分号 % 通配符

%表示任何字符出现任意次数

```mysql
    select booksName
    -> from books
    -> where booksName
    -> Like '三%';
```

找书的名字以三开头的

%表示此处开始接受任意字符，不管多少，**0，1，任意多**都行。

根据MySQL配置搜索可以分大小写。（检索时不分大小写）



**通配符可以放在任意位置**

```mysql
select booksName
    -> from books
    -> where booksName
    -> LIKE '%和%';
```

%注意项

1. 尾空格，可以在最后加一个%，或用函数
2. NULL，%不能匹配NULL



##### 6.1.2 下划线 _ 通配符 

_ 用途与 % 类似，但只能匹配一个字符，不能多不能少。

是不是很像白板卡，谁都能变，但只能一次，哈哈。



#### 6.2 使用通配符技巧

1. 不要过度使用通配符，能用其他操作就用其他操作。因为效率低。
2. 不得不使用时，除非绝对的必要，否则不要放在一开始的搜索处，这样效率最低
3. 注意通配符的位置，不一样的位置，可能返回错误的数据



### 第七章 正则表达式进行搜索

#### 7.1 MySQL正则表达式

where语句支持基本正则表达式，对select筛选的数据进行过滤

关键字：**REGEXP**，不区分大小写



##### 7.1.1 基本字符匹配

```mysql
select booksName
    -> from books
    -> where booksName REGEXP '三';
```

```mysql
select booksName
    -> from books
    -> where booksName like '三';
```

我们发现，用法上，regexp和like基本相同

但是，运行后结果却不同

REGEXP有一行数据，LIKE没有数据

因为，LIKE是对整个列进行匹配，必须严格一一对应，书名并没有单独叫 “三”的

但是，REXEXP确实对列的值进行匹配，可以想象成自带通配符比较，书名中有个“三”字的就能被选中



##### 7.1.2 进行OR匹配

符号： **|**

```mysql
select booksName
    -> from books
    -> where booksTypeId = 1 OR booksTypeId = 2;
#这是一般的使用操作符，下面看使用REGEXP关键字的

select booksName
    -> from books
    -> where booksTypeId
    -> REGEXP '1|2';
#答案一样，更写法更简洁
```



##### 7.1.3 匹配几个字符之一

符号：**[ ]**

```mysql
select booksName
    -> from books
    -> where booksTypeID REGEXP '[123]';
```

[ ]的作用与 | 类似，可以看成多个 | 并用

但是有区别，如书上所讲，若也有不同。例子见书本P66。

我没有书中的数据库，我自己这个数据库没有合适的例子，所以在此不多讲。



简单说两句。

```mysql
where prod_name REGEXP '[123] TON';
#返回的是 1 TON 或者 2 TON 或者 3 TON

where prod_name REGEXP '1|2|3 TON';
#返回的是 1 或者 2 或者 3 TON
#然后，REGEXP正则关键字，搜索的是列值，所以会返回很多，包括1或者2的行。
#这与我们期待的不一样
```



**7.1.4 匹配范围**

简化书写：**-**

如[0123456789]，可以写成[0-9] 

```mysql
select booksName
    -> from books
    -> where booksTypeId REGEXP '[0-5]';
```



##### 7.1.5 匹配特殊字符

我们知道 **- **是正则字符，那么，我们要是取寻找带有 **-** 的字符怎么办呢

如同C语言中一样，使用转义字符 \\\



| 元字符 | 说明     |
| ------ | -------- |
| \\\f   | 换页     |
| \\\n   | 换行     |
| \\\r   | 回车     |
| \\\t   | 制表     |
| \\\v   | 纵向制表 |

为了匹配\ 本身需要 \\\来表示



##### 7.1.6 匹配字符类

![字符类](D:\biji\mysql\mysql必知必会\images\字符类.bmp)



##### 7.1.7 匹配多个给实例

##### 7.1.8 定位符

以上两章看书，p69~72



### 第八章 创建计算字段

一般数据库表中的内容，并不是我们直接需要的。

例如，我们在一个字段中，既显示公司名，又显示公司地址，但这两个信息，往往不在一个列中。所以这时候计算字段发挥作用。

计算字段是select内创建的，不在数据库表中。



**字段**与列的意思相同，只是列一般指数据库的列表，而术语字段通常用于计算字段上。

注意，客户角度看不出哪些是实际的表列，哪些是计算字段。

只有数据库知道。



#### 8.1 拼接字段

将值连到一起，形成一个单个值

Mysql的select中使用Concat()函数来拼接两个列。



注意：多数DBMS使用 + 或者 || 来实现拼接。MySQL是concat。

SQL转MySQL时需要注意。



```mysql
select concat(booksName, '(', booksPrice, ')')
    -> from books
    -> order by booksPrice;

#不加括号也可以，但是这样看起来不美观
select concat(booksName, booksPrice)
    -> from books
    -> order by booksPrice;
```



RTrim(), LTrim(), Trim()

删除右空格，做空格，两侧全部空格。



#### 8.2 使用别名

拼接的字段没有名字，客户端没有办法引用，怎么办呢。

我们需要引用**别名**。

**别名**是一个字段或者值得替换名。用AS关键字赋予。

```mysql
select concat(RTrim(booksName), '(', RTrim(booksPrice), ')') AS booksname
    -> from books;
```



#### 8.3 执行算术计算

```mysql
select prod_id,
	quantity,
	item_price,
	quantity*item_price AS expanded_price
from orderitems
where order_num = 2005;

#其中quantity*item_price AS expanded_price就是计算字段然后命别名
```



支持基本操作符

| 操作符 | 说明 |
| ------ | ---- |
| +      | 加   |
| -      | 减   |
| *      | 乘   |
| /      | 除   |



### 第九章 使用数据处理函数

#### 9.1 什么叫函数

函数用来处理数据

注意，不通的DBMS对函数的支持是不一样的。所以，尽量不要使用特殊的函数，在使用函数时，也要做好注释。



#### 9.2 使用函数

总体支持4种函数

1. 处理文本串的文本函数
2. 在数值数据上进行算术操作的数值函数
3. 日期和时间函数
4. 系统函数



##### 9.2.1 文本函数

常用的函数

| 函数        | 说明                 |
| ----------- | -------------------- |
| left()      | 返回字符串左边的字符 |
| length()    | 返回串的长度         |
| locate()    | 找出串的一个子串     |
| lower()     | 将串转化为小写       |
| ltrim()     | 去掉串左边的空格     |
| right()     | 返回右边的字串       |
| rtrim()     | 去掉右边的空格       |
| soundex()   | 返回串的SOUNDEX值    |
| substring() | 返回字串的字符       |
| upper()     | 将串转换为大写       |

soundex是发音比较，不是字母比较

具体见书本P81



##### 9.2.2 日期和时间处理函数

![常用日期函数](D:\biji\mysql\mysql必知必会\images\常用日期函数.bmp)

日期在使用时，总是应该使用4位数字的年份

在写代码时，如果只想找日期，只是用DATE()函数

DATE(date) 进提取日期部分，后面的时：分：秒不会干扰选择



如果你想检索出时间段的，用BETWEEN ...  AND ...

```mysql
where Date(order_date) BETWEEN '2005-09-01' AND '2005-09-30';
```

也可以用别的

```mysql
where Year(order_date) = 2005 AND Month(order_date) = 9;
```



##### 9.2.3 数值处理函数

![数值处理函数](D:\biji\mysql\mysql必知必会\images\数值处理函数.bmp)



### 第10章 汇总数据

聚集函数 运行在行组上，计算和返回单个值的函数

| 函数  | 说明             |
| ----- | ---------------- |
| AVG   | 返回某列的平均值 |
| COUNT | 返回某列的行数   |
| MAX   | 返回某列的最大值 |
| MIN   | 返回某列的最小值 |
| SUM   | 返回某列值的和   |



#### 10.1 函数

##### 10.1.1  AVG() 函数

返回某列的平均价格，也可以用where做限制条件

```mysql
select AVG(booksPrice) as prices
    -> from books;
    
select AVG(booksPrice) as prices
    -> from books
    -> where booksTypeId = 1;
```

AVG只能用于单列，且自动忽略NULL行。



##### 10.1.2 COUNT()函数

确定表中行的数目或符合特定条件的行的数目

1. count(*) 对表中行的数目进行计数，不管是否为NULL
2. count(column)对特定列中的具有值得行进行技术，忽略NULL值



```mysql
select count(*) as counts
    -> from books;
    
select count(booksId) as counts
    -> from books;
```



##### 10.1.3 MAX函数

返回最大值，忽略NULL。

返回任意列得最大值，包括文本。



```mysql
select MAX(booksPrice) as price
    -> from books;
```



##### 10.1.4 MIN函数

和max一模一样，除了功能。



##### 10.1.5 SUM函数

返回列的总和。

忽略NULL，可以在多列计算。

```mysql
select sum(nums * prices) as prices;
```



#### 10.2 组合聚集

```mysql
 select max(booksPrice),
    -> min(booksPrice)
    -> from books
    -> ;
```

不取别名表中默认显示 函数名（列明）
