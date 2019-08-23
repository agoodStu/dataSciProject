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

**思路：** 需要进行比较，所以需要连接两个表

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

**思路：** 平均成绩，需要GROUP BY；大于60，需要having（where在group by之前执行）

**代码：**

```mysql
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

| sid  | avg_grade |
| ---- | --------- |
| 1    | 89.66667  |
| 2    | 70.00000  |
| 3    | 80.00000  |
| 5    | 81.50000  |
| 7    | 93.50000  |

#### 3. 查询所有同学的学号、姓名、选课数、总成绩

**思路：** 需要连接Student和SC两张表；选课数和总成绩需要聚合函数；Student表中有全部学生ID，使用左连接

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
| 1    | 赵雷  | 3            | 269.0     |
| 2    | 钱电  | 3            | 210.0     |
| 3    | 孙风  | 3            | 240.0     |
| 4    | 李云  | 3            | 100.0     |
| 5    | 周梅  | 2            | 163.0     |
| 6    | 吴兰  | 2            | 65.0      |
| 7    | 郑竹  | 2            | 187.0     |
| 8    | 王菊  | 0            | null      |

#### 4. 查询姓“李”的老师的个数

**思路：** 个数使用count(distincnt)；姓使用Like搜索

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

**思路：** 根据Teacher表查询Tid，根据Tid在courese表中查询cid，在SC表中查询sid，在student表中查sname

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

**思路：** 连接cid='01'和cid='02'的两个表，找出相同的sid，再连接Student表

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

| sid  | sname |
| ---- | ----- |
| 1    | 赵雷  |
| 2    | 钱电  |
| 3    | 孙风  |
| 4    | 李云  |
| 5    | 周梅  |

#### 7. 查询学过“张三”老师所教的课的同学的学号、姓名

**思路：** 和5题类似；NOT IN改成IN即可

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

| sid  | sname |
| ---- | ----- |
| 1    | 赵雷  |
| 2    | 钱电  |
| 3    | 孙风  |
| 4    | 李云  |
| 5    | 周梅  |
| 7    | 郑竹  |

#### 8. 查询课程编号“01”的成绩比课程编号“02”课程低的所有同学的学号、姓名

**思路：** 与第6题类似

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
| 1    | 赵雷  |
| 5    | 周梅  |

#### 9. 查询所有课程成绩小于60分的同学的学号、姓名

**思路：** 选出分数最大值，如果最大值小于60，那所以课程都小于60.

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

| sid  | sname |
| ---- | ----- |
| 4    | 李云  |
| 6    | 吴兰  |

#### 10. 查询没有学全所有课的同学的学号、姓名

**思路：** 使用count统计所有课程和每位同学所选课程；连接Student表

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

| sid  | sname |
| ---- | ----- |
| 5    | 周梅  |
| 6    | 吴兰  |
| 7    | 郑竹  |

#### 11. 查询至少有一门课与学号为“01”的同学所学相同的同学的学号和姓名

**思路：** 选出‘01’同学的课程号，做对比

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

| sid  | sname |
| ---- | ----- |
| 2    | 钱电  |
| 3    | 孙风  |
| 4    | 李云  |
| 5    | 周梅  |
| 6    | 吴兰  |
| 7    | 郑竹  |

#### 12. 查询和"01"号的同学学习的课程完全相同的其他同学的学号和姓名

**思路：** 利用GROUP_CONCAT函数

**代码：** 

```mysql
SELECT
	t1.sid,
	student.sname 
FROM
	( SELECT sid, GROUP_CONCAT( cid ORDER BY cid ) AS cid1 FROM sc GROUP BY sid ) AS t1
	JOIN ( SELECT sid, GROUP_CONCAT( cid ORDER BY cid ) AS cid2 FROM sc WHERE sid = '01' ) AS t2 ON t1.cid1 = t2.cid2
	LEFT JOIN student ON t1.sid = student.sid 
WHERE
	t1.sid <> '01'
