:::danger
以下代码执行环境为 MySQL 8.0，有一部分不可照搬入 SQL Server。较为明显的便是存在分页查询的语句
:::

## 1. 查询平均成绩大于等于 60 分的同学的学生编号和学生姓名和平均成绩

```sql
SELECT
	s.Sid AS 学生编号,
	s.Sname AS 学生姓名,
	AVG( sc.score ) AS 平均成绩
FROM
	student AS s
	JOIN sc ON s.Sid = sc.Sid
GROUP BY
	s.Sid
HAVING
	AVG( sc.score ) >= 60;
```

## 2. 查询在 SC 表存在成绩的学生信息

```sql
SELECT
	s.*
FROM
	student AS s
	JOIN sc ON s.Sid = sc.Sid
GROUP BY
	s.Sid;
```

## 3. 查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩（没成绩的显示为 null ）

```sql
SELECT
	s.Sid AS 学生编号,
	s.Sname AS 学生姓名,
	( SELECT COUNT(*) FROM sc WHERE s.Sid = sc.Sid ) AS 选课总数,
	( SELECT SUM( sc.score ) FROM sc WHERE s.Sid = sc.Sid ) AS 总成绩
FROM
	student AS s;
```

## 4. 查有成绩的学生信息

```sql
SELECT
	s.*
FROM
	student AS s
	JOIN sc ON s.Sid = sc.Sid
GROUP BY
	s.Sid;
```

## 5. 查询学过「张三」老师授课的同学的信息

```sql
SELECT
	s.*
FROM
	student AS s
	JOIN sc ON s.Sid = sc.Sid
WHERE
	sc.Cid IN (
	SELECT
		c.Cid
	FROM
		course AS c
	WHERE
	c.Tid = ( SELECT t.Tid FROM teacher AS t WHERE t.Tname = '张三' ));
```

## 6. 查询没有学全所有课程的学生的信息

```sql
SELECT
	* 
FROM
	student AS s 
WHERE
	( SELECT COUNT(*) FROM sc WHERE sc.Sid = s.Sid ) = ( SELECT COUNT(*) FROM course AS c );
```

## 7. 查询和 "01" 号的同学学习的课程完全相同的其他同学的信息

```sql
SELECT
	* 
FROM
	student AS s 
WHERE
	s.Sid != '01' 
	AND (
	SELECT
		COUNT(*) 
	FROM
		sc 
	WHERE
		sc.Sid = s.Sid 
		AND sc.Cid IN ( SELECT sc.Cid FROM sc WHERE sc.Sid = '01' ) 
	) = ( SELECT COUNT(*) FROM sc WHERE sc.Sid = '01' );
```

## 8. 查询没学过 "张三" 老师讲授的任一门课程的学生姓名

```sql
SELECT
	s.Sname
FROM
	student AS s
WHERE
	0 = (
	SELECT
		COUNT(*)
	FROM
		sc
	WHERE
		sc.Sid = s.Sid
		AND sc.Cid NOT IN (
		SELECT
			c.Cid
		FROM
			course AS c
		WHERE
		c.Tid = ( SELECT t.Tid FROM teacher AS t WHERE t.Tname = '张三' )));
```

## 9. 查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩

```sql
SELECT
	s.Sid AS 学号,
	s.Sname AS 姓名,
	( SELECT AVG( sc.score ) FROM sc WHERE sc.Sid = s.Sid ) AS 平均成绩
FROM
	student AS s
WHERE
	2 <= ( SELECT COUNT(*) FROM sc WHERE sc.Sid = s.Sid AND sc.score < 60 );
```

## 10. 检索 "01" 课程分数小于 60，按分数降序排列的学生信息和

```sql
SELECT
	s.*,
	sc.score
FROM
	student AS s
	JOIN sc ON s.Sid = sc.Sid
WHERE
	sc.Cid = '01'
	AND sc.score < 60
ORDER BY
	sc.score DESC;
```

## 11. 按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

```sql
SELECT
	s.*,
	AVG( sc.score ) AS 平均成绩
FROM
	student AS s
	JOIN sc ON s.Sid = sc.Sid
GROUP BY
	s.Sid
ORDER BY
	平均成绩 DESC;
```

