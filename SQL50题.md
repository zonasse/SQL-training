#SQL50题

## SQL:

```mysql
#建表
#学生表
CREATE TABLE IF NOT EXISTS Student(
		s_id	VARCHAR(20),
    s_name VARCHAR(20) NOT NULL DEFAULT '',
    s_birth VARCHAR(20) NOT NULL DEFAULT '',
    s_sex VARCHAR(10) NOT NULL DEFAULT '',
    PRIMARY KEY(s_id)
);
#课程表
CREATE TABLE IF NOT EXISTS Course(
    c_id  VARCHAR(20),
    c_name VARCHAR(20) NOT NULL DEFAULT '',
    t_id VARCHAR(20) NOT NULL,
    PRIMARY KEY(c_id)
);
#教师表
CREATE TABLE IF NOT EXISTS Teacher(
    t_id VARCHAR(20),
    t_name VARCHAR(20) NOT NULL DEFAULT '',
    PRIMARY KEY(t_id)
);
#成绩表
CREATE TABLE IF NOT EXISTS Score(
    s_id VARCHAR(20),
    c_id  VARCHAR(20),
    s_score INT(3),
    PRIMARY KEY(s_id,c_id)
);
#插入学生表测试数据
insert into Student values('01' , '赵雷' , '1990-01-01' , '男');
insert into Student values('02' , '钱电' , '1990-12-21' , '男');
insert into Student values('03' , '孙风' , '1990-05-20' , '男');
insert into Student values('04' , '李云' , '1990-08-06' , '男');
insert into Student values('05' , '周梅' , '1991-12-01' , '女');
insert into Student values('06' , '吴兰' , '1992-03-01' , '女');
insert into Student values('07' , '郑竹' , '1989-07-01' , '女');
insert into Student values('08' , '王菊' , '1990-01-20' , '女');
#课程表测试数据
insert into Course values('01' , '语文' , '02');
insert into Course values('02' , '数学' , '01');
insert into Course values('03' , '英语' , '03');

#教师表测试数据
insert into Teacher values('01' , '张三');
insert into Teacher values('02' , '李四');
insert into Teacher values('03' , '王五');

#成绩表测试数据
insert into Score values('01' , '01' , 80);
insert into Score values('01' , '02' , 90);
insert into Score values('01' , '03' , 99);
insert into Score values('02' , '01' , 70);
insert into Score values('02' , '02' , 60);
insert into Score values('02' , '03' , 80);
insert into Score values('03' , '01' , 80);
insert into Score values('03' , '02' , 80);
insert into Score values('03' , '03' , 80);
insert into Score values('04' , '01' , 50);
insert into Score values('04' , '02' , 30);
insert into Score values('04' , '03' , 20);
insert into Score values('05' , '01' , 76);
insert into Score values('05' , '02' , 87);
insert into Score values('06' , '01' , 31);
insert into Score values('06' , '03' , 34);
insert into Score values('07' , '02' , 89);
insert into Score values('07' , '03' , 98);
```

##1、查询"01"课程比"02"课程成绩高的学生的信息及课程分数  

```mysql
SELECT ali_student.*, ali_score.s_score FROM Student AS ali_student
INNER JOIN Score AS ali_score ON ali_student.s_id = ali_score.s_id AND ali_score.c_id = '01' 
LEFT JOIN Score ali_score_2 ON ali_student.s_id = ali_score_2.s_id AND (ali_score_2.c_id = '02' OR ali_score_2.c_id IS NULL)
WHERE ali_score.s_score > ali_score_2.s_score;
```



## 2、查询"01"课程比"02"课程成绩低的学生的信息及课程分数

```
SELECT ali_student.*, ali_score.s_score FROM Student AS ali_student
INNER JOIN Score AS ali_score ON ali_student.s_id = ali_score.s_id AND ali_score.c_id = '01' 
LEFT JOIN Score ali_score_2 ON ali_student.s_id = ali_score_2.s_id AND (ali_score_2.c_id = '02' OR ali_score_2.c_id IS NULL)
WHERE ali_score.s_score < ali_score_2.s_score;
```



