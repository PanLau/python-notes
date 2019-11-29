# mysql

----
1. 基本介绍（安装）
2. 常用函数
3. 坑
4. 案例

----
[TOC]

### 1. mysql-时间取数
>以 2019-1-14 为 curdate() 进行计算
#### 今日:

```
SELECT curdate(); -- 2019-01-14
```

#### 昨日
```
select date_sub(curdate(),interval 1 day); -- 2019-01-13
```

#### 本月
```
# ###以T-1（昨日）为基准进行计算
select date_format(date_sub(curdate(),interval 1 day),'%Y-%m'); --2019-01
select extract(year_month from (date_sub(curdate(),interval 1 day))) -- 201901
```

#### 月初
```
# ###以T-1（昨日）为基准进行计算
select date_sub(date_sub(curdate(),interval 1 day),interval dayofmonth(date_sub(curdate(),interval 1 day))-1 day); --2019-01-01

select date_format(date_sub(curdate(),interval 1 day),'%Y-%m-01'); --2019-01-01

```

#### 月末
```
# ###以T-1（昨日）为基准进行计算
select last_day(date_sub(curdate(),interval 1 day)); -- 2019-01-31
```

#### 本周一
```
# ###以T（今日）为基准进行计算
select date_sub(curdate(),interval WEEKDAY(curdate()) day); -- 2019-01-14

select subdate(curdate(),date_format(curdate(),'%w')-1) ; -- 2019-01-14
```

#### 本周末
```
# ###以T（今日）为基准进行计算
select subdate(curdate(),date_format(curdate(),'%w')-7)  -- 2019-01-20
```

#### 上周一
```
# ###以T（今日）为基准进行计算
select date_sub(curdate(),interval WEEKDAY(curdate())+7 day);
# ###以T-1（昨日）为基准进行计算
select date_sub(date_sub(curdate(),interval 1 day),interval WEEKDAY(date_sub(curdate(),interval 1 day))+7 day);   -- 2019-01-07
```

### 上周末(待验证)
```
# ###以T（今日）为基准进行计算
SELECT date(DATE_SUB(CURDATE(),INTERVAL (DAYOFWEEK(DATE_SUB(CURDATE(),INTERVAL 1 day))) DAY))
between date_sub(curdate(),interval 7+weekday(curdate()) day) and date_sub(curdate(),interval weekday(curdate()) day)
```

#### 上月初
```
# ###以T-1（昨日）为基准进行计算
select date_format(DATE_SUB(date_sub(curdate(),interval 1 day), INTERVAL 1 MONTH), '%Y-%m-01'); -- 2018-12-01
```

#### 上月末
```
# ###以T-1（昨日）为基准进行计算
select date_sub(date_sub(curdate(),interval 1 day),interval dayofmonth(date_sub(curdate(),interval 1 day)) day); -- 2018-12-31

select date_add(date_sub(curdate(),interval 1 day), interval - day(curdate()) + 1 day); -- 2018-12-31
```

#### 前两个月月末
```
select date_sub(
date_sub(date_sub(curdate(),interval 1 day),interval dayofmonth(date_sub(curdate(),interval 1 day)) day),interval day(date_sub(date_sub(curdate(),interval 1 day),interval dayofmonth(date_sub(curdate(),interval 1 day)) day)) day
); -- 2018-11-30

select last_day(date_sub(date_sub(curdate(),interval 1 day),interval 2 month)); -- 2018-11-30
```

---

### sql执行顺序   
> 参考文章1：https://www.cnblogs.com/yyjie/p/7788428.html
> 参考文章2：https://www.cnblogs.com/liluxiang/p/10557451.html
一、sql执行顺序 
(1) from 
(3) join 
(2) on 
(4) where 
(5) group by(开始使用select中的别名，后面的语句中都可以使用)
(6) avg,sum.... 
(7) having 
(8) select 
(9) distinct 
(10) order by 

