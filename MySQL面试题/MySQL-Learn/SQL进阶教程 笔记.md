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





## Update Log

- 2020-10-21 20:31:43，自连接的用法