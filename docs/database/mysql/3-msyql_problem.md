created by:张慧姝
date:2019-03-25

## 1.where 条件

当where 条件返回一个数值x,
若 x = 0 则为False,返回结果为空
若 x>0 则为True,按条件返回结果

**举个栗子**
```sql
select *
from bidata.will_user_list_crm ulc
where locate('集团军',ulc.department_name)
```
![](/uploads/201903/analyst/attach_158f1556f6da48f6.png)




```sql
select *
from bidata.will_user_list_crm ulc
where locate('小组',ulc.department_name)
```
![](/uploads/201903/analyst/attach_158f155b1b834cf5.png)


## 2.Null值
created by: 张慧姝
date: 2019-04-09

**三值逻辑的真值表(NOT)**

| x  | NOT x  |
| ------------ | ------------ |
|  t | f  |
| u  | u  |
|  f |  t |

**三值逻辑的真值表(AND)**

|  AND | t  | u  |  f |
| ------------ | ------------ | ------------ | ------------ |
|  t | t  |  u |  f |
|  u | u  | u  | f  |
|  f |  f | f  | f  |

**三值逻辑真值表(OR)**

| OR  |  t | u  |  f |
| ------------ | ------------ | ------------ | ------------ |
|  t | t  | t  |  t |
| u  |  t |  u | u  |
| f  | t  | u  | f  |

**优先级:**
AND的情况:false > unknown > true
OR的情况:true > unknown >false



#### 1.not in
**not in 里面如果有null,整个查询会返回空,需要在where条件中剔除null.而in里面有null,查询不受null影响.**

```sql
 select
 	distinct tcp.pay_method_new
 from view_tms_contract_payment tcp
 where submit_time >= curdate() - INTERVAL 1 day
 	and submit_time < curdate()
     and tcp.pay_method_new is not null   ###这里有个坑,支付方式有空,用not in之后就会返回空,因此要把空去掉或者用not exists,但是用not exists这里很慢
     and tcp.pay_method_new not in
       (select distinct pay_method_new
        from view_tms_contract_payment
        where submit_time < curdate() - INTERVAL 1 day
            and pay_method_new is not null);
```




```sql
select 'A' not in ('B',null)
```
![](/uploads/201904/analyst/attach_15941e036bfb9d01.png)

```sql
select 'A' not in ('A',null)

```
![](/uploads/201904/analyst/attach_15941e1df2e7744e.png)



#### 2.concat
**concat 如有任何一个参数为NULL，则返回值为 NULL**

```sql
select concat('A',null)
```
![](/uploads/201904/analyst/attach_15941dedec584302.png)

```sql
select
    a.user_id,
    date_format(a.last_pay_date,'%Y-%m') 年月,
    name 姓名,
    round(sum(real_pay_sum),0) 实际业绩
from
    (
    select
        tcp.contract_id,
        max(tcp.pay_date) last_pay_date,
        s.student_no,
        sum(tcp.sum)/100 real_pay_sum,
        ui.user_id,
        ui.`name`,
        ui.job_number,
        SUBSTRING_INDEX(tc.department_name,',',-1) dept,
        (tc.sum-666)*10 contract_amount
    from hfjydb.view_tms_contract_payment tcp
    left join hfjydb.view_tms_contract tc on tcp.contract_id  = tc.contract_id
    left join view_student s on s.student_intention_id = tc.student_intention_id
    left join view_user_info ui on ui.user_id = tc.submit_user_id
    left join bidata.will_user_list_crm ulc on ui.user_id = ulc.user_id and ulc.stats_date = (select max(stats_date) from bidata.will_user_list_crm where department_name not like '%离职%' and user_id = ulc.user_id)
    where tcp.pay_status in (2,4)
        and tc.`status` <> 8 and ui.account_type = 1
        and ui.user_id = 684578
        and INSTR(
                concat(ifnull(ulc.first_level,''),ifnull(ulc.second_level,''),ifnull(ulc.third_level,''),ifnull(ulc.fourth_level,''),ifnull(ulc.fifth_level,''),ifnull(ulc.sixth_level,''),ifnull(ulc.seventh_level,'')),
                (select concat(ifnull(first_level,''),ifnull(second_level,''),ifnull(third_level,''),ifnull(fourth_level,''),ifnull(fifth_level,''),ifnull(sixth_level,''),ifnull(seventh_level,'')) from bidata.will_user_list_crm where user_id = 265794 and stats_date = curdate()))
        and ulc.department_name <> (select department_name from bidata.will_user_list_crm where user_id = 265794 and stats_date = curdate())
    group by tcp.contract_id
    having real_pay_sum >= contract_amount
    order by ui.user_id, last_pay_date
    ) a
group by a.user_id,date_format(a.last_pay_date,'%Y-%m')

```

