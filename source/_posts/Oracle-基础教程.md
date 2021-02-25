---
title: Oracle-基础教程
date: 2019-08-13 18:01:54
tags: oracle
category: 数据库
---

#### SELECT语句
1. 语法：
```SQL
SELECT
  column_1, 
  column_2, 
  ...
FROM
  table_name;
```
2. 请注意，**只能使用星号(\*)进行测试**。 在实践中，即使想要从表的所有列中检索数据，也**应该明确指定要从中查询数据的列**。
这是因为业务变化，表格未来可能会有更多或更少的列。 如果在应用程序代码中使用星号(\*)，并假定该表有一组固定的列，**但应用程序可能不处理其他不相关的列或访问已删除的列**。

#### ORDER BY子句
1. 在Oracle中，**表中是以非指定顺序存储行数据记录的**，它不管行插入数据库的顺序如何。要按列以升序或降序查询行记录，必须明确指示Oracle数据库要如何去排序。
2. 语法：
```SQL
SELECT
    column_1,
    column_2,
    column_3,
    ...
FROM
    table_name
ORDER BY
    column_1 [ASC | DESC] [NULLS FIRST | NULLS LAST],
    column_1 [ASC | DESC] [NULLS FIRST | NULLS LAST],
```
3. `NULLS FIRST`在非`NULL`值之前放置NULL值，`NULLS LAST`在非NULL值之后放置`NULL`值
4. 请注意，`ORDER BY`子句总是`SELECT`语句中的最后一个子句
5. 要对多列进行排序，可以用逗号分隔`ORDER BY`子句中的每列。
6. 不需要指定用于排序数据的列名称。如果您愿意，可以使用`ORDER BY`子句中列的位置。请参考下语句：
```SQL
SELECT name, credit_limit,address
FROM customers
ORDER BY 2 DESC, 1;
```
在这个例子中，name列的位置是1，credit_limit列的位置是2
7. 当使用非NULL值对混合`NULL`进行排序时，Oracle允许指定哪个应该首先出现。要放置`NULL`值在后面，可以使用`NULLS LAST`
8. `ORDERBY`子句可在一列上应用一个函数，例如字符串函数，数学函数等，并按函数的结果对数据进行排序。
例如，以下语句使用`ORDER BY`子句中的`UPPER()`函数来区分客户名称的大小写：
```SQL
SELECT customer_id, name
FROM customers
ORDER BY UPPER( name );
```

#### SELECT DISTINCT语句
1. 请注意，`DISTINCT`不是SQL标准的`UNIQUE`的同义词。**总是使用`DISTINCT`而不使用`UNIQUE`是一个好的习惯**。
2. `DISTINCT`将`NULL`值视为重复值。如果使用`SELECT DISTINCT`语句从具有多个`NULL`值的列中查询数据，则结果集只包含一个`NULL`值。

#### WHERE子句
1. Oracle按以下顺序评估子句：`FROM WHERE`和`SELECT`
	* **首先**，`FROM`子句指定查询数据的表。
	* **其次**，`WHERE`子句基于条件(例如`product_name ='Kingston'`过滤行记录)。
	* **第三**，`SELECT`子句选择了应该返回的列。
2. 使用比较运算符选择行记录，除了等于(=)运算符之外，Oracle还提供了下表中所示的许多其他比较运算符：

	| 编号 |         运算符          |                             描述                             |
	| :--: | :---------------------: | :----------------------------------------------------------: |
	|  1   |           `=`           |                             等于                             |
	|  2   |       `!=`, `<>`        |                            不等于                            |
	|  3   |           `>`           |                             大于                             |
	|  4   |           `<`           |                             小于                             |
	|  5   |          `>=`           |                          大于或等于                          |
	|  6   |          `<=`           |                          小于或等于                          |
	|  7   |          `IN`           |                     等于值列表中的任何值                     |
	|  8   |     `ANY/SOME/ALL`      | 将值与列表或子查询进行比较.它必须以另一个运算符(例如：`=`,`>`,`<`)作为前缀 |
	|  9   |        `NOT IN`         |                    不等于值列表中的任何值                    |
	|  10  | `[NOT] BETWEEN n AND m` |                  相当于`[Not] >= n`且`<= y`                  |
	|  11  |     `[NOT] EXISTS`      |             如果子查询返回至少一行，则返回`true`             |
	|  12  |     `IS [NOT] NULL`     |                        测试`NULL`的值                        |

#### AND和OR运算符
1. 在声明中使用多个逻辑运算符时，Oracle始终首先评估`AND`运算符。但是，可以使用括号来更改评估的顺序
2. 如果在语句中使用多个逻辑运算符，则Oracle会在评估`NOT`和`AND`运算符之后评估`OR`运算符。 但是，可以使用括号更改评估的顺序
3. 可以使用IN运算符来代替使用多个OR运算符

#### FETCH子句
1. 语法：
```SQL
[ OFFSET offset ROWS]
 FETCH  NEXT [  row_count | percent PERCENT  ] ROWS  [ ONLY | WITH TIES ]
```

2. `OFFSET`子句指定在行限制开始之前要跳过行数。`OFFSET`子句是可选的。 如果跳过它，则偏移量为0，行限制从第一行开始计算。偏移量必须是一个数字或一个表达式，其值为一个数字。偏移量遵守以下规则:
	* 如果偏移量是负值，则将其视为0
	* n如果偏移量为NULL或大于查询返回的行数，则不返回任何行
	* 如果偏移量包含一个分数，则分数部分被截断

3. `FETCH`子句指定要返回的行数或百分比。为了语义清晰的目的，您可以使用关键字`ROW`而不是`ROWS`，`FIRST`而不是`NEXT`。 例如，以下子句的行为和产生的结果相同:
	* `FETCH NEXT 1 ROWS`
	* `FETCH FIRST 1 ROW`

4. `ONLY | WITH TIES`选项

	* 仅返回`FETCH NEXT`(或`FIRST`)后的行数或行数的百分比。	
	* `WITH TIES`返回与最后一行相同的排序键。请注意，如果使用`WITH TIES`，则必须在查询中指定一个`ORDER BY`子句。如果不这样做，查询将不会返回额外的行。

#### LIKE运算符
1. LIKE运算符的语法：`expresion [NOT] LIKE pattern [ ESCAPE escape_characters ]`
2. 在上面的语法中，
	1. expression - 该表达式是一个列名称或一个表达式，要针对该模式(pattern)进行测试
	2. pattern - 该模式是在表达式中搜索的字符串。此模式包含以下通配符：
		* ％(百分号)匹配**零个或多个字符**的**任何字符串**
		* _(下划线)匹配**任何单个字符**
	3. escape_character `- escape_character`是出现在通配符前面的字符，用于指定通配符不应被解释为通配符而是常规字符。
	4. `escape_character`(如果指定)必须是一个字符，并且没有默认值
