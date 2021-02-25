---

title: Oracle-事务
date: 2019-08-13 18:07:37
tags: oracle
category: 数据库
---

## 概念
1. 什么是事务
在数据库中事务是**工作的逻辑单元**，一个事务是由一个或多个完成一组的相关行为的SQL语句组成，通过事务机制确保这一组SQL语句所作的操作要么都成功执行，**完成整个工作单元操作，要么一个也不执行**。
2. 事务特性
SQL92标准定义了数据库事务的四个特点：
	
	* **原子性(Atomicity)**：一个事务里面所有包含的SQL语句是一个执行整体，不可分割，要么都做，要么都不做。
	* **一致性(Consistency)**：事务开始时，数据库中的数据是一致的，事务结束时，数据库的数据也应该是一致的。
	* **隔离性(Isolation)**：是指数据库允许多个并发事务同时对其中的数据进行读写和修改的能力，隔离性可以防止事务的并发执行时，由于他们的操作命令交叉执行而导致的数据不一致状态。
* **持久性 (Durability)** : 是指当事务结束后，它对数据库中的影响是永久的，即便系统遇到故障的情况下，数据也不会丢失。
	
	一组SQL语句操作要成为事务，数据库管理系统必须保证这组操作的原子性（Atomicity）、一致性（consistency）、隔离性（Isolation）和持久性（Durability），这就是**ACID**特性。

## 提交事务（COMMIT语句）
1. 语句  
Oracle / PLSQL中`COMMIT`语句的语法是：
	
	```sql
	COMMIT [ WORK ] [ COMMENT clause ] [ WRITE clause ] [ FORCE clause ]; 
	```

2. 参数    
	* `WORK` - 可选的。它被Oracle添加为符合SQL标准。使用或不使用`WORK`参数来执行`COMMIT`	将产生相同的结果。
	* `COMMENT clause` - 可选的。 它用于指定与当前事务关联的注释。 	* 该注释最多可以包含在单引号中的255个字节的文本中。 如果出现问题，它将与事务ID一起存储在名为`	* DBA_2PC_PENDING`的系统视图中。
	* `WRITE clause` - 可选的。 它用于指定将已提交事务的重做信息写入重做日志的优先级。 	用这个子句，有两个参数可以指定：
		* `WAIT` 或 `NOWAIT` (如果省略，`WAIT`是默认值)
		* `IMMEDIATE`或`BATCH`(`IMMEDIATE`是省略时的默认值)
	* `FORCE clause` - 可选的。 它用于强制提交可能已损坏或有疑问的事务。 	* 有了这个子句，可以用3种方式指定FORCE：
	* `FORCE'string'，[integer]`或`FORCE CORRUPT_XID'string'`或`FORCE CORRUPT_XID_ALL`
	
	注意：  
		必须拥有DBA权限才能访问系统视图 - `DBA_2PC_PENDING`和`V$CORRUPT_XID_LIST`。
		必须拥有DBA权限才能指定`COMMIT`语句的某些功能。

## 回滚事务（ROLLBACK语句）
1. 在Oracle中，ROLLBACK语句用于撤销当前事务或有问题的事务所执行的工作。
2. 语法  
ROLLBACK语句的语法是：
    ```sql
    ROLLBACK [ WORK ] [ TO [SAVEPOINT] savepoint_name  | FORCE 'string' ];
    ```

3. 参数   
	* `WORK` - 可选的。 它被Oracle添加为符合SQL标准。 使用或不使用WORK参数来发出ROLLBACK会导致相同的结果。
	* `TO SAVEPOINT savepoint_name` - 可选的。 	`ROLLBACK`语句撤消当前会话的所有更改，直到由`savepoint_name`指定的保存点。 如果省略该子句，则所有更改都将被撤消。
	* `FORCE ‘string’` - 可选的。它用于强制回滚可能已损坏或有问题的事务。 使用此子句，可以将单引号中的事务ID指定为字符串。 可以在系统视图中找到名为`DBA_2PC_PENDING`的事务标识。
	* 必须拥有DBA权限才能访问系统视图 - `DBA_2PC_PENDING`和`V$CORRUPT_XID_LIST`。
	* 您无法将有问题的事务回滚到保存点。

## 设置事务（SET TRANSACTION语句）
1. 在Oracle中，`SET TRANSACTION`语句用于将事务设置为只读，将事务设置为读/写，设置事务的隔离级别，为事务分配名称或将事务分配回滚段。

2. 语法  
    Oracle / PLSQL中`SET TRANSACTION`语句的语法是：
  ```sql
  SET TRANSACTION [ READ ONLY | READ WRITE ]
               [ ISOLATION LEVEL [ SERIALIZE | READ COMMITED ]]
               [ USE ROLLBACK SEGMENT 'segment_name' ]
               [ NAME 'transaction_name' ];
  ```
3. 参数
  * `READ ONLY` - 可选的。 如果指定，它将事务设置为只读事务。
  * `READ WRITE` - 可选的。 如果指定，它将事务设置为读/写事务。
  * `ISOLATION LEVEL` - 可选的。 如果指定，它有两个选项：
  	* `ISOLATION LEVEL SERIALIZE` - 如果事务尝试更新由另一个事务更新并未提交的资源，则事务将失败。
  	* `ISOLATION LEVEL READ COMMITTED` - 如果事务需要另一个事务持有的行锁，则事务将等待，直到行锁被释放。
  * `USE ROLLBACK SEGMENT` - 可选的。 如果指定，它将事务分配给由`'segment_name'`标识的回退段，该段是用引号括起来的段名称。
  * `NAME` - 为`'transaction_name'`标识的事务分配一个名称，该事务用引号括起来。

## 锁表（LOCK TABLE语句）
1. `LOCK TABLE`语句用于锁定表，表分区或表子分区。
2. 语法  
`LOCK TABLE`语句的语法是：
   
    ```sql
    LOCK TABLE tables IN lock_mode MODE [ WAIT [, integer] | NOWAIT ];
    ```
3. 参数
* tables - 用逗号分隔的表格列表。
* lock_mode - 它是以下值之一

lock_mode|描述
:-----|:-----
ROW SHARE|允许同时访问表，但阻止用户锁定整个表以进行独占访问。
ROW EXCLUSIVE|允许对表进行并发访问，但阻止用户以独占访问方式锁定整个表并以共享方式锁定表。
SHARE UPDATE|允许同时访问表，但阻止用户锁定整个表以进行独占访问。
SHARE|允许并发查询，但用户无法更新锁定的表。
SHARE ROW EXCLUSIVE|用户可以查看表中的记录，但是无法更新表或锁定SHARE表中的表。
EXCLUSIVE|允许查询锁定的表格，但不能进行其他活动。

* WAIT - 它指定数据库将等待(达到指定整数的特定秒数)以获取DML锁定。
* NOWAIT - 它指定数据库不应该等待释放锁。