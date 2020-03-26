### 一、操作数据库

#### 1.创建数据库

```mysql
create database if not exists westos character set utf8 collate utf8_general_ci
```

#### 2.删除数据库

```mysql
drop database if exists westos
```

#### 3.使用数据库

```mysql
如果你的表名或者字段名是一个特殊字符，就需要带 `` 符合
use westos
```

#### 4.查看数据库

```mysql
show databases --查看所有的数据库
```

### 二、数据库的列类型

> 数值

| 名称      | 描述               | 大小                     |
| --------- | ------------------ | ------------------------ |
| tinyint   | 十分小的数据       | 1个字节                  |
| smallint  | 较小的数据         | 2个字节                  |
| mediumint | 中等大小的数据     | 3个字节                  |
| **int**   | **标准的整数**     | **4个字节**              |
| bigint    | 较大的数据         | 8个字节                  |
| float     | 浮点数             | 4个字节                  |
| double    | 浮点数             | 8个字节                  |
| decimal   | 字符串形式的浮点数 | 金融计算的时候，一般使用 |

> 字符串

| 名称        | 描述           | 大小        |
| ----------- | -------------- | ----------- |
| char        | 字符串固定大小 | 0-255       |
| **varchar** | **可变字符串** | **0-65535** |
| tinytext    | 微型文本       | 2^8-1       |
| text        | 文本串         | 2^16-1      |

> 时间日期

| 名称          | 描述                               |
| ------------- | ---------------------------------- |
| date          | YYYY-MM-DD,日期格式                |
| time          | HH:mm:ss时间格式                   |
| **datetime**  | **YYYY-MM-DD HH:mm:ss**            |
| **timestamp** | **时间戳，1970.1.1到现在的毫秒数** |
| year          | 年份表示                           |

> null

* 没有值，未知
* 注意，不要使用null进行运算，结果为null

### 三、数据库的字段属性

> Unsigned：

* 无符号的整数
* 声明了该列不能为负数

> zerofill：

* 0填充
* 不足的位数，使用0来填充 int(3)  ,5 -- 005 

> 自增：auto_increment

* 通常理解为自增，自动在上一条记录的基础上+1
* 通常用来设计唯一的主键，必须是整数类型
* 可以自定义设计主键自增的起始值和步长

> 非空 （null）  （no null）:

* 假设设置为not null，如果不给它赋值，就会报错
* null，如果不填写值，默认就是null

> 默认：default

* 设置默认的值！
* sex，默认值为男，如果不指定该列的值，则会有默认的值！

**拓展**

```mysql
每一个表都必须存在以下五个字段，未来做项目用的，表示一个记录存在的意义！
id           主键
version      乐观锁
is_delelt    伪删除
gmt_create   创建时间
gmt_update   修改时间

```

### 四、操作数据库表

#### 1.创建数据库表

```mysql
-- 注意点，使用英文(),表的名称和字段尽量使用``括起来
-- 字符串使用单引号括起来
-- 所有的语句后面加  ,(英文的) 最后一个字段不要加
create table if not exists `studet`(
	`id` int(4) not null auto_increment comment '学号',
    `name` varchar(30) not null default '匿名' comment '姓名',
    `pwd` varchar(20) not null default '123456' comment '密码',
	`sex` varchar(2) not null default '女' comment '性别',
    `birthday` datetime default null comment '出生日期',
    `address` varchar(100) default null comment '家庭住址',
    `email` varchar(50) default null comment '邮箱',
    primary key (`id`)
)engine=innodb default charset=utf8
```

#### 2.修改数据库表

```mysql
-- 修改表名
alter table student rename as student1
-- 增加表字段
alter table student1 add age int(11) default null comment '年龄'
-- 修改表字段
alter table student1 modify age varchar(11) -- 修改约束
alter table student1 change age age1 int(3) -- 重命名
-- 删除表字段
alter table student1 drop age1
```

#### 3.删除数据库表

```mysql
drop table if exists student1
```



> 注意点：

* ``字段名，使用这个包裹
* 注释 --  /**/
* sql关键字大小写不敏感，建议写小写
* 所有的符号全部用英文

### 五、查看建库建表语句

```mysql
show create database school -- 查看创建数据库的定义语句
show create table student -- 查看创建数据表的定义语句
desc student -- 显示表的结构
```

### 六、数据表的类型

```mysql
-- 关于数据库引擎
innodb 默认使用
myisam 早些年使用
```

|              | myisam | innodb |
| ------------ | ------ | ------ |
| 事务支持     | 不支持 | 支持   |
| 数据行锁定   | 不支持 | 支持   |
| 外键约束     | 不支持 | 支持   |
| 全文索引     | 支持   | 不支持 |
| 表空间的大小 | 较小   | 较大   |