3. ESCAPE子句允许查找**包含一个或多个通配符的字符串**。  
	例如，表可能包含具有百分比字符的数据，例如折扣值，折旧率。要搜索字符串25％，可以使用ESCAPE子句，如下所示：  
	`LIKE '%25!%%' ESCAPE '!'`  
	如果不使用ESCAPE子句，则Oracle将返回字符串为25的任何行

#### JOIN语法
###### INNER JOIN
1. 查询通过基于连接谓词组合表T1和T2的列值来返回结果集。它将表T1的每一行与表T2的行进行比较，以查找满足连接谓词的所有行记录。只要通过匹配非NULL值来满足连接谓词，则T1和T2表的**每对匹配行的列值**就会被**合并到结果集中的一行中**
2. 除`ON`子句外，还可以使用`USING`子句指定在连接表时要测试哪些列的相等性  
> 请注意，`USING`子句中列出的列(如c1和c2)必须在T1和T2表中都存在(可用)

###### LEFT JOIN
1. 左连接(Left join)返回左表中的**所有行**，并从右表中返回**匹配的行**
2. ON与WHERE子句中的条件  
	以下语句查询获得订单ID为58的订单和销售员数据
```sql
SELECT
    order_id,
    status,
    employee_id,
    last_name
FROM
    orders
LEFT JOIN employees ON
    employee_id = salesman_id
WHERE
    order_id = 58;
```
现在，如果将条件从WHERE子句移动到LEFT JOIN的ON子句：
```sql
SELECT
    order_id,
    status,
    employee_id,
    last_name
FROM
    orders
LEFT JOIN employees ON
    employee_id = salesman_id
    AND order_id = 58;
```
在这种情况下，**查询将返回所有订单，但只有订单58具有与其关联的销售员数据**，   
请注意，**对于内连接，置于ON中的条件与置于WHERE子句中的条件相同**

###### CROSS JOIN子句
1. 与其他连接(如`INNER JOIN`或`LEFT JOIN`)不同，`CROSS JOIN`没有连接谓词的ON子句

###### 自连接
1. 自连接是连接表与自身的连接。自连接对比较表中的行或查询分层数据非常有用。  
	自连接使用其他连接，如内连接和左连接。 另外，它使用表别名在同一查询中为表提供不同的名称。  
> **请注意，在不使用表别名的情况下，在查询中多次引用同一个表会导致错误**。  

	以下说明了表T如何与自身连接：
```sql
SELECT
    column_list
FROM
    T t1
INNER JOIN T t2 ON
    join_predicate;
```
 请注意，除了`INNER JOIN`之外，还可以在上面的语句中使用`LEFT JOIN`

#### GROUP BY子句
1. `GROUP BY`子句在SELECT语句中用于按行或表达式的值将行组合到分组汇总的行中。 **`GROUP BY`子句为每个分组返回一行**。
2. `GROUP BY`子句出现在FROM子句之后。在有提供`WHERE`子句的情况下，`GROUP BY`子句必须放在WHERE子句之前。  
	GROUP BY子句按分组列中的值(如c1，c2和c3)对进行分组。GROUP BY子句只能包含聚合或分组的列。

#### HAVING子句
1. `HAVING`子句是SELECT语句的可选子句。**它用于过滤由`GROUP BY`子句返回的行分组**。 这就是为什么`HAVING`子句通常与`GROUP BY`子句一起使用的原因。
2. 请注意，**`HAVING`子句过滤分组的行**，而**`WHERE`子句过滤行**。这是`HAVING`和`WHERE`子句之间的主要区别。
3. `HAVING`子句为什么不能使用列的别名：  
	虽然聚合函数在`GROUP BY`之前执行，但是只有在`SELECT`执行后才能产生别名，而**`SELECT`在`GROUP BY`后执行**，同理，因为**`ORDER BY`在`SELECT`后执行，所以可以使用列的别名**

#### 子查询
1. 子查询嵌套在外部查询中。**请注意，子查询必须出现在括号内**。
2. 嵌套在`SELECT`语句的`FROM`子句中的子查询称为**内联视图**。请注意，其他RDBMS(如MySQL和PostgreSQL)使用术语**派生表**，而不是内联视图。  
	嵌套在`SELECT`语句的`WHERE`子句中的子查询称为**嵌套子查询**。
3. 子查询可以包含另一个子查询。Oracle允许在顶级查询的FROM子句中使用无限数量的子查询级别，**并在WHERE子句中使用多达255个子查询级别**。
4. 下面是子查询的主要优点：
	* 提供一种替代方法来解决查询需要复杂联接和联合的数据
	* 使复杂的查询更具可读性
	* 允许以一种可以隔离每个部分的方式来构建复杂的查询

#### Exists运算符
1. Oracle Exists运算符是返回true或false的布尔运算符。EXISTS运算符通常与子查询一起使用来测试行的存在：  
```sql
SELECT
    *
FROM
    table_name
    WHERE
        EXISTS(subquery);
```
如果子查询返回任何行，则`EXISTS`运算符返回true，否则返回false。 另外，当子查询返回第一行，`EXISTS`操作符终止子查询的处理。
2. 如果`WHERE`子句使用`EXISTS`运算符来检索子查询的客户，则返回相关的行。
> 请注意，Oracle**会忽略子查询中的选择列表**，因此您可以使用**任何列，字面值，表达式**等。在上面的查询中是使用了文字数字值： 1。
> 3. Oracle EXISTS与IN  
> `EXISTS`操作符在子查询返回第一行时停止扫描行，因为它可以确定结果，而`IN`操作符必须扫描子查询返回的所有行以结束结果。  
> 另外，`IN`子句不能与NULL值进行任何比较，但`EXISTS`子句可以将所有值与NULL值进行比较。例如，第一个语句不返回行记录，而第二个语句返回customers表中的所有行：

	\-- 第一个语句
	```sql
	SELECT
	    *
	FROM
	    customers
	WHERE
	    customer_id IN(NULL);
	```
	\-- 第二个语句
	```sql
	SELECT
	    *
	FROM
	    customers
	WHERE
	    EXISTS (
	        SELECT
	            NULL
	        FROM
	            dual
	    );
	```
	通常，**当子查询的结果集较大时，`EXISTS`运算符比`IN`运算符更快。相比之下，当子查询的结果集很小时，`IN`运算符比`EXISTS`运算符更快**。

4. NOT EXISTS与NOT IN
	以下语句对子查询使用`IN`运算符：
```sql
SELECT
 *
FROM
    table_name
WHERE
    id IN(subquery);
```
	假设子查询返回四个值：1,2,3和`NULL`。可以重写上面的整个查询，如下所示：
```sql
SELECT
    *
FROM
    table_name
WHERE
    id = 1
    OR id = 2  
    OR id = 3
    OR id = NULL;
```
	下面的表达式**总是返回一个`NULL`值**，因为**`NULL`值不能和任何东西比较**。  
	`id = NULL`  
	因此，**如果子查询的结果集中的任何行为`NULL`，则以下表达式将返回`NULL`值**。  
	**`id NOT IN (subquery)`**  
	相比之下，`NULL`不会影响`NOT EXIST`运算符的结果，因为`NOT EXISTS`运算符仅检查子查询中是否存在行：