```

**结果：**

| sid  | sname |
| ---- | ----- |
| 2    | 钱电  |
| 3    | 孙风  |
| 4    | 李云  |

#### 13. 查询没学过"张三"老师讲授的任一门课程的学生姓名

**思路：** 先查找张三老师的tid和所授课程cid

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

| sid  | sname |
| ---- | ----- |
| 6    | 吴兰  |
| 8    | 王菊  |

#### 14. 查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩

**思路：** 先选出小于60的表，再连接到student表

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

| sid  | sname | avg_score |
| ---- | ----- | --------- |
| 4    | 李云  | 33.33333  |
| 6    | 吴兰  | 32.50000  |

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

| sid  | score |
| ---- | ----- |
| 4    | 50.0  |
| 6    | 31.0  |

#### 16. 按平均成绩从高到低显示所有学生的平均成绩

**思路：** 用avg()取平均成绩，GROUP BY分组

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

| sid  | avg_score |
| ---- | --------- |
| 7    | 93.50000  |
| 1    | 89.66667  |
| 5    | 81.50000  |
| 3    | 80.00000  |
| 2    | 70.00000  |
| 4    | 33.33333  |
| 6    | 32.50000  |

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

| cid  | cname | max_score | min_score | avg_score | pass_rate |
| ---- | ----- | --------- | --------- | --------- | --------- |
| 1    | 语文  | 80.0      | 31.0      | 64.50000  | 0.6667    |
| 2    | 数学  | 90.0      | 30.0      | 72.66667  | 0.8333    |
| 3    | 英语  | 99.0      | 20.0      | 68.50000  | 0.6667    |

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

| cid  | avg_score | pass_rate |
| ---- | --------- | --------- |
| 2    | 72.66667  | 0.8333    |
| 3    | 68.50000  | 0.6667    |
| 1    | 64.50000  | 0.6667    |

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

| total_score |
| ----------- |
| 269.0       |
| 240.0       |
| 210.0       |
| 187.0       |
| 163.0       |
| 100.0       |
| 65.0        |

#### 20. 查询不同老师所教不同课程平均分从高到低显示

**思路：** 连接course表和sc表，使用course表中数据更多的情况，使用左连接

**代码：**

```mysql
SELECT
	tid,
	AVG( score ) AS avg_score 
FROM
	course
	LEFT JOIN sc ON course.cid = sc.cid 
GROUP BY
	tid 
ORDER BY
	avg_score DESC
```

**结果：**

| tid  | avg_score |
| ---- | --------- |
| 1    | 72.66667  |
| 3    | 68.50000  |
| 2    | 64.50000  |

#### 21. 查询所有课程的成绩第2名到第3名的学生信息及该课程成绩

**思路：** 利用dense_rank排序后，再生成一个子查询（因为where在SELECT之前执行）

**代码：**

```mysql
SELECT
	cid,
	sid,
	score,
	ranking 
FROM
	( SELECT cid, sid, score, dense_rank () over ( PARTITION BY cid ORDER BY score DESC ) AS ranking FROM sc ) AS t 
WHERE
	ranking IN ( 2, 3 )
```

**结果：** 

| cid  | sid  | score | ranking |
| ---- | ---- | ----- | ------- |
| 1    | 5    | 76.0  | 2       |
| 1    | 2    | 70.0  | 3       |
| 2    | 7    | 89.0  | 2       |
| 2    | 5    | 87.0  | 3       |
| 3    | 7    | 98.0  | 2       |
| 3    | 2    | 80.0  | 3       |
| 3    | 3    | 80.0  | 3       |

#### 22. 统计各科成绩各分数段人数：课程编号,课程名称,[100-85],[85-70],[70-60],[0-60]及所占百分比

**思路：** 对于统计各阶段人数和百分比，可以使用COUNT() 和If() 函数。这题要注意的是，在MySQL中，BETWEEN AND的取值区间在两端都闭合，即[a, b]。直接写[60, 70]，[70, 80]之间会有重复计数。 

**代码：**

```mysql
SELECT
	sc.cid,
	cname,
	COUNT(
	IF
		( score BETWEEN 0 AND 60, sid, NULL )) / COUNT(
	IFNULL( sid, NULL )) AS '0-60',
	COUNT(
	IF
		( score BETWEEN 61 AND 70, sid, NULL )) / COUNT(
	IFNULL( sid, NULL )) AS '60-70',
	COUNT(
	IF
		( score BETWEEN 71 AND 85, sid, NULL )) / COUNT(
	IFNULL( sid, NULL )) AS '70-85',
	COUNT(
	IF
		( score BETWEEN 86 AND 100, sid, NULL )) / COUNT(
	IFNULL( sid, NULL )) AS '85-100' 
FROM
	sc
	JOIN course ON sc.cid = course.cid 
GROUP BY
	sc.cid,
	cname