## 3、查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩

```mysql
SELECT ali_student.s_id, ali_student.s_name, AVG(ali_score.s_score) AS average_score 
FROM Student AS ali_student INNER JOIN Score AS ali_score 
ON ali_student.s_id = ali_score.s_id  GROUP BY ali_student.s_id 
HAVING average_score >= 60;
```

## 4、查询平均成绩小于60分的同学的学生编号和学生姓名和平均成绩(包括有成绩的和无成绩的)

```mysql
SELECT ali_student.s_id, ali_student.s_name, AVG(ali_score.s_score) AS average_score 
FROM Student AS ali_student INNER JOIN Score AS ali_score 
ON ali_student.s_id = ali_score.s_id  GROUP BY ali_student.s_id 
HAVING average_score < 60;
```

## 5、查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩

```mysql
SELECT ali_student.s_id,ali_student.s_name, COUNT(*) AS course_count, SUM(ali_score.s_score) AS sum_score 
FROM Student AS ali_student INNER JOIN Score AS ali_score ON ali_student.s_id = ali_score.s_id GROUP BY ali_score.s_id;
```

## 6、查询"李"姓老师的数量 

```mysql
SELECT COUNT(t_name) FROM Teacher WHERE t_name like '李%';
```

## 7、查询学过"张三"老师授课的同学的信息 

```mysql
#1
SELECT ali_student.* FROM 
((Student AS ali_student INNER JOIN Score AS ali_score ON ali_student.s_id = ali_score.s_id) 
 INNER JOIN Course AS ali_course ON ali_score.c_id = ali_course.c_id) 
 INNER JOIN Teacher AS ali_teacher ON ali_course.t_id = ali_teacher.t_id AND ali_teacher.t_name = '张三';
 #2
 SELECT ali_student.* FROM Student AS ali_student INNER JOIN Score AS ali_score ON ali_student.s_id = ali_score.s_id WHERE ali_score.c_id IN (SELECT c_id FROM Course WHERE t_id = (SELECT t_id FROM Teacher WHERE t_name = '张三') );
```

## 8、查询没学过"张三"老师授课的同学的信息 

```mysql
SELECT ali_student.* FROM Student AS ali_student WHERE ali_student.s_id NOT IN 
(SELECT ali_student_1.s_id FROM Student AS ali_student_1 INNER JOIN Score AS ali_score ON ali_student_1.s_id =  ali_score.s_id WHERE ali_score.c_id IN 
 (SELECT c_id FROM Course WHERE t_id IN 
  (SELECT t_id FROM Teacher WHERE t_name = '张三')));

```

## 9、查询学过编号为"01"并且也学过编号为"02"的课程的同学的信息

```mysql
#1
SELECT ali_student.* FROM Student AS ali_student, Score AS ali_score1, Score AS ali_score2 WHERE ali_student.s_id = ali_score1.s_id AND ali_student.s_id = ali_score2.s_id AND ali_score1.c_id = '01' AND ali_score2.c_id = '02';
#2
SELECT * FROM Student WHERE s_id IN (SELECT ali_s1.s_id FROM Score AS ali_s1  INNER JOIN Score AS ali_s2 ON ali_s1.s_id = ali_s2.s_id AND ali_s1.c_id = '01' AND ali_s2.c_id = '02');
```

## 10、查询学过编号为"01"但是没有学过编号为"02"的课程的同学的信息

````mysql
SELECT ali_student.* FROM Student AS ali_student 
WHERE ali_student.s_id IN (SELECT s_id FROM Score WHERE c_id = '01') 
AND ali_student.s_id NOT IN (SELECT s_id FROM Score WHERE c_id = '02');
````

## 11、查询没有学全所有课程的同学的信息 