```sql
SELECT
    *
FROM
    table_name
WHERE
    NOT EXISTS(subquery);
```
	总而言之，当存在`NULL`值时，`NOT EXISTS`和`NOT IN`的行为会有所不同。

#### ANY运算符
1. Oracle `ANY`运算符用于将值与子查询返回的值或结果集列表进行比较。下面举例说明ANY运算符与列表或子查询一起使用时的语法：  
`operator ANY ( v1, v2, v3)`   
`operator ANY ( subquery)`   
在这个语法中：
	* **`ANY`运算符前面必须有一个运算符**，例如：=，!=，>，>=，<，<=。
	* **列表或子查询必须用圆括号包围**。
2. 如果使用`ANY`运算符将一个值与子查询返回的结果集进行比较，则Oracle**使用`EXISTS`运算符将查询转换为等效的查询，而不使用`ANY`运算符**。
3. 请注意，**如果子查询不返回任何行，则以下条件的计算结果为false**：  
`operator ANY (subquery)`  
因此，整个查询不返回行
4. 在Oracle中，**`SOME`和`ANY`的行为完全相同**，因此它们完全可以互换

#### ALL操作符
1. Oracle `ALL`操作符用于将值与子查询返回的值列表或结果集进行比较。  
以下显示了与列表或子查询一起使用的`ALL`运算符的语法：  
`operator ALL ( v1, v2, v3)`  
`operator ALL ( subquery)`  
在这个语法中，  
	* **`ALL`运算符前面必须有一个运算符**，例如：=，!=,>，>=，<，<=，后跟一个列表或子查询。
	* 列表或子查询必须用圆括号包围。
2. 使用`ALL`运算符将值与列表进行比较时，Oracle将初始条件扩展到列表的所有元素，并使用`AND`运算符将它们组合在一起，如下所示：
```sql
SELECT
    *
FROM
    table_name
WHERE
    c > ALL (
        v1,
        v2,
        v3
    );

--  以上语句转换为 ALL 运算符后

SELECT
    *
FROM
    table_name
WHERE
    c > v1
    AND c > v2
    AND c > v3;
```
如果使用`ALL`运算符将值与子查询返回的结果集进行比较，则Oracle执行两步转换，如下所示：
```sql
SELECT product_name,
       list_price
FROM products
WHERE list_price > ALL
    ( SELECT list_price
     FROM products
     WHERE category_id = 1 )
ORDER BY product_name;

-- 1st step: transformation that uses ANY

SELECT product_name,
       list_price
FROM products p1
WHERE NOT( p1.list_price <= ANY
            (SELECT list_price
             FROM products p2
             WHERE category_id = 1 ))
ORDER BY product_name; 

-- 2nd step: transformation that eliminates ANY

SELECT product_name,
       list_price
FROM products p1
WHERE NOT EXISTS
    (SELECT p2.list_price
     FROM products p2
     WHERE p2.category_id = 1
       AND p2.list_price >= p1.list_price )
ORDER BY product_name;
```
3. 如果子查询不返回行，则以下条件的计算结果为true：  
`operator ALL (subquery)`  
这意味着在WHERE子句中使用上述条件的查询将返回所有行，以防子查询返回任何行。  
```sql
SELECT
    *
FROM
    table_name
WHERE
    col operator ALL(subquery);
```

#### UNION运算符
1. `UNION`运算符是一个集合运算符，它将两个或多个SELECT语句的结果集组合到一个结果集中。  
	以下说明了组合两个查询的结果集的UNION运算符的语法：
```sql
SELECT
    column_list_1
FROM
    T1
UNION 
SELECT
    column_list_1
FROM
    T2;
```
在此声明中，column_list_1和column_list_2**必须具有相同顺序的相同列数**。 另外，**对应列的数据类型必须是相同的数据类型组**，例如数字或字符。
2. 要对由`UNION`运算符返回的结果集进行排序，可以将`ORDER BY`子句添加到最后一个`SELECT`语句中
3. `UNION`运算符**删除(消除)重复的行**，`UNION ALL`操作符**不会删除重复的行**
4. `UNION`将结果集放置在另一个上面，这意味着它将**垂直地附加结果集**。但是，诸如**`INNER JOIN`或`LEFT JOIN`的连接将结果集水平组合**。

#### INTERSECT运算符
1. `INTERSECT`运算符来比较两个查询，并返回由两者相交的行
2. 与UNION运算符相似，使用INTERSECT运算符时必须遵循以下规则：
	* **两个查询中列的数量和顺序必须相同**。
	* 相应列的数据类型必须处于**相同的数据类型组中**，例如数字或字符。		

#### MINUS操作符
1. `MINUS`运算符比较两个查询，并返回第一个查询中但不是第二个查询输出的行。 换句话说，`MINUS`运算符**从一个结果集中减去另一个结果集**。
2. 与UNION和INTERSECT操作符类似，上面的查询必须符合以下规则：
	* **列数和它们的顺序必须匹配一致**。
	* 相应列的数据类型必须处于**相同的数据类型组中**，例如数字或字符。

#### INSERT语句
1. 要将新行插入到表中，请按如下方式使用`INSERT`语句：
```sql
INSERT INTO table_name (column_1, column_2, column_3, ... column_n)
VALUES( value_1, value_2, value_3, ..., value_n);
```
在这个声明语句中：
	* 首先，指定要插入的表的名称 - table_name。
	* 其次，在圆括号内指定逗号分隔列名的列表。
	* 第三，指定对应于列列表的逗号分隔值列表。

	如果值列表与表列具有相同的顺序，则可以跳过不指定列的列表，但**这不被认为是一种好的做法**：
	```sql
	INSERT INTO table_name
	VALUES (value_1, value_2, value_3, ..., value_n);
	```
	如果从`INSERT`语句中排除一列或多列，则必须指定列列表，因为Oracle需要它与值列表中的值相匹配。   
	在`INSERT`语句中省略的列**将使用缺省值(如果可用)或者如果列接受`NULL`值，则使用`NULL`值**。

#### INSERT INTO SELECT语句
1. 有时候，想要将从其它表中选择数据并将其插入到另一个表中。要做到这一点，可使用`INSERT INTO SELECT`语句，如下所示：
```sql
INSERT INTO target_table (col1, col2, col3)
SELECT col1,
       col2,
       col3
FROM source_table
WHERE condition;
```
`INSERT INTO SELECT`语句**要求源表和目标表匹配数据类型**。