```

**结果：**

| cid  | cname | 0-60   | 60-70  | 70-85  | 85-100 |
| ---- | ----- | ------ | ------ | ------ | ------ |
| 1    | 语文  | 0.3333 | 0.1667 | 0.5000 | 0.0000 |
| 2    | 数学  | 0.3333 | 0.0000 | 0.1667 | 0.5000 |
| 3    | 英语  | 0.3333 | 0.0000 | 0.3333 | 0.3333 |

#### 23. 查询学生平均成绩及其名次

**思路：** MySQL中没有rank()函数，要手动实现

**代码：**

```mysql
-- 注意，因为SELECT比ORDER BY先执行，所以要做一个子查询，之后再实现名次；
SET @curRank := 0;
SELECT
	sid,
	avg_score,
	@curRank := @curRank + 1 AS rankorder 
FROM
	( SELECT sid, AVG( score ) AS avg_score FROM sc GROUP BY sid ORDER BY avg_score DESC ) AS t
```

**结果：**

| sid  | avg_score | rankorder |
| ---- | --------- | --------- |
| 7    | 93.50000  | 1         |
| 1    | 89.66667  | 2         |
| 5    | 81.50000  | 3         |
| 3    | 80.00000  | 4         |
| 2    | 70.00000  | 5         |
| 4    | 33.33333  | 6         |
| 6    | 32.50000  | 7         |

#### 24. 查询各科成绩前三名的记录

**思路：** 利用窗口函数dense_rank()

**代码：**

```mysql
SELECT
	sid,
	cid,
	ranking 
FROM
	( SELECT cid, sid, dense_rank () over ( PARTITION BY cid ORDER BY score DESC ) AS ranking FROM sc ) AS t 
WHERE
	ranking <= 3
```

**结果：**

| sid  | cid  | ranking |
| ---- | ---- | ------- |
| 1    | 1    | 1       |
| 3    | 1    | 1       |
| 5    | 1    | 2       |
| 2    | 1    | 3       |
| 1    | 2    | 1       |
| 7    | 2    | 2       |
| 5    | 2    | 3       |
| 1    | 3    | 1       |
| 7    | 3    | 2       |
| 2    | 3    | 3       |
| 3    | 3    | 3       |

#### 25. 查询每门课程被选修的学生数

**思路：** 以cid分组，使用COUNT() 函数计数

**代码：** 

```mysql
SELECT
	cid,
	COUNT( DISTINCT sid ) AS cnt 
FROM
	sc 
GROUP BY
	cid
```

**结果：**

| cid  | cnt  |
| ---- | ---- |
| 1    | 6    |
| 2    | 6    |
| 3    | 6    |

#### 26. 查询出只选修了二门课程的全部学生的学号和姓名

**思路：** 查询出后连接到student表即可

**代码：**

```mysql
SELECT
	t.sid,
	student.sname 
FROM
	( SELECT sid, count( sid ) AS cnt FROM sc GROUP BY sid HAVING cnt = 2 ) AS t
	LEFT JOIN student ON t.sid = student.sid
```

**结果：**

| sid  | sname |
| ---- | ----- |
| 5    | 周梅  |
| 6    | 吴兰  |
| 7    | 郑竹  |

#### 27. 查询男生、女生人数

**思路：** 用ssex分组，count(sid)即可

**代码：**

```mysql
SELECT
	ssex,
	COUNT( sid ) AS cnt
FROM
	student 
GROUP BY
	ssex
```

**结果：**

| ssex | cnt  |
| ---- | ---- |
| 男   | 4    |
| 女   | 4    |

#### 28. 查询名字中含有"风"字的学生信息

**思路：** LIKE的用法

**代码：**

```mysql
SELECT
	* 
FROM
	student 
WHERE
	sname LIKE "%风%"
```

**结果：**

| sid  | sname | sage                | ssex |
| ---- | ----- | ------------------- | ---- |
| 3    | 孙风  | 1990-05-20 00:00:00 | 男   |

#### 29. 查询1990年出生的学生名单(注：Student表中Sage列的类型是datetime)

**思路：** where筛选；year() 返回年份

**代码：**

```mysql
SELECT
	sid, sname 
FROM
	student 
WHERE
	YEAR ( sage ) = 1990
```

**结果：**

| sid  | sname |
| ---- | ----- |
| 1    | 赵雷  |
| 2    | 钱电  |
| 3    | 孙风  |
| 4    | 李云  |
| 8    | 王菊  |

#### 30. 查询每门课程的平均成绩，结果按平均成绩升序排列，平均成绩相同时，按课程号降序排列

**思路：** 考察AVG()和ORDER BY

**代码：**

```mysql
SELECT
	cid,
	AVG( score ) AS avg_score 