```mysql
SELECT * FROM Student WHERE s_id IN 
(SELECT s_id FROM Score GROUP BY s_id HAVING COUNT(s_id) < (SELECT COUNT(*) FROM Course)) OR s_id NOT IN (SELECT s_id FROM Score);
```

## 12、查询至少有一门课与学号为"01"的同学所学相同的同学的信息 

```mysql
SELECT * FROM Student WHERE s_id IN ( SELECT DISTINCT s_id FROM Score WHERE s_id <> '01' AND c_id IN ( SELECT c_id FROM Score WHERE s_id = '01'));
```

## 13、查询和"01"号的同学学习的课程完全相同的其他同学的信息 

```mysql
SELECT * FROM Student WHERE s_id IN
( SELECT DISTINCT s_id FROM Score WHERE s_id <> '01' AND c_id IN 
 ( SELECT c_id FROM Score WHERE s_id = '01') GROUP BY s_id HAVING COUNT(1) = (SELECT COUNT(1) FROM Score WHERE s_id = '01'));
```

## 14、查询没学过"张三"老师讲授的任一门课程的学生姓名 

```mysql
SELECT * FROM Student WHERE s_id NOT IN (SELECT s_id FROM Score WHERE c_id IN (SELECT c_id FROM Course AS ali_course INNER JOIN Teacher AS ali_teacher ON ali_course.t_id = ali_teacher.t_id AND ali_teacher.t_name = '张三'));
```

## 15、查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩 

```mysql
SELECT ali_student.s_id,ali_student.s_name,ali_1.avg_score FROM Student AS ali_student INNER JOIN 
(SELECT s_id,AVG(s_score) AS avg_score FROM Score WHERE s_score < 60 GROUP BY s_id  HAVING COUNT(1) >= 2) AS ali_1 
ON ali_student.s_id = ali_1.s_id;
```

## 16、检索"01"课程分数小于60，按分数降序排列的学生信息

```mysql
SELECT ali_student.*,ali_1.s_score FROM Student AS ali_student INNER JOIN 
(SELECT s_id,s_score FROM Score WHERE c_id = '01' AND s_score < 60) AS ali_1 
ON ali_student.s_id = ali_1.s_id ORDER BY ali_1.s_score DESC;
```

## 17、按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

```mysql
SELECT ali_score.s_id,
(SELECT s_score FROM Score WHERE c_id = '01' AND s_id = ali_score.s_id) AS chinese, (SELECT s_score FROM Score WHERE c_id = '02' AND s_id = ali_score.s_id) AS math,
(SELECT s_score FROM Score WHERE c_id = '03' AND s_id = ali_score.s_id) AS english, ROUND(AVG(s_score),2) AS avg_score 
FROM Score AS ali_score GROUP BY ali_score.s_id ORDER BY avg_score DESC;
```

## 18.查询各科成绩最高分、最低分和平均分：以如下形式显示：课程ID，课程name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率--及格为>=60，中等为：70-80，优良为：80-90，优秀为：>=90

```mysql
SELECT 
ali_score.c_id,
ali_course.c_name,
MAX(s_score),
MIN(s_score), 
AVG(s_score), 
CONCAT(ROUND(100 * SUM(CASE WHEN ali_score.s_score >=60 THEN 1 ELSE 0 END)/SUM(CASE WHEN ali_score.s_score IS NOT NULL THEN 1 ELSE 0 END),2),'%') AS '及格率', 
CONCAT(ROUND(100* SUM(CASE WHEN ali_score.s_score >= 70 AND ali_score.s_score <= 80 THEN 1 ELSE 0 END)/SUM(CASE WHEN ali_score.s_score IS NOT NULL THEN 1 ELSE 0 END),2),'%') AS '中等率', 
CONCAT(ROUND(100 * SUM(CASE WHEN ali_score.s_score >= 80 AND ali_score.s_score <= 90 THEN 1 ELSE 0 END)/SUM(CASE WHEN ali_score.s_score IS NOT NULL THEN 1 ELSE 0 END),2),'%') AS '优良率',
CONCAT(ROUND(100 * SUM(CASE WHEN ali_score.s_score >= 90 THEN 1 ELSE 0 END)/SUM(CASE WHEN ali_score.s_score IS NOT NULL THEN 1 ELSE 0 END),2),'%') AS '优秀率'  
FROM Score AS ali_score  INNER JOIN Course AS ali_course ON ali_score.c_id = ali_course.c_id GROUP BY c_id ;
```