#### Insert All语句
1. Oracle提供了两种类型的多项式插入语句：无条件的和有条件的。
2. 无条件的`INSERT ALL`语句
将多行插入到表中，要将多行插入到表中，请使用以下`INSERT ALL`语句：
```sql
INSERT ALL
    INTO table_name(col1,col2,col3) VALUES(val1,val2, val3)
    INTO table_name(col1,col2,col3) VALUES(val4,val5, val6)
    INTO table_name(col1,col2,col3) VALUES(val7,val8, val9)
Subquery;
```
在这个语句中，每个值表达式值：val1，val2或val3必须引用由子查询的选择列表返回的列对应的值。  
如果要使用文字值而不是子查询返回的值，请使用以下子查询：  
`SELECT * FROM dual;`
3. 有条件的`INSERT ALL`语句  
条件多项插入语句允许根据指定的条件将行插入到表中。  
以下显示了条件多项插入语句的语法：  
```sql
INSERT [ ALL | FIRST ]
    WHEN condition1 THEN
        INTO table_1 (column_list ) VALUES (value_list)
    WHEN condition2 THEN 
        INTO table_2(column_list ) VALUES (value_list)
    ELSE
        INTO table_3(column_list ) VALUES (value_list)
Subquery
```
如果指定了`ALL`关键字，则Oracle将在WHEN子句中评估每个条件。如果条件评估/计算为true，则Oracle执行相应的`INTO`子句。  
但是，当指定`FIRST`关键字时，对于由子查询返回的每一行，Oracle都会从`WHEN`子句的上下方向评估每个条件。 如果Oracle发现条件的计算结果为true，则执行相应的`INTO`子句并跳过给定行的后续`WHEN`子句。  
请注意，单条件多项式插入语句最多可以有127个`WHEN`子句。
4. `INSERT ALL`限制  
Oracle多表插入语句受以下主要限制：  
	* 它可以用来将数据只插入到表中，而不是视图或物化视图。  
	* 它不能用来将数据插入到远程表中。  
	* 所有`INSERT INTO`子句中的列总和不得超过999。  
	* 一个表集合表达式不能在一个多表插入语句中使用。  
	* 多表插入语句的子查询不能使用序列。

#### Delete语句
1. 要从表中删除一行或多行，请按如下所示使用`DELETE`语句：
	```sql
	DELETE
	FROM
	    table_name
	WHERE
	    condition;
	```
	在这个语句中，
	* 首先，指定要从中删除数据的表的名称。
	* 其次，通过使用`WHERE`子句中的条件指定应该删除哪些行。如果省略了`WHERE`子句，则`DELETE`语句将从表中删除所有行。

	请注意，**使用TRUNCATE TABLE语句删除大型表中的所有行会更快更高效**。

2. 级联删除  
在现实应用中，经常从与其他表中的行具有外键关系的表中来删除行记录。  
例如，想要从orders表中删除id为1的销售订单，并从order_items表中删除与订单id是1关联的所有行项目。 通常可以发出两个`DELETE`语句，如下所示：
```sql
-- 第一条
DELETE FROM
    orders
WHERE
    order_id = 1;

-- 第二条
DELETE FROM
    order_items
WHERE
    order_id = 1;

COMMIT WORK;
```
请注意，**`COMMIT WORK`语句确保两个`DELETE`语句以全部或全部方式执行**，以防第一条语句成功，但第二个`DELETE`语句失败时在order_items表中的孤行。  
但是，**如果知道如何正确设置表的约束，那么这是不必要的**。  
在这种情况下，当创建order_items表时，可以使用`DELETE CASCADE`选项定义一个外键约束，如下所示：
```sql
CREATE TABLE order_items 
(
    order_id   NUMBER( 12, 0 )                                , 
    -- other columns
    -- ...
    CONSTRAINT fk_order_items_orders 
    FOREIGN KEY( order_id ) 
    REFERENCES orders( order_id ) 
    ON DELETE CASCADE
);
```
通过这样做，每当从orders表中删除一行时，例如：
```sql
DELETE
FROM
    orders
WHERE
    order_id = 1;
```
order_items表中order_id为1的所有行也**被数据库系统自动删除**

#### Merge语句
1. `MERGE`语句从一个或多个源表中选择数据并更新或将其插入到目标表中。 `MERGE`语句可指定一个条件来确定是更新数据还是将数据插入到目标表中。  
以下说明了`MERGE`语句的语法：
```sql
MERGE INTO target_table 
USING source_table 
ON search_condition
    WHEN MATCHED THEN
        UPDATE SET col1 = value1, col2 = value2,...
        WHERE <update_condition>
        [DELETE WHERE <delete_condition>]
    WHEN NOT MATCHED THEN
        INSERT (col1,col2,...)
        values(value1,value2,...)
        WHERE <insert_condition>;
```
下面来仔细看看上面`MERGE`语句的语法：  
	* 首先，指定要在`INTO`子句中更新或插入的目标表(target_table)。
	* 其次，指定要更新或插入`USING`子句中的数据源(source_table)。
	* 第三，指定合并操作在`ON`子句中更新或插入的搜索条件。

2. 对于目标表中的每一行，Oracle都会评估搜索条件：  
	* 如果结果为true，则Oracle使用源表(source_table)中的相应数据更新该行。  
	* 如果任何行的结果为false，则Oracle将源表(source_table)中相应的行插入到目标表(target_table)中。

	当想要在单个操作中组合多个`INSERT`，`UPDATE`和`DELETE`语句时，`MERGE`语句变得很方便。  
	因为`MERGE`是确定性语句，所以不能在同一个`MERGE`语句中多次更新目标表的同一行。  
	可以将一个可选的`DELETE WHERE`子句添加到`MATCHED`子句中，以在合并操作之后进行清理。`DELETE`子句只删除目标表中与`ON`和`DELETE WHERE`子句匹配的行。

3. MERGE前提条件  
	要执行MERGE语句，**必须在源表上具有INSERT和UPDATE对象权限**。  
	如果使用DELETE子句，则还必须在目标表上具有DELETE对象特权。

#### Create Table语句
1. 要在Oracle数据库中创建一个新表，可以使用`CREATE TABLE`语句。下面说明了`CREATE TABLE`语句的基本语法：
```sql
CREATE TABLE schema_name.table_name (
    column_1 data_type column_constraint,
    column_2 data_type column_constraint,
    ...
    table_constraint
 );
```
在上面这个语法中，
	* 首先，在CREATE TABLE子句中，指定新表所属的表名和模式名称。
	* 其次，在圆括号内列出所有列。 如果一个表有多个列，则需要用逗号分隔每个列的定义。列定义包括列名	* ，后跟它的数据类型，例如NUMBER，VARCHAR2和列约束，如NOT NULL，主键，约束检查等。
	* 第三，如果适用，例如：主键，外键，约束检查，则为表添加约束。

> 请注意用户权限问题，必须具有CREATE TABLE系统特权才能在模式中创建新表，并使用CREATE ANY TABLE系统特权在其他用户的模式中创建新表。除此之外，新表的所有者必须具有包含新表或UNLIMITED TABLESPACE系统特权的表空间的配额。

