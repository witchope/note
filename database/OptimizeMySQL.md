# MySQL 优化建议

## 1. 使用查询缓存

```sql
# 查询缓存不开启
SELECT username FROM user WHERE signup_date >= CURDATE();

# 开启查询缓存
today = date("Y-m-d");
SELECT username FROM user WHERE signup_date >= ${today};
```

MySQL 的查询缓存对函数是不起作用的，我们所需要做的是用一个变量替代这个函数，从而使得缓存起作用。

## 2. 使用 EXPLAIN 你的查询语句

使用 `EXPLAIN` 语句能够分析出你的语句中影响速度的关键所在。

## 3. 为常用搜索的字段添加索引

索引不仅仅是给主键用的，也可以给常用的的搜索字段添加索引。

## 4. 优化 JOIN 字段

确保 `ON` 或者 `USING` 子句中的列上有索引。在创建索引的时候就要考虑到关联的顺序。一旦 JOIN 字段添加索引，MySQL 内部会为你优化 JOIN 语句。

## 5. 避免使用 SELECT *

从数据库读出的数据越多，相应的查询也会越慢；并且如果是独立的数据库服务器的话，也会导致网络负载增加。

## 6. 使用 ENUM 而不是 VARCHAR

ENUM 是一种比较高效的数据类型，实际上，它是以 TINYINT 类型进行存储，但看起来像字符串。因此如果数据库中某些字段有限且固定，就可以使用 ENUM 。

## 7. 通过 PROCEDURE ANALYSE() 分析表结构

一般来说，可以为数据库修改优化提供参考，不过，只有当数据量到达一定的时候才更准确。

## 8. 尽可能的使用 NOT NULL

NULL 也是需要存储空间的，且在需要数据比较时候 NULL 会让程序更复杂。

## 9. 适当冗余字段

为了避免 `JOIN` 过多表浪费性能，可以考虑适当冗余部分字段。

## 10. 越小的列速度会越快

如果一张表的数据量在可控的将来不会有大量的数据，可以优先选择 MEDIUMINT ，SMALLINT 或是更小的 TINYINT 会更经济一些。如果你不需要记录时间，使用 DATE 要比 DATETIME 好得多。

## 11. 固定长度的表会更快

如果表中的所有字段都是“固定长度”的，整个表会被认为是 “static” 或 “fixed-length”。 例如，表中没有如下类型的字段： VARCHAR，TEXT，BLOB。只要你包括了其中一个这些字段，那么这个表就不是“固定长度静态表”了，这样，MySQL 引擎会用另一种方法来处理。

## 12. 选择正确的存储引擎

在 MySQL 中有两个存储引擎 MyISAM 和 InnoDB，每个引擎都有利有弊。

MyISAM 相对比较简单的存储引擎，适合大量查询的应用；但是对于大量写入操作支持并不好，一旦 `UPDATE` 操作，就会触发表级锁，甚至无法进行读操作。

InnoDB 支持事务、行级锁以及更优秀的写入操作。但是它比 MyISAM 更慢。

## 13. 优化 OFFSET 和 LIMIT

通常我们使用 offset 和 limit 来进行分页，但当 offset 过大时性能表现可能会不如人意。
一般最简单的优化方式是索引覆盖扫描，而不是扫描所有的列。

然后根据一次关联操作再返回需要的列。在偏移量很大的时候，这样会大大提升效率：

```sql
## 优化前
SELECT film_id, description
FROM sakila.film ORDER BY title LIMIT 50, 5;

## 优化后
SELECT film.film_id, film.description
FROM sakila.film
INNER JOIN (
    SELECT film_id FROM sakila.film
    ORDER BY title LIMIT 50, 5
) AS lim USING(film_id);
```
