---
title: Oracle-函数
date: 2019-08-13 18:06:37
tags: oracle
category: 数据库
---

#### Ascii()函数
1. Oracle / PLSQL `ASCII()`函数返回代表指定字符的**数字值代码**。

	###### 语法
	Oracle / PLSQL中`ASCII()`函数的语法是：
	```sql
	ASCII( single_character )
	```
	###### 参数
	* single_character - 指定的字符来检索NUMBER代码。 如果输入多个字符，则ASCII函数将返回第一个字符的值，并忽略第一个字符后的所有字符。

	###### 返回值
	* ASCII函数返回一个数值。

#### Asciistr()函数
1. Oracle / PLSQL `ASCIISTR()`函数使用数据库字符集将任何字符集中的字符串转换为ASCII字符串。

	###### 语法
	Oracle / PLSQL中`ASCIISTR()`函数的语法是：
	```sql
	ASCIISTR( string )
	```
	###### 参数
	* `string` - 任何字符集中的字符串，希望将其转换为数据库字符集中的**ASCII字符串**s。
	###### 返回值
	* 返回一个字符串值。
	###### 适用于
	`ASCIISTR()`函数可用于以下版本的Oracle / PLSQL：  
	`Oracle 12c`, `Oracle 11g`, `Oracle 10g`, `Oracle 9i`

#### Chr()函数
1. Oracle / PLSQL `Chr()`函数**与ASCII函数的功能相反**。 它根据数字代码**返回字符**。

	###### 语法
	Oracle / PLSQL中`Chr()`函数的语法是：
	```sql
	CHR( number_code )
	```
	###### 参数
	* number_code - 用于检索对应字符的NUMBER代码。

	###### 返回值
	* 返回一个字符串值。