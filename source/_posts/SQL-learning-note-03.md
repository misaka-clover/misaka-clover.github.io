---
title: SQL 学习笔记【03】
date: 2022-10-24 15:09:34
updated: 2022-11-15 15:59:52
description: 未完待续，或者没有后续了
tags: 
    - 博文
    - SQL
    - 笔记
    - 知识
    - 《SQL基础教程》
categories: 叶子的小知识
comment: true
keywords: SQL,学习,笔记,《SQL基础教程》
---

![cover](/images/SQL-learning-note-03/cover.jpg)

不同于我的第一篇笔记，仅仅是对 SQL 语句做了最基础的了解；在上一篇[**文章**](https://mskclover.com/2022/10/18/SQL-learning-note-02/)中，我们跟着《SQL 基础教程》成体系地学习了数据库语言的基础操作，现在我们将对数据库进行更深一步地学习。在完成这段学习以后，相信在面对相关领域的问题时，我们都不会再一头雾水、不知所措了。

这应该是这段时间的《SQL 学习笔记》的最后一篇了。

<!-- more -->

## 数据库的复杂查询

从这部分开始，对数据库的各类操作将不再限于一张表，而是会以此为基础进行延伸。

### 视图 VIEW

* 从 SQL 的角度来看，视图和表是相同的，两者的区别在于：表中保存的是实际的数据，而视图中保存的是从表中取出数据所使用的 `SELECT` 语句（视图本身并不存储数据）。 

![sql03-05-01](/images/SQL-learning-note-03/sql03-05-01.jpg)

* 使用视图，可以轻松完成跨多表查询数据等复杂操作。
* 可以将常用的 SELECT语句做成视图来使用。 

**视图的优点：**

其一：由于视图无需保存数据，因此可以节省存储设备的容量。

其二：可以将频繁使用的 `SELECT` 语句保存成视图，这样就不用每次都重新书写了，可以直接在 `SELECT` 语句中进行调用。（视图就是保存好的 SELECT 语句）

#### 创建视图

* 创建视图需要使用 `CREATE VIEW` 语句。

```sql
-- 创建视图的 CREATE VIEW 语句
CREATE VIEW 视图名称 (<视图列名1>, <视图列名2>, ……) 
AS
<SELECT语句>

-- 例如：ProductSum 视图
CREATE VIEW ProductSum (product_type, cnt_product)
AS
SELECT product_type, COUNT(*) 
  FROM Product
 GROUP BY product_type;

-- 使用视图：
SELECT product_type, cnt_product
  FROM ProductSum;
```

![sql03-05-02](/images/SQL-learning-note-03/sql03-05-02.jpg)

#### 视图查询

在 FROM 子句中使用视图的查询，通常有如下两个步骤： 

* 首先执行定义视图的 `SELECT` 语句；
* 根据得到的结果，再执行在 `FROM` 子句中使用视图的 `SELECT` 语句；

也就是说，使用视图的查询通常需要执行 2 条以上的 `SELECT` 语句，两条以上是因为可能出现以视图为基础创建的多重视图：

![sql03-05-03](/images/SQL-learning-note-03/sql03-05-03.jpg)

**注：**应该避免在视图的基础上创建视图。这是因为对多数 DBMS 来说，多重视图会降低 SQL 的性能。

#### 视图的限制

* 定义视图时不要使用 `ORDER BY` 子句（因为视图与表一样，数据行都是没有顺序的）；
* 可对视图进行有限制的更新。视图和表需要同时进行更新，因此通过汇总得到的视图无法进行更新；

#### 删除视图

* 删除视图需要使用 DROP VIEW语句。

```sql
-- 删除视图的 DROP VIEW 语句：
DROP VIEW 视图名称 (<视图列名1>, <视图列名2>, ……)

-- 删除视图：
DROP VIEW ProductSum;
```



### 子查询

* 视图并不是用来保存数据的，而是通过保存读取数据的 `SELECT` 语句的方法来为用户提供便利。反之，子查 
  询就是将用来定义视图的 `SELECT` 语句直接用于 `FROM` 子句当中。
* 一言以蔽之，子查询就是一次性视图（`SELECT` 语句）。与视图不同，子查询在 `SELECT` 语句执行完毕之后就会消失。
* 由于子查询需要命名，因此需要根据处理内容来指定恰当的名称。 

```sql
-- 与上文 ProductSum 实现同样功能的子查询：
-- 在FROM子句中直接书写定义视图的SELECT语句 
SELECT product_type, cnt_product
  FROM ( 
    SELECT product_type, COUNT(*) AS cnt_product 
      FROM Product
     GROUP BY product_type
	) AS ProductSum;
```

**注意：**该 `SELECT` 语句包含嵌套的结构，首先会执行 `FROM` 子句中的 `SELECT` 语句，然后才会执行外层的 `SELECT` 语句。即：**子查询作为内层查询会首先执行**。

![sql03-05-04](/images/SQL-learning-note-03/sql03-05-04.jpg)

同时，随着子查询嵌套层数的增加，SQL 语句会变得越来越难读懂，性能也会越来越差。因此，请大家尽量避免使用多层嵌套的子查询。

#### 标量子查询

* **标量**就是单一的意思，**标量子查询**就是只能返回单一值的子查询，而且只能返回 1 行 1 列的结果。
* 由于返回的是单一的值，因此标量子查询的返回值可以用在 = 或者 <> 这样需要单一值的比较运算符之中。这也正是标量子查询的优势所在。

```sql
-- 在 WHERE 子句中使用标量子查询
-- 例如：选取出销售单价（sale_price）高于全部商品的平均单价的商品

-- 错误示例：
-- 在WHERE子句中不能使用聚合函数
SELECT product_id, product＿name, sale_price 
  FROM Product
 WHERE sale_price > AVG(sale_price);

-- 使用标量子查询
SELECT product_id, product_name, sale_price 
  FROM Product
 WHERE sale_price > (SELECT AVG(sale_price)
                     FROM Product);
```

同理，`SELECT` 语句会现执行 `FROM` 中的内层语句，再执行外层语句：

![sql03-05-05](/images/SQL-learning-note-03/sql03-05-05.jpg)

**标量子查询的书写位置：**能够使用常数或者列名的地方，无论是 `SELECT` 子 句、`GROUP BY` 子句、`HAVING` 子句，还是 `ORDER BY` 子句，几乎所有的地方都可以使用。

**注意：标量子查询绝不能返回多行结果。**

```sql
-- 错误示例：

SELECT product_id, product_name, sale_price 
  FROM Product
 WHERE sale_price > (SELECT AVG(sale_price) 
                     FROM Product
                     GROUP BY product_type);
```



### 关联子查询

为了解决上文中子查询返回多行结果的情况，我们可以使用**关联子查询**：

* 关联子查询会在细分的组内进行比较时使用。
* 关联子查询和 GROUP BY子句一样，也可以对表中的数据进行切分。

```sql
-- 通过关联子查询按照商品种类对平均销售单价进行比较
-- 在细分的组内进行比较时，需要使用关联子查询。

SELECT product_type, product_name, sale_price
  FROM Product AS P1                           ① 
 WHERE sale_price > (SELECT AVG(sale_price)
                     FROM Product AS P2
                     WHERE P1.product_type = P2.product_type		-- 关键作用
                     GROUP BY product_type);
```

![sql03-05-06](/images/SQL-learning-note-03/sql03-05-06.jpg)

这次由于作为比较对象的都是同一张 Product 表，因此为了进行区别，分别使用了 P1 和 P2 两个别名。在使用关联子查询时，需要在表所对应的列名之前加上表的别名，以「 `<表名>.<列名>` 」的形式记述。

**注意：**这里起到关键作用的就是在子查询中添加的 `WHERE` 子句的条件。该条件的意思就是，在同一商品种类中对各商品的销售单价和平均单价进行比较。

![sql03-05-07](/images/SQL-learning-note-03/sql03-05-07.jpg)

* 关联子查询的结合条件如果未出现在子查询之中就会发生错误。

因为这里**关联名称**有**作用域**，也就是存在一个有效范围的限制。具体来讲，子查询内部设定的关联名称，只能在该子查询内部使用。换句话说，就是「内部可以看到外部，而外部看不到内部」。

![sql03-05-08](/images/SQL-learning-note-03/sql03-05-08.jpg)



## 数据库的函数

这一部分会涉及到一些具有代表性的 SQL 函数、谓词和 `CASE` 表达式。

### 函数种类

在 SQL 中的常用函数大概有三五十个，它们都可以分为这几种类型：

* 算术函数（数值计算） 
* 字符串函数（字符串操作） 
* 日期函数（日期操作） 
* 转换函数（转换数据类型和值） 
* 聚合函数（数据聚合）

#### 算数函数

```sql
-- 加减乘除
+-*/

-- 绝对值
ABS(数值)

-- 求余
MOD(被除数,除数)

-- 四舍五入
ROUND(对象数值,保留小数的位数)
```

#### 字符串函数

```sql
-- 拼接
str1 || str2
-- 注意：在 MySQL 中使用 CONNECT 函数
CONNECT(str1, str2, str3)

-- 字符串长度
LENGTH(字符串)

-- 小写转换
LOWER(字符串)
-- 大写转换
UPPER(字符串)

-- 字符串替换
REPLACE(对象字符串, 替换前的字符串, 替换后的字符串)
-- 注：替换的是对象字符串中，与第二个参数匹配的内容，
--     并不是直接替换整个字符串

-- 字符串截取
SUBSTRING(对象字符串 FROM 截取的起始位置 FOR 截取的字符数)
```

#### 日期函数

```sql
-- 当前日期
CURRENT_DATE
-- 返回值：2016-05-25

-- 当前时间
CURRENT_TIME
-- 返回值：17:26:50.995+09

-- 当前的日期和时间
CURRENT_TIMESTAMP
-- 返回值：2016-04-25 18:31:03.704+09
```

截取日期元素的 `EXTRACT 函数` 示例单独列出：

```sql
-- 街区日期元素
EXTRACT(日期元素 FROM 日期)

-- 例如：
SELECT CURRENT_TIMESTAMP,
			 EXTRACT(YEAR   FROM CURRENT_TIMESTAMP)  AS year,
			 EXTRACT(MONTH  FROM CURRENT_TIMESTAMP)  AS month,
			 EXTRACT(DAY    FROM CURRENT_TIMESTAMP)  AS day,
			 EXTRACT(HOUR   FROM CURRENT_TIMESTAMP)  AS hour,
			 EXTRACT(MINUTE FROM CURRENT_TIMESTAMP)  AS minute,
			 EXTRACT(SECOND FROM CURRENT_TIMESTAMP)  AS second;
```

![sql03-06-01](/images/SQL-learning-note-03/sql03-06-01.jpg)

#### 转换函数

转换函数中的**「转换」**二字，在 SQL 中主要有两种意思：一种是**数据类型的转换**，在英语中称为 **CAST**；另一种是**值的转换**。

类型转换可用 `CAST 函数` 做到：

```sql
-- 类型转换
CAST(转换前的值 AS 想要转换的数据类型)

-- 例如，字符串转换为数值类型
SELECT CAST('0001' AS INTEGER) AS int_col;

int_col
---------
1

-- 字符串转换为日期类型
SELECT CAST('2009-12-14' AS DATE) AS date_col;

date_col
---------
2009-12-14
```

下面介绍的 `COALESCE 函数` 可将 NULL 转换为其他值：

```sql
-- 将 NULL 转换为其他值：
COALESCE(数据1, 数据2, 数据3 …)
```

该函数的参数为**可变参数**，即参数的个数并不是限定的。函数调用时**返回从左侧开始第一个不是 NULL 的值**，该 SQL 语句具有将 NULL 转换为其他值的作用。

```sql
-- 例如：
SELECT COALESCE(NULL, 1)									AS col_1,
       COALESCE(NULL, 'test', NULL)				AS col_2, 
       COALESCE(NULL, NULL, '2009-11-01')	AS col_3;

-- 执行结果：
col_1  |  col_2 |  col_3
-------+--------+-----------
     1 |  test  |  2009-11-01
```

函数的种类很多，无需全都记住，只需要记住具有代表性的函数就可以了，其他的可以在使用时再进行查询手册。

### 谓词

这一节的内容就会比较难了，因为涉及到 SQL 的进阶用法，会有许多的谓词需要了解：

* **谓词**就是**返回值为真值的函数**。

谓词也是函数，但其与函数的区别主要是：对通常的函数来说，返回值有可能是数字、字符串或者日期等，但是谓词的返回值全都是真值（TRUE / FALSE / UNKNOWN）。

#### LIKE —— 字符串部分一致查询

在之前字符串作为查询条件时，常常使用「=」限定条件，而 `LIKE` 谓词可以对字符串进行**部分一致查询**。

* 谓词 `LIKE` 有**三种使用方法**：前方一致、中间一致、后方一致。

```sql
-- 原来的表单 SampleLike 表为：
strcol
--------
abcddd
dddabc
abdddc
abcdd
ddabc
abddc

-- 例如想查询字符串 ddd：

-- 前方一致查询：
SELECT *
  FROM SampleLike
 WHERE strcol LIKE 'ddd%';
-- 查询结果：dddabc

-- 中间一致查询：
SELECT *
  FROM SampleLike
 WHERE strcol LIKE '%ddd%';
-- 查询结果：abcddd，dddabc，abdddc

-- 后方一致查询：
SELECT *
  FROM SampleLike
 WHERE strcol LIKE '%ddd';
-- 查询结果：abcddd
```

像这样不使用「=」来指定条件字符串，而以字符串中是否包含该条件的规则为基础的查询，称为**模式匹配**。

此处的「**%**」符号代表「0 字符以上的任意字符串」的特殊符号。此外，我们还可以使用「**_** 」（下划线）来代替「%」，不同的是，它只代表了「任意 1 个字符」。

#### BETWEEN —— 范围查询

* 注意： `BETWEEN` 谓词包含了三个参数。

```sql
-- 例如：选取销售单价为 100 ～ 1000日元的商品

SELECT product_name, sale_price 
  FROM Product
 WHERE sale_price BETWEEN 100 AND 1000;
```

* 注意：`BETWEEN` 的特点 —— 结果中会包含 100 和 1000 这两个临界值。如果不想让结果中包含临界值，那就必须使用 < 和 >。

#### IS NULL / IS NOT NULL —— 判断 NULL

* 对于 NULL 类型的数据，不能够使用「=」等运算符限定条件，想要取得 NULL 数据时必须使用 `IS NULL`。 相反，想要选取 NULL 以外的数据时，需要使用 `IS NOT NULL`。

```sql
-- 例如：选取出进货单价（purchase_price）为 NULL 的商品
SELECT product_name, purchase_price 
  FROM Product
 WHERE purchase_price IS NULL;

-- 例如：选取进货单价（purchase_price）不为 NULL 的商品
SELECT product_name, purchase_price 
  FROM Product
 WHERE purchase_price IS NOT NULL;
```

#### IN —— 简便的 OR

```sql
-- 原来：通过 OR 指定多个进货单价进行查询
SELECT product_name, purchase_price 
  FROM Product
 WHERE purchase_price =  320 
    OR purchase_price =  500 
    OR purchase_price = 5000;

-- 简便：通过 IN 来指定多个进货单价进行查询
SELECT product_name, purchase_price 
  FROM Product
 WHERE purchase_price IN (320, 500, 5000);
```

* **注意：**使用 `IN` 和 `NOT IN` 时是无法选取出 NULL 数据的。
* **特别注意：**这是 SQL 中最危险的陷阱，`IN` 与 `NOT IN` 的参数中包含 NULL 时结果通常会为空，也就是无法选取出任何记录。不仅仅是指定 NULL 的情况，使用子查询作为`IN` 或 `NOT IN` 的参数时，该子查询的返回值 
  也不能是 NULL。

#### 使用子查询作为 IN 的参数

* `IN` 或 `NOT IN` 谓词具有其他谓词所没有的用法，那就是可以使用子查询作为其参数。

例如新建一张 ShopProduct 表，显示出哪些商店在销售哪些商品：

![sql03-06-02](/images/SQL-learning-note-03/sql03-06-02.jpg)

创建表的 SQL 语句为：

```sql
-- 创建 ShopProduct 表的 CREATE TABLE 语句：

CREATE TABLE ShopProduct
(shop_id    CHAR(4)      NOT NULL, 
 shop_name  VARCHAR(200) NOT NULL, 
 product_id CHAR(4)      NOT NULL, 
 quantity   INTEGER      NOT NULL, 
 PRIMARY KEY (shop_id, product_id));
```

**注意：**这个 `CREAT TABLE` 语句指定了两个列作为主键：shop_id 与 product_id。这样做当然还是为了区分表中每一行数据，由于单独使用商店编号 （shop_id）或者商品编号（product_id）不能满足要求，因此需要对商店和商品进行组合。

```sql
-- 取得「在大阪店销售的商品的销售单价」

SELECT product_name, sale_price 
  FROM Product
 WHERE product_id IN (SELECT product_id 
                        FROM ShopProduct
                       WHERE shop_id = '000C');
```

为什么一定要使用子查询呢？由于数据记录在未来可能会发生改变，为了语句能够复用，所以需要**易维护程序**。

> 这是因为 ShopProduct（商店商品）表并不是一成不变的。实际上由于各个商店销售的商品都在不断发生变化，因此 ShopProduct 表内大阪店销售的商品也会发生变化。如果 SELECT 语句中没有使用子查询的话，一旦商品发生了改变，那么 `SELECT` 语句也不得不进行修改，而且这样的修改工作会变得没完没了。
>
> 如果在 `SELECT` 语句中使用了子查询，那么即使数据发生了变更，还可以继续使用同样的 `SELECT `语句。这样也就减少了我们的常规作业（单纯的重复操作）。

谓词 `NOT IN` 的使用方式也是同理的。

#### EXIST —— SQL 的中级工具

* 可以将子查询作为 `IN` 和 `EXISTS` 的参数。
* 实际上即使不使用 `EXIST`，基本上也都可以使用 `IN` 或者 `NOT IN` 来代替。
* 谓词的作用就是「判断是否存在满足某种条件的记录」。如果存在这样的记录就返回真（TRUE），如果不存在就返回假（FALSE）。`EXIST` 谓词的主语是 “记录”。

```sql
-- 使用 EXIST 选取出「大阪店在售商品的销售单价」

SELECT product_name, sale_price
  FROM Product AS P
 WHERE EXISTS (SELECT *
                 FROM ShopProduct AS SP
                WHERE SP.shop_id = '000C'
                  AND SP.product_id = P.product_id);
```

![sql03-06-03](/images/SQL-learning-note-03/sql03-06-03.jpg)

* 因为 `EXIST` 左侧没有参数，是只有 1 个参数的谓词。`EXIST` 只需要在右侧书写 1 个参数，**通常指定关联子查询作为 `EXIST` 的参数，**作为 `EXIST` 参数的子查询中经常会使用 `SELECT *`。
* `NOT EXIST` 与 `EXIST` 相反，当「不存在」满足子查询中指定条件的记录时返回真（TRUE）。

```sql
-- 使用 NOT EXIST 读取出「东京店在售之外的商品的销售单价」

SELECT product_name, sale_price 
  FROM Product AS P
 WHERE NOT EXISTS (SELECT *
                     FROM ShopProduct AS SP
                    WHERE SP.shop_id = '000A'
                      AND SP.product_id = P.product_id);
```

### CASE —— 条件表达式

* `CASE` 表达式分为**简单 `CASE` 表达式**和**搜索 `CASE` 表达式**两种。搜索 `CASE` 表达式包含简单 `CASE` 表达式的全部功能。

```sql
-- 搜索 CASE 表达式

CASE WHEN <求值表达式 > THEN <表达式 > 
     WHEN <求值表达式 > THEN <表达式 > 
     WHEN <求值表达式 > THEN <表达式 >
       . 
       . 
       .
     ELSE <表达式 > 
END
```

* 虽然 `CASE` 表达式中的 `ELSE` 子句可以省略，但为了让 SQL 语句更加容易理解，还是希望大家不要省略。
* `CASE` 表达式中的 `END` 不能省略。

```sql
-- 通过 CASE 表达式将 A～C 的字符串加入到商品种类当中

SELECT product_name,
       CASE WHEN product_type = '衣服' 
            THEN 'A ：' || product_type 
            WHEN product_type = '办公用品' 
            THEN 'B ：' || product_type 
            WHEN product_type = '厨房用具' 
            THEN 'C ：' || product_type 
            ELSE NULL
       END AS abc_product_type 
  FROM Product;

-- 由于 CASE 表达式是函数，所以只会返回一个值，6 行 CASE 表达式代码
-- 最后只相当于 1 列（abc_product_type） 。
```

![sql03-06-04](/images/SQL-learning-note-03/sql03-06-04.jpg)

* 使用 `CASE` 表达式能够将 `SELECT` 语句的结果进行组合。

```sql
-- 通常使用 GROUP BY也无法实现行列转换

SELECT product_type,
       SUM(sale_price) AS sum_price 
  FROM Product
 GROUP BY product_type;
```

![sql03-06-05](/images/SQL-learning-note-03/sql03-06-05.jpg)

```sql
-- 使用 CASE 表达式进行行列转换
-- 对按照商品种类计算出的销售单价合计值进行行列转换

SELECT SUM(CASE WHEN product_type = '衣服'
           THEN sale_price ELSE 0 END) AS sum_price_clothes,
       SUM(CASE WHEN product_type = '厨房用具'
           THEN sale_price ELSE 0 END) AS sum_price_kitchen,
       SUM(CASE WHEN product_type = '办公用品'
           THEN sale_price ELSE 0 END) AS sum_price_office
  FROM Product;
```

![sql03-06-06](/images/SQL-learning-note-03/sql03-06-06.jpg)

* 虽然有些 DBMS 提供了各自特有的 `CASE` 表达式的简化函数，例如 Oracle 中的 `DECODE` 和 MySQL 中的 `IF`，等等，但由于它们并非通用的函数，功能上也有些限制，因此有些场合无法使用。

```sql
-- MySQL 中使用 IF 代替 CASE 表达式 

SELECT  product_name,
        IF( IF( IF(product_type = '衣服', 
                   CONCAT('A ：', product_type), NULL) 
                  IS NULL AND product_type = '办公用品', 
                   CONCAT('B ：', product_type), 
            IF(product_type = '衣服', 
               CONCAT('A ：', product_type), NULL)) 
                   IS NULL AND product_type = '厨房用具', 
                      CONCAT('C ：', product_type), 
                   IF( IF(product_type = '衣服', 
                          CONCAT('A ：', product_type), NULL) 
                IS NULL AND product_type = '办公用品', 
                   CONCAT('B ：', product_type), 
            IF(product_type = '衣服',
               CONCAT('A ', product_type), 
          NULL))) AS abc_product_type
  FROM Product;
```





<div class="danger">

> 还余下了好些，可惜暂时没有机会看完了，就先学习这么多吧。来日方长，给未来多留点坑（

</div>



