从这个顺序中我们不难发现，所有的 查询语句都是从from开始执行的，在执行过程中，每个步骤都会为下一个步骤生成一个虚拟表，这个虚拟表将作为下一个执行步骤的输入。 
第一步：首先对from子句中的前两个表执行一个笛卡尔乘积，此时生成虚拟表 vt1（选择相对小的表做基础表） 
第二步：接下来便是应用on筛选器，on 中的逻辑表达式将应用到 vt1 中的各个行，筛选出满足on逻辑表达式的行，生成虚拟表 vt2 
第三步：如果是outer join 那么这一步就将添加外部行，left outer jion 就把左表在第二步中过滤的添加进来，如果是right outer join 那么就将右表在第二步中过滤掉的行添加进来，这样生成虚拟表 vt3 
第四步：如果 from 子句中的表数目多余两个表，那么就将vt3和第三个表连接从而计算笛卡尔乘积，生成虚拟表，该过程就是一个重复1-3的步骤，最终得到一个新的虚拟表 vt3。 
第五步：应用where筛选器，对上一步生产的虚拟表引用where筛选器，生成虚拟表vt4，在这有个比较重要的细节不得不说一下，对于包含outer join子句的查询，就有一个让人感到困惑的问题，到底在on筛选器还是用where筛选器指定逻辑表达式呢？on和where的最大区别在于，如果在on应用逻辑表达式那么在第三步outer join中还可以把移除的行再次添加回来，而where的移除的最终的。举个简单的例子，有一个学生表（班级,姓名）和一个成绩表(姓名,成绩)，我现在需要返回一个x班级的全体同学的成绩，但是这个班级有几个学生缺考，也就是说在成绩表中没有记录。为了得到我们预期的结果我们就需要在on子句指定学生和成绩表的关系（学生.姓名=成绩.姓名）那么我们是否发现在执行第二步的时候，对于没有参加考试的学生记录就不会出现在vt2中，因为他们被on的逻辑表达式过滤掉了,但是我们用left outer join就可以把左表（学生）中没有参加考试的学生找回来，因为我们想返回的是x班级的所有学生，如果在on中应用学生.班级='x'的话，left outer join会把x班级的所有学生记录找回（感谢网友康钦谋__康钦苗的指正），所以只能在where筛选器中应用学生.班级='x' 因为它的过滤是最终的。 
第六步：group by 子句将中的唯一的值组合成为一组，得到虚拟表vt5。如果应用了group by，那么后面的所有步骤都只能得到的vt5的列或者是聚合函数（count、sum、avg等）。原因在于最终的结果集中只为每个组包含一行。这一点请牢记。 
第七步：应用cube或者rollup选项，为vt5生成超组，生成vt6. 
第八步：应用having筛选器，生成vt7。having筛选器是第一个也是为唯一一个应用到已分组数据的筛选器。 
第九步：处理select子句。将vt7中的在select中出现的列筛选出来。生成vt8. 
第十步：应用distinct子句，vt8中移除相同的行，生成vt9。事实上如果应用了group by子句那么distinct是多余的，原因同样在于，分组的时候是将列中唯一的值分成一组，同时只为每一组返回一行记录，那么所以的记录都将是不相同的。 
第十一步：应用order by子句。按照order_by_condition排序vt9，此时返回的一个游标，而不是虚拟表。sql是基于集合的理论的，集合不会预先对他的行排序，它只是成员的逻辑集合，成员的顺序是无关紧要的。对表进行排序的查询可以返回一个对象，这个对象包含特定的物理顺序的逻辑组织。这个对象就叫游标。正因为返回值是游标，那么使用order by 子句查询不能应用于表表达式。排序是很需要成本的，除非你必须要排序，否则最好不要指定order by，最后，在这一步中是第一个也是唯一一个可以使用select列表中别名的步骤。 
第十二步：应用top选项。此时才返回结果给请求者即用户。 

二、mysql的执行顺序 
SELECT语句定义 
一个完成的SELECT语句包含可选的几个子句。SELECT语句的定义如下： 
SQL代码 
```
select 
    clause
from clause
where clause
group by clause
having clause
order by clause
limit clause
```
   