FROM
	sc 
GROUP BY
	cid 
ORDER BY
	avg_score ASC,
	cid DESC
```

**结果：**

| cid  | avg_score |
| ---- | --------- |
| 1    | 64.50000  |
| 3    | 68.50000  |
| 2    | 72.66667  |

#### 31. 查询同名同性学生名单，并统计同名人数

**思路：** 自连接

**代码：**

```mysql
SELECT s1.sname, s1.ssex, COUNT(*) FROM student AS s1
JOIN student AS s2
on s1.sname = s2.sname AND s1.ssex = s2.ssex AND s1.sid <> s2.sid
GROUP BY s1.sname, s2.ssex
```

**结果：**

NULL

#### 32. 查询平均成绩大于等于85的所有学生的学号、姓名和平均成绩 

**思路：**算平均成绩，连接Student表

**代码：**

```mysql
SELECT
	stu.sid,
	stu.sname,
	AVG( score ) AS avg_score 
FROM
	sc
	LEFT JOIN student AS stu ON sc.sid = stu.sid 
GROUP BY
	stu.sid,
	stu.sname 
HAVING
	avg_score >= 85
```

**结果：**

| sid  | sname | avg_score |
| ---- | ----- | --------- |
| 1    | 赵雷  | 89.66667  |
| 7    | 郑竹  | 93.50000  |

#### 33. 查询课程名称为"数学"，且分数低于60的学生姓名和分数

**思路：** 用表连接的方式

**代码：**

```mysql
SELECT
	student.sname,
	sc.score 
FROM
	course
	JOIN sc ON course.cid = sc.cid
	LEFT JOIN student ON sc.sid = student.sid 
WHERE
	course.cname = "数学" 
	AND score < 60
```

**结果：**

| sname | score |
| ----- | ----- |
| 李云  | 30.0  |

#### 34. 查询所有学生的课程及分数情况

**思路：** 注意是所有学生，所以是LEFT JOIN;

**代码：**

```mysql
SELECT
	student.sid,
	course.cname,
	sc.score 
FROM
	student
	LEFT JOIN sc ON student.sid = sc.sid
	LEFT JOIN course ON sc.cid = course.cid 
ORDER BY
	student.sid ASC,
	sc.cid ASC
```

**结果：**

| sid  | cname | score |
| ---- | ----- | ----- |
| 1    | 语文  | 80.0  |
| 1    | 数学  | 90.0  |
| 1    | 英语  | 99.0  |
| 2    | 语文  | 70.0  |
| 2    | 数学  | 60.0  |
| 2    | 英语  | 80.0  |
| 3    | 语文  | 80.0  |
| 3    | 数学  | 80.0  |
| 3    | 英语  | 80.0  |
| 4    | 语文  | 50.0  |
| 4    | 数学  | 30.0  |
| 4    | 英语  | 20.0  |
| 5    | 语文  | 76.0  |
| 5    | 数学  | 87.0  |
| 6    | 语文  | 31.0  |
| 6    | 英语  | 34.0  |
| 7    | 数学  | 89.0  |
| 7    | 英语  | 98.0  |
| 8    |       |       |

#### 35. 查询任何一门课程成绩在70分以上的姓名、课程名称和分数

**思路：** 考察表连接；注意，是每位同学的所有课程都大于70；

**代码：**

```mysql
-- 错误写法
SELECT
	student.sname,
	course.cname,
	score 
FROM
	sc
	LEFT JOIN student ON sc.sid = student.sid
	LEFT JOIN course ON sc.cid = course.cid 
WHERE
	score > 70
	
-- 正确结果
SELECT
	student.sname,
	course.cname,
	sc2.score 
FROM
	sc AS sc2
	JOIN (
	SELECT
		sid 
	FROM
		( SELECT sid, COUNT(*) AS cnt FROM sc WHERE score > 60 GROUP BY sid ) AS t 
	WHERE
	cnt = ( SELECT count( cid ) FROM sc WHERE t.sid = sc.sid )) AS t1 ON sc2.sid = t1.sid
	LEFT JOIN course ON sc2.cid = course.cid
	LEFT JOIN student ON t1.sid = student.sid