#### Drop Table语句
1. 要将表移动到回收站或将其从数据库中完全删除，请使用`DROP TABLE`语句：
```sql
DROP TABLE schema_name.table_name
[CASCADE CONSTRAINTS | PURGE];
```
在这个语句中：
	* 首先，指出要在`DROP TABLE`子句之后删除的表及其模式。如果不明确指定模式名称，则该语句假定将从模式中删除该表。
	* 其次，指定`CASCADE CONSTRAINTS`子句删除引用表中主键和唯一键的所有参照完整性约束。 	如果存在这种引用完整性约束，并且不使用此子句，Oracle将返回错误并停止删除表。
	* 第三，如果想删除表格并且一次释放与之关联的空间，指定`PURGE`子句。 通过使用`PURGE`子句，Oracle不会将表及其依赖对象放入回收站。

> 请注意，PURGE子句不允许您回滚或恢复删除的表。 因此，如果不希望敏感数据出现在回收站中，这很有用。

#### Alter Table Modify列语句
1. 要更改表中列的定义，请按如下所示使用`ALTER TABLE MODIFY`列语法：
```sql
ALTER TABLE table_name 
MODIFY column_name action;
```
语句很直接。要修改表的列，需要指定要执行的列名，表名和操作。  
Oracle允许执行多种操作，但以下是主要常用的操作：  
	* 修改列的可见性
	* 允许或不允许NULL值
	* 缩短或扩大列的大小
	* 更改列的默认值
	* 修改虚拟列的表达式

	要修改多个列，请使用以下语法：
```sql
	ALTER TABLE table_name
	MODIFY (
	    column_name_1 action,
	    column_name_2 action,
	    ...
	);
```

2. 修改列的可见性  
	可以将表列定义为不可见或可见。**不可见列不可用于查询**，但是，可以通过**在查询中显式指定不可见列来查询**  
	默认情况下，表列是可见的。可以在创建表或使用`ALTER TABLE MODIFY`列语句时定义不可见列。  
	例如，以下语句使full_name列不可见：
```sql
ALTER TABLE accounts 
MODIFY full_name INVISIBLE;
```
要将列从不可见变为可见，请使用以下语句：
```sql
ALTER TABLE accounts 
MODIFY full_name VISIBLE;
```
3. 允许或不允许null示例  
	以下语句将email列更改为接受非空(not null)值：
```sql
ALTER TABLE accounts 
MODIFY email VARCHAR2( 100 ) NOT NULL;
```
但是，Oracle发出以下错误：  
`SQL Error: ORA-02296: cannot enable (OT.) - null values found`  
因为当将列从可为null改为not null时，**必须确保现有数据符合新约束**(也就是说，如果原来数据中NULL是不行的)。  
为了解决这个问题，首先更新email列的值，然后改变email列的约束
4. 扩大或缩短列示例的大小
```sql
ALTER TABLE accounts 
MODIFY phone VARCHAR2( 24 );
```
要**缩短**列的大小，**请确保列中的所有数据都符合新的大小**
5. 修改虚拟列  
	可以更改虚拟列full_name的表达式，如下所示：
```sql
ALTER TABLE accounts 
MODIFY full_name VARCHAR2(52) 
GENERATED ALWAYS AS (last_name || ', ' || first_name);
```
6. 修改列的默认值
添加一个名为status的新列，默认值为1到accounts表中。参考以下语句 -
```sql
ALTER TABLE accounts
ADD status NUMBER( 1, 0 ) DEFAULT 1 NOT NULL ;
```
当执行了该语句，就会将accounts表中的所有现有行的status列中的值设置为1

#### TRUNCATE TABLE语句
1. 如果要从表中删除所有数据，可以使用不带WHERE子句的DELETE语句，如下所示：  
`DELETE FROM table_name;`  
对于有少量行记录的表，DELETE语句做得很好。 但是，当拥有大量行记录的表时，使用DELETE语句删除所有数据**效率并不高**。  
Oracle引入了`TRUNCATE TABLE`语句，**用于删除大表中的所有行**。
2. TRUNCATE TABLE语句的语法：
```sql
TRUNCATE TABLE schema_name.table_name
[CASCADE]
[[ PRESERVE | PURGE] MATERIALIZED VIEW LOG ]]
[[ DROP | REUSE]] STORAGE ]
```
3. `TRUNCATE TABLE CASCADE`语句删除table_name表中的所有行，并递归地截断链中的关联表。
> 请注意，`TRUNCATE TABLE CASCADE`语句需要使用`ON DELETE CASCADE`子句定义的外键约束才能工作。

	通过`MATERIALIZED VIEW LOG`子句，可以指定在表上定义的物化视图日志是否在截断表时被保留或清除。 **默认情况下，物化视图日志被保留**。  
	
	`STORAGE`子句允许选择删除或重新使用由截断行和关联索引(如果有的话)释放的存储。 **默认情况下，存储被删除**。
> 请注意，要截断表，它必须在您自己的模式中，或者必须具有DROP ANY TABLE系统权限。

#### Rename表语句
1. 要重命名表，可以使用以下Oracle RENAME表语句，如下所示：
```sql
RENAME table_name TO new_name;
```
在RENAME表语句中：
	* 首先，指定将要重命名的表名称。
	* 其次，指定新的表名。新名称不能与同一模式中的另一个表相同。

	> 请注意，一旦执行了RENAME语句，就**不能回滚**了。
	
	当重命名表时，Oracle自动将旧表上的索引，约束和授权转移到新表上。 另外，它使依赖重命名表(原表)的所有对象失效，如**视图，存储过程，函数和同义词**。

#### 数据类型
1. Oracle具有下表中所示的一些内置数据类型：

	| 代码 | 数据类型                                                     |
	| :--- | :----------------------------------------------------------- |
	| 1    | VARCHAR2(size [BYTE / CHAR])                                 |
	| 1    | NVARCHAR2(size)                                              |
	| 2    | NUMBER[(precision [, scale])]                                |
	| 8    | LONG                                                         |
	| 12   | DATE                                                         |
	| 22   | BINARY_DOUBLE                                                |
	| 23   | RAW(size)                                                    |
	| 34   | LONG RAW                                                     |
	| 69   | ROWID                                                        |
	| 96   | CHAR [(size [BYTE / CHAR])]                                  |
	| 96   | NCHAR[(size)]                                                |
	| 112  | CLOB                                                         |
	| 112  | NCLOB                                                        |
	| 113  | BLOB                                                         |
	| 114  | BFILE                                                        |
	| 180  | TIMESTAMP [(fractional_seconds)]                             |
	| 181  | TIMESTAMP [(fractional_seconds)] WITH TIME ZONE              |
	| 182  | INTERVAL YEAR [(year_precision)] TO MONTH                    |
	| 183  | INTERVAL DAY [(day_precision)] TO SECOND[(fractional_seconds)] |
	| 208  | UROWID [(size)]                                              |
	| 231  | TIMESTAMP [(fractional_seconds)] WITH LOCAL TIMEZONE         |

	> 每种数据类型都有由Oracle内部管理的代码。要查找列中值的数据类型代码，请使用`DUMP()`函数。

