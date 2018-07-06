---
layout: post
title:  "MySQL 索引优化"
date:   2018-07-04 19:30
categories: MySQL
permalink: /mysql/index
---

# 创建高性能 MySQL 索引

本文内容参考 《高性能MySQL》 第五章。

## 一、 索引类型

### 1.1 B-Tree 索引

B-Tree 索引是 InnoDB 和 MyISAM 引擎默认的索引类型，内部使用 B+Tree 存储。

B-Tree 索引按照你指定的索引列顺序存储，假设数据表是这样的:

```sql
CREATE TABLE people
    last_name varchar(50) not null,
    first_name varchar(5) not null,
    dob date not null,
    gender enum('m', 'f') not null,

    KEY(last_name, first_name, dob) /*这是索引列*/
);
```

索引在内部存储的其实是这个样子的：

![](../images/mysql/index/index_1.png)

可以看到，索引中按序包含 last_name, first_name, dob 三个值。

该索引可以高效的进行：

* **全值匹配**：和索引中的所有列进行匹配，即提供上例的 last_name, first_name, dob 三个值。

* **最左前缀匹配**：按照索引创建顺序等值匹配，直到遇到范围查询为止，注意不能跳过任何索引列。

* **只访问索引的查询**：SELECT 的数据列就在索引中，那么查询只会访问索引而不是真正的数据，这种优化方式称为 “索引覆盖” 优化。

---

### 1.2 HASH 索引

顾名思义哈希索引就是利用哈希算法快速定位数据的一种技术，只有 Memory 引擎显式支持，InnoDB 和 MyISAM 并不支持该索引，语法如下：

```sql
CREATE TABLE hash_index_test(
    rid int NOT NULL AUTO_INCREMENT,
    col_key int,

    PRIMARY KEY(`rid`),
    KEY USING HASH(col_key) /*哈希索引创建*/
)ENGINE=MEMORY;

```

内部使用哈希表实现，哈希表中存储每一行的指针，如果冲突则使用链表存储。和 B-Tree 索引比较，哈希索引只支持等值比较，包括 =, IN(), <=>，不支持范围查询

---

### 1.3 FULLTEXT 索引

全文（FULLTEXT）索引，原来只有 MyISAM 引擎支持，新版本的 InnoDB 也开始支持全文索引。它查找的是文本中的关键词而不是等值匹配。只能在 char, varchar, text 上建立该索引。在查询时，会计算出一个相关度值，做一个排序并选取相关度高的返回，语法如下：

```sql
CREATE TABLE fulltext_index_test(
    rid int NOT NULL AUTO_INCREMENT,
    text_col text,

    PRIMARY KEY(`rid`),
    FULLTEXT(text_col) /*全文索引创建*/
)ENGINE=MyISAM;

/*查询语法*/
SELECT * FROM fulltext_index_test WHERE MATCH(text_col) AGAINST('keyword')
```

---

## 二、索引策略

### 2.1 索引选择性

所谓索引的选择性（Selectivity），是指不重复的索引值（也叫基数，Cardinality）与表记录数 T 的比值：**Index Selectivity = Cardinality / T**。


显然选择性的取值范围为(0, 1]，选择性越高的索引价值越大。

---

### 2.2 前缀索引

---

### 2.3 多列索引

---

### 2.4 聚簇索引

---

### 2.5 覆盖索引

---
