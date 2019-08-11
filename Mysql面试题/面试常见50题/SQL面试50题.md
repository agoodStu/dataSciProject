## SQL面试50题

看了一些SQL面试的题目，这里做个记录。本文代码为MySql。

这个练习题目里面共有四张表：

- Student 学生表	

| 字段名 | 字段含义 | 字段类型    |
| ------ | -------- | ----------- |
| sid    | 学生编号 | varchar(10) |
| sname  | 学生姓名 | varchar(10) |
| sage   | 学生年龄 | datetime    |
| ssex   | 学生性别 | varchar(10) |

```mysql
# Student表数据
insert into Student values('01' , '赵雷' , '1990-01-01' , '男');
insert into Student values('02' , '钱电' , '1990-12-21' , '男');
insert into Student values('03' , '孙风' , '1990-05-20' , '男');
insert into Student values('04' , '李云' , '1990-08-06' , '男');
insert into Student values('05' , '周梅' , '1991-12-01' , '女');
insert into Student values('06' , '吴兰' , '1992-03-01' , '女');
insert into Student values('07' , '郑竹' , '1989-07-01' , '女');
insert into Student values('08' , '王菊' , '1990-01-20' , '女');
```

- Courese 课程表

| 字段名 | 字段含义 | 字段类型    |
| ------ | -------- | ----------- |
| cid    | 课程编号 | varchar(10) |
| cname  | 课程名字 | varchar(10) |
| tid    | 教师姓名 | varchar(10) |

```mysql
# Course表数据
insert into Course values('01' , '语文' , '02');
insert into Course values('02' , '数学' , '01');
insert into Course values('03' , '英语' , '03');
```

- Teacher 教师表

| 字段名 | 字段含义 | 字段类型    |
| ------ | -------- | ----------- |
| tid    | 教师编号 | varchar(10) |
| tname  | 教师姓名 | varchar(10) |

```mysql
# Teacher表数据
insert into Teacher values('01' , '张三');
insert into Teacher values('02' , '李四');
insert into Teacher values('03' , '王五');
```

- SC 成绩表

| 字段名 | 字段含义 | 字段类型       |
| ------ | -------- | -------------- |
| sid    | 学生编号 | varchar(10)    |
| cid    | 课程编号 | varchar(10)    |
| score  | 学生成绩 | decimal(18, 1) |

```mysql
# SC表数据
insert into SC values('01' , '01' , 80);
insert into SC values('01' , '02' , 90);
insert into SC values('01' , '03' , 99);
insert into SC values('02' , '01' , 70);
insert into SC values('02' , '02' , 60);
insert into SC values('02' , '03' , 80);
insert into SC values('03' , '01' , 80);
insert into SC values('03' , '02' , 80);
insert into SC values('03' , '03' , 80);
insert into SC values('04' , '01' , 50);
insert into SC values('04' , '02' , 30);
insert into SC values('04' , '03' , 20);
insert into SC values('05' , '01' , 76);
insert into SC values('05' , '02' , 87);
insert into SC values('06' , '01' , 31);
insert into SC values('06' , '03' , 34);
insert into SC values('07' , '02' , 89);
insert into SC values('07' , '03' , 98);
```

### 题目

#### 1. 查询“01”课程比“02”课程成绩高的所有学生的学号

**思路：**需要进行比较，所以需要连接两个表

**代码：**

```mysql
SELECT DISTINCT
	SC1.sid 
FROM
	( SELECT sid, score FROM SC WHERE cid = '01' ) AS SC1
	JOIN ( SELECT sid, score FROM SC WHERE cid = '02' ) AS SC2 ON SC1.sid = SC2.sid 
WHERE
	SC1.score > SC2.score
```

**结果：**

| sid  |
| ---- |
| 2    |
| 4    |

#### 2. 查询平均成绩大于60分的同学的学号和平均成绩

**思路：**平均成绩，需要GROUP BY；大于60，需要having（where在group by之前执行）

**代码：**

``` mysql
SELECT
	sid,
	AVG( score ) AS avg_grade 
FROM
	SC 
GROUP BY
	sid 
HAVING
	avg_grade >= 60
```

**结果：**

| sid | avg_grade|
| ---- | ---- |
|1|89.66667|
|2|70.00000|
|3	|80.00000|
|5	|81.50000|
|7	|93.50000|

#### 3. 查询所有同学的学号、姓名、选课数、总成绩

**思路：**需要连接Student和SC两张表；选课数和总成绩需要聚合函数；Student表中有全部学生ID，使用左连接

**代码**

```mysql
SELECT
	stu.sid,
	stu.sname,
	COUNT( DISTINCT cid ) AS course_total,
	sum( score ) AS total_sum 
FROM
	student AS stu
	LEFT JOIN SC ON stu.sid = SC.sid 
GROUP BY
	stu.sid,
	stu.sname
```

**结果：**

| sid  | sname | course_total | total_sum |
| ---- | ----- | ------------ | --------- |
|1	|赵雷	|3	|269.0|
|2	|钱电	|3	|210.0|
|3	|孙风	|3	|240.0|
|4	|李云	|3	|100.0|
|5	|周梅	|2	|163.0|
|6	|吴兰	|2	|65.0|
|7	|郑竹	|2	|187.0|
|8	|王菊	|0	| null |