常规使用操作：

* innodb 节约空间，速度较快
* innodb 安全性高，事务的处理，多表多用户操作

### 七、MySQL数据管理

#### 1.外键

方式一

```mysql
-- 创建表年级表
create table `grade`(
	`gradeid` int(10) not null auto_increment comment '年级id',
    `gradename` varchar(50) not null comment '年纪名称',
    primary key (`gradeid`)
)engine=innodb default charset=utf8

-- 创建学生表并添加约束
-- 学生表的gradeid字段要去引用年纪表的gradeid
-- 定义外键key
-- 给这个外键添加约束（执行引用） references 引用
create table if not exists `studet`(
	`id` int(4) not null auto_increment comment '学号',
    `name` varchar(30) not null default '匿名' comment '姓名',
    `pwd` varchar(20) not null default '123456' comment '密码',
	`sex` varchar(2) not null default '女' comment '性别',
    `birthday` datetime default null comment '出生日期',
    `address` varchar(100) default null comment '家庭住址',
    `email` varchar(50) default null comment '邮箱',
    `gradeid` int(10) not null comment '学生的年纪',
    primary key (`id`),
 	constraint `FK_gradeid` foreign key (`gradeid`) references `grade` (`gradeid`)
)engine=innodb default charset=utf8
```

方式二

```mysql
-- 创建表年级表
create table `grade`(
	`gradeid` int(10) not null auto_increment comment '年级id',
    `gradename` varchar(50) not null comment '年纪名称',
    primary key (`gradeid`)
)engine=innodb default charset=utf8

-- 创建学生表
create table if not exists `studet`(
	`id` int(4) not null auto_increment comment '学号',
    `name` varchar(30) not null default '匿名' comment '姓名',
    `pwd` varchar(20) not null default '123456' comment '密码',
	`sex` varchar(2) not null default '女' comment '性别',
    `birthday` datetime default null comment '出生日期',
    `address` varchar(100) default null comment '家庭住址',
    `email` varchar(50) default null comment '邮箱',
    `gradeid` int(10) not null comment '学生的年纪',
    primary key (`id`)
)engine=innodb default charset=utf8

-- 添加约束
alter table `student`
add constraint `FK_gradeid` foreign key (`gradeid`) references `grade` (`gradeid`)
```

#### 2.数据表数据添加

```mysql
insert into `grade` (`gradename`) value ('大四')
```

#### 3.数据表数据修改

```mysql
update `student` set `name`='fanhuajin',`email`='78235129@qqcom' where id=1
```

#### 4.数据表数据删除

```mysql
delete from `student` where id=1;
```

#### 5.清空数据表数据

```mysql
truncate `student`
```

#### 6.数据表数据查询

##### 6.0 select查询规则

```mysql
SELECT [ALL | DISTINCT]
{* | table.* 1 [table. field1[as alias1][,table. field2[as alias2]][,...]]}
FROM table_ name [as table_ _alias]
[left | right | inner join table_ name2] --联合查询
[WHERE .. .] -- 指定结果需满足的条件
[GROUP BY ...] -- 指定结果按照哪几个字段来分组
[HAVING]-- 过滤分组的记录必须满足 的次要条件
[ORDER BY ...] -- 指定查询记录按一个或多 个条件排序
[LIMIT {[offset,]row_ countI row_ countOFFSET offset}];-- 指定查询的记录从哪条至哪条
```



##### 6.1 指定查询字段

  ```mysql
-- 查询全部学生
select * from student

-- 查询指定字段
select `studentNo`,`studentName` from student

-- 别名，给结果起一个名字 as 可以给字段起别名，也可以给表起别名
select `studentNo` as 学号,`studentName` as 学生姓名 from student as s

-- 函数 concat(a,b)
select concat('姓名:',studentName) as 新名字 from student

-- 去重 distinct 重复的数据只显示一条
select distinct `studentNo` from result


-- 数据库表达式
-- 查询系统版本(函数)
select version()

-- 用来计算(表达式)
select 100*3-1 as 计算结果

-- 查询自增的步长(变量)
select @@auto_increment_increment

-- 学员考试成绩+1分 查看
select `studnetNo`,`studentResult`+1 as '提分后' from result
  ```

##### 6.2 where 条件子句

> 逻辑运算符

| 运算符       | 语法                | 描述   |
| ------------ | ------------------- | ------ |
| and   &&     | a and b    a&&b     | 逻辑与 |
| or      \|\| | a or b       a\|\|b | 逻辑或 |
| not     !    | not a         !a    | 逻辑非 |