2. 字符数据类型  
	字符数据类型由:`CHAR`，`NCHAR`，`VARCHAR2`，`NVARCHAR2`和`VARCHAR`组成。`NCHAR`和`NVARCHAR2`数据类型用于存储Unicode字符串。  
	**固定长度字符数据类型**是`CHAR`，`NCHAR`，**可变长度字符数据类型**是：`VARCHAR2`和`NVARCHAR2`。  
	**`VARCHAR`是`VARCHAR2`的同义词**。但是，**不建议使用`VARCHAR`**，因为Oracle将来可能会更改它的语义。  
	对于字符数据类型，可以用字节或字符指定它们的大小。
3. 数字数据类型  
	`NUMBER`数据类型具有精度p和比例s。精度范围从1到38，范围从-84到127。
	如果不指定精度，那么该列可以存储包括定点数和浮点数的值。比例的默认值是零。

4. 日期时间和时间间隔数据类型  
	日期时间数据类型是`DATE`，`TIMESTAMP`，`TIMESTAMP WITH TIME ZONE`和`TIMESTAMP WITH LOCAL TIME ZONE`。 **日期时间数据类型的值是日期时间**。  
	区间数据类型为`INTERVAL YEAR TO MONTH`和`INTERVAL DAY TO SECOND`。 **区间数据类型的值是区间**。
5. `RAW`和`LONG RAW`数据类型  
	`RAW`和`LONG RAW`数据类型用于存储二进制数据或字节字符串，例如文档，声音文件和视频文件的内容。  
	`RAW`数据类型最多可以存储2000字节，而`LONG RAW`数据类型最多可以存储2GB。
6. `BFILE`数据类型  
	BFILE数据类型将定位器存储到位于数据库之外的大型二进制文件。 定位器由目录和文件名组成。
7. `BLOB`数据类型
	`BLOB`代表二进制大对象。可以使用`BLOB`数据类型来存储最大大小为(4千兆字节 - 1)\*(数据库块大小)的二进制对象。
8. `CLOB`数据类型  
	`CLOB`代表字符大的对象。使用`CLOB`存储单字节或多字节字符，最大大小为(4千兆字节 - 1)\*(数据库块大小)。
> 请注意，CLOB支持固定字符集和变量字符集。

9. `NCLOB`数据类型
`NCLOB`与`CLOB`类似，不同的是它可以存储Unicode字符。
10. UROWID数据类型
`UROWID`主要用于`ROWID`伪列返回的值。它的值是基本的64个字符串，它们表示表中行的唯一地址。
11. 数据类型：Oracle和ANSI  
	将ANSI数据类型用于列定义时，Oracle将根据以下映射表将Oracle转换为其相应的数据类型：

	| ANSI SQL数据类型                                             | Oracle SQL数据类型 |
	| :----------------------------------------------------------- | :----------------- |
	| CHARACTER(n), CHAR(n)                                        | CHAR(n)            |
	| CHARACTER VARYING(n), CHAR VARYING(n)                        | VARCHAR2(n)        |
	| NATIONAL CHARACTER(n), NATIONAL CHAR(n), NCHAR(n)            | NCHAR(n)           |
	| NATIONAL CHARACTER VARYING(n), NATIONAL CHAR VARYING(n), NCHAR VARYING(n) | NVARCHAR2(n)       |
	| NUMERIC(p,s), DECIMAL(p,s) (a)                               | NUMBER(p,s)        |
	| INTEGER, INT, SMALLINT                                       | NUMBER(38)         |
	| FLOAT (b), DOUBLE PRECISION (c), REAL (d)                    | NUMBER             |

#### Number数字类型
1. `NUMBER`数据类型用于存储可能为负值或正值的数值。以下说明了`NUMBER`数据类型的语法：
```sql
NUMBER[(precision [, scale])]
```
`NUMBER`数据类型具有以下精度和尺度。
	* 精度是一个数字中的位数。 范围从1到38。
	* 尺度是数字中小数点右侧的位数。 范围从-84到127。

	例如，数字1234.56的精度是6，尺度是2。所以要存储这个数字，需要定义为：NUMBER(6,2)。  
	精度和尺度都是十进制数字，可选。 如果跳过精度和小数位，Oracle**使用数字的最大范围和精度。**

2. 请注意，如果在NUMBER(p，s)列中插入数字，并且**数字超过精度p，则Oracle将发出错误**。 但是，如果**数量超过尺度s，则Oracle将对该值进行四舍五入**。
3. NUMBER数据类型别名

| ANSI数据类型 | Oracle NUMBER数据类型 |
| :----------- | :-------------------- |
| INT          | NUMBER(38)            |
| SMALLINT     | NUMBER(38)            |
| NUMBER(p,s)  | NUMBER(p,s)           |
| DECIMAL(p,s) | NUMBER(p,s)           |

> 请注意，`INT`，`SMALLINT`，`NUMERIC`和`DECIMAL`只是别名。它们不是真正的数据类型。 Oracle在内部将这些别名映射到相应的NUMBER数据类型。

#### Float类型
1. `FLOAT`数据类型是`NUMBER`数据类型的子类型。 其主要目的是促进与`ANSI SQL FLOAT`数据类型的兼容。
2. 以下显示`FLOAT`数据类型的语法：
```sql
FLOAT(p)
```
我们只能指定`FLOAT`数据类型的精度。不能指定尺度，因为Oracle数据库从数据中解析尺度的。 `FLOAT`的最大精度是126。
3. 在`FLOAT`中，精度是二进制位，而在`NUMBER`中精度是十进制数。可以使用以下公式在二进制和十进制精度之间进行转换：
```sql
P(d) = 0.30103 * P(b)
```
根据这个公式，因二进制精度的最大126位数，**大致等于十进制数位数的38位数**。
4. 为了使它与`SQL ANSI FLOAT`兼容，Oracle提供了一些别名，如下表所示：

	| ANSI SQL FLOAT   | Oracle FLOAT |
	| :--------------- | :----------- |
	| FLOAT            | FLOAT(126)   |
	| REAL             | FLOAT(63)    |
	| DOUBLE PRECISION | FLOAT(126)   |

	例如，不使用`FLOAT(63)`数据类型，可以使用`REAL`别名代替。

#### Char类型
1. `CHAR`数据类型用于存储**固定长度**的字符串。 CHAR数据类型可以存储1到2000字节的字符串。
2. 要定义一个`CHAR`列，需要用字节或字符来指定一个字符串长度，如下所示：
```sql
CHAR(length BYTE)
CHAR(length CHAR)
```
如果没有明确指定BYTE或CHAR，那么Oracle会默认使用`BYTE`。  
如果像下面的例子那样不指定长度，那么**长度的默认值是1**。
```sql
column_name CHAR
```
插入或更新固定长度字符串列时，Oracle将字符存储为固定长度数据。 这意味着如果存储的值的长度小于列中定义的最大长度，则Oracle**将空格填充到最大长度的字符串**。 **如果插入一个长度大于列的值，Oracle会返回一个错误**。

	> **Oracle使用空格填充后来比较CHAR值**。