#### 4. 查询姓“李”的老师的个数

**思路：**个数使用count(distincnt)；姓使用Like搜索

**代码：**

```mysql
SELECT COUNT(DISTINCT Tid) AS sum_li
FROM teacher
WHERE tname LIKE '李%'
```

**结果：**

| sum_li |
| ------ |
| 1      |

#### 5. 查询没学过“张三”老师课的同学的学号、姓名

**思路：**根据Teacher表查询Tid，根据Tid在courese表中查询cid，在SC表中查询sid，在student表中查sname

**代码：**

```mysql
SELECT
	sid,
	sname 
FROM
	student 
WHERE
	student.sid NOT IN (
	SELECT
		sid 
	FROM
		sc 
WHERE
	sc.cid IN ( SELECT cid FROM course JOIN teacher ON course.tid = teacher.tid WHERE teacher.tname = '张三' ))
```

**结果：**

| sid  | sname |
| ---- | ----- |
| 6    | 吴兰  |
| 8    | 王菊  |

#### 6. 查询学过“01”并且也学过编号“02”课程的同学的学号、姓名

**思路：**连接cid='01'和cid='02'的两个表，找出相同的sid，再连接Student表

**代码：**

```mysql
SELECT
	sid,
	sname 
FROM
	student 
WHERE
	sid IN (
	SELECT
		sid1 
	FROM
		(
			( SELECT sid AS sid1 FROM sc WHERE cid = '01' ) AS sid1
		JOIN ( SELECT sid FROM sc WHERE cid = '02' ) AS sid2 ON sid1.sid1 = sid2.sid 
	))
```

**结果：**

|    sid  |sname      |
| ---- | ---- |
|  1    |赵雷      |
|    2  |钱电      |
|3	|孙风|
|4	|李云|
|5	|周梅|

#### 7. 查询学过“张三”老师所教的课的同学的学号、姓名

**思路：**和5题类似；NOT IN改成IN即可

**代码：**

```mysql
SELECT
	sid,
	sname 
FROM
	student 
WHERE
	student.sid IN (
	SELECT
		sid 
	FROM
		sc 
WHERE
	sc.cid IN ( SELECT cid FROM course JOIN teacher ON course.tid = teacher.tid WHERE teacher.tname = '张三' ))
```

**结果：**

|sid|sname|
|---|---|
|1|	赵雷|
|2|	钱电|
|3|	孙风|
|4|	李云|
|5|	周梅|
|7|	郑竹|

#### 8. 查询课程编号“01”的成绩比课程编号“02”课程低的所有同学的学号、姓名

**思路：**与第6题类似

**代码：**

```mysql
# 方法1
SELECT
	sid,
	sname 
FROM
	student 
WHERE
	sid IN (
	SELECT
		t1_sid 
	FROM
		(
			( SELECT sid AS t1_sid, score FROM sc WHERE cid = '01' ) t1
			JOIN ( SELECT sid AS t2_sid, score FROM sc WHERE cid = '02' ) t2 ON t1.t1_sid = t2.t2_sid 
		AND t1.score < t2.score 
	))
# 方法2
SELECT
	t.sid,
	student.sname 
FROM
	(
	SELECT
		t1.sid 
	FROM
		( SELECT sid, score FROM sc WHERE cid = '01' ) AS t1
		JOIN ( SELECT sid, score FROM sc WHERE cid = '02' ) AS t2 ON t1.sid = t2.sid 
	WHERE
		t1.score < t2.score 
	) t
	JOIN student ON t.sid = student.sid
```

**结果：**

| sid  | sname |
| ---- | ----- |
|1	|赵雷|
|5	|周梅|

#### 9. 查询所有课程成绩小于60分的同学的学号、姓名

**思路：**选出分数最大值，如果最大值小于60，那所以课程都小于60.

**代码：**

```mysql
SELECT
	t.sid,
	s.sname 
FROM
	( SELECT sid, MAX( score ) AS max_sc FROM sc GROUP BY sid HAVING max_sc < 60 ) AS t
	JOIN student AS s ON t.sid = s.sid
```

**结果：**

|sid|	sname|
|---|---|
|4	|李云|
|6	|吴兰|

#### 10. 查询没有学全所有课的同学的学号、姓名

**思路：**使用count统计所有课程和每位同学所选课程；连接Student表

**代码：**

```mysql
SELECT
	t.sid,
	student.sname 
FROM
	(
	SELECT
		sid,
		COUNT( DISTINCT cid ) AS stu_count 
	FROM
		sc 
	GROUP BY
		sid 
	HAVING
	stu_count < ( SELECT COUNT( DISTINCT cid ) FROM course )) AS t
	JOIN student ON t.sid = student.sid
```

**结果：**

|sid|	sname|
|---|---|
|5	|周梅|
|6	|吴兰|
|7	|郑竹|

#### 11. 查询至少有一门课与学号为“01”的同学所学相同的同学的学号和姓名