#### 3.REGEXP
| 选项  | 说明  | 例子  |  匹配值示例 |
| ------------ | ------------ | ------------ | ------------ |
|  ^ | 匹配文本的开始字符  | '^b' 匹配以字母 b 开头 的字符串  |  book、big、banana、 bike |
| $  |  匹配文本的结束字符 | 'st$' 匹配以 st 结尾的字 符串  |  test、resist、persist |
| .  | 匹配任何单个字符  |  'b.t' 匹配任何 b 和 t 之间有一个字符 | bit、bat、but、bite  |
| *  | 匹配零个或多个在它前面的字 符  | 'f*n’ 匹配字符 n 前面有 任意个字符 f |  fn、fan、faan、abcn |
| +  | 匹配前面的字符 1 次或多次  | 'ba+' 匹配以 b 开头，后 面至少紧跟一个 a  | ba、bay、bare、battle  |
| <字符串> | 匹配包含指定字符的文本  |  'fa' | fan、afa、faad  |
| [字符集合]  | 匹配字符集合中的任何一个字 符  |  '[xz]'匹配 x 或者 z | dizzy、zebra、x-ray、 extra  |
| [^]  | 匹配不在括号中的任何字符  | '[^abc]’ 匹配任何不包 含 a、b 或 c 的字符串  | desk、fox、f8ke  |
| 字符串{n,}  | 匹配前面的字符串至少 n 次  | b{2} 匹配 2 个或更多 的 b  | bbb、 bbbb、 bbbbbbb  |
| 字符串{n,m}  | 匹配前面的字符串至少 n 次， 至多 m 次  | b{2,4} 匹配最少 2 个， 最多 4 个 b  | bbb、 bbbb|

#### LIKE
| 选项  | 说明  | 例子  |  匹配值示例 |
| ------------ | ------------ | ------------ | ------------ |
| % | 替代一个或多个字符  | 'Mc%'匹配以 Mc 开头的所有字符串  | McBadden  |
| % | 替代一个或多个字符  | '%inger'匹配以 inger 结尾的所有字符串 | Ringer、Stringer |
| % | 替代一个或多个字符  | '%en%'匹配在任何位置包含字母 en 的所有字符串  |  Bennet、Green、McBadden |
| _ |   仅替代一个字符 | '_heryl' 以字母 heryl 结尾的所有六个字母的名称 | Cheryl、Sheryl  |
| [字符集合]  |  字符列中的任何单一字符 |  '[CK]'匹配C或者K| Carson 和 Karson  |
| [^]  |  匹配不在括号中的任何字符 | ‘[^abc]’ 匹配任何不包 含 a、b 或 c 的字符串  |  desk、fox、f8ke |
**注:使用like时, mysql不支持[字符集合],[^]**

常用:
```sql
####执行时间:1.616s
select *
from sys_department
where department_name like '%第一集团军%'
	or department_name like '%第二集团军%'
	or department_name like '%第三集团军%'
```
等价于
```sql
####执行时间:1.092s
select *
from sys_department
where department_name regexp '第一集团军|第二集团军|第三集团军'
```

## 4.组内排序
# 每个学生最后一次上课的时间和老师

# mysql
```


1:
select
    a.student_id,
    a.adjust_start_time 上课时间,
    a.teacher_id
from
    (select *
     from
         lesson_plan lp
     where adjust_start_time>'2018-12-01'
           and lp.status in(3,5)
           and lp.solve_status<>6
           and lp.lesson_type=1
     order by lp.student_id,lp.adjust_start_time desc ) a
group by student_id
```


