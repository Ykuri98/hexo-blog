---
title: 记一次Oracle SQL调优
date: 2023-09-01 15:46:53
categories:
- 技术
tags:
- java
- Oracle
---

公司一个数据库的查询很慢，测试环境200w数据大概需要2秒，正式环境400w数据就需要7秒了。看一下查询语句。

```sql
SELECT
	* 
FROM
	(
	SELECT
		TMP_PAGE.*,
		ROWNUM ROW_ID 
	FROM
		(
		SELECT
			PRODUCTVARIANTID AS id,
			BARCODE AS code,
			PARENT_CODE,
			NAME,
			COST_PRICE,
			ORIGINAL_PRICE,
			GUIDE_PRICE,
			UPDATE_USER,
			UPDATE_TIME,
			CREATE_USER,
			UPDATE_USER_ID,
			CREATE_USER_ID,
			CREATE_TIME,
			IS_MEDICARE,
			IS_MEDICARE_IN_CITY,
			IS_ON_SHELF_IN_CITY,
			MEDICARE_PRICE,
			UPC,
			IS_DEL,
			STATUS,
			CITY_ID,
			CODE_TYPE,
			CHANNEL 
		FROM
			NST_PRODUCT_O2O_PRODUCT 
		WHERE
			IS_DEL = 0 
			AND (UPDATE_TIME IS NOT NULL) 
		ORDER BY
			UPDATE_TIME DESC 
		) TMP_PAGE 
	) 
WHERE
	ROW_ID <= 10   ## 这里是分页插件要传的参数
	AND ROW_ID > 0;
```

再用` explain plan for`分析一下整个语句，发现问题，`TABLE ACCESS(FULL)`说明整张表并未走索引，所以导致慢查询。但是表中的`UPDATE_TIME`和`IS_DEL`字段是建立了联合索引的，说明出现了一些情况导致Oracle不走索引。

贴上一些不会走索引的情况

> ‍1,<>
> 2,单独的>,<,(有时会用到，有时不会)
> 3,like "%_" 百分号在前.
> 4,表没分析.
> 5,单独引用复合索引里非第一位置的索引列.
> 6,字符型字段为数字时在where条件里不添加引号.
> 7,对索引列进行运算.需要建立函数索引.
> 8,not in ,not exist.
> 9,当变量采用的是times变量，而表的字段采用的是date变量时.或相反情况。
> 10, 索引失效。
> 11,基于cost成本分析(oracle因为走全表成本会更小)：查询小表,或者返回值大概在10%以上
> 12,有时都考虑到了 但就是不走索引,drop了从建试试在
> 13,B-tree索引 is null不会走,is not null会走,位图索引 is null,is not null  都会走
> 14,联合索引 is not null 只要在建立的索引列（不分先后）都会走, 
> in null时  必须要和建立索引第一列一起使用,当建立索引第一位置条件是is null 时,其他建立索引的列可以是is null（但必须在所有列都满足is null的时候）,或者=一个值；
> 当建立索引的第一位置是=一个值时,其他索引列可以是任何情况（包括is null =一个值）,以上两种情况索引都会走。其他情况不会走。

解决方法主要有三种

> 1. 选用适合的Oracle优化器
>
>    Oracle优化器有三种，RULE(基于规则)，COST(基于成本)，CHOOSE(选择性)
>
>    在缺省情况下,Oracle采用CHOOSE优化器, 为了避免那些不必要的全表扫描（full table scan）, 你必须尽量避免使用CHOOSE优化器,而直接采用基于规则或者基于成本的优化器。
>
> 2. 重建索引
>
>    ‍alter index 索引名 rebuild 
>
> 3. 强制索引
>
>    在select 后，from 前加上 `/*+INDEX(表名 索引名)*/`

那么思路就很清晰了。首先公司的数据库我肯定是不敢乱动的，第一种方式pass，那就剩下两个互相尝试。最后测试环境的索引重建后就开始走索引了，速度从2s变成了1s；但是正式环境就比较奇怪，重建后也没有走索引，强制走索引后反而变得更慢了，说明此时走索引是更差的选择，Oracle选了全表来提高效率，目前也想不到更好的处理方法了，或许增加限制条件（比如添加一些默认值，让查询数量变少）和分库分表能解决吧。



2022/9/7更新

今天又跑了一下测试环境，发现索引又失效了，而且不能强制使用，否则会更慢，估计还是因为策略问题，那么想从数据库方面优化是不太可能了，试着优化一下SQL语句吧。

网上一篇博客https://www.cnblogs.com/quartz/p/14668586.html给了我启发，可能是pageHelper源码写的SQL语句导致查询速度慢。

修改后的SQL语句如下

```sql
SELECT
	* 
FROM
	(
	SELECT
		TMP_PAGE.*,
		ROWNUM ROW_ID 
	FROM
		(
		SELECT
			PRODUCTVARIANTID AS id,
			BARCODE AS code,
			PARENT_CODE,
			NAME,
			COST_PRICE,
			ORIGINAL_PRICE,
			GUIDE_PRICE,
			UPDATE_USER,
			UPDATE_TIME,
			CREATE_USER,
			UPDATE_USER_ID,
			CREATE_USER_ID,
			CREATE_TIME,
			IS_MEDICARE,
			IS_MEDICARE_IN_CITY,
			IS_ON_SHELF_IN_CITY,
			MEDICARE_PRICE,
			UPC,
			IS_DEL,
			STATUS,
			CITY_ID,
			CODE_TYPE,
			CHANNEL 
		FROM
			NST_PRODUCT_O2O_PRODUCT 
		WHERE
			IS_DEL = 0 
			AND (UPDATE_TIME IS NOT NULL) 
		ORDER BY
			UPDATE_TIME DESC 
		) TMP_PAGE 
        ## 将一个条件放入子查询中
        WHERE
        ROWNUM <= 10
	) 
WHERE
	## 这里是分页插件要传的参数
	ROW_ID > 0;
```

在navicat上跑了一下修改后的SQL语句，速度确实提高了一倍左右。现在的问题就是如何修改源码了，参考博客https://www.jianshu.com/p/47dbb01f3aaa?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation，可以知道只需要在项目中创建与源码相同的包和类，再修改源码就行，这样编译后的class文件会替换maven依赖中的class文件。

修改成功后，测试环境测试发现确实快了很多！唯一的缺点是当页数太大时，还是会拖慢查询的速度，目前还没想到好办法，所以先部署在正式环境先。