**思路：**选出‘01’同学的课程号，做对比

**代码：**

```mysql
SELECT
	sid,
	sname 
FROM
	student 
WHERE
	sid IN (
	SELECT
		sid 
	FROM
		sc 
	WHERE
		sid != '01' 
		AND cid IN ( SELECT DISTINCT cid FROM sc WHERE sid = '01' ) 
	GROUP BY
	sid 
	)
```

**结果：**

|sid|sname|
|--|--|
|2	|钱电|
|3|	孙风|
|4|	李云|
|5|	周梅|
|6|	吴兰|
|7|	郑竹|

#### 12. 查询和"01"号的同学学习的课程完全相同的其他同学的学号和姓名

**思路：**

***先跳过***

#### 13. 查询没学过"张三"老师讲授的任一门课程的学生姓名

**思路：**先查找张三老师的tid和所授课程cid

**代码：**

```mysql
SELECT
	sid,
	sname 
FROM
	student 
WHERE
	NOT EXISTS (
	SELECT DISTINCT
		sid 
	FROM
		sc
		JOIN course ON sc.cid = course.cid
		JOIN teacher ON course.tid = teacher.tid 
	WHERE
		tname = '张三' 
	AND student.sid = sc.sid 
	)
```

**结果：**

|sid|	sname|
|--|--|
|6|	吴兰|
|8|	王菊|

#### 14. 查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩

**思路：**先选出小于60的表，再连接到student表

```mysql
SELECT
	student.sid,
	sname,
	avg_score 
FROM
	student
	JOIN (
	SELECT
		sid,
		COUNT( score ) AS cnt,
		AVG( score ) AS avg_score 
	FROM
		sc 
	WHERE
	score < 60 GROUP BY sid HAVING cnt >= 2 
	) AS t ON student.sid = t.sid
```

**结果：**

|sid|	sname|	avg_score|
|-|-|
|4	|李云	|33.33333|
|6	|吴兰	|32.50000|

#### 15. 检索"01"课程分数小于60，按分数降序排列的学生信息

**思路**：降序ORDER BY DESC

```mysql
SELECT
	sid,
	score 
FROM
	sc 
WHERE
	cid = '01' 
	AND score < 60 
ORDER BY
	score DESC
```
|sid|score|
|-|-|
|4|	50.0|
|6	|31.0|

#### 16. 按平均成绩从高到低显示所有学生的平均成绩

**思路：**用avg()取平均成绩，GROUP BY分组

**代码：**

```mysql
SELECT
	sid,
	AVG( score ) AS avg_score 
FROM
	sc 
GROUP BY
	sid 
ORDER BY
	avg_score DESC
```

**结果：**

|sid|	avg_score|
|-|-|
|7	|93.50000|
|1	|89.66667|
|5|	81.50000|
|3|	80.00000|
|2|	70.00000|
|4|	33.33333|
|6|	32.50000|

#### 17. 查询各科成绩最高分、最低分和平均分：以如下形式显示：课程ID，课程name，最高分，最低分，平均分，及格率

**思路：**连接course表和sc表；及格率使用IF()和COUNT函数计算

**代码：**

```mysql
SELECT
	sc.cid,
	cname,
	MAX( score ) AS max_score,
	MIN( score ) AS min_score,
	AVG( score ) AS avg_score,
	COUNT(
	IF
		( score >= 60, sid, NULL )) / COUNT(
	IFNULL( sid, NULL )) AS pass_rate 
FROM
	sc
	LEFT JOIN course ON sc.cid = course.cid 
GROUP BY
	sc.cid,
	cname
```

**结果：**
|cid|	cname|	max_score|	min_score|	avg_score|	pass_rate|
|-|-|-|-|-|-|
|1|	语文|	80.0|	31.0|	64.50000|	0.6667|
|2|	数学|	90.0|	30.0|	72.66667|	0.8333|
|3|	英语|	99.0|	20.0|	68.50000|	0.6667|

#### 18. 按各科平均成绩从低到高和及格率的百分数从高到低顺序

**思路：**平均成绩和及格率的计算参考17题

**代码：**

```mysql
SELECT
	cid,
	AVG( score ) AS avg_score,
	COUNT(
	IF
		( score >= 60, sid, NULL )) / COUNT(
	IF
	( score, sid, NULL )) AS pass_rate 
FROM
	sc 
GROUP BY
	cid 
ORDER BY
	avg_score DESC,
	pass_rate DESC
```

**结果：**

|cid|	avg_score|	pass_rate|
|-|-|-|
|2	|72.66667|	0.8333|
|3|	68.50000|	0.6667|
|1|	64.50000|	0.6667|

#### 19. 查询学生的总成绩并进行排名

**思路：**考察GROUP BY和聚合函数

**代码：**

```mysql
SELECT
	SUM( score ) AS total_score 
FROM
	sc 
GROUP BY
	sid 
ORDER BY
	total_score DESC
```

**结果：**

|total_score|
|-|
|269.0|
|240.0|
|210.0|
|187.0|
|163.0|
|100.0|
|65.0|