```
2:

select
 	lp1.student_id,
 	(select adjust_start_time
	from
    	lesson_plan lp
        where adjust_start_time>'2018-12-01'
           and lp.status in(3,5)
           and lp.solve_status<>6
           and lp.lesson_type=1
           and lp.student_id=lp1.student_id
        order by lp.adjust_start_time desc  limit 1) adjust_start_time,
	(select teacher_id
	from
    	lesson_plan lp
        where adjust_start_time>'2018-12-01'
           and lp.status in(3,5)
           and lp.solve_status<>6
           and lp.lesson_type=1
           and lp.student_id=lp1.student_id
        order by lp.adjust_start_time desc  limit 1) teacher_id
    from
    	lesson_plan lp1
    where  lp1.adjust_start_time>'2018-12-01'
           and lp1.status in(3,5)
           and lp1.solve_status<>6
           and lp1.lesson_type=1
   group by lp1.student_id

```

```
3:

select
    a.student_id,a.max_time,lp1.teacher_id
from
(select
    lp.student_id,max(lp.adjust_start_time) max_time
from
    lesson_plan lp
where adjust_start_time>'2018-12-01'
  and lp.status in(3,5)
  and lp.solve_status<>6
  and lp.lesson_type=1
group by lp.student_id) a

inner join #此时left join 和inner join  结果一致  因为a包含于lesson_plan 表中,使用inner join 效率更高

lesson_plan lp1 on a.student_id=lp1.student_id and lp1.adjust_start_time=a.max_time
where adjust_start_time>'2018-12-01'
  and lp1.status in(3,5)
  and lp1.solve_status<>6
  and lp1.lesson_type=1
```


```

4:
（可以选多行）
select *
from
(
select
    temp.*,
    if(@new_class = temp.student_id,@rank:= @rank+1,@rank:=1) as rank,
    @new_class:=temp.student_id as new_class
from
    (select
     	lp.student_id,lp.teacher_id,lp.adjust_start_time
    from
    	lesson_plan lp
    where adjust_start_time>'2018-12-01'
           and lp.status in(3,5)
           and lp.solve_status<>6
           and lp.lesson_type=1
    order by lp.student_id,lp.adjust_start_time desc
    )temp,(select @new_class := null,@rank :=0)t
)a
where a.rank<=2
```
# python
```
rank 函数：

df['score']= df.groupby("student_id")['adjust_start_time'].rank(ascending=1,method='first')
python:


def top(x,column="adjust_start_time",n=50):
    return x.sort_values(by=column,ascending=False)[:n]

df1 = df.groupby(["student_id"],group_keys=False).apply(top,n=1)
df1.head()
```


https://blog.csdn.net/justry_deng/article/details/80597916

## SQL执行顺序
###### Create by:along


一条sql语句搞定基于mysql的sql执行顺序的基本理解

| 拼写顺序 |        sql             | 执行顺序 |
| -------- | ------------------- | -------- |
| 1        | select              | 7        |
| 2        | distinct            | 8        |
| 3        | from                | 1        |
| 4        | join（如left join） | 3        |
| 5        | on                  | 2        |
| 6        | where               | 4        |
| 7        | group by            | 5        |
| 8        | having              | 6        |
| 9        | order by            | 9        |
| 10       | limit               | 10       |

---
![sql顺序解析](/uploads/201810/analyst/attach_155c203ccbf87f32.png "sql顺序解析")


---


```
一、执行FROM语句，mysql是从左往右执行，oracle是从右往左执行，
SQL语句的执行过程中，都会产生一个虚拟表，用来保存SQL语句的执行结果（这是重点），
执行from语句之后会产生一个虚拟表暂时叫VT1（vitual table 1），VT1是根据笛卡尔积生成

二、执行on进行过滤
根据on后面的条件过滤掉不符合条件的数据，参数VT2

三、执行链接的类型
inner join内连接、left join左链接、right右链接、outer join 外链接、full outer join 全连接
执行完产生VT3

四、执行where后面的条件
这时候使用WHERE条件的时候要注意：不能使用组函数、并且字段的别名不能放到条件中使用
例如SELECT city as c FROM t WHERE c='shanghai'

五、执行group by 进行分组

六、执行having过滤
HAVING子句主要和GROUP BY子句配合使用，having后面可以跟组函数的条件

七、执行select

八、执行distinct，去掉重复的数据

九、执行order by 语句排序

十、执行分页语句
```

