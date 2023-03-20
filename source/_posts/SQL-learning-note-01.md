---
title: SQL 学习笔记【01】
date: 2022-10-02 15:09:34
#updated: 2022-05-01 15:59:52
description: 开始入门
tags: 
    - 博文
    - SQL
    - 笔记
    - 知识
categories: 叶子的小知识
comment: true
keywords: SQL,学习,笔记,菜鸟教程
---

<img src="/images/SQL-learning-note-01/cover.jpg" width="650" alt="cover" />_题图来自网络_

作为一个只会增删查改、从来没有系统性学习数据库的小白，这些天有了一个契机来学习相关的知识，便想到以此开一篇新的学习博文，记录一些关键语句与自己的理解。

本篇笔记主要是基于[**菜鸟教程**](https://www.runoob.com/sql/sql-tutorial.html)的内容进行初步的学习。至少在快速入门这方面，菜鸟教程还从未让我失望过。

<!-- more -->

## 开始：增删查改

SQL 的全称为 Structured Query Language，下列语句均以 MySQL 语法为准。我们先假设有一个如下名为 Website 的「表」：

```
+----+--------------+---------------------------+-------+---------+
| id | name         | url                       | alexa | country |
+----+--------------+---------------------------+-------+---------+
| 1  | Google       | https://www.google.cm/    | 1     | USA     |
| 2  | 淘宝          | https://www.taobao.com/   | 13    | CN      |
| 3  | 菜鸟教程      | http://www.runoob.com/    | 4689  | CN      |
| 4  | 微博          | http://weibo.com/         | 20    | CN      |
| 5  | Facebook     | https://www.facebook.com/ | 3     | USA     |
+----+--------------+---------------------------+-------+---------+
```

---

### SELECT

以下语句用于在指定的表中选择列：

```sql
# 从指定表中选取指定列输出
SELECT column_name, column_name FROM table_name;

# 从指定表中选取所有列输出（即相当于输出整张表）
SELECT * FROM table_name;

# 从表中选取指定列，并输出其中的不同值（即去除列中重复的元素）
SELECT DISTINCT column_name, column_name FROM table_name;
```

---

### WHERE

本子句用于提取满足指定条件的数据：

```sql
SELECT column_name, column_name 
FROM table_name 
WHERE column_name operator value;
```

例如 `SELECT * FROM Websites WHERE country='CN';`，将会仅输出表的 `country` 列中值为 `CN` 的行。其后面可以接逻辑运算符号、条件判断和 `BETWEEN`、`LIKE` 和 `IN` 等关键词。

---

### OEDER BY

本子句用于对输出的结果集按照一列或多列进行排序：

```sql
SELECT column_name,column_name 
FROM table_name 
ORDER BY column_name,column_name ASC|DESC;
```

#### 升降序关键词

其中的 `ASC` 代表升序，默认如此，可不写出；而 `DESC` 代表降序，需要手动指定。

#### 多列排序

在 `ORDER BY` 后写多列的参数即可，其效果类似分别排序：
`SELECT * FROM Websites ORDER BY country,alexa;`

#### 与 HERE 之关系

如果需要使用到 `WHERE` 语句，则 `ORDER BY` 必须写到 `WHERE` 到后面；而如果没有  `WHERE` 语句，则  `ORDER BY` 放在表的后面。如：`SELECT * FROM Website WHERE country='CN' ORDER BY alexa;`。

---

### INSERT INTO

本语句用于向表中插入新的数据：

```sql
# 第一种形式无需指定要插入数据的列名，只需提供被插入的值即可：
INSERT INTO table_name
VALUES (value1,value2,value3,...);

# 第二种形式需要指定列名及被插入的值：
INSERT INTO table_name (column1,column2,column3,...)
VALUES (value1,value2,value3,...);
```

而 `id` 字段是自动更新的，表中的每条数据都有一个唯一的数字。

---

### UPDATE

本语句用于更新表中已存在的记录：

```sql
UPDATE table_name
SET column1=value1,column2=value2,...
WHERE some_column=some_value;
```

**注意：**`WHERE` 子句规定了哪条 / 哪些记录需要更新，若省略，则表中所有的列数据都会被更新！

---

### DELETE

本语句用于删去表中指定的行：

```sql
DELETE FROM table_name
WHERE some_column=some_value;
```

**注意：**与 `UPDATE` 语句类似，`WHERE` 子句指定了删除的对象，若省略，整张表中所有的数据都将被删除！
（注：表中所有的行被删除，但表的结构、属性和索引将保持不变）


