# `mysql练习&总结`

## 聚合函数长度：length

```java
select tweet_id from Tweets where length(content) >15
```



## 内连接

```sql
select b.id as Id from Weather a left join Weather b on datediff(b.recordDate, a.recordDate) = 1 where b.temperature  > a.temperature 
```

> datediff函数来判断b.recordDate - a.recordDate的时间间隔。

举例：表aaa自连接

表aaa

![image-20231016183407673](C:\Users\86134\AppData\Roaming\Typora\typora-user-images\image-20231016183407673.png)

语句：SELECT * FROM aaa a left JOIN aaa b on a.uid = b.uid

> 需要给自连接的表起别名，这里是a和b。下图中列名是直接进行拼接。

结果：

![image-20231016183532880](C:\Users\86134\AppData\Roaming\Typora\typora-user-images\image-20231016183532880.png)

## AVG函数

> AVG()函数的语法如下：
>
> ```sql
> AVG(DISTINCT expression)
> ```
>
> 您可以使用AVG()函数中的[DISTINCT](http://www.yiibai.com/mysql/distinct.html)运算符来计算不同值的平均值。 例如，如果您有一组值`1`,`1`,`2`,`3`，具有`DISTINCT`操作的AVG()函数将返回不同值的和，即：`(1 + 2 + 3)/3 = 2.00` 。

```sql
select a1.machine_id, round(avg(a2.timestamp - a1.timestamp), 3) as processing_time from Activity a1 left join  Activity a2 on a1.machine_id = a2.machine_id and a1.process_id = a2.process_id where a1.activity_type = 'start' and a2.activity_type='end' group by a1.machine_id
```

其中使用`round(avg(a2.timestamp - a1.timestamp), 3) as processing_time`聚合函数起别名的方式来创建新的一列。

## 笛卡尔积（`cross join`）

> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20191031192720529.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9zbWlsZW5pY2t5LmJsb2cuY3Nkbi5uZXQ=,size_16,color_FFFFFF,t_70)

## 如何左连接多个表

> select * from A表 a left join B表 b on a.学号=b.学号 left join C表 c on a.班级编号=c.班级编号

## `left join` ... on  and与 left join ... on where

```sql
select Students.student_id, Students.student_name, Subjects.subject_name, count(Examinations.subject_name) attended_exams from Students
cross join Subjects 
-> left join  Examinations 
on Examinations.student_id = Students.student_id and Subjects.subject_name = Examinations.subject_name 
group by Students.student_id, Subjects.subject_name 
order by Students.student_id, Subjects.subject_name 
```

其中 

left join  Examinations 
on Examinations.student_id = Students.student_id `and` Subjects.subject_name = Examinations.subject_name 

与

left join  Examinations 
on Examinations.student_id = Students.student_id `where` Subjects.subject_name = Examinations.subject_name 

如果使用and （条件），则左连接中未匹配的值将会用null代替

如果使用where(过滤),则会将传递的表中的值进行比对，根据条件淘汰

## CASE [ expr ] WHEN [ val1 ] THEN [res1] ... ELSE [ default ] END

> CASE WHEN [ val1 ] THEN [res1] ... ELSE [ default ] END 如果val1为true，返回res1，... 否 则返回default默认值 
>
> CASE [ expr ] WHEN [ val1 ] THEN [res1] ... ELSE [ default ] END 如果expr的值等于val1，返回 res1，... 否则返回default 默认值

```sql
select s.user_id, round(avg(case c.action when 'confirmed ' then 1 else 0 end ),2) as confirmation_rate 
from Signups as s 
left join Confirmations as c 
on s.user_id = c.user_id
group by user_id
```

