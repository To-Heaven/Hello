# MySQL综合练习
http://www.cnblogs.com/piperck/

- 导入数据
	- `source /path/init.sql`
	- 路径不需要引号，最后不要加分号


```
# init.sql文件
/*
 数据导入：
 Navicat Premium Data Transfer

 Source Server         : localhost
 Source Server Type    : MySQL
 Source Server Version : 50624
 Source Host           : localhost
 Source Database       : sqlexam

 Target Server Type    : MySQL
 Target Server Version : 50624
 File Encoding         : utf-8

 Date: 10/21/2016 06:46:46 AM
*/

SET NAMES utf8;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
--  Table structure for `class`
-- ----------------------------
DROP TABLE IF EXISTS `class`;
CREATE TABLE `class` (
  `cid` int(11) NOT NULL AUTO_INCREMENT,
  `caption` varchar(32) NOT NULL,
  PRIMARY KEY (`cid`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8;

-- ----------------------------
--  Records of `class`
-- ----------------------------
BEGIN;
INSERT INTO `class` VALUES ('1', '三年二班'), ('2', '三年三班'), ('3', '一年二班'), ('4', '二年九班');
COMMIT;

-- ----------------------------
--  Table structure for `course`
-- ----------------------------
DROP TABLE IF EXISTS `course`;
CREATE TABLE `course` (
  `cid` int(11) NOT NULL AUTO_INCREMENT,
  `cname` varchar(32) NOT NULL,
  `teacher_id` int(11) NOT NULL,
  PRIMARY KEY (`cid`),
  KEY `fk_course_teacher` (`teacher_id`),
  CONSTRAINT `fk_course_teacher` FOREIGN KEY (`teacher_id`) REFERENCES `teacher` (`tid`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8;

-- ----------------------------
--  Records of `course`
-- ----------------------------
BEGIN;
INSERT INTO `course` VALUES ('1', '生物', '1'), ('2', '物理', '2'), ('3', '体育', '3'), ('4', '美术', '2');
COMMIT;

-- ----------------------------
--  Table structure for `score`
-- ----------------------------
DROP TABLE IF EXISTS `score`;
CREATE TABLE `score` (
  `sid` int(11) NOT NULL AUTO_INCREMENT,
  `student_id` int(11) NOT NULL,
  `course_id` int(11) NOT NULL,
  `num` int(11) NOT NULL,
  PRIMARY KEY (`sid`),
  KEY `fk_score_student` (`student_id`),
  KEY `fk_score_course` (`course_id`),
  CONSTRAINT `fk_score_course` FOREIGN KEY (`course_id`) REFERENCES `course` (`cid`),
  CONSTRAINT `fk_score_student` FOREIGN KEY (`student_id`) REFERENCES `student` (`sid`)
) ENGINE=InnoDB AUTO_INCREMENT=53 DEFAULT CHARSET=utf8;

-- ----------------------------
--  Records of `score`
-- ----------------------------
BEGIN;
INSERT INTO `score` VALUES ('1', '1', '1', '10'), ('2', '1', '2', '9'), ('5', '1', '4', '66'), ('6', '2', '1', '8'), ('8', '2', '3', '68'), ('9', '2', '4', '99'), ('10', '3', '1', '77'), ('11', '3', '2', '66'), ('12', '3', '3', '87'), ('13', '3', '4', '99'), ('14', '4', '1', '79'), ('15', '4', '2', '11'), ('16', '4', '3', '67'), ('17', '4', '4', '100'), ('18', '5', '1', '79'), ('19', '5', '2', '11'), ('20', '5', '3', '67'), ('21', '5', '4', '100'), ('22', '6', '1', '9'), ('23', '6', '2', '100'), ('24', '6', '3', '67'), ('25', '6', '4', '100'), ('26', '7', '1', '9'), ('27', '7', '2', '100'), ('28', '7', '3', '67'), ('29', '7', '4', '88'), ('30', '8', '1', '9'), ('31', '8', '2', '100'), ('32', '8', '3', '67'), ('33', '8', '4', '88'), ('34', '9', '1', '91'), ('35', '9', '2', '88'), ('36', '9', '3', '67'), ('37', '9', '4', '22'), ('38', '10', '1', '90'), ('39', '10', '2', '77'), ('40', '10', '3', '43'), ('41', '10', '4', '87'), ('42', '11', '1', '90'), ('43', '11', '2', '77'), ('44', '11', '3', '43'), ('45', '11', '4', '87'), ('46', '12', '1', '90'), ('47', '12', '2', '77'), ('48', '12', '3', '43'), ('49', '12', '4', '87'), ('52', '13', '3', '87');
COMMIT;

-- ----------------------------
--  Table structure for `student`
-- ----------------------------
DROP TABLE IF EXISTS `student`;
CREATE TABLE `student` (
  `sid` int(11) NOT NULL AUTO_INCREMENT,
  `gender` char(1) NOT NULL,
  `class_id` int(11) NOT NULL,
  `sname` varchar(32) NOT NULL,
  PRIMARY KEY (`sid`),
  KEY `fk_class` (`class_id`),
  CONSTRAINT `fk_class` FOREIGN KEY (`class_id`) REFERENCES `class` (`cid`)
) ENGINE=InnoDB AUTO_INCREMENT=17 DEFAULT CHARSET=utf8;

-- ----------------------------
--  Records of `student`
-- ----------------------------
BEGIN;
INSERT INTO `student` VALUES ('1', '男', '1', '理解'), ('2', '女', '1', '钢蛋'), ('3', '男', '1', '张三'), ('4', '男', '1', '张一'), ('5', '女', '1', '张二'), ('6', '男', '1', '张四'), ('7', '女', '2', '铁锤'), ('8', '男', '2', '李三'), ('9', '男', '2', '李一'), ('10', '女', '2', '李二'), ('11', '男', '2', '李四'), ('12', '女', '3', '如花'), ('13', '男', '3', '刘三'), ('14', '男', '3', '刘一'), ('15', '女', '3', '刘二'), ('16', '男', '3', '刘四');
COMMIT;

-- ----------------------------
--  Table structure for `teacher`
-- ----------------------------
DROP TABLE IF EXISTS `teacher`;
CREATE TABLE `teacher` (
  `tid` int(11) NOT NULL AUTO_INCREMENT,
  `tname` varchar(32) NOT NULL,
  PRIMARY KEY (`tid`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;

-- ----------------------------
--  Records of `teacher`
-- ----------------------------
BEGIN;
INSERT INTO `teacher` VALUES ('1', '张磊老师'), ('2', '李平老师'), ('3', '刘海燕老师'), ('4', '朱云海老师'), ('5', '李杰老师');
COMMIT;

SET FOREIGN_KEY_CHECKS = 1;

```