## 19、按各科成绩进行排序，并显示排名(实现不完全)
        -- mysql没有rank函数

```mysql
SELECT ali_temp.s_id,ali_temp.c_id, @i:=@i+1 AS '完全排名',@k := (CASE WHEN @score = ali_temp.s_score THEN @k ELSE @i END) AS '不完全排名', @score := ali_temp.s_score AS s_score  FROM ( SELECT s_id, c_id, s_score FROM Score WHERE c_id = '01' GROUP BY s_id,c_id,s_score ORDER BY s_score DESC) AS ali_temp, (SELECT @k:=0, @i:=0, @score:=0) AS ali_temp1
union
SELECT ali_temp.s_id,ali_temp.c_id, @i := @i+1 AS '完全排名', @k := (CASE WHEN @score = ali_temp.s_score THEN @k ELSE @i END) AS '不完全排名', @score := ali_temp.s_score AS s_score FROM (SELECT s_id, c_id, s_score FROM Score WHERE c_id = '02' GROUP BY s_id,c_id,s_score ORDER BY s_score DESC) AS ali_temp, (SELECT @k:=0,@i:=0,@score:=0) AS ali_temp1
union
SELECT ali_temp.s_id,ali_temp.c_id, @i := @i+1 AS '完全排名', @k := (CASE WHEN @score = ali_temp.s_score THEN @k ELSE @i END) AS '不完全排名', @score := ali_temp.s_score AS s_score FROM (SELECT s_id, c_id, s_score FROM Score WHERE c_id = '03' GROUP BY s_id,c_id,s_score ORDER BY s_score DESC) AS ali_temp, (SELECT @k:=0,@i:=0,@score:=0) AS ali_temp1;
```

## 20、查询学生的总成绩并进行排名

```mysql
SELECT ali_temp.s_id, @i := @i+1 AS '完全排名', @k := (CASE WHEN @score = ali_temp.sum_score THEN @k ELSE @i END) AS '不完全排名', @score := ali_temp.sum_score AS '总分' FROM 
(SELECT s_id, SUM(s_score) AS sum_score FROM Score GROUP BY s_id ORDER BY sum_score DESC) AS ali_temp, (SELECT @k :=0, @i:=0, @score :=0) AS ali_temp1;
```

## 21、查询不同老师所教不同课程平均分从高到低显示 

```mysql
select a.t_id,c.t_name,a.c_id,ROUND(avg(s_score),2) as avg_score from course a
        left join score b on a.c_id=b.c_id 
        left join teacher c on a.t_id=c.t_id
        GROUP BY a.c_id,a.t_id,c.t_name ORDER BY avg_score DESC;
```

## 22、查询所有课程的成绩第2名到第3名的学生信息及该课程成绩

```mysql
SELECT ali_student.*, ali_temp._rank, ali_temp.s_score, ali_temp.c_id FROM (SELECT s_id, @i := @i+1 AS _rank, s_score, c_id FROM Score, (SELECT @i:=0) AS temp WHERE c_id = '01') AS ali_temp
INNER JOIN Student AS ali_student ON ali_temp.s_id = ali_student.s_id WHERE ali_temp._rank BETWEEN 2 AND 3
UNION 
SELECT ali_student.*, ali_temp._rank, ali_temp.s_score, ali_temp.c_id FROM (SELECT s_id, @j := @j+1 AS _rank, s_score, c_id FROM Score, (SELECT @j:=0) AS temp WHERE c_id = '02') AS ali_temp 
INNER JOIN Student AS ali_student ON ali_temp.s_id = ali_student.s_id WHERE ali_temp._rank BETWEEN 2 AND 3
UNION 
SELECT ali_student.*, ali_temp._rank, ali_temp.s_score, ali_temp.c_id FROM (SELECT s_id, @k := @k+1 AS _rank, s_score, c_id FROM Score, (SELECT @k:=0) AS temp WHERE c_id = '03') AS ali_temp 
INNER JOIN Student AS ali_student ON ali_temp.s_id = ali_student.s_id WHERE ali_temp._rank BETWEEN 2 AND 3;;
```