```

**结果：**

| sname | cname | score |
| ----- | ----- | ----- |
| 赵雷  | 语文  | 80.0  |
| 赵雷  | 数学  | 90.0  |
| 赵雷  | 英语  | 99.0  |
| 孙风  | 语文  | 80.0  |
| 孙风  | 数学  | 80.0  |
| 孙风  | 英语  | 80.0  |
| 周梅  | 语文  | 76.0  |
| 周梅  | 数学  | 87.0  |
| 郑竹  | 数学  | 89.0  |
| 郑竹  | 英语  | 98.0  |

#### 36. 查询不及格的课程

**思路：** 注意表连接顺序

**代码：**

```mysql
SELECT
	student.sname,
	course.cname,
	score 
FROM
	sc
	LEFT JOIN student ON sc.sid = student.sid
	LEFT JOIN course ON sc.cid = course.cid 
WHERE
	score < 60
```

**结果：**

| sname | cname | score |
| ----- | ----- | ----- |
| 李云  | 语文  | 50.0  |
| 吴兰  | 语文  | 31.0  |
| 李云  | 数学  | 30.0  |
| 李云  | 英语  | 20.0  |
| 吴兰  | 英语  | 34.0  |

#### 37. 查询课程编号为01且课程成绩在80分以上的学生的学号和姓名

**思路：** 连接Student和SC表

**代码：**

```mysql
SELECT
	sc.sid,
	student.sname 
FROM
	sc
	LEFT JOIN student ON sc.sid = student.sid 
WHERE
	cid = '01' 
	AND score >= 80
```

**结果：**

| sid  | sname |
| ---- | ----- |
| 1    | 赵雷  |
| 3    | 孙风  |

#### 38. 求每门课程的学生人数

**思路：**  考察GROUP BY 和COUNT

**代码：**

```mysql
SELECT
	cid,
	COUNT(*) AS cnt 
FROM
	sc 
GROUP BY
	cid
```

**结果：**

| cid  | cnt  |
| ---- | ---- |
| 1    | 6    |
| 2    | 6    |
| 3    | 6    |

#### 39. 查询选修"张三"老师所授课程的学生中，成绩最高的学生信息及其成绩

**思路：** 连接Teacher，course和SC表

**代码：**

```mysql
SELECT
	student.sid,
	student.sname,
	score 
FROM
	teacher
	LEFT JOIN course ON teacher.tid = course.tid
	LEFT JOIN sc ON course.cid = sc.cid
	LEFT JOIN student ON sc.sid = student.sid 
WHERE
	tname = '张三' 
ORDER BY
	score DESC 
	LIMIT 1
```

**结果：**

| sid  | sname | score |
| ---- | ----- | ----- |
| 1    | 赵雷  | 90.0  |

#### 40. 查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩 

**思路：** 自连接；cid不同，score相同

**代码：**

```mysql
SELECT DISTINCT
	sc1.sid,
	sc1.cid,
	sc1.score 
FROM
	sc AS sc1
	JOIN sc AS sc2 ON sc1.cid <> sc2.cid 
	AND sc1.score = sc2.score
```

**结果：**

| sid  | cid  | score |
| ---- | ---- | ----- |
| 2    | 3    | 80.0  |
| 3    | 2    | 80.0  |
| 3    | 3    | 80.0  |
| 1    | 1    | 80.0  |
| 3    | 1    | 80.0  |

#### 41. 查询每门功成绩最好的前两名

**思路：** 和24题类似

**代码：**

```mysql
-- 解法一
SELECT
	sid,
	cid,
	ranking 
FROM
	( SELECT sid, cid, dense_rank () over ( PARTITION BY cid ORDER BY score DESC ) AS ranking, score FROM sc ) AS t 
WHERE
	ranking <= 2

```

**结果：**

| sid  | cid  | ranking |
| ---- | ---- | ------- |
| 1    | 1    | 1       |
| 3    | 1    | 1       |
| 5    | 1    | 2       |
| 1    | 2    | 1       |
| 7    | 2    | 2       |
| 1    | 3    | 1       |
| 7    | 3    | 2       |

#### 42. 统计每门课程的学生选修人数（超过5人的课程才统计）。要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列

**思路：** 考察GROUP BY和ORDER BY

**代码：**

```mysql
SELECT
	cid,
	COUNT(*) AS cnt 
FROM
	sc 
GROUP BY
	cid 
HAVING
	cnt >= 5 
ORDER BY
	cnt DESC,
	cid ASC
```

**结果：**

| cid  | cnt  |
| ---- | ---- |
| 1    | 6    |
| 2    | 6    |
| 3    | 6    |

#### 43. 检索至少选修三门课程的学生学号

**思路：** 和上一题类似

**代码：**

```mysql
SELECT
	sid,
	COUNT(*) AS cnt 