- 练习题

```
1、查询所有的课程的名称以及对应的任课老师姓名

2、查询学生表中男女生各有多少人

3、查询物理成绩等于100的学生的姓名

4、查询平均成绩大于八十分的同学的姓名和平均成绩

5、查询所有学生的学号，姓名，选课数，总成绩

6、 查询姓李老师的个数

7、 查询没有报李平老师课的学生姓名

8、 查询物理课程比生物课程高的学生的学号

9、 查询没有同时选修物理课程和体育课程的学生姓名

10、查询挂科超过两门(包括两门)的学生姓名和班级

11 、查询选修了所有课程的学生姓名

12、查询李平老师教的课程的所有成绩记录
 
13、查询全部学生都选修了的课程号和课程名

14、查询每门课程被选修的次数

15、查询之选修了一门课程的学生姓名和学号

16、查询所有学生考出的成绩并按从高到低排序（成绩去重）

17、查询平均成绩大于85的学生姓名和平均成绩

18、查询生物成绩不及格的学生姓名和对应生物分数

19、查询在所有选修了李平老师课程的学生中，这些课程(李平老师的课程，不是所有课程)平均成绩最高的学生姓名

20、查询每门课程成绩最好的前两名学生姓名

21、查询不同课程但成绩相同的学号，课程号，成绩

22、查询没学过“叶平”老师课程的学生姓名以及选修的课程名称；

23、查询所有选修了学号为1的同学选修过的一门或者多门课程的同学学号和姓名；

24、任课最多的老师中学生单科成绩最高的学生姓名
```

