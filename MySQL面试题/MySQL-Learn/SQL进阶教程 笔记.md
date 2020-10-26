## SQL进阶教程 笔记和习题

### 1-1 CASE表达式

#### 笔记

##### 别名引用

```mysql
-- 把县编号转换成地区编号 (2) ：将 CASE 表达式归纳到一处
SELECT CASE pref_name
    WHEN  ' 德岛 ' THEN  ' 四国 '
    WHEN  ' 香川 ' THEN  ' 四国 '
    WHEN  ' 爱媛 ' THEN  ' 四国 '
    WHEN  ' 高知 ' THEN  ' 四国 '
    WHEN  ' 福冈 ' THEN  ' 九州 '
    WHEN  ' 佐贺 ' THEN  ' 九州 '
    WHEN  ' 长崎 ' THEN  ' 九州 '
    ELSE  ' 其他 ' END AS district,
SUM(population)
FROM PopTbl
GROUP BY district;  
```

> 没错，这里的 GROUP BY 子句使用的正是 SELECT 子句里定义的列的别称—— district 。但是严格来说，这种写法是违反标准 SQL 的规则的。因为 GROUP BY 子句比 SELECT 语句先执行，所以在 GROUP BY 子句中引用在 SELECT 子句里定义的别称是不被允许的。事实上，在 Oracle、DB2、SQL Server 等数据库里采用这种写法时就会出错。
>
> 不过也有支持这种 SQL 语句的数据库，例如在 PostgreSQL 和 MySQL中，这个查询语句就可以顺利执行。这是因为，这些数据库在执行查询语句时，会先对 SELECT 子句里的列表进行扫描，并对列进行计算。不过因
> 为这是违反标准的写法，所以这里不强烈推荐大家使用。但是，这样写出来的 SQL 语句确实非常简洁，而且可读性也很好。

##### 在 CASE 表达式中使用聚合函数

```mysql
SELECT
	std_id,
CASE
		WHEN count(*) = 1 THEN
		min( club_id ) ELSE MAX( CASE WHEN main_club_flg = 'Y' THEN club_id ELSE NULL END ) 
	END AS main_club 
FROM
	studentclub 
GROUP BY
	std_id
```

> 这条 SQL 语句在 CASE 表达式里使用了聚合函数，又在聚合函数里使用了 CASE 表达式。这种嵌套的写法人有点眼花缭乱，其主要目的是用`CASE WHEN COUNT(*) = 1 …… ELSE …….` 这样的 CASE 表达式来表示“只加入了一个社团还是加入了多个社团”这样的条件分支。我们在初学SQL的时候，都学过对聚合结果进行条件判断时要用 HAVING 子句，但从这道例题可以看到，在 SELECT 语句里使用 CASE 表达式也可以完成同样的工作，这种写法比较新颖。
>
> 如果用一句话来形容这个技巧，可以这样说：**新手用 HAVING 子句进行条件分支，高手用 SELECT 子句进行条件分支。**
> 通过这道例题我们可以明白： CASE 表达式用在 SELECT 子句里时，既可以写在聚合函数内部，也可以写在聚合函数外部。这种高度自由的写法正是 CASE 表达式的魅力所在。

#### 习题

##### 1-1-1 多列数据的最大值

```mysql
SELECT KEYC,
	CASE WHEN (CASE WHEN  X < Y THEN Y ELSE X END) < Z
	THEN Z 
	ELSE (CASE WHEN X < Y THEN Y ELSE X END)
	END
FROM GREATESTS
```

##### 1-1-2 转换行列——在表头里加入汇总和再揭

```mysql
SELECT sex,
	sum(population) as '全国',
	sum(case when pref_name = '德岛' then population else 0 end) as '德岛',
	sum(case when pref_name = '香川' then population else 0 end) as '香川',
	sum(case when pref_name = '爱媛' then population else 0 end) as '爱媛',
	sum(case when pref_name = '高知' then population else 0 end) as '高知',
	sum(case when pref_name in ('德岛', '香川', '爱媛', '高知') THEN population else 0 end) as '四国'
from poptbl2
GROUP BY sex
```

##### 1-1-3 用 ORDER BY 生成“排序”列

