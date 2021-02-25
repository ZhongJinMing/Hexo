---
title: Oracle-执行顺序
date: 2019-08-13 17:02:06
tags: oracle
category: 数据库
---

## Sql的执行顺序
在oracle中select语句的基本结构如下：
```SQL
1  [ WITH <common_table_expression>]
2  SELECT [DISTINCT] [TOP_SEPCIFICATION] <select_list> 
3  [ INTO new_table ]
4  FROM 
5  <table_source| 
6  <table_left> [JOIN_TYPE] JOIN <table_right> 
7  ON <join_condition> >
8  [ WHERE <search_condition> ]
9  [ GROUP BY <group_by_list> ]
10 [ HAVING <search_condition> ]
11 [ ORDER BY order_expression [ ASC | DESC ] ]
```
其中[]中的部分为可选内容，另外其中的 
`[ WITH <common_table_expression>] `
语句是用于添加其他sql行为的，例如你可以写成 
`create <newtablename> as `
来创建一个以之后的select语句检索到的内容为表内容的新表。 
而除此之外其他部分的执行顺序如下所示： 

1. 执行FROM语句，如果FROM之后接的是一个表则执行步骤（4），如果接的是一个JOIN语句则执行步骤（2）； 
2. 执行ON语句对ON之前的JOIN语句中两个表**进行裁剪**（这样可以避免直接进行JOIN得到全表的笛卡尔积而找出空间和时间的浪费）； 
3. 对（2）中裁剪得到的两张表进行JOIN操作，得到一张联合的表.如果之后还有其他JOIN语句则将得到的表作为table_left继续进行步骤（2）的操作，否则进行步骤（4）； 
4. 执行WHERE语句，根据search_condition对之前步骤得到的表进行筛选； 
5. 执行GROUP BY语句按照group_by_list对数据进行分组； 
6. 执行HAVING语句对用GROUP BY 分组后的数据进行筛选，HAVING跟WHERE语句的区别在于**WHERE一般用于对FROM语句得到的数据进行筛选**，其中的筛选条件不能是聚合函数，**而HAVING一般用于对GROUP BY语句处理后的数据进行筛选，可以使用聚合函数作为判断条件**； 
7. 执行SELECT语句，对查询到的字段进行组织； 
8. 使用DISTINCT对结果去重； 
9. 使用ORDER BY 语句对结果进行排序； 
10. 使用TOP指定要顺畅前几行的数据。