- 参考答案

```1、查询所有的课程的名称以及对应的任课老师姓名
方法一：联表查询
SELECT a.cname, b.tname FROM 
(SELECT cname, teacher_id FROM course ) AS a
INNER JOIN 
(SELECT tid, tname FROM teacher) AS b
ON a.teacher_id=b.tid;

2、查询学生表中男女生各有多少人

SELECT gender, COUNT(sid) FROM  student GROUP BY gender;


3、查询物理成绩等于100的学生的姓名
'''
物理课id  select cid from course where cname='物理'
'''
方法一：联表+子查询
SELECT b.sname FROM
(SELECT student_id FROM score WHERE course_id=(SELECT cid FROM course WHERE cname='物理') AND num=100) AS a
INNER JOIN 
(SELECT sname, sid FROM student) AS b
ON 
a.student_id = b.sid;



方法二：纯联表查询
SELECT student.sname FROM score 
INNER JOIN course ON score.course_id=course.cid
INNER JOIN student ON score.student_id=student.sid
WHERE course.cname='物理' AND score.num=100;


方法三：子查询

SELECT  DISTINCT sname FROM student WHERE sid  IN (
	SELECT student_id FROM score WHERE course_id=(
		SELECT cid FROM course WHERE cname='物理'
			) AND num=100
);



4、查询平均成绩大于八十分的同学的姓名和平均成绩
'''
平均成绩大于80分： select student_id from score group by student_id having avg(num)>80
'''
方法一：联表查询
SELECT b.sname 姓名, a.平均成绩 FROM
(SELECT student_id,AVG(num) AS 平均成绩 FROM score GROUP BY student_id HAVING AVG(num)>80) AS a
INNER JOIN
(SELECT sid, sname FROM student) AS b
ON a.student_id=b.sid;


5、查询所有学生的学号，姓名，选课数，总成绩
'''
选课数： select count(course_id) from score group by student_id 

'''
方法一：联表查询
SELECT b.sid 学号 , b.sname 姓名, a.选课数, a.总成绩 FROM
(SELECT student_id,COUNT(course_id) 选课数,SUM(num) AS 总成绩 FROM score GROUP BY student_id) AS a
RIGHT JOIN
(SELECT sid,sname FROM student ) AS b
ON a.student_id=b.sid;


6、 查询姓李老师的个数

SELECT COUNT(tid) FROM teacher WHERE tname LIKE '李%';


7、 查询没有报李平老师课的学生姓名
'''
李老师的tid： select tid from teacher where tname='李平老师'
李老师课id： select cid from course where teacher_id=李老师的tid
'''

方法一：子查询
SELECT DISTINCT sname FROM student WHERE sid NOT IN (
	SELECT student_id FROM score WHERE course_id IN (
		SELECT cid FROM course WHERE teacher_id=(
			SELECT tid FROM teacher WHERE tname='李平老师'))
);

方法二：联表查询：
待补充
-- select * from 
-- (select student_id,course_id from score ) as sc
-- inner join
-- (select tid from teacher where tname='李平老师') as t
-- inner join
-- (select sid,sname from student) as st
-- inner join 
-- (select cid,teacher_id from course ) as c
-- on 
-- st.sid = sc.student_id and c.cid=sc.course_id and t.tid=c.teacher_id



 


8、 查询物理课程比生物课程成绩高的学生的学号
'''
物理课程id：select cid from course where cname='物理'
生物课程id：select cid from course where cname='生物'
'''
方法一：联表+子查询
SELECT a.student_id FROM
(SELECT student_id, course_id, num FROM score WHERE course_id = (SELECT cid FROM course WHERE cname='物理')) AS a 
INNER JOIN 
(SELECT student_id, course_id,num FROM score WHERE course_id = (SELECT cid FROM course WHERE cname='生物')) AS b
ON a.student_id=b.student_id
WHERE a.num > b.num

方法二：联表查询
SELECT a.sid FROM 
(SELECT score.student_id AS sid, score.course_id,score.num AS num FROM score INNER JOIN course ON course.cid=score.course_id WHERE course.cname='物理') AS a 
INNER JOIN 
(SELECT score.student_id AS sid, score.course_id,score.num  AS num FROM score INNER JOIN course ON course.cid=score.course_id WHERE course.cname='生物') AS b
ON a.sid=b.sid
WHERE a.num > b.num


9、 查询没有同时选修物理课程和体育课程的学生姓名
'''
物理课程id：select cid from course where cname='物理'
体育课程id：select cid from course where cname='体育'
选修物理的学生： select student_id from score inner join course on score.course_id=course.cid where course.cname='物理'
选修体育的学生： select student_id from score inner join course on score.course_id=course.cid where course.cname='体育'
'''

SELECT sname FROM student WHERE sid NOT IN 
(
SELECT w.student_id FROM 
(SELECT student_id FROM score INNER JOIN course ON score.course_id=course.cid WHERE course.cname='物理') AS w
INNER JOIN
(SELECT student_id FROM score INNER JOIN course ON score.course_id=course.cid WHERE course.cname='体育') AS t
ON w.student_id=t.student_id
);


10、查询挂科超过两门(包括两门)的学生姓名和班级名
'''
学生姓名：stduent.sname
班级名：class.cname
'''
方法一：联表法

SELECT st.sname,c.caption FROM
(SELECT student_id FROM score WHERE num<60 GROUP BY student_id  HAVING  COUNT(course_id)>=2) AS sc 
INNER JOIN
(SELECT sid,sname,class_id FROM student) AS st
INNER JOIN
(SELECT cid,caption FROM class) AS c
ON sc.student_id=st.sid AND st.class_id=c.cid

方法二：联表+子查询

SELECT sname,class.caption 
FROM student 
INNER JOIN class
ON student.class_id=class.cid
WHERE student.sid=(SELECT student_id FROM score WHERE num<60 GROUP BY student_id HAVING COUNT(student_id) >=2)



11 、查询选修了所有课程的学生姓名
'''
所有课程数： select count(cid) from course
'''
方法一： 联表法

SELECT c.sname FROM
(SELECT student_id,COUNT(course_id) AS count_course  FROM score GROUP BY student_id ) AS a
INNER JOIN
(SELECT COUNT(cid) AS count_course FROM course ) AS b
INNER JOIN
(SELECT sname,sid FROM student ) AS c
ON a.student_id=c.sid
WHERE a.count_course = b.count_course

方法二： 子查询(从要查出的字段所在的表开始，层层剥开，遇到什么就查询什么)

SELECT sname FROM student WHERE sid IN (
SELECT student_id FROM score GROUP BY student_id HAVING COUNT(course_id)=(
SELECT COUNT(cid) FROM course));


方法三： 联表+子查询
SELECT sname FROM student INNER JOIN(
SELECT student_id FROM score GROUP BY student_id HAVING COUNT(course_id) = (
SELECT COUNT(cid) FROM course)) AS  a
ON a.student_id = student.sid;


12、查询李平老师教的课程的所有成绩记录

方法一：子查询
SELECT * FROM score WHERE course_id IN (
SELECT cid FROM course WHERE teacher_id=(
SELECT tid FROM teacher WHERE tname='李平老师'
));

方法二： 联表查询
SELECT a.student_id, a.course_id, a.num FROM 
(SELECT sid,student_id,course_id,num FROM score ) AS a
INNER JOIN
(SELECT cid, teacher_id FROM course ) AS b
INNER JOIN
(SELECT tid FROM teacher WHERE tname='李平老师') AS c
ON c.tid=b.teacher_id 
WHERE a.course_id = b.cid




13、查询全部学生都选修了的课程号和课程名
方法一： 子查询
'''
course: 课程号，课程名
'''

SELECT cid, cname FROM course WHERE cid IN (
SELECT course_id FROM score GROUP BY course_id HAVING COUNT(student_id)=(
SELECT COUNT(sid) FROM student
));

'''
步骤：
1. select cid, cname from course where cid in (???);
2. select cid,cname from course where cid in (
SELECT course_id FROM score GROUP BY course_id HAVING COUNT(student_id)=(???)
);
3. SELECT cid, cname FROM course WHERE cid IN (
SELECT course_id FROM score GROUP BY course_id HAVING COUNT(student_id)=(
SELECT COUNT(sid) FROM student
));
'''

方法二： 联表查询





14、查询每门课程被选修的次数
'''
目标字段： score --> count(student_id)
'''

方法一： 子查询 + 联表
SELECT b.cname AS 课程名, 次数 FROM
(SELECT  course_id, COUNT(student_id) 次数 FROM score GROUP BY course_id ) AS a 
INNER JOIN
(SELECT cid, cname FROM course ) AS b
ON a.course_id=b.cid




15、查询之选修了一门课程的学生姓名和学号
方法一：子查询
'''
目标字段： student  --->  sname,sid
'''

SELECT sid 学号, sname FROM student WHERE sid IN (
SELECT student_id FROM score GROUP BY student_id HAVING COUNT(course_id)=1);

方法二： 联表查询
SELECT a.student_id 学号,b.sname 姓名 FROM 
(SELECT student_id FROM score GROUP BY student_id HAVING COUNT(course_id)=1) AS a
INNER JOIN
(SELECT sid, sname FROM student) AS b
ON a.student_id = b.sid

16、查询所有学生考出的（总成绩？）成绩并按从高到低排序（成绩去重）
方法一： 子查询
'''
目标字段： score --- > num
'''
SELECT student_id 学号, SUM(num) 总成绩 FROM score GROUP BY student_id ORDER BY 总成绩 DESC


17、查询平均成绩大于85的学生姓名和平均成绩
方法一： 子查询 + 联表查询
'''
目标字段： student ---> sname  ;  score ---> avg(num)   group by 
'''
 错误思路：     SELECT sname 姓名, AVG(num)  FROM (score INNER JOIN student ON score.student_id=student.sid ) AS a GROUP BY a.student_id
 
SELECT sname 姓名, 平均成绩 FROM student INNER JOIN 
(SELECT student_id, AVG(num) AS 平均成绩 FROM score GROUP BY student_id HAVING AVG(num)>85 ) AS a 
ON  student.sid=a.student_id




方法二： 联表查询
SELECT b.sname 姓名, 平均成绩 FROM 
(SELECT student_id, AVG(num) 平均成绩 FROM score GROUP BY student_id) AS a
INNER JOIN
(SELECT sid, sname FROM student) AS b
ON a.student_id =b.sid
WHERE 平均成绩>85


18、查询生物成绩不及格的学生姓名和对应生物分数
方法一： 子查询+联表查询
'''
目标字段： student ---> sname     score  ---> num
生
'''
SELECT b.sname 姓名, a.num 生物分数 FROM 
(SELECT student_id, num FROM score WHERE course_id=(
SELECT cid FROM course WHERE cname='生物'
) AND num<60) AS a
INNER JOIN
(SELECT sid, sname FROM student) AS b
ON a.student_id = b.sid

方法二： 联表查询
'''
需要的表： stduent, score（num<60） , course ,
组合条件： 学号，课程id
'''

SELECT sname 姓名, a.num 生物分数 FROM 
(SELECT student_id,course_id, num FROM score WHERE num < 60 ) AS a
INNER JOIN
(SELECT cid FROM course WHERE cname='生物')  AS b
INNER JOIN
(SELECT sid,sname FROM student ) AS c
ON a.student_id=c.sid AND a.course_id=b.cid


19、查询在所有选修了李平老师课程的学生（选了两门课的？）中，这些课程(李平老师的课程，不是所有课程)平均成绩最高的学生姓名
方法一： 子查询 + 联表查询
'''
目标字段： stduent ---> sname 
order by desc + limit 1
'''
SELECT sname 姓名 FROM student  WHERE sid =(
SELECT student_id FROM score WHERE course_id IN (
SELECT cid FROM course WHERE teacher_id =(
SELECT tid FROM teacher WHERE tname='李平老师')) GROUP BY student_id
ORDER BY AVG(num) DESC
LIMIT 1
);

方法二：
  SELECT student_id,avg(num),sname FROM score 
 LEFT JOIN student ON score.student_id=student.sid
         WHERE course_id in (SELECT cid FROM course LEFT JOIN teacher ON teacher.tid=course.teacher_id WHERE tname LIKE "李平%")
         GROUP BY student_id
 ORDER BY AVG(num) DESC
 LIMIT 1

方法三:
 SELECT sname,MAX(B) FROM 
(SELECT student_id,sname,AVG(num) as B FROM score 
                         LEFT JOIN student ON score.student_id=student.sid
             WHERE course_id in (SELECT cid FROM course LEFT JOIN teacher ON teacher.tid=course.teacher_id WHERE tname LIKE "李平%")                        
                         GROUP BY student_id
) as A
WHERE B=(
SELECT MAX(B) FROM
 (SELECT student_id,sname,AVG(num) as B FROM score 
                         LEFT JOIN student ON score.student_id=student.sid
             WHERE course_id in (SELECT cid FROM course LEFT JOIN teacher ON teacher.tid=course.teacher_id WHERE tname LIKE "李平%")
                         GROUP BY student_id
) as A)





20、查询每门课程成绩最好的前两名学生姓名

21、查询不同课程但成绩相同的学号，课程号，成绩

22、查询没学过“叶平”老师课程的学生姓名以及选修的课程名称；

23、查询所有选修了学号为1的同学选修过的一门或者多门课程的同学学号和姓名；

24、任课最多的老师中学生单科成绩最高的学生姓名


```