## 23、统计各科成绩各分数段人数：课程编号,课程名称,[100-85],           [85-70],[70-60],[0-60]及所占百分比

```mysql
SELECT ali_score.c_id, ali_course.c_name, 
SUM(CASE WHEN ali_score.s_score > 85 AND ali_score.s_score <= 100 THEN 1 ELSE 0 END) AS '(85,100]',CONCAT(ROUND(100*(SUM(CASE WHEN ali_score.s_score > 85 AND ali_score.s_score <= 100 THEN 1 ELSE 0 END))/SUM(CASE WHEN ali_score.s_score IS NOT NULL THEN 1 ELSE 0 END),2),'%') AS '(85,100] rate',
SUM(CASE WHEN ali_score.s_score > 70 AND ali_score.s_score <= 85 THEN 1 ELSE 0 END) AS '(70,85]',CONCAT(ROUND(100*(SUM(CASE WHEN ali_score.s_score > 70 AND ali_score.s_score <= 85 THEN 1 ELSE 0 END))/SUM(CASE WHEN ali_score.s_score IS NOT NULL THEN 1 ELSE 0 END),2),'%') AS '(70,85] rate',
SUM(CASE WHEN ali_score.s_score > 60 AND ali_score.s_score <= 70 THEN 1 ELSE 0 END) AS '(60,70]', CONCAT(ROUND(100*(SUM(CASE WHEN ali_score.s_score > 60 AND ali_score.s_score <= 70 THEN 1 ELSE 0 END))/SUM(CASE WHEN ali_score.s_score IS NOT NULL THEN 1 ELSE 0 END),2),'%') AS '(60,70] rate',
SUM(CASE WHEN ali_score.s_score >= 0 AND ali_score.s_score <= 60 THEN 1 ELSE 0 END) AS '(0,60]', CONCAT(ROUND(100*(SUM(CASE WHEN ali_score.s_score >= 0 AND ali_score.s_score <= 60 THEN 1 ELSE 0 END))/SUM(CASE WHEN ali_score.s_score IS NOT NULL THEN 1 ELSE 0 END),2),'%') AS '[0,60] rate'
FROM Score AS ali_score INNER JOIN Course AS ali_course ON ali_score.c_id = ali_course.c_id GROUP BY ali_score.c_id;
```

##24、查询学生平均成绩及其名次 

````mysql
SELECT ali_temp.s_id,ali_temp.avg_score AS '平均成绩', @i := @i+1 AS '完全排名', @k := (CASE WHEN @score = ali_temp.avg_score THEN @k ELSE @i END) AS '不完全排名' FROM 
(SELECT s_id, ROUND(AVG(s_score),2) AS avg_score FROM Score GROUP BY s_id ORDER BY avg_score DESC) AS ali_temp, 
(SELECT @i:=0,@k:=0) AS ali_temp1;
````

##25、查询各科成绩前三名的记录

##        1.选出b表比a表成绩大的所有组
        2.选出比当前id成绩大的 小于三个的

```mysql
SELECT ali_score.s_id, ali_score.c_id, ali_score.s_score FROM Score AS ali_score LEFT JOIN Score AS ali_score2 ON 
ali_score.c_id = ali_score2.c_id AND ali_score.s_score < ali_score2.s_score
GROUP BY ali_score.s_id, ali_score.c_id, ali_score.s_score HAVING COUNT(ali_score2.s_id) < 3
ORDER BY ali_score.c_id,ali_score.s_score DESC;
```

