## HackerRank SQL

### Basic Select

#### Revising the Select Query I

##### Problem

Query all columns for all American cities in the **CITY** table with populations larger than `100000`. The **CountryCode** for America is `USA`.

The **CITY** table is described as follows: 

![CITY.jpg](https://s3.amazonaws.com/hr-challenge-images/8137/1449729804-f21d187d0f-CITY.jpg)

##### Answer

```mysql
SELECT *
FROM CITY
WHERE POPULATION > 100000 AND COUNTRYCODE = 'USA'
```

#### Revising the Select Query II

##### Problem

Query the **NAME** field for all American cities in the **CITY** table with populations larger than `120000`. The *CountryCode* for America is `USA`.

The **CITY** table is described as follows:

![CITY.jpg](https://s3.amazonaws.com/hr-challenge-images/8137/1449729804-f21d187d0f-CITY.jpg)

##### Answer

```mysql
SELECT NAME 
FROM CITY
WHERE POPULATION > 120000 AND COUNTRYCODE = 'USA'
```

#### SELECT ALL

##### Problem

Query all columns (attributes) for every row in the **CITY** table.

CITY 表同上。

##### Answer

```mysql
SELECT * FROM CITY
```

#### Select By ID

##### Problem

Query all columns for a city in **CITY** with the *ID* `1661`.

CITY 表同上。

##### Answer

```mysql
SELECT * FROM CITY
WHERE ID = 1661
```

#### Japanese Cities' Attributes

##### Problem

Query all attributes of every Japanese city in the CITY table. The COUNTRYCODE for Japan is JPN.

CITY 表同上。

##### Answer

```mysql
SELECT * FROM CITY
WHERE COUNTRYCODE = 'JPN'
```

#### Japanese Cities' Names

##### Problem

Query the names of all the Japanese cities in the **CITY** table. The **COUNTRYCODE** for Japan is `JPN`.

CITY 表同上。

##### Answer

```mysql
SELECT NAME FROM CITY
WHERE COUNTRYCODE = 'JPN'
```

#### Weather Observation Station 1

##### Problem

Query a list of **CITY** and **STATE** from the **STATION** table.
	The **STATION** table is described as follows:

![img](https://s3.amazonaws.com/hr-challenge-images/9336/1449345840-5f0a551030-Station.jpg)

##### Answer

```mysql
SELECT CITY, STATE
FROM STATION
```

#### Weather Observation Station 3

##### Problem

Query a list of **CITY** names from **STATION** for cities that have an even **ID** number. Print the results in any order, but exclude duplicates from the answer.

STATION 表同上。

##### Answer

```mysql
SELECT DISTINCT CITY FROM STATION
WHERE ID % 2 = 0
```

#### Weather Observation Station 4

##### Problem

Find the difference between the total number of **CITY** entries in the table and the number of distinct **CITY** entries in the table.
	STATION 表同上。

##### Answer

考察COUNT() 和 DISTINCT的用法。

```mysql
SELECT COUNT(CITY) - COUNT(DISTINCT CITY) FROM STATION
```

#### Weather Observation Station 5

##### Problem

Query the two cities in **STATION** with the shortest and longest *CITY* names, as well as their respective lengths (i.e.: number of characters in the name). If there is more than one smallest or largest city, choose the one that comes first when ordered alphabetically.

##### Answer

考察ORDER BY 和 LIMIT的用法。

```mysql
(SELECT city, LENGTH(city) FROM STATION
ORDER BY LENGTH(city) ASC, city ASC
LIMIT 1)
UNION
(SELECT city, LENGTH(city) FROM STATION
ORDER BY LENGTH(city) DESC, city DESC
LIMIT 1);
```

#### Weather Observation Station 6

##### Problem

Query the list of *CITY* names starting with vowels (i.e., `a`, `e`, `i`, `o`, or `u`) from **STATION**. Your result *cannot* contain duplicates.

##### Answer

**注意：**下面那种写法在`HackerRank`网站上，只能在MS SQL Server语言下运行。

```sql
SELECT DISTINCT city FROM STATION
WHERE city LIKE '[aeiou]%'
```

如果需要在MySQL下运行，需要使用正则表达式，结果如下：

```mysql
SELECT DISTINCT city FROM STATION
WHERE CITY  REGEXP '^[aeiou]'
```

**同样要注意的是，**`MySQL`既支持`LIKE`语句，也支持正则表达式。但两则不要混着用，因为它们彼此不认识。用郭老师的话说就是——同行是冤家。

#### Weather Observation Station 7

##### Problem

Query the list of *CITY* names ending with vowels (a, e, i, o, u) from **STATION**. Your result *cannot* contain duplicates.

##### Answer

上面一题的要求是以元音字母开头，这一题则是以元音字母结尾。

```mysql
-- MySQL
SELECT DISTINCT city FROM STATION
WHERE CITY  REGEXP '[aeiou]$'

-- MS SQL Server
SELECT DISTINCT city FROM STATION
WHERE city LIKE '%[aeiou]'
```

#### Weather Observation Station 8

##### Problem

Query the list of *CITY* names from **STATION** which have vowels (i.e., *a*, *e*, *i*, *o*, and *u*) as both their first *and* last characters. Your result cannot contain duplicates.

##### Answer

首位都有元音字母。

```mysql
-- MySQL
SELECT DISTINCT city FROM STATION
WHERE CITY  REGEXP '^[aeiou].*[aeiou]$'  

-- MS SQL Server
SELECT DISTINCT city FROM STATION
WHERE city LIKE '[aeiou]%' AND city LIKE '%[aeiou]'
```

#### Weather Observation Station 9

##### Problem

Query the list of *CITY* names from **STATION** that *do not start* with vowels. Your result cannot contain duplicates.

##### Answer

不以元音字母开头。

```mysql
-- MySQL
SELECT DISTINCT city FROM STATION
WHERE CITY  REGEXP '^[^aeiou]'

-- MS SQL Server
SELECT DISTINCT city FROM STATION
WHERE city LIKE '[^aeiou]%'
```

#### Weather Observation Station 10

##### Problem

Query the list of *CITY* names from **STATION** that *do not end* with vowels. Your result cannot contain duplicates.

##### Answer

不以元音字母结尾。

```mysql
-- MySQL
SELECT DISTINCT city FROM STATION
WHERE CITY  REGEXP '[^aeiou]$'

-- MS SQL Server
SELECT DISTINCT city FROM STATION
WHERE city LIKE '%[^aeiou]'
```

#### Weather Observation Station 11

##### Problem

Query the list of *CITY* names from **STATION** that either do not start with vowels or do not end with vowels. Your result cannot contain duplicates.

##### Answer

首或位没有元音字母。**注意：**这里的关系是**“或”**。

```mysql
-- MySQL
SELECT DISTINCT city FROM STATION
WHERE CITY  REGEXP '^[^aeiou]|[^aeiou]$'  

-- MS SQL Server
SELECT DISTINCT city FROM STATION
WHERE city LIKE '[^aeiou]%' OR city LIKE '%[^aeiou]'
```

#### Weather Observation Station 12

##### Problem

Query the list of *CITY* names from **STATION** that *do not start* with vowels and *do not end* with vowels. Your result cannot contain duplicates.

##### Answer

首位都没有元音字母。**注意：**这里的关系是**“与”**。

```mysql
-- MySQL
SELECT DISTINCT city FROM STATION
WHERE CITY  REGEXP '^[^aeiou].*[^aeiou]$'

-- MS SQL Server
SELECT DISTINCT city FROM STATION
WHERE city LIKE '[^aeiou]%' AND city LIKE '%[^aeiou]'
```

#### Higher Than 75 Marks

##### Problem

Query the *Name* of any student in **STUDENTS** who scored higher than **75** *Marks*. Order your output by the *last three characters* of each name. If two or more students both have names ending in the same last three characters (i.e.: Bobby, Robby, etc.), secondary sort them by ascending *ID*.

![img](https://s3.amazonaws.com/hr-challenge-images/12896/1443815243-94b941f556-1.png)

##### Answer

```mysql
SELECT NAME FROM 
(SELECT NAME, ID, MARKS, RIGHT(NAME, 3) AS r3 FROM STUDENTS 
where MARKS > 75
ORDER BY r3, ID ASC) as t1
```

#### Employee Names

##### Problem

Write a query that prints a list of employee names (i.e.: the *name* attribute) from the **Employee** table in alphabetical order.

Where *employee_id* is an employee's ID number, *name* is their name, *months* is the total number of months they've been working for the company, and *salary* is their monthly salary.

##### Answer

```mysql
SELECT name FROM Employee
ORDER BY name
```

#### Employee Salaries

##### Problem

Write a query that prints a list of employee names (i.e.: the *name* attribute) for employees in **Employee** having a salary greater than `2000` per month who have been employees for less than `10` months. Sort your result by ascending *employee_id*.

##### Answer

```mysql
SELECT name FROM
(
SELECT name, employee_id, avg(salary) as avg_sal FROM Employee
WHERE months < 10
GROUP BY name, employee_id
HAVING avg_sal > 2000
ORDER BY employee_id) AS t1
```

---

### Aggregation

#### Weather Observation Station 2

##### Problem

Query the following two values from the **STATION** table:

1. The sum of all values in *LAT_N* rounded to a scale of 2 decimal places.
2. The sum of all values in *LONG_W* rounded to a scale of 2 decimal places.

##### Answer

```mysql
SELECT ROUND(SUM(LAT_N), 2) AS lat, ROUND(SUM(LONG_W), 2) AS lon 
FROM STATION
```

#### Weather Observation Station 13

##### Problem

Query the sum of *Northern Latitudes* (*LAT_N*) from **STATION** having values greater than 38.7880  and less than 137.2345 . Truncate your answer to 4 decimal places.

##### Answer

```mysql
SELECT TRUNCATE(SUM(LAT_N), 4) FROM STATION
WHERE LAT_N > 38.7880 AND LAT_N < 137.2345
```

#### Weather Observation Station 14

##### Problem

Query the greatest value of the *Northern Latitudes* (*LAT_N*) from **STATION** that is less than 137.2345. Truncate your answer to 4 decimal places.

##### Answer

```mysql
SELECT TRUNCATE(MAX(LAT_N), 4) FROM STATION
WHERE LAT_N < 137.2345
```

#### Weather Observation Station 15

##### Problem

Query the *Western Longitude* (*LONG_W*) for the largest *Northern Latitude* (*LAT_N*) in **STATION** that is less than 137.2345. Round your answer to 4 decimal places.

##### Answer

```mysql
SELECT ROUND(LONG_W, 4) FROM STATION
WHERE LAT_N = (SELECT MAX(LAT_N) FROM STATION 
WHERE LAT_N < 137.2345)
```

#### Weather Observation Station 16

##### Problem

Query the smallest *Northern Latitude* (*LAT_N*) from **STATION** that is greater than 38.7880. Round your answer to 4 decimal places.

##### Answer

```mysql
SELECT ROUND(MIN(LAT_N), 4) FROM STATION
WHERE LAT_N > 38.7880
```

#### Weather Observation Station 17

##### Problem

Query the *Western Longitude* (*LONG_W*)where the smallest *Northern Latitude* (*LAT_N*) in **STATION** is greater than 38.7780. Round your answer to 4 decimal places.

##### Answer

```mysql
SELECT ROUND(LONG_W, 4) FROM STATION
WHERE LAT_N = (
SELECT MIN(LAT_N) FROM STATION
WHERE LAT_N > 38.7780)
```

#### Weather Observation Station 18

##### Problem

Consider p1(a, b) and p2(c, d) to be two points on a *2D* plane.

-  happens to equal the minimum value in *Northern Latitude* (*LAT_N* in **STATION**).
-  happens to equal the minimum value in *Western Longitude* (*LONG_W* in **STATION**).
-  happens to equal the maximum value in *Northern Latitude* (*LAT_N* in **STATION**).
-  happens to equal the maximum value in *Western Longitude* (*LONG_W* in **STATION**).

Query the [Manhattan Distance](https://xlinux.nist.gov/dads/HTML/manhattanDistance.html) between points P1 and P2 and round it to a scale of 4 decimal places.

##### Answer

计算两点间的曼哈顿距离。

```mysql
SELECT ROUND(ABS(MIN(LAT_N) - MAX(LAT_N)), 4) + ROUND(ABS(MIN(LONG_W) - MAX(LONG_W)), 4) 
FROM STATION 
```

#### Weather Observation Station 19

##### Problem

Query the [Euclidean Distance](https://en.wikipedia.org/wiki/Euclidean_distance) between points p1 and p2 and *format your answer* to display 4 decimal digits.

##### Answer

计算欧几里得距离。

```mysql
SELECT ROUND(SQRT(POWER(MIN(LAT_N) - MAX(LAT_N), 2) + POWER(MIN(LONG_W) - MAX(LONG_W), 2)), 4)
FROM STATION 
```

#### Weather Observation Station 20

##### Problem

A *median* is defined as a number separating the higher half of a data set from the lower half. Query the *median* of the *Northern Latitudes* (*LAT_N*) from **STATION** and round your answer to 4 decimal places.

##### Answer

题目要计算中位数。中位数的计算，**首先**要讲数据按升序排列，这一点可以通过`order_by`实现。**第二，**要新增一列，按顺序标记处行数以方便最后取出。这一点可以先构建一个`@variable`，在每一次SELECT后都加1，之后使用`WHERE`来完成。**第三，**中位数的计算，可以简单分为奇数行和偶数行两种，因此与一个`@variable`来记录行数值，并在上一步中的`WHERE`出通过`CASE WHEN`进一步判断。如果是奇数，则平均单一行（结果和不平均一样）；如果是偶数，则平均两行。

```mysql
SET @N := 0;
SELECT COUNT(*) FROM STATION INTO @TOTAL;

SELECT ROUND(AVG(T.LAT_N), 4) FROM
(
SELECT @N := @N + 1 AS ROW_ID, LAT_N FROM STATION ORDER BY LAT_N) AS T
WHERE
	CASE WHEN MOD(@TOTAL, 2) = 0
	THEN ROW_ID IN (@TOTAL/2, (@TOTAL/2+1))
	ELSE ROW_ID = (@TOTAL + 1) / 2
	END;
```

#### Challenges

##### Problem

Julia asked her students to create some coding challenges. Write a query to print the *hacker_id*, *name*, and the total number of challenges created by each student. Sort your results by the total number of challenges in descending order. If more than one student created the same number of challenges, then sort the result by *hacker_id*. If more than one student created the same number of challenges and the count is less than the maximum number of challenges created, then exclude those students from the result.

##### Answer

这题有点难度哈。让我们屡一下思路。

- 首先，要把两个表做联结，输出hacker_id, name, 和count(challenge_id)

- 接下来，对分组后输出的内容做一定限制。根据题目要求，也可以分为两部分。

  - 对于challenge次数最多的同学，不管是有一位，还是有许多位，都可以输出。这样，我们就可以直接找出最大的challenge次数是多少，与上个步骤中的联结表匹配。

  - 第二步，题目要求，如果有两位同学challenge次数相同，且这个次数都小于最大的challenge次数，那么这些同学就不应该出现在输出表中。好像有点绕哈。

    那我们**仔细想一下哈**。假设有两位同学challenge次数相同，这个次数要么大于等于最大的challenge次数，要么小于。对于第一种情况的同学，已经被前面一小步的代码**带走了**，所以不用考虑。第二种情况，则要被舍弃。

    那么我们最终的输入表中，只剩下两种：1. challenge次数最大的同学；challenge次数不同的同学。第一种我们已经找到了，第二种怎么找呢？先找出challenge次数，对这个次数分组。分组后的内容就是challenge次数相同的个数。比如有49, 38, 49, 30几种challenge次数，对其分组后，则为49-2, 38-1, 30-1。我们需要的就是重复个数为1的challenge次数。再把这些challenge次数与前面的联结表匹配即可。

```mysql
SELECT
	h.hacker_id,
	h.NAME,
	count( c.challenge_id ) AS challenge_created 
FROM
	hackers AS h
	INNER JOIN challenges AS c ON h.hacker_id = c.hacker_id 
GROUP BY
	h.hacker_id,
	h.NAME 
HAVING
	challenge_created = ( SELECT max( c1.cnt_c1 ) FROM ( SELECT hacker_id, count( challenge_id ) AS cnt_c1 FROM challenges GROUP BY hacker_id ) AS c1 ) 
	OR challenge_created IN (
	SELECT
		c2.cnt_c2 
	FROM
		( SELECT count( challenge_id ) AS cnt_c2 FROM challenges GROUP BY hacker_id ) c2 
	GROUP BY
		c2.cnt_c2 
	HAVING
		count( c2.cnt_c2 ) = 1 
	) 
ORDER BY
	challenge_created DESC,
	hacker_id
```

#### Contest Leaderboard

##### Problem

You did such a great job helping Julia with her last coding contest challenge that she wants you to work on this one, too!

The total score of a hacker is the sum of their maximum scores for all of the challenges. Write a query to print the *hacker_id*, *name*, and total score of the hackers ordered by the descending score. If more than one hacker achieved the same total score, then sort the result by ascending *hacker_id*. Exclude all hackers with a total score of 0 from your result.

##### Answer

通过网页中的Sample可以发现，一个hacker_id可以由多个challenge_id，并且一个challenge_id可以出现多次，最终要challenge_id中分数最高的。简单说就是，每位同学可以做很多题，每题可以提交很多次（分数最高的计入成绩），最终算每位同学的总分。这就是平常MOOC上的算法一样的。

```mysql
SELECT
    t2.hacker_id,
    hackers.NAME,
    t2.total_score 
FROM
    (
    SELECT
        t1.hacker_id,
        SUM( t1.max_score ) AS total_score 
    FROM
        ( SELECT hacker_id, challenge_id, max( score ) AS max_score FROM submissions GROUP BY hacker_id, challenge_id ORDER BY hacker_id, challenge_id ) AS t1 
    GROUP BY
        t1.hacker_id 
    HAVING
        total_score > 0 
    ) AS t2
    LEFT JOIN hackers ON t2.hacker_id = hackers.hacker_id 
ORDER BY
    t2.total_score DESC,
    t2.hacker_id 
```

#### Average Population

##### Problem

Query the average population for all cities in **CITY**, rounded *down* to the nearest integer.

##### Answer

```mysql
SELECT round(avg(population), 0) FROM city
```

#### Japan Population

#### Problem

Query the sum of the populations for all Japanese cities in **CITY**. The *COUNTRYCODE* for Japan is **JPN**.

##### Answer

```mysql
SELECT sum(population) FROM city
where countrycode = 'JPN'
```

#### Population Density Difference

##### Problem

Query the difference between the maximum and minimum populations in **CITY**.

##### Answer

```mysql
SELECT MAX(population) - MIN(population) FROM city
```

#### The Blunder

##### Problem

Samantha was tasked with calculating the average monthly salaries for all employees in the **EMPLOYEES** table, but did not realize her keyboard's 0 key was broken until after completing the calculation. She wants your help finding the difference between her miscalculation (using salaries with any zeroes removed), and the actual average salary.

Write a query calculating the amount of error (i.e.: *actual - miscalculated* average monthly salaries), and round it up to the next integer.

##### Answer

```mysql
SELECT CEIL(AVG(salary) - AVG(REPLACE(salary, '0', ''))) FROM employees
```

#### Top Earners

##### Problem

We define an employee's *total earnings* to be their monthly *salary \* months* worked, and the *maximum total earnings* to be the maximum total earnings for any employee in the **Employee** table. Write a query to find the *maximum total earnings* for all employees as well as the total number of employees who have maximum total earnings. Then print these values as  space-separated integers.

##### Answer

```mysql
SELECT
	salary * months AS total_earning,
	count(*) 
FROM
	employee 
GROUP BY
	total_earning 
ORDER BY
	total_earning DESC 
	LIMIT 1;
```

#### Revising Aggregations - The Count Function

##### Problem

Query a *count* of the number of cities in **CITY** having a *Population* larger than 100, 000.

##### Answer

```mysql
SELECT COUNT(*) FROM CITY
WHERE POPULATION > 100000
```

#### Revising Aggregations - The Sum Function

##### Problem

Query the total population of all cities in **CITY** where *District* is **California**.

##### Answer

```mysql
SELECT SUM(POPULATION) FROM CITY
WHERE DISTRICT = 'California'
```

#### Revising Aggregations - Averages

##### Problem

Query the average population of all cities in **CITY** where *District* is **California**.

##### Answer

```mysql
SELECT AVG(POPULATION) FROM CITY
WHERE DISTRICT = 'California'
```





---

### Update Log

- 2020-10-13 20:32:35，Weather Observation Station 15
- 2020-10-14 20:22:18，Challegens
- 2020-10-15 16:28:06，Aggregation part is done.