SELECT子句是必选的，其它子句如WHERE子句、GROUP BY子句等是可选的。 
一个SELECT语句中，子句的顺序是固定的。例如GROUP BY子句不会位于WHERE子句的前面。 

SELECT语句执行顺序 
SELECT语句中子句的执行顺序与SELECT语句中子句的输入顺序是不一样的，所以并不是从SELECT子句开始执行的，而是按照下面的顺序执行： 
开始->FROM子句->WHERE子句->GROUP BY子句->HAVING子句->SELECT子句->ORDER BY子句->LIMIT子句->最终结果 
每个子句执行后都会产生一个中间结果，供接下来的子句使用，如果不存在某个子句，就跳过 
对比了一下，mysql和sql执行顺序基本是一样的, 标准顺序的 SQL 语句为: 

``` 
select 考生姓名, max(总成绩) as max总成绩   
  
from tb_Grade   
  
where 考生姓名 is not null   
  
group by 考生姓名   
  
having max(总成绩) > 600   
  
order by max总成绩   
```

 在上面的示例中 SQL 语句的执行顺序如下: 

　　 (1). 首先执行 FROM 子句, 从 tb_Grade 表组装数据源的数据 

　　 (2). 执行 WHERE 子句, 筛选 tb_Grade 表中所有数据不为 NULL 的数据 

　　 (3). 执行 GROUP BY 子句, 把 tb_Grade 表按 "学生姓名" 列进行分组(注：这一步开始才可以使用select中的别名，他返回的是一个游标，而不是一个表，所以在where中不可以使用select中的别名，而having却可以使用，感谢网友  zyt1369  提出这个问题)

　　 (4). 计算 max() 聚集函数, 按 "总成绩" 求出总成绩中最大的一些数值 

　　 (5). 执行 HAVING 子句, 筛选课程的总成绩大于 600 分的. 

　　 (7). 执行 ORDER BY 子句, 把最后的结果按 "Max 成绩" 进行排序. 
---
### count(1)、count(*) 与 count(列名) 的执行区别
执行效果上：  
	count(*)包括了所有的列，相当于行数，在统计结果的时候，不会忽略列值为NULL  
	count(1)包括了忽略所有列，用1代表代码行，在统计结果的时候，不会忽略列值为NULL  
	count(列名)只包括列名那一列，在统计结果的时候，会忽略列值为空（这里的空不是只空字符串或者0，而是表示null）的计数，即某个字段值为NULL时，不统计。
执行效率上：  
	列名为主键，count(列名)会比count(1)快  
	列名不为主键，count(1)会比count(列名)快  
	如果表多个列并且没有主键，则 count（1） 的执行效率优于 count（*）  
	如果有主键，则 select count（主键）的执行效率是最优的  
	如果表只有一个字段，则 select count（*）最优。
	转自：http://eeeewwwqq.iteye.com/blog/1972576
---

### 2. MySQL函数

#### 1. SUBSTRING_INDEX    
SUBSTRING_INDEX（“待截取有用部分的字符串”，“截取数据依据的字符”，截取字符的位置N）
取第一个逗号前的字符串 ：
SELECT SUBSTRING_INDEX('15,151,152,16',',',1); ==>得到结果为： 15
https://www.cnblogs.com/lijiageng/p/6511334.html

#### 2. EXTRACT()    
EXTRACT() 函数用于返回日期/时间的单独部分，比如年、月、日、小时、分钟等等。  

#### 3. CURDATE() 函数返回当前的日期。
SELECT NOW(),CURDATE(),CURTIME()

#### 4. BETWEEN ... AND()
这些值可以是数值、文本或者日期，除了日期特殊外，在sqlserver中都是左右包含！
datetime(now) 不包含右边的
date(CURDATE())包含右边的
```
select*from 表名 where 字段 between  1 and 10    相当于>=1 <=10

如果日期是长型 （2016-04-01 18:12:49.923）

用select*from 表名 where 日期列 between  '2017-5-16' and '2017-5-17 ' 则只查出 16号（左包含，右不包含），因为数据库默认2017-5-17 00：00:00

想用左右包含可使用

select*from 表名 where   convert(varchar,日期列,102)   between  '2017.05.16' and '2017.05.17' （左右包含）
```