##26、查询每门课程被选修的学生数 

```mysql
SELECT c_id, COUNT(s_id) AS '选课人数' FROM Score GROUP BY c_id;
```

##27、查询出只有两门课程的全部学生的学号和姓名 

```mysql
SELECT s_id,s_name FROM Student WHERE s_id IN (SELECT s_id FROM Score GROUP BY s_id HAVING COUNT(c_id) = 2);
```

##28、查询男生、女生人数 

```mysql
#1
select s_sex,COUNT(s_sex) as '人数'  from Student GROUP BY s_sex;
#2
SELECT (SELECT  COUNT(*)  FROM Student GROUP BY s_sex HAVING s_sex = '男') AS '男生人数',(SELECT COUNT(*)  FROM Student GROUP BY s_sex HAVING s_sex = '女') AS '女生人数';
```

## 29、查询名字中含有"风"字的学生信息

```mysql
SELECT * FROM Student WHERE s_name LIKE '%风%';
```

##30、查询同名同性学生名单，并统计同名人数 

```mysql
SELECT ali_student.s_name, ali_student.s_sex,COUNT(*) FROM Student AS ali_student INNER JOIN Student AS ali_student2 ON ali_student.s_id != ali_student2.s_id AND ali_student.s_name = ali_student2.s_name AND ali_student.s_sex = ali_student2.s_sex
GROUP BY ali_student.s_name, ali_student.s_sex;
```

##31、查询1990年出生的学生名单

```mysql
SELECT * FROM Student WHERE YEAR(s_birth) = '1990';
```

##32、查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列 

```mysql
SELECT c_id,ROUND(AVG(s_score),2) AS '平均成绩' FROM Score GROUP BY c_id ORDER BY AVG(s_score) DESC, c_id ASC;
```

##33、查询平均成绩大于等于85的所有学生的学号、姓名和平均成绩 

```mysql
SELECT ali_student.s_id, ali_student.s_name,ROUND(ali_temp.avg_score,2) AS '平均成绩' FROM Student AS ali_student INNER JOIN (SELECT s_id, AVG(s_score) AS avg_score FROM Score GROUP BY s_id HAVING AVG(s_score) >= 85) AS ali_temp ON ali_student.s_id = ali_temp.s_id;
```

##34、查询课程名称为"数学"，且分数低于60的学生姓名和分数 

```mysql
SELECT s_name,s_score FROM Student AS ali_student INNER JOIN Score AS ali_score ON ali_student.s_id = ali_score.s_id AND ali_score.s_score < 60 INNER JOIN Course AS ali_course ON ali_score.c_id = ali_course.c_id AND ali_course.c_name = '数学';
```

##35、查询所有学生的课程及分数情况； 

```mysql
SELECT ali_student.s_id,ali_student.s_name,ali_score.c_id,ali_course.c_name,ali_score.s_score FROM Student AS ali_student INNER JOIN Score AS ali_score ON ali_student.s_id = ali_score.s_id INNER JOIN Course AS ali_course ON ali_score.c_id = ali_course.c_id;
```

##36、查询任何一门课程成绩在70分以上的姓名、课程名称和分数； 

```mysql
SELECT ali_student.s_name,ali_course.c_name,ali_score.s_score FROM Student AS ali_student INNER JOIN Score AS ali_score ON ali_student.s_id
= ali_score.s_id AND ali_score.s_score >= 70 INNER JOIN Course AS ali_course ON ali_score.c_id = ali_course.c_id;
```

##37、查询不及格的课程

```mysql
SELECT ali_student.s_name,ali_course.c_name,ali_score.s_score FROM Student AS ali_student INNER JOIN Score AS ali_score ON ali_student.s_id
= ali_score.s_id AND ali_score.s_score < 60 INNER JOIN Course AS ali_course ON ali_score.c_id = ali_course.c_id;
```

##38、查询课程编号为01且课程成绩在80分以上的学生的学号和姓名； 