3. 在比较长度不等的字符类型的字符串时，Oracle使用非空格字符填充语义。   
要使其工作，需要使用`RTRIM()`函数从`CHAR`数据中去除空格，然后将其与输入字符串进行比较

#### NChar类型
1. `NCHAR`数据类型用于存储**固定长度的Unicode字符数据**。`NCHAR`的字符集只能是AL16UTF16或UTF8，在数据库创建时**指定为国家字符集**。
2. 当使用NCHAR列创建表时，NCHAR列的最大大小始终在字符长度语义中，例如：
```sql
CREATE TABLE nchar_demo (
    description NCHAR(10)
);
```
在本例中，description列的最大长度是10个字符。对于NCHAR列的最大大小，不可能使用字节长度，如下所示：
```sql
description NCHAR(10 BYTE) -- not possible
```
NCHAR列的最大字节长度取决于当前的国家字符集。 它是每个字符中**最大字符长度和最大字节数的乘积**。  
要查找当前的国家字符集，请使用以下语句：
```sql
SELECT
    *
FROM
    nls_database_parameters
WHERE
    PARAMETER = 'NLS_NCHAR_CHARACTERSET';
```
`AL16UTF16`字符集使用2个字节存储一个字符，所以description列的最大字节长度为20个字节。  
> Oracle将NCHAR列的**最大长度限制为2000字节**。 这意味着一个NCHAR列只能容纳2000字符的1字节字符或1000个字符的2字节字符。

3. NCHAR与CHAR比较/区别  
首先，`NCHAR`的最大长度**只在字符长度语义上**，而`CHAR`的最大长度可以是**字符长度或字节长度语义**。  
其次，`NCHAR`将字符存储在国家默认字符集中，而`CHAR`将字符存储在默认字符集中。  
以下语句返回由CHAR使用的默认字符集和由NCHAR使用的默认国家字符集：
```sql
SELECT
    *
FROM
    nls_database_parameters
WHERE
    PARAMETER IN(
        'NLS_CHARACTERSET',
        'NLS_NCHAR_CHARACTERSET'
    );
```

#### Varchar2类型
1. 要存储可变长度的字符串，可以使用`VARCHAR2`数据类型。 `VARCHAR2`列可以存储1到4000字节的值。 这意味着对于单字节字符集，**最多可以在`VARCHAR2`列中存储4000个字符**。
2. 当使用VARCHAR2列创建表时，**必须指定最大字符串长度(以字节为单位)**：
```sql
VARCHAR2(max_size BYTE)
```
或以字符
```sql
VARCHAR2(max_size CHAR)
```
默认情况下，**如果没有在max_size之后显式指定BYTE或CHAR，则Oracle使用BYTE**。 换句话说，VARCHAR2(N)列最多可以容纳N个字节的字符。  
如果存储大小超过`VARCHAR2`列最大长度的字符串，则Oracle发出错误。
2. 如果在`VARCHAR2(20)`列中存储10个字符，则Oracle仅使用10个字节进行存储，而不是20个字节。 因此，使用`VARCHAR2`数据类型可以帮助您节省表使用的空间。  
**比较`VARCHAR2`值时，Oracle使用非填充比较语义**。
3. 从Oracle 12c开始，可以为`VARCHAR2`数据类型最大长度为：32767。 Oracle使用`MAX_STRING_SIZE`参数来控制最大长度。 如果`MAX_STRING_SIZE`是`STANDARD`，则`VARCHAR2`的最大大小是4000字节。 如果`MAX_STRING_SIZE`为`EXTENDED`，则`VARCHAR2`的大小限制为32767。  
要获取`MAX_STRING_SIZE`参数的值，请使用以下查询：
```sql
SELECT
    name,
    value
FROM
    v$parameter
WHERE
    name = 'max_string_size';
```

#### Date类型
1. `DATE`数据类型允许以一秒的精度存储包括日期和时间的时间点值。  
`DATE`数据类型存储年份(包括世纪)，月份，日期，小时数，分钟数和秒数。 它的范围从公元前4712年1月1日到公元9999年12月31日(共同时代)。 默认情况下，如果未明确使用BCE，则Oracle使用CE日期条目。
2. Oracle数据库有其自己的专用格式来存储日期数据。它**使用7个字节的固定长度的字段**，每个字段对应于世纪，年，月，日，时，分和秒来存储日期数据。
3. Oracle日期格式  
输入和输出的标准日期格式是`DD-MON-YY`，例如由`NLS_DATE_FORMAT`参数的值表示为：
`01-JAN-17`。   
以下语句显示了NLS_DATE_FORMAT参数的当前值：
```sql
SELECT
  value
FROM
  V$NLS_PARAMETERS
WHERE
  parameter = 'NLS_DATE_FORMAT';
```
在Oracle数据库系统中，NLS_DATE_FORMAT的值是：
```sql
DD-MON-RR
```
以下语句通过使用SYSDATE函数以标准日期格式返回当前日期。
```sql
SELECT
  sysdate
FROM
  dual;
```
4. 假设想要将标准日期格式更改为`YYYY-MM-DD`，那么可以使用`ALTER SESSION`语句来更改`NLS_DATE_FORMAT`参数的值，如下所示：
```sql
ALTER SESSION SET NLS_DATE_FORMAT = 'YYYY-MM-DD';
```
5. 使用`TO_CHAR()`函数格式化日期
`TO_CHAR()`函数采用DATE值作为参数，根据指定的格式对其进行格式化，并返回一个日期字符串。  
例如，要以特定的格式显示当前的系统日期，请按如下所示使用`TO_CHAR()`函数：
```sql
SELECT
  TO_CHAR( SYSDATE, 'MM/DD/YYYY' )
FROM
  dual;
```
6. 将字符串转换为日期  
由于Oracle使用内部格式来存储DATE数据，因此在将日期值存储到日期列之前，通常必须将字符串转换为日期值。  
要转换不是标准格式的日期值，可以使用带有格式字符串的`TO_DATE()`函数。   
以下示例使用TO_DATE()函数将字符串“August 01，2017”转换为相应的日期：
```sql
-- alter session set nls_language='SIMPLIFIED CHINESE';
SELECT
  TO_DATE('2018-10-21', 'YYYY-MM-DD' )
FROM
  dual;
```
7. 日期文字
除了使用TO_DATE()函数之外，还可以使用以下语法将日期值指定为字符串文字：
```sql
DATE 'YYYY-MM-DD'
```
请注意，要使用日期作为文字，必须使用公历。 以下示例显示2017年8月1日的ANSI日期字面值：
```sql
DATE '2017-08-01'
```
ANSI日期文字没有时间部分，**必须采用确切格式('YYYY-MM-DD')**。 如果要将时间数据包含在日期值中，则必须使用上面介绍的`TO_DATE()`函数。