P.s. :[步步深入：MySQL架构总览->查询执行流程->SQL解析顺序](https://www.cnblogs.com/annsshadow/p/5037667.html)

## mysql 别名问题
created by:吉莹莹
update time: 2019-02-11


在**标准sql**中,where 和 group by子句中都不能使用别名进行条件判断,而order by可以

这跟sql的执行顺序有关, select 子句在where 和 group by 之后执行,所以在 where 和 GROUP BY 子句中引用在 select 子句里定义的别称是不被允许的

而在**mysql**中,group by 可以使用别名,这是因为，mysql在执行查询语句时，会先对 select 子句里的列表进行扫描，并对列进行计算。



**sql语句执行顺序**

(7)    SELECT

(8)    DISTINCT <select_list>

(1)    FROM <left_table>

(3)    <join_type> JOIN <right_table>

(2)    ON <join_condition>

(4)    WHERE <where_condition>

(5)    GROUP BY <group_by_list>

(6)    HAVING <having_condition>

(9)    ORDER BY <order_by_condition>

(10)   LIMIT <limit_number>

## insert into 方法
1. 插一条数据（主键是自动生成的,加不加都可以，但不能加已经有的）；

```
#第一种方法将列名和列值分开，在使用时，列名必须和列值的数一致。
insert into test(student_id,city,date)
values('10003','suzhou','2018-08-21');#不加主键列,会默认生成

insert into test(id,student_id,city,date) values(null,'10003','suzhou','2018-08-21');#加主键列,可以指定也可以填null

insert into test values(null,'10003','suzhou','2018-08-21');#加主键列,可不写列名

#第二种方法允许列名和列值成对出现和使用
INSERT INTO test SET student_id ='88888',city = 'hangzhou',date = '2018-08-21';#
```

2. 插多条数据；

```
#一般插入方法；
insert into test(student_id,city,date) values('10004','suzhou','2018-08-21'),('10005','shanghai','2018-08-22');

#假设两个表结构一样,把smj表数据插入smj_1
INSERT INTO smj_1 SELECT * FROM smj;

#假设两个表结构不一样,需要指定两个表的列名插入数据到smj_1
INSERT INTO smj_1(start_time,teacher_id) SELECT start_time,teacher_id FROM smj;
```

3. 插整张表；

```
#只插表结构到新表
#第一种方法：主键类型和自增方式是不会复制过去的；
CREATE TABLE smj_1 SELECT * FROM test WHERE 1<>1;
#第二种方法：把旧表的所有字段类型都复制到新表；
CREATE TABLE smj_1 LIKE test ;

#插表结构及数据到新表
CREATE TABLE smj_2 SELECT * FROM smj;
```

4. 夸实例插；
```
#Navicat中有个转储sql文件；
```


## 6.mysql left join 优先级

###### Create by:along


#### 优先级
```
两者放置相同条件，之所以可能会导致结果集不同，就是因为优先级。on的优先级是高于where的。
```
首先明确两个概念：

* LEFT JOIN 关键字会从左表 (table_name1) 那里返回所有的行，即使在右表 (table_name2) 中没有匹配的行。
* 数据库在通过连接两张或多张表来返回记录时，都会生成一张中间的临时表，然后再将这张临时表返回给用户。
在left join下，两者的区别：

* on是在生成临时表的时候使用的条件，不管on的条件是否起到作用，都会返回左表 (table_name1) 的行。
* where则是在生成临时表之后使用的条件，此时已经不管是否使用了left join了，只要条件不为真的行，全部过滤掉。
*

#### 测试
表1：table1

id | no
---|---
1  | n1
2  | n2
3  | n3



表2：table2

No | name
---|---
n1 | aaa
n2 | bbb
n3 | ccc


```
select a.id,a.No,b.name from table1 a left join table2 b on (a.No = b.No and b.name='aaa');
select a.id,a.No,b.name from table1 a left join table2 b on (a.No = b.No) where b.name='aaa';
```

第一个结果集：

|id |No |name|
|---|---|---|
|1  |n1 |aaa|
|2  |n2 |(Null)|
|3  |n3 |(Null)|

第二个结果集：

|id |No |name|
|---|---|---|
|1  |n1 |aaa|

* 第一个sql的执行流程：首先找到b表的name为aaa的记录行（on (a.No = b.No and b.name=’aaa’) ）。然后找到a的数据（以a表为主表进行连接），生成临时表返回用户。
* 第二个sql的执行流程：首先生成临时表，然后执行where过滤b.name=’aaa’不为真的结果集，最后返回给用户。

* 因为on会首先过滤掉不符合条件的行，然后才会进行其它运算，所以按理说on是最快的。

* 在多表查询时，on比where更早起作用。系统首先根据各个表之间的联接条件，把多个表合成一个临时表后，再由where进行过滤，然后再计算，计算完后再由having进行过滤。由此可见，要想过滤条件起到正确的作用，首先要明白这个条件应该在什么时候起作用，然后再决定放在那里。

* 对于JOIN参与的表的关联操作，如果需要不满足连接条件的行也在我们的查询范围内的话，我们就必需把连接条件放在ON后面，而不能放在WHERE后面，如果我们把连接条件放在了WHERE后面，那么所有的LEFT,RIGHT,等这些操作将不起任何作用，对于这种情况，它的效果就完全等同于INNER连接。对于那些不影响选择行的条件，放在ON或者WHERE后面就可以。

* 记住：所有的连接条件都必需要放在ON后面，不然前面的所有LEFT,和RIGHT关联将作为摆设，而不起任何作用。

## 7.create 用法
###### Create by:along

1、一般 create table 标准语法
```
CREATE TABLE <表名>
(column_name column_type);
```
示例

```
CREATE TABLE customers
    ( cust_id int NOT NULL AUTO_INCREMENT,
    cust_name char(50) NOT NULL,
    cust_address char(50) NULL,
    cust_eamil char(255) NULL,
    cust_city char(50) NOT NULL DEFAULT 'BEIJIGN',
    PRIMARY KEY(cust_id) )ENGINE=InnoDB DEFAULT CHARSET=utf8
```


```
说明：
    1、设置NOT NULL，约束该字段不能为空，为空时不可以被提交
    2、AUTO_INCREMENT设置为自增字段，会自动生成sequence
    3、DEFAULT 设置默认值
    4、ENGINE=InnoDB设置数据库引擎，可以选择MyISAM或InnoDB
    5、DEFAULT CHARSET=utf8，设置表编码格式
```

```
MyISAM与InnoDB的区别是什么？
1、 存储结构
MyISAM：每个MyISAM在磁盘上存储成三个文件。第一个文件的名字以表的名字开始，扩展名指出文件类型。.frm文件存储表定义。数据文件的扩展名为.MYD (MYData)。索引文件的扩展名是.MYI (MYIndex)。
InnoDB：所有的表都保存在同一个数据文件中（也可能是多个文件，或者是独立的表空间文件），InnoDB表的大小只受限于操作系统文件的大小，一般为2GB。
2、 存储空间
MyISAM：可被压缩，存储空间较小。支持三种不同的存储格式：静态表(默认，但是注意数据末尾不能有空格，会被去掉)、动态表、压缩表。
InnoDB：需要更多的内存和存储，它会在主内存中建立其专用的缓冲池用于高速缓冲数据和索引。
3、 可移植性、备份及恢复
MyISAM：数据是以文件的形式存储，所以在跨平台的数据转移中会很方便。在备份和恢复时可单独针对某个表进行操作。
InnoDB：免费的方案可以是拷贝数据文件、备份 binlog，或者用 mysqldump，在数据量达到几十G的时候就相对痛苦了。
4、 事务支持
MyISAM：强调的是性能，每次查询具有原子性,其执行数度比InnoDB类型更快，但是不提供事务支持。
InnoDB：提供事务支持事务，外部键等高级数据库功能。 具有事务(commit)、回滚(rollback)和崩溃修复能力(crash recovery capabilities)的事务安全(transaction-safe (ACID compliant))型表。
5、 AUTO_INCREMENT
MyISAM：可以和其他字段一起建立联合索引。引擎的自动增长列必须是索引，如果是组合索引，自动增长可以不是第一列，他可以根据前面几列进行排序后递增。
InnoDB：InnoDB中必须包含只有该字段的索引。引擎的自动增长列必须是索引，如果是组合索引也必须是组合索引的第一列。
6、 表锁差异
MyISAM：只支持表级锁，用户在操作myisam表时，select，update，delete，insert语句都会给表自动加锁，如果加锁以后的表满足insert并发的情况下，可以在表的尾部插入新的数据。
InnoDB：支持事务和行级锁，是innodb的最大特色。行锁大幅度提高了多用户并发操作的新能。但是InnoDB的行锁，只是在WHERE的主键是有效的，非主键的WHERE都会锁全表的。
7、 全文索引
MyISAM：支持 FULLTEXT类型的全文索引
InnoDB：不支持FULLTEXT类型的全文索引，但是innodb可以使用sphinx插件支持全文索引，并且效果更好。
8、 表主键
MyISAM：允许没有任何索引和主键的表存在，索引都是保存行的地址。
InnoDB：如果没有设定主键或者非空唯一索引，就会自动生成一个6字节的主键(用户不可见)，数据是主索引的一部分，附加索引保存的是主索引的值。
9、 表的具体行数
MyISAM：保存有表的总行数，如果select count(*) from table;会直接取出出该值。
InnoDB：没有保存表的总行数，如果使用select count(*) from table；就会遍历整个表，消耗相当大，但是在加了wehre条件后，myisam和innodb处理的方式都一样。
10、 CURD操作
MyISAM：如果执行大量的SELECT，MyISAM是更好的选择。
InnoDB：如果你的数据执行大量的INSERT或UPDATE，出于性能方面的考虑，应该使用InnoDB表。DELETE 从性能上InnoDB更优，但DELETE FROM table时，InnoDB不会重新建立表，而是一行一行的删除，在innodb上如果要清空保存有大量数据的表，最好使用truncate table这个命令。
11、 外键
MyISAM：不支持
InnoDB：支持
通过上述的分析，基本上可以考虑使用InnoDB来替代MyISAM引擎了，原因是InnoDB自身很多良好的特点，比如事务支持、存储 过程、视图、行级锁定等等，在并发很多的情况下，相信InnoDB的表现肯定要比MyISAM强很多。另外，任何一种表都不是万能的，只用恰当的针对业务类型来选择合适的表类型，才能最大的发挥MySQL的性能优势。如果不是很复杂的Web应用，非关键应用，还是可以继续考虑MyISAM的，这个具体情况可以自己斟酌。
```
2、根据select 的结果集来创建表标准语法

```
create [temporary] table [if not exists] tbl_name
    [(create_definition,...)] [table_options] [partition_options]
    [ignore | replace] [as]
    query_expression
```
示例

```
create table person_as
    as
    select id,
    name
    from person;
```
3、根据 create table tbname as 标准语法

```
CREATE TABLE <新建表名> like <原有表名>
```
示例

```
create table new_person
    like person
```
4、create table tb as select 和create table tb like的区别

```
create table tb as select用于创建相同的表结构并复制源表数据，并不会创建源表的索引
create table tb like用于创建完整表结构和全部索引，不复制源表数据（注：Oracle不支持like用法）
```

## update
###### Create by:along

标准语法
```
update<表名>
set <列明> = <表达式>
where <条件>;
```

建表
```
create table addresses
    (id int  auto_increment not null,
    name varchar(20) not null,
    sex varchar(2) not null,
    number integer(10) not null,
    address varchar(50) not null,
    to_date date,
    primary key(id)
    )charset = utf8;
```

插入字段
```
insert into addresses(name,sex,number,address,to_date)
values
    ('吉祥','男',110,'海风教育1202','2000-01-01'),
    ('杨香玉','女',120,'海风教育1202','2000-01-01'),
    ('王涤非','女',130,'海风教育1202','2000-01-01'),
    ('闫洋龙','男',180,'海风教育1204','2000-01-01'),
    ('赵淼','女',220,'海风教育1202','2000-01-01'),
    ('李明慧','女',132,'海风教育1204','2000-01-01'),
    ('史明杰','男',118,'海风教育1202','2000-01-01'),
    ('王娟','女',150,'海风教育1202','2000-01-01');
```
### 1单条字段更新

(1)不带条件更新
```
update addresses
set to_date = '2018-08-24';
```
(2)带条件更新
```
update addresses
set sex = '男'
where name = '王涤非';
```
### 2多条字段更新
一:
```
update addresses
set address = '海风教育0502'
where sex = '女';

update addresses
set address = '海风教育0504'
where sex = '男';
```
二:
```
update addresses
set name = (select distinct name from courses where name = '赤井')
where sex = '男';
```
三：
```
update addresses a,addresses_test b
set a.to_date = b.to_date
where a.name=b.name;
```

四：
```
update addresses
set number=(case when number<120 then number+50
when number>=120 and number<130 then number+60
when number>=130 then number+70 end);
```