```mysql
SELECT ali_student.s_id,ali_student.s_name,ali_score.s_score FROM Student AS ali_student INNER JOIN Score AS ali_score ON ali_student.s_id
= ali_score.s_id AND ali_score.s_score >= 80 AND ali_score.c_id = '01';
```

##39、求每门课程的学生人数 

```mysql
SELECT ali_course.c_id,ali_course.c_name,student_count FROM Course AS ali_course INNER JOIN (SELECT c_id,COUNT(*) AS student_count FROM Score GROUP BY c_id) AS temp ON ali_course.c_id = temp.c_id;
```

##40、查询选修"张三"老师所授课程的学生中，成绩最高的学生信息及其成绩

```mysql
SELECT ali_student.*,ali_score.s_score FROM 
Student AS ali_student INNER JOIN Score AS ali_score ON ali_student.s_id = ali_score.s_id
 INNER JOIN Course AS ali_course ON ali_score.c_id = ali_course.c_id
 INNER JOIN Teacher AS ali_teacher ON ali_course.t_id = ali_teacher.t_id AND ali_teacher.t_name = '张三'  
 ORDER BY ali_score.s_score DESC LIMIT 1;
```

##41、查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩 

```mysql
SELECT DISTINCT ali_score1.s_id,ali_score1.c_id,ali_score1.s_score FROM Score AS ali_score1 INNER JOIN Score AS ali_score2 
ON ali_score1.c_id != ali_score2.c_id AND ali_score1.s_id = ali_score2.s_id AND ali_score1.s_score = ali_score2.s_score;
```

##42、查询每门功课成绩最好的前两名 

```mysql
SELECT ali_score1.s_id,ali_score1.c_id,ali_score1.s_score FROM Score AS ali_score1 WHERE (SELECT COUNT(1) FROM Score AS ali_score2 WHERE ali_score1.c_id = ali_score2.c_id AND ali_score2.s_score >= ali_score1.s_score) <=2 ORDER BY ali_score1.c_id;
```

## 43、统计每门课程的学生选修人数（超过5人的课程才统计）。要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列  

````mysql
SELECT c_id,COUNT(1) AS student_count FROM Score GROUP BY c_id HAVING COUNT(1) >= 5 ORDER BY student_count DESC,c_id ASC;
````

##44、检索至少选修两门课程的学生学号 

```mysql
SELECT s_id FROM Score GROUP BY s_id HAVING COUNT(1) >= 2;
```

##45、查询选修了全部课程的学生信息 

```mysql
SELECT * FROM Student WHERE s_id IN (SELECT s_id FROM Score GROUP BY s_id HAVING COUNT(1) = (SELECT COUNT(1) FROM Course));
```

##46、查询各学生的年龄

##-- 按照出生日期来算，当前月日 < 出生年月的月日则，年龄减一

```mysql
select s_birth, DATE_FORMAT(NOW(),'%Y') - DATE_FORMAT(s_birth,'%Y') - (CASE WHEN DATE_FORMAT(NOW(),'%m%d') < DATE_FORMAT(s_birth,'%m%d') THEN 1 ELSE 0 END) AS '年龄'
from student;
```

##47、查询本周过生日的学生

```
SELECT * FROM Student WHERE WEEK(DATE_FORMAT(NOW(),'%Y%m%d')) = WEEK(s_birth);
```

##48、查询下周过生日的学生

```
SELECT * FROM Student WHERE WEEK(DATE_FORMAT(NOW(),'%Y%m%d')) + 1 = WEEK(s_birth);
```



## 49、查询本月过生日的学生

```mysql
SELECT * FROM Student WHERE MONTH(DATE_FORMAT(NOW(),'%Y%m%d')) = MONTH(s_birth);
```

##50、查询下月过生日的学生

```mysql
SELECT * FROM Student WHERE MONTH(DATE_FORMAT(NOW(),'%Y%m%d'))+1 = MONTH(s_birth);
```