## 12. 查询成绩表中各科成绩前三名的信息以及排名

```sql
SELECT
	s.*,
	sc.Cid,
	sc.score,
	( SELECT COUNT(DISTINCT t.score) FROM sc AS t WHERE t.Cid = sc.Cid AND t.score >= sc.score ) AS 排名
FROM
	sc
	JOIN student AS s ON sc.Sid = s.Sid
WHERE
	( SELECT COUNT(*) FROM sc AS t WHERE t.Cid = sc.Cid AND t.score >= sc.score ) <= 3
ORDER BY
	sc.Cid,
	sc.score DESC;
```

## 13. 查询出只选修两门课程的学生学号和姓名

```sql
SELECT
	s.sid AS 学号,
	s.Sname AS 姓名 
FROM
	student AS s 
WHERE
	2 = ( SELECT COUNT(*) FROM sc WHERE sc.Sid = s.Sid );
```

## 14. 查询名字中含有「风」字的学生信息

```sql
SELECT
	*
FROM
	student AS s
WHERE
	s.Sname LIKE '%风%';
```

## 15. 查询 1990 年出生的学生信息

```sql
SELECT
	*
FROM
	student AS s
WHERE
	YEAR ( s.Sage ) = '1990';
```

## 16. 查询各学生的姓名、年龄（只按年份来计算）

```sql
SELECT
	s.Sname,
	YEAR ( NOW() ) - YEAR ( s.Sage ) AS 年龄
FROM
	student AS s;
```

## 17. 查询本月过生日的学生信息

```sql
SELECT
	* 
FROM
	student AS s 
WHERE
	MONTH (NOW()) = MONTH ( s.Sage );
```

## 18. 查询下下月过生日的学生信息

```sql
-- 跳两次
-- 大于 10，就得跳到下一年
SELECT 
	* 
FROM 
	student AS s 
WHERE ( 
	CASE 
		WHEN MONTH ( NOW()) > 10 THEN (NOW()) - 12 
		ELSE MONTH ( NOW()) 
	END 
) + 2 = MONTH ( s.Sage );
```

## 19. 查询成绩表第 2 页数据（每页 5 条数据，按照成绩降序排序）

```sql
SELECT
	* 
FROM
	sc 
ORDER BY
	sc.score DESC 
LIMIT 5,5;
```

## 20. 查询成绩表前 5 条数据（每页 5 条数据，按照成绩降序排序）

```sql
SELECT
	*
FROM
	sc
ORDER BY
	sc.score DESC
LIMIT 5
```

## 21. 修改 Course 表 Cname 字段类型为 nvarchar，长度为 100

> 答案只写 SQL

```sql
-- MySQL
ALTER TABLE Course MODIFY Cname NVARCHAR(100);
```

```sql
-- SQL Server
ALTER TABLE Course ALTER COLUMN Cname NVARCHAR(100);
```

## 22. 使用 SQL 语言复制 student 表结构和数据，复制的表名为 student_copy

> 答案只写 SQL

```sql
-- MySQL
CREATE TABLE student_copy SELECT * FROM student;
```

```sql
-- SQL Server
SELECT * INTO student_copy FROM student;
```

## 23. 查询所有的学生姓名和老师姓名（使用 `union`）

```sql
SELECT
	s.Sname
FROM
	student AS s UNION
SELECT
	t.Tname
FROM
	teacher AS t;
```

## 24. 列出每个学生的学生编号、姓名、课程名、授课老师姓名（没课程的学生不用列出）

```sql
SELECT
	s.Sid,
	s.Sname,
	c.Cid,
	c.Cname,
	t.Tname
FROM
	sc
	JOIN student AS s ON sc.Sid = s.Sid
	JOIN course AS c ON sc.Cid = c.Cid
	JOIN teacher AS t ON c.Tid = t.Tid;
```

## 25. 查询课程编号“02”的成绩比课程编号 “01” 课程低的所有同学的学号、姓名

```sql
SELECT
	s.Sid,
	s.Sname 
FROM
	student AS s 
WHERE
	( SELECT score FROM sc WHERE sc.Sid = s.Sid AND sc.Cid = '02' ) < ( SELECT score FROM sc WHERE sc.Sid = s.Sid AND sc.Cid = '01' );
```