FROM
	sc 
GROUP BY
	sid 
HAVING
	cnt >= 3
```

**结果：**

| sid  | cnt  |
| ---- | ---- |
| 1    | 3    |
| 2    | 3    |
| 3    | 3    |
| 4    | 3    |

#### 44. 查询选修了全部课程的学生信息

**思路：** 先算course表中的课程数，然后连接student表

**代码：**

```mysql
SELECT
	sc.sid,
	student.sname,
	COUNT( cid ) AS cnt 
FROM
	sc
	LEFT JOIN student ON sc.sid = student.sid 
GROUP BY
	sc.sid,
	student.sname 
HAVING
	cnt = ( SELECT COUNT( DISTINCT cid ) FROM course )
```

**结果：**

| sid  | sname | cnt  |
| ---- | ----- | ---- |
| 1    | 赵雷  | 3    |
| 2    | 钱电  | 3    |
| 3    | 孙风  | 3    |
| 4    | 李云  | 3    |

#### 45. 查询各学生的年龄

**思路：** 从学生表中查询；考察curdate()和year()函数的用法

**代码：** 

```mysql
SELECT
	sid,
	sname,
	YEAR (
	CURDATE()) - YEAR ( sage ) AS age 
FROM
	student
```

**结果：**

| sid  | sname | age  |
| ---- | ----- | ---- |
| 1    | 赵雷  | 29   |
| 2    | 钱电  | 29   |
| 3    | 孙风  | 29   |
| 4    | 李云  | 29   |
| 5    | 周梅  | 28   |
| 6    | 吴兰  | 27   |
| 7    | 郑竹  | 30   |
| 8    | 王菊  | 29   |

#### 46. 查询本周过生日的同学

**思路：** 考察Weekofyear()函数用法

**代码：**

```mysql
SELECT
	sid,
	sname 
FROM
	student 
WHERE
	WEEKOFYEAR( sage ) = WEEKOFYEAR(
	CURDATE())
```

**结果：**

没人过生日，20190823

#### 47. 查询下周过生日的学生

**思路：** 考察date_add，和上一题类似

**代码：** 

```mysql
SELECT
	sid,
	sname 
FROM
	student 
WHERE
	WEEKOFYEAR( sage ) = WEEKOFYEAR(
	DATE_ADD( curdate(), INTERVAL 1 WEEK ))
```

**结果：**

没人过生日，20190823

#### 48. 查询本月过生日的学生

**思路：** 考察month()用法

**代码：**

```mysql
SELECT
	sid,
	sname 
FROM
	student 
WHERE
	MONTH(sage) = MONTH(CURDATE())
```

**结果：** 20190823

| sid  | sname |
| ---- | ----- |
| 4    | 李云  |

#### 49. 查询下个月过生日的学生

**思路：** 和47，48题类似

**代码：**

```mysql
SELECT
	sid,
	sname 
FROM
	student 
WHERE
	MONTH(sage) = MONTH(DATE_ADD(CURDATE(),INTERVAL 1 MONTH))
```

**结果：** 20190823，没人生日

#### 50. 把“SC”表中“张三”老师教的课的成绩都更改为此课程的平均成绩

**思路：** 考察UPDATE用法；为了不破坏原数据表，复制了一张sc表，命名为sc_copy1

**代码：**

```mysql
-- 错误写法
UPDATE sc_copy1 
SET score = (
	SELECT
		AVG( score ) 
	FROM
		teacher
		LEFT JOIN course ON teacher.tid = course.tid
		LEFT JOIN sc_copy1 ON course.cid = sc_copy1.cid 
	WHERE
	tname = '张三' 
	)
-- 报错信息：You can't specify target table 'sc_copy1' for update in FROM clause
-- 可以看出，不能在同一个SQL语句中，先SELECT，再UPDATE

-- 正确写法
UPDATE sc_copy1 
SET score = (
	SELECT
		avg_score 
	FROM
		(
		SELECT
			AVG( score ) AS avg_score 
		FROM
			teacher
			LEFT JOIN course ON teacher.tid = course.tid
			LEFT JOIN sc_copy1 ON course.cid = sc_copy1.cid 
		WHERE
		tname = '张三' 
	) AS t)
-- 多套了一层即可，此问题只出现在MYSQL中
```

### 总结

- 感觉这套题里面有一些重复
- 对于SQL，还是要多写多练
- 总的来说题目难度不算大
- 要多学习函数的用法

## Update log

- 2019年8月23日 13:36:23， 完成