```mysql
-- 解法1
SELECT keyc from greatests 
ORDER BY 
	case KEYC when 'A' THEN 2 
			 WHEN 'B' THEN 1 
			 WHEN 'D' THEN 3 
			 WHEN 'C' THEN 4  END
			 
-- 解法2
SELECT 	keyc, case KEYC when 'A' THEN 2 
			 WHEN 'B' THEN 1 
			 WHEN 'D' THEN 3 
			 WHEN 'C' THEN 4  END as newOrder
from greatests
ORDER BY newOrder
```

### 1-2 自连接的用法

#### 笔记

##### 总结

> 1. 自连接经常和非等值连接结合起来使用。
> 2. 自连接和 GROUP BY 结合使用可以生成递归集合。
> 3. 将自连接看作不同表之间的连接更容易理解。
> 4. 应把表看作行的集合，用面向集合的方法来思考。
> 5. 自连接的性能开销更大，应尽量给用于连接的列建立索引。

#### 习题

##### 1-2-1 可重组合

```msyql
SELECT p1.name as name_1, p2.`name` as name_2
FROM products as p1, products as p2
WHERE p1.name = p2.`name` or p1.`name` > p2.`name`
```

##### 1-2-2 ：分地区排序

```mysql
-- 解法一：窗口函数
SELECT
	district,
	NAME,
	price,
	rank () over ( PARTITION BY district ORDER BY price DESC ) AS rank_1 
FROM
	districtproducts
	
-- 解法二：关联子查询 
SELECT
	d1.district,
	d1.`name`,
	d1.price,
	(
	SELECT
		count( d2.price ) 
	FROM
		districtproducts AS d2 
	WHERE
		d1.price < d2.price 
		AND d1.district = d2.district 
	) + 1 AS rank_1 
FROM
	districtproducts AS d1 
ORDER BY
	d1.district,
	rank_1

-- 解法三：自查询
SELECT
	d1.district,
	d1.`name`,
	max( d1.price ),
	count( d2.`name` ) + 1 AS rank_1 
FROM
	districtproducts AS d1
	LEFT JOIN districtproducts AS d2 ON d1.district = d2.district 
	AND d1.price < d2.price 
GROUP BY
	d1.district,
	d1.`name` 
ORDER BY
	d1.district,
	rank_1
```

#####  1-2-3 ：更新位次

这一题在更新上遇到了问题，感觉csdn几位大佬解答。@evanweng  @percentfl  @qq_18379499

```mysql
-- 解法一
UPDATE districtproducts2
LEFT JOIN (
SELECT
	count( d2.`name` ) + 1 AS rank1,
	d1.`name`,
	d1.district 
FROM
	districtproducts2 AS d1
	LEFT JOIN districtproducts2 AS d2 ON d1.district = d2.district 
	AND d1.price < d2.price 
GROUP BY
	d1.district,
	d1.`name` 
ORDER BY
	d1.district,
	rank1 
	) AS t ON ( districtproducts2.`name` = t.`name` AND districtproducts2.district = t.district ) 
	SET ranking = t.rank1
	
-- 解法二
UPDATE districtproducts2 d 
SET d.ranking = (
	SELECT
		count( 1 ) + 1 
	FROM
		( SELECT d1.* FROM districtproducts2 d1 ) d2 
	WHERE
		d2.district = d.district 
		AND d2.price > d.price 
	)
```



### 1-3 暂缺

### 1-4 HAVING子句的力量

#### 笔记

##### 众数

主要是面向集合的思想。

```mysql
-- 解法一：ALL()函数。当有NULL时，这种方法失效。
SELECT
	income,
	count(*) AS cnt 
FROM
	Graduates 
GROUP BY
	income 
HAVING
	cnt >= ALL ( SELECT count(*) FROM Graduates GROUP BY income ) 

-- 解法二：子查询
SELECT
	income,
	count(*) AS cnt 
FROM
	graduates 
GROUP BY
	income 
HAVING
	cnt >= (
	SELECT
		MAX( t.cnt2 ) 
FROM
	( SELECT count(*) AS cnt2 FROM graduates GROUP BY income ) AS t)
```



## Update Log

- 2020-10-21 20:31:43，自连接的用法
- 2020-10-26 19:37:43，习题1-2-3；1-4众数