#### 5.LOCATE(substr,str)，LOCATE(substr,str,pos)

第一个语法返回字符串str第一次出现的子串substr的位置。
第二个语法返回第一次出现在字符串str的子串substr的位置，从位置pos开始。 substr不在str中，则返回0。
```
SQL> SELECT LOCATE('bar', 'foobarbar');
+---------------------------------------------------------+
| LOCATE('bar', 'foobarbar')                              |
+---------------------------------------------------------+
| 4                                                       |
+---------------------------------------------------------+
1 row in set (0.00 sec) 
```

#### 6.存储过程    
带参数的存储过程
MySql 支持 IN (传递给存储过程) , OUT (从存储过程传出) 和 INOUT (对存储过程传入和传出) 类型的参数 , 存储过程的代码位于 BEGIN 和 END 语句内 , 它们是一系列 SQL 语句 , 用来检索值 , 然后保存到相应的变量 (通过指定INTO关键字) ;    
语法
```
CREATE PROCEDURE  过程名([[IN|OUT|INOUT] 参数名 数据类型[,[IN|OUT|INOUT] 参数名 数据类型…]]) [特性 ...] 过程体

DELIMITER //
  CREATE PROCEDURE myproc(OUT s int)
    BEGIN
      SELECT COUNT(*) INTO s FROM students;
    END
    //
DELIMITER ;
```

#### 7.distinct
注意：不能不分使用distinct
distinct 关键字作用于所有的列，不仅仅是跟在气候的那一列。例如，你指定select distinct vend_id,prod_price,因为指定的两列不完全相同，所以所有的行都会被检索出来

#### 8.in 操作符：
in 操作符一般比一组or操作符执行得更快
in 的最大优点是可以包含其他select语句，能够更动态地建立where子句

#### 9.通配符%
不匹配null

#### 10.soundex()
将任何文本串转换为描述其语音表示的字母数字模式的算法

#### 11.插入数据库
```
名：create_time 
类型：timestamp 
长度：0 
小数点：0 
不是null：勾选
默认：CURRENT_TIMESTAMP 
注释：插入时间
根据当前时间戳更新：不勾选
 
名：update_time 
类型：timestamp 
长度：0 
小数点：0 
不是null：勾选
默认：CURRENT_TIMESTAMP 
注释：最后更新时间
根据当前时间戳更新：勾选
```

----

### 3. mysql for Mac忘记密码的问题

用homebrew安装的mysql，忘记了安装密码，如下为解决方案

##### 安装homebrew

官方网址：https://brew.sh/

##### 确认brew在正常工作

```
$ brew doctor
```

##### 更新包

```
$ brew update
```

##### 安装mysql

```
$ brew install mysql
```

##### 关闭mysql服务器

```
$ sudo /usr/local/Cellar/mysql/5.7.20/support-files/mysql.server stop
```

##### 进入目录

```
$ cd /usr/local/Cellar/mysql/5.7.20/bin
```

##### 重启服务器

```
$ sudo su
$ ./mysqld_safe --skip-grant-tables &
```

##### control + D退出编辑

##### 配置短命令

```
# 普通安装的配置短命令
$ alias mysql=/usr/local/mysql/bin/mysql
# 用homebrew安装mysql的配置短命令
$ alias mysql=/usr/local/Cellar/mysql/5.7.20/bin/mysql
```

##### 进入mysql命令模式

```
$ mysql
```

##### 进入mysql数据库

```
use mysql
```

##### 获取更改权限

```
flush privileges;
```

##### 重置密码：以新密码为mysql示例，可以更改为其他密码

```
set password for 'root'@'localhost'=password('mysql');
```

## 开启mysql服务器
```
$ mysql.server start
```
##### 以上，谢谢！

# 4. mysql基本命令

##### 查看当前的数据库
```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)
```