#### Timestamp类型
1. `TIMESTAMP`数据类型用于存储日期和时间数据，包括年，月，日，时，分和秒。  
另外，它存储小数秒，它不是由`DATE`数据类型存储的。  
要定义TIMESTAMP列，请使用以下语法：
```sql
column_name TIMESTAMP[(fractional_seconds_precision)]
```
`fractional_seconds_precision`指定SECOND字段小数部分的位数。它的范围从0到9，这意味着可以使用`TIMESTAMP`数据类型来存储到纳秒的精度。  
如果省略`fractional_seconds_precision`，则默认为6。  以下表达式说明了如何定义TIMESTAMP列：
```sql
...
started_at TMESTAMP(6),
...
```
在此示例中，started_at列是一个TIMESTAMP列，其分数秒精度设置为微秒。
2. TIMESTAMP文字  
	要指定TIMESTAMP文字，请使用以下格式：
```sql
TIMESTAMP 'YYYY-MM-DD HH24:MI:SS.FF'
```
3. 格式化TIMESTAMP值  
	要更改TIMESTAMP值的输出，可以使用`TO_CHAR()`函数，将TIMESTAMP值或列的名称作为第一个参数，将格式字符串作为第二个参数。  

	以下语句使用`TO_CHAR()`函数来格式化logged_at列中的值：
```sql
SELECT message,
    TO_CHAR(logged_at, 'MONTH DD, YYYY "at" HH24:MI')
FROM logs;
```
4. 提取TIMESTAMP组件
	要提取TIMESTAMP组件(如年，月，日，小时，分钟和秒)，请**使用`EXTRACT()`函数**：
```sql
EXTRACT( component FROM timestamp);
```

#### Interval类型
1. Oracle提供了两种日期时间数据类型：DATE和TIMESTAMP用于存储时间点数据。另外，它提供`INTERVAL`数据类型用于存储一段时间。  
有两种类型的`INTERVAL`：
	* `INTERVAL YEAR TO MONTH` - 间隔使用年份和月份。
	* `INTERVAL DAY TO SECOND` - 使用包括小数秒在内的天，小时，分钟和秒存储间隔。

2. INTERVAL YEAR TO MONTH  
	`INTERVAL YEAR TO MONTH`数据类型使用YEAR和MONTH字段存储一段时间。  
	下面介绍一个`INTERVAL YEAR TO MONTH`：
```sql
	INTERVAL YEAR [(year_precision)] TO MONTH
```
	`year_precision`代表YEAR字段中的位数。范围从0到9。  
	`year_precision`是可选的。如果省略`year_precision`参数，则默认为2。也就是说，默认情况下，最多可以存储99年和11个月的期限，这个期限**必须小于100年**。
3. INTERVAL YEAR TO MONTH文字  
	要指定`INTERVAL YEAR TO MONTH`数据类型的文字值，可以使用以下格式：
```sql
	INTERVAL 'year[-month]' leading (precision) TO trailing
```
	leading和trailing可以是YEAR或MONTH。  
	以下是参数：
	  * year[-month]  
		year和month是整个区间的leading和trailing的整数。 如果leading是YEAR，trailing是MONTH，那么month字段的范围从0到11。  
		**trailing列必须小于leading列**。 例如，INTERVAL '1-2' MONTH TO YEAR是无效的，因为leading列YEAR大于MONTH的leading字段。
	  * precision  
		是leading字段中的最大数字位数。精度范围为:0~9，默认值为2。
4. 下表说明了`INTERVAL YEAR TO MONTH`文字的示例：

	| INTERVAL YEAR TO MONTH文字        | 含意                                                         |
	| :-------------------------------- | :----------------------------------------------------------- |
	| INTERVAL '120-3' YEAR(3) TO MONTH | 间隔120年，3个月; 由于前导字段的值大于默认精度(2位)，因此必须指定前导字段精度YEAR(3)。 |
	| INTERVAL '105' YEAR(3)            | 间隔105年0个月。                                             |
	| INTERVAL '500' MONTH(3)           | 间隔500个月。                                                |
	| INTERVAL '9' YEAR                 | 间隔9年，相当于INTERVAL '9-0' YEAR TO MONTH                  |
	| INTERVAL '40' MONTH               | 40个月或3年4个月，相当于INTERVAL '3-4' YEAR TO MONTH         |
	| INTERVAL '180' YEAR               | 无效的时间间隔，因为’180’有3个数字，它大于默认精度(2)        |
	
5. `INTERVAL DAY TO SECOND`数据类型  
	`INTERVAL DAY TO SECOND`是一种按天，小时，分钟和秒钟存储一段时间。  
	以下显示`INTERVAL DAY TO SECOND`数据类型的语法：
```sql
INTERVAL DAY [(day_precision)] TO SECOND [(fractional_seconds_precision)]
```
	在上面这个语法中，  
	`day_precision` - 是DAY字段中的位数。取值范围是0~9，缺省值是2。
	`fractional_seconds_precision` 是SECOND字段小数部分的位数。范围从0~9。如果省略`fractional_seconds_precision`，则默认为6。

#### Oracle主键
1. 主键是表中列的唯一标识表中的行的**一列或多列的组合**。  
以下是设置列成为主键的规则：
	* **主键列不能包含NULL值或空字符串**。
	* 主键值在整个表中必须是唯一的。
	* 主键值不应随时间而改变。

2. 根据1中这些规则，以下是对主键的建议：
	* 首先，主键应该是没有意义的。 有时，您可能需要使用有意义的数据，例如：社会安全号码(SSN)，车辆识别号码(VIN)，电子邮件和电话号码等，这些数据被认为是唯一的。 但是，您不知道电子邮件或电话号码何时更改或被他人重复使用。 在这种情况下，会造成很多数据问题。
	* 其次，主键应该是紧凑的。主键通常是数字的，因为**Oracle通常处理数字的速度比任何其他数据类型更快**。

	> 注意：尽管在Oracle中不是强制性的，但在每个表中都有一个主键是最佳实践。

3. 通常，在创建表时创建表的主键。另外，通过使用`ALTER TABLE`语句，可以在创建表之后再添加一个主键。
4. 删除Oracle PRIMARY KEY约束  
一般很少会从表中删除PRIMARY KEY约束。 如果必须要删除主键，则使用以下ALTER TABLE语句。
```sql
ALTER TABLE table_name
DROP CONSTRAINT primary_key_constraint_name;
```
5. 启用/禁用Oracle PRIMARY KEY约束  
要在将大量数据加载到表中或更新海量数据时需要**提高性能**，可以暂时禁用PRIMARY KEY约束。  
要禁用表的主键约束，可以使用ALTER TABLE语句：
```sql
ALTER TABLE table_name
DISABLE CONSTRAINT primary_key_constraint_name;
```
或者，
```sql
ALTER TABLE table_name
DISABLE PRIMARY KEY;
```
要启用主键约束，请使用以下ALTER TABLE语句：
```sql
ALTER TABLE table_name
ENABLE CONSTRAINT primary_key_constraint_name;
```
或者，
```sql
ALTER TABLE table_name
ENABLE PRIMARY KEY;
```