```mysql
-- 查询考试成绩在95~100分之间
select student,studentresult from result
where studentresult>=95 and studentresult<=100

-- 除了1000号学生之外的同学的成绩
select student,studentresult from result
where studentNo!=1000
```

##### 6.3 模糊查询：比较运算符

| 运算符      | 语法               | 描述                                          |
| ----------- | ------------------ | --------------------------------------------- |
| is null     | a is null          | 如果操作符为null,结果为真                     |
| is not null | a is not null      | 如果操作符不为null,结果为真                   |
| between     | a between b        | 若a在b和c之间，则结果为真                     |
| **like**    | a like b           | SQL匹配，如果a匹配b，则结果为真               |
| **in**      | a in (a1,a2,a3...) | 假设a在a1,或者a2...其中的某一个值中，结果为真 |

```mysql
-- 查询姓刘的同学
-- like 结合 %（代表0到任意个字符） _（代表一个字符）
select student,studentname from student
where studentname like '刘%'

-- 查询姓刘的同学，名字后面只有一个字的
select student,studentname from student
where studentname like '刘_'

-- 查询姓刘的同学，名字后面只有两个字的 _ _ 
select student,studentname from student
where studentname like '刘__'

-- 查询名字中间有加字的同学 %加%
select student,studentname from student
where studentname like '%加%'

-- in(具体的一个或者多个值)
-- 查询 1001,1002,1003
select `student`,`studentName` from student
where studentNo in(1001,1002,1003)

-- 查询地址为空的学生 null ''
select `student`,`studentName` from student
where address='' or address is null

```

##### 6.4 联表查询

| 操作       | 描述                                       |
| ---------- | ------------------------------------------ |
| inner join | 如果表中至少有一个匹配，就返回行           |
| left join  | 会从左表中返回所有的值，即使右表中没有匹配 |
| right join | 会从右表中返回所有的值，即使左表中没有匹配 |

```mysql
-- 思路
-- 1.分析需求，分析查询的字段来自哪些表
-- 2.确定使用哪种连接查询
-- 确定交叉点（这两个表中哪个数据是相同的）
-- 判断的条件，学生表 studentNo = 成绩表 studentNo

-- join (连接的表) on (判断的条件) 连接查询
-- where 等值查询
-- 查询参加了考试的同学（学号，姓名，科目编号，分数）
select s.studentNo,studentName,subjectNo,studentResult
from student as s
inner join result as r
on s.studentNo=r.studentNo

-- right join 
select s.studentNo,studentName,subjectNo,studentResult
from student as s
right join result as r
on s.studentNo=r.studentNo

-- left join
select s.studentNo,studentName,subjectNo,studentResult
from student as s
left join result as r
on s.studentNo=r.studentNo

-- 查询缺考的同学
select s.studentNo,studentName,subjectNo,studentResult
from student as s
left join result as r
on s.studentNo=r.studentNo
where studentresult is null
```

##### 6.5 自连接

* 一张表中有两张表的内容将一张表以别名的方式作为两张表来用

##### 6.6.分页和排序

```mysql
-- order by 排序
-- desc 降序排序  asc升序排序
select s.student,studentname,stbjectname,studentresult
from student s
inner join result r
on s.student=r.student
inner join subject sub
on r.subjectno =sub.subjectno
where subjectname='数据库结构-1'
order by studentresult desc

-- 分页 每页只显示5条数据
-- limit 起始值,页面大小
-- limit 0,5 1-5条数据
from student s
inner join result r
on s.student=r.student
inner join subject sub
on r.subjectno =sub.subjectno
where subjectname='数据库结构-1'
order by studentresult desc
limit 0,5
-- 第一页 limit 0,5 	（1-1）*5
-- 第二页 limit 5,5  	（2-1）*5
-- 第三页 limit 10,5  	（3-1）*5
-- 第N页 limit （n-1）*pageSize,pageSize
-- 【pageSize：页面大小】
-- 【（n-1）*pageSize：起始值】
-- 【 n ：当前页】
-- 【数据总数/页面大小 = 总页数】

```

##### 6.7 子查询

> wherer

```mysql
-- 1.查询数据库结构-1 的所有考试结果（学号，科目编号，成绩），降序排列 
-- 方式一：使用连接查询
select studentNo,r.subjectNo,studentresult
from result r
inner join subject sub
on r.subjectNo = sub.subjectNo
where subjectName = '数据库结构-1'
order by studentResult desc

-- 方式二：使用子查询
select studentNo,subjectNo,studentresult
from result
where subjectNo = (
	select subjectNo from subject 
    where subjectName = '数据库结构-1'
)
order by studentResult desc
```

##### 6.8 分组和过滤