```
2、查询“生物”课程比“物理”课程成绩高的所有学生的学号；
思路：
    获取所有有生物课程的人（学号，成绩） - 临时表
    获取所有有物理课程的人（学号，成绩） - 临时表
    根据【学号】连接两个临时表：
        学号  物理成绩   生物成绩
 
    然后再进行筛选
 
        select A.student_id,sw,ty from
 
        (select student_id,num as sw from score left join course on score.course_id = course.cid where course.cname = '生物') as A
 
        left join
 
        (select student_id,num  as ty from score left join course on score.course_id = course.cid where course.cname = '体育') as B
 
        on A.student_id = B.student_id where sw > if(isnull(ty),0,ty);
 
3、查询平均成绩大于60分的同学的学号和平均成绩；
    思路：
        根据学生分组，使用avg获取平均值，通过having对avg进行筛选
 
        select student_id,avg(num) from score group by student_id having avg(num) > 60
 
4、查询所有同学的学号、姓名、选课数、总成绩；
 
    select score.student_id,sum(score.num),count(score.student_id),student.sname
    from
        score left join student on score.student_id = student.sid  
    group by score.student_id
 
5、查询姓“李”的老师的个数；
    select count(tid) from teacher where tname like '李%'
 
    select count(1) from (select tid from teacher where tname like '李%') as B
 
6、查询没学过“叶平”老师课的同学的学号、姓名；
    思路：
        先查到“李平老师”老师教的所有课ID
        获取选过课的所有学生ID
        学生表中筛选
    select * from student where sid not in (
        select DISTINCT student_id from score where score.course_id in (
            select cid from course left join teacher on course.teacher_id = teacher.tid where tname = '李平老师'
        )
    )
 
7、查询学过“001”并且也学过编号“002”课程的同学的学号、姓名；
    思路：
        先查到既选择001又选择002课程的所有同学
        根据学生进行分组，如果学生数量等于2表示，两门均已选择
 
    select student_id,sname from
 
    (select student_id,course_id from score where course_id = 1 or course_id = 2) as B
      
    left join student on B.student_id = student.sid group by student_id HAVING count(student_id) > 1
 
 
8、查询学过“叶平”老师所教的所有课的同学的学号、姓名；
 
    同上，只不过将001和002变成 in (叶平老师的所有课)
 
9、查询课程编号“002”的成绩比课程编号“001”课程低的所有同学的学号、姓名；
    同第1题
 
 
10、查询有课程成绩小于60分的同学的学号、姓名；
         
    select sid,sname from student where sid in (
        select distinct student_id from score where num < 60
    )
 
11、查询没有学全所有课的同学的学号、姓名；
    思路：
        在分数表中根据学生进行分组，获取每一个学生选课数量
        如果数量 == 总课程数量，表示已经选择了所有课程
 
        select student_id,sname
        from score left join student on score.student_id = student.sid
        group by student_id HAVING count(course_id) = (select count(1) from course)
 
 
12、查询至少有一门课与学号为“001”的同学所学相同的同学的学号和姓名；
    思路：
        获取 001 同学选择的所有课程
        获取课程在其中的所有人以及所有课程
        根据学生筛选，获取所有学生信息
        再与学生表连接，获取姓名
 
        select student_id,sname, count(course_id)
        from score left join student on score.student_id = student.sid
        where student_id != 1 and course_id in (select course_id from score where student_id = 1) group by student_id
 
13、查询至少学过学号为“001”同学所有课的其他同学学号和姓名；
        先找到和001的学过的所有人
        然后个数 ＝ 001所有学科     ＝＝》 其他人可能选择的更多
 
        select student_id,sname, count(course_id)
        from score left join student on score.student_id = student.sid
        where student_id != 1 and course_id in (select course_id from score where student_id = 1) group by student_id having count(course_id) ＝ (select count(course_id) from score where student_id = 1)
 
14、查询和“002”号的同学学习的课程完全相同的其他同学学号和姓名；
         
        个数相同
        002学过的也学过
 
        select student_id,sname from score left join student on score.student_id = student.sid where student_id in (
            select student_id from score  where student_id != 1 group by student_id HAVING count(course_id) = (select count(1) from score where student_id = 1)
        ) and course_id in (select course_id from score where student_id = 1) group by student_id HAVING count(course_id) = (select count(1) from score where student_id = 1)
 
 
15、删除学习“叶平”老师课的score表记录；
 
    delete from score where course_id in (
        select cid from course left join teacher on course.teacher_id = teacher.tid where teacher.name = '叶平'
    )
 
16、向SC表中插入一些记录，这些记录要求符合以下条件：①没有上过编号“002”课程的同学学号；②插入“002”号课程的平均成绩；
    思路：
        由于insert 支持 
                inset into tb1(xx,xx) select x1,x2 from tb2;
        所有，获取所有没上过002课的所有人，获取002的平均成绩
 
    insert into score(student_id, course_id, num) select sid,2,(select avg(num) from score where course_id = 2)
    from student where sid not in (
        select student_id from score where course_id = 2
    )
     
17、按平均成绩从低到高 显示所有学生的“语文”、“数学”、“英语”三门的课程成绩，按如下形式显示： 学生ID,语文,数学,英语,有效课程数,有效平均分；
    select sc.student_id,
        (select num from score left join course on score.course_id = course.cid where course.cname = "生物" and score.student_id=sc.student_id) as sy,
        (select num from score left join course on score.course_id = course.cid where course.cname = "物理" and score.student_id=sc.student_id) as wl,
        (select num from score left join course on score.course_id = course.cid where course.cname = "体育" and score.student_id=sc.student_id) as ty,
        count(sc.course_id),
        avg(sc.num)
    from score as sc
    group by student_id desc        
 
18、查询各科成绩最高和最低的分：以如下形式显示：课程ID，最高分，最低分；
     
    select course_id, max(num) as max_num, min(num) as min_num from score group by course_id;
 
19、按各科平均成绩从低到高和及格率的百分数从高到低顺序；
    思路：case when .. then
    select course_id, avg(num) as avgnum,sum(case when score.num > 60 then 1 else 0 END)/count(1)*100 as percent from score group by course_id order by avgnum asc,percent desc;
 
20、课程平均分从高到低显示（现实任课老师）；
 
    select avg(if(isnull(score.num),0,score.num)),teacher.tname from course
    left join score on course.cid = score.course_id
    left join teacher on course.teacher_id = teacher.tid
 
    group by score.course_id
 
 
21、查询各科成绩前三名的记录:(不考虑成绩并列情况)
    select score.sid,score.course_id,score.num,T.first_num,T.second_num from score left join
    (
    select
        sid,
        (select num from score as s2 where s2.course_id = s1.course_id order by num desc limit 0,1) as first_num,
        (select num from score as s2 where s2.course_id = s1.course_id order by num desc limit 3,1) as second_num
    from
        score as s1
    ) as T
    on score.sid =T.sid
    where score.num <= T.first_num and score.num >= T.second_num
 
22、查询每门课程被选修的学生数；
     
    select course_id, count(1) from score group by course_id;
 
23、查询出只选修了一门课程的全部学生的学号和姓名；
    select student.sid, student.sname, count(1) from score
 
    left join student on score.student_id  = student.sid
 
     group by course_id having count(1) = 1
 
 
24、查询男生、女生的人数；
    select * from
    (select count(1) as man from student where gender='男') as A ,
    (select count(1) as feman from student where gender='女') as B
 
25、查询姓“张”的学生名单；
    select sname from student where sname like '张%';
 
26、查询同名同姓学生名单，并统计同名人数；
 
    select sname,count(1) as count from student group by sname;
 
27、查询每门课程的平均成绩，结果按平均成绩升序排列，平均成绩相同时，按课程号降序排列；
    select course_id,avg(if(isnull(num), 0 ,num)) as avg from score group by course_id order by avg     asc,course_id desc;
 
28、查询平均成绩大于85的所有学生的学号、姓名和平均成绩；
 
    select student_id,sname, avg(if(isnull(num), 0 ,num)) from score left join student on score.student_id = student.sid group by student_id;
 
29、查询课程名称为“数学”，且分数低于60的学生姓名和分数；
 
    select student.sname,score.num from score
    left join course on score.course_id = course.cid
    left join student on score.student_id = student.sid
    where score.num < 60 and course.cname = '生物'
 
30、查询课程编号为003且课程成绩在80分以上的学生的学号和姓名；
    select * from score where score.student_id = 3 and score.num > 80
 
31、求选了课程的学生人数
 
    select count(distinct student_id) from score
 
    select count(c) from (
        select count(student_id) as c from score group by student_id) as A
 
32、查询选修“杨艳”老师所授课程的学生中，成绩最高的学生姓名及其成绩；
     
    select sname,num from score
    left join student on score.student_id = student.sid
    where score.course_id in (select course.cid from course left join teacher on course.teacher_id = teacher.tid where tname='张磊老师') order by num desc limit 1;
 
33、查询各个课程及相应的选修人数；
    select course.cname,count(1) from score
    left join course on score.course_id = course.cid
    group by course_id;
 
 
34、查询不同课程但成绩相同的学生的学号、课程号、学生成绩；
    select DISTINCT s1.course_id,s2.course_id,s1.num,s2.num from score as s1, score as s2 where s1.num = s2.num and s1.course_id != s2.course_id;
 
35、查询每门课程成绩最好的前两名；
 
    select score.sid,score.course_id,score.num,T.first_num,T.second_num from score left join
    (
    select
        sid,
        (select num from score as s2 where s2.course_id = s1.course_id order by num desc limit 0,1) as first_num,
        (select num from score as s2 where s2.course_id = s1.course_id order by num desc limit 1,1) as second_num
    from
        score as s1
    ) as T
    on score.sid =T.sid
    where score.num <= T.first_num and score.num >= T.second_num
 
36、检索至少选修两门课程的学生学号；
    select student_id from score group by student_id having count(student_id) > 1
 
37、查询全部学生都选修的课程的课程号和课程名；
    select course_id,count(1) from score group by course_id having count(1) = (select count(1) from student);
 
38、查询没学过“叶平”老师讲授的任一门课程的学生姓名；
    select student_id,student.sname from score
    left join student on score.student_id = student.sid
    where score.course_id not in (
        select cid from course left join teacher on course.teacher_id = teacher.tid where tname = '张磊老师'
    )
    group by student_id
 
39、查询两门以上不及格课程的同学的学号及其平均成绩；
 
    select student_id,count(1) from score where num < 60 group by student_id having count(1) > 2
 
40、检索“004”课程分数小于60，按分数降序排列的同学学号；
    select student_id from score where num< 60 and course_id = 4 order by num desc;
 
41、删除“002”同学的“001”课程的成绩；
    delete from score where course_id = 1 and student_id = 2

```