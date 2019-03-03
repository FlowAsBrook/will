---
title: mysql snippet
date: 2015-12-16 20:01:30
tags: snippet
categories: mysql
---

### 知识点

- the size of blob column

  ```
    A BLOB can be 65535 bytes (64 KB) maximum.
  If you need more consider using:
  a MEDIUMBLOB for 16777215 bytes (16 MB)
  a LONGBLOB for 4294967295 bytes (4 GB).
  ```

- join sql
  ![](https://i.stack.imgur.com/VQ5XP.png)

- string convert to timstamp

  `SELECT STR_TO_DATE('2014-05-28 11:30:10','%Y-%m-%d %H:%i:%s');`

### sql语句

- IN

确定给定的值是否与子查询或列表中的值相匹配。in在查询的时候，首先查询子查询的表，然后将内表和外表做一个笛卡尔积，然后按照条件进行筛选。所以相对内表比较小的时候，in的速度较快。 

```sql
SELECT
    *
FROM
    `user`
WHERE
    `user`.id IN (
        SELECT
            `order`.user_id
        FROM
            `order`
    )
```

以上查询使用了in语句,in()只执行一次,它查出B表中的所有id字段并缓存起来.之后,检查A表的id是否与B表中的id相等,如果相等则将A表的记录加入结果集中,直到遍历完A表的所有记录。

可以看出,当B表数据较大时不适合使用in(),因为它会B表数据全部遍历一次. 如:A表有10000条记录,B表有1000000条记录,那么最多有可能遍历10000`*`1000000次,效率很差. 再如:A表有10000条记录,B表有100条记录,那么最多有可能遍历10000*100次,遍历次数大大减少,效率大大提升。

- exists

指定一个子查询，检测行的存在。遍历循环外表，然后看外表中的记录有没有和内表的数据一样的。匹配上就将结果放入结果集中。 

```sql
select a.* from A a where exists(select 1 from B b where a.id=b.id)
```

以上查询使用了exists语句,exists()会执行A.length次,它并不缓存exists()结果集,因为exists()结果集的内容并不重要,重要的是结果集中是否有记录,如果有则返回true,没有则返回false。

当B表比A表数据大时适合使用exists(),因为它没有那么遍历操作,只需要再执行一次查询就行. 如:A表有10000条记录,B表有1000000条记录,那么exists()会执行10000次去判断A表中的id是否与B表中的id相等. 如:A表有10000条记录,B表有100000000条记录,那么exists()还是执行10000次,因为它只执行A.length次,可见B表数据越多,越适合exists()发挥效果. 再如:A表有10000条记录,B表有100条记录,那么exists()还是执行10000次,还不如使用in()遍历10000*100次,因为in()是在内存里遍历比较,而exists()需要查询数据库,我们都知道查询数据库所消耗的性能更高,而内存比较很快. 

- where

sql查询条件中`where 1=1,1=2和1=0`，这种写法，主要是为了拼凑动态的sql语句，如果使用不好会起到副作用的，是根据个人的一些习惯，是为了避免where 关键字后面的第一个词直接就是 “and”而导致语法错误，是为了后面附加and ...方便程序逻辑处理用的。 