```mysql
-- 查询不同课程的平均分，最高分，最低分，平均分大于80分
-- 核心：（根据不同的课程分组）
select subjectName,avg(studentResult) as 平均分,max(studentResult),min(studentResult)
form result r
inner join subject sub
on r.subjectNo=sub.subjectNo
group by r.subjectNo -- 通过什么字段来分组
having 平均分>80
```



### 八、MySQL函数

#### 1. 常用函数

```mysql
-- 数学运算
select abs(-8) -- 绝对值
select ceiling(9.4) -- 向上取整
select floor(9.4) -- 向下取整
select rand() -- 返回一个0-1之间的随机数
select sign(10) -- 判断一个数的符合  0-0 负数返回-1 正数返回1

-- 字符串函数
select char_length('即使再小的帆也能远航') -- 返回字符串长度
select concat('我','love') -- 拼接字符串
select insert('我爱编程',1,2,'超级热爱') -- 查询 替换 从1开始到2结束 我爱 被替换为 超级热爱
select lower('ceshiyixia') -- 变为小写字母
select upper('ceshiyixia') -- 变为大写字母
select instr('ceshiyixia','h') -- h第一次出现的位置
select replace('坚持就能成功','坚持','努力') -- 将坚持替换为努力
select substr('坚持就能成功',2,4) -- 返回指定的字符串长度 从第二个开始截取6个
select reverse() -- 反转字符 

-- 时间日期函数（记住）
select current_date() -- 获取当前日期
select curdate() -- 获取当前日期
select now() -- 获取当前日期及时间
select localtime() -- 本地时间
select sysdate() -- 系统时间

select year(now()) -- 年
select month(now()) -- 月
select day(now()) -- 日
select hour(now()) -- 时
select minute(now()) -- 分
select second(now()) -- 秒

-- 系统
select system_user() -- 系统登陆用户
select user()
select version()
```

#### 2.聚合函数

| 函数名称 | 描述   |
| -------- | ------ |
| count()  | 计数   |
| sum()    | 求和   |
| avg()    | 平均值 |
| max()    | 最大值 |
| min()    | 最小值 |
| ...      |        |

```mysql
select count(studentNo) from student -- count(字段)，会忽略所有的null值
select count(*) from student -- count(*)，不会忽略所有的null值
select count(1) from student -- count(1)，不会忽略所有的null值

select sum(`studentResult`) as 总和 from result
select avg(`studentResult`) as 平均分 from result
select max(`studentResult`) as 最高分 from result
select min(`studentResult`) as 最低分 from result

-- 查询不同课程的平均分，最高分，最低分，平均分大于80分
-- 核心：（根据不同的课程分组）
select subjectName,avg(studentResult) as 平均分,max(studentResult),min(studentResult)
form result r
inner join subject sub
on r.subjectNo=sub.subjectNo
group by r.subjectNo -- 通过什么字段来分组
having 平均分>80
```

### 九、数据库授权和备份

#### 1.授权

```mysql
-- 创建用户
create user fanhuajin identified by '123456'

-- 修改密码 修改当前用户密码
set password=password('666666')

-- 修改密码 修改指定用户密码
set password for fanhuajin=password('666666')

-- 重命名
rename user fanhuaji to fanhuajin2

-- 用户授权 授予全部的权限
grant all privileges on *.* to fanhuajin2

-- 查看权限
show grants for fanhuajin -- 查看指定用户的权限
show grants for root@localhost

-- 撤销权限
revoke all privileges on *.* from fanhuajin

-- 删除用户
drop user fanhuajin
```

#### 2.备份

```mysql
-- 直接拷贝物理文件
-- 在可视化工具中手动导出
-- 使用命令行cmd 导出 mysqldump 命令行使用

mysqldump -hlocalhost -uroot -proot school  > D:/school.sql  -- 导出数据库
mysqldump -hlocalhost -uroot -proot school student > D:/student.sql -- 导出表
mysqldump -hlocalhost -uroot -proot school student result > D:/student_result.sql -- 导出多张表
-- 导入
-- 登陆的情况下，切换到指定数据库
source 备份文件

```

### 十、三大范式

#### 1.第一范式

* 原子性：保证每一列不可再分

#### 2.第二范式

* 前提：满足第一范式
* 每张表只描述一件事情

#### 3.第三范式

* 前提：满足第一范式和第二范式
* 第三范式需要确保数据表中的每一列数据都和主键直接相关，而不能间接相关

#### 规范性和性能的问题

关联查询的表不得超过三张表

* 考虑商业化的需求和目标，（成本，用户体验！）数据库的性能更加重要
* 在规范性能的问题的时候，需要适当的考虑一下规范性
* 故意给某些表增加一些冗余的字段。（从多表查询中变为单表查询）