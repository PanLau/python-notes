# impala

## 基本介绍
## 常用函数
## 时间取数
## 常用语法&案例
## 坑

----
## 常用函数
> https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-Built-inTable-GeneratingFunctions(UDTF)
### 1.行转列 Lateral View explode()
1.1 问题引入：

如何将
```
a       b       1,2,3

c       d       4,5,6
```
变为：
```
a       b       1

a       b       2

a       b       3

c       d       4

c       d       5

c       d       6 
```
1.2 原始数据：
```
test.txt

a b 1,2,3

c d 4,5,6
```

1.3 解决方法

方案1：
```
drop table test_jzl_20140701_test;

建表：

create table test_jzl_20140701_test

(

col1 string,

col2 string,

col3 string

)

row format delimited fields terminated by ' '

stored as textfile;

加载数据：

load data local inpath '/home/jiangzl/shell/test.txt' into table test_jzl_20140701_test;

查看表中所有数据：

select * from test_jzl_20140701_test  

a       b       1,2,3

c       d       4,5,6

遍历数组中的每一列

select col1,col2,name 

from test_jzl_20140701_test  

lateral view explode(split(col3,',')) col3 as name;

a       b       1

a       b       2

a       b       3

c       d       4

c       d       5

c       d       6 
```

方案2：
```
drop table test_jzl_20140701_test1;

建表：

create table test_jzl_20140701_test1

(

col1 string,

col2 string,

col3 array<int>

)

row format delimited 

fields terminated by ' '

collection items terminated by ','   //定义数组的分隔符

stored as textfile;

加载数据：

load data local inpath '/home/jiangzl/shell/test.txt' into table test_jzl_20140701_test1;

查看表中所有数据：

select * from test_jzl_20140701_test1; 

a       b       [1,2,3]

c       d       [4,5,6]

遍历数组中的每一列：

select col1,col2,name 

from test_jzl_20140701_test1  

lateral view explode(col3) col3 as name;

a       b       1

a       b       2

a       b       3

c       d       4

c       d       5

c       d       6
```
1.4补充知识点：
```
select * from test_jzl_20140701_test; 

a       b       1,2,3

c       d       4,5,6



select t.list[0],t.list[1],t.list[2] from (

select (split(col3,',')) list from test_jzl_20140701_test)t;



OK

1       2       3

4       5       6



--查看数组长度

select size(split(col3,',')) list from test_jzl_20140701_test;

3

3
```

---

### 2.列转行collect_set() 去重

2.1问题引入：

hive如何将
```
a       b       1
a       b       2
a       b       3
c       d       4
c       d       5
c       d       6
```
变为：
```
a       b       1,2,3
c       d       4,5,6
```
2,2原始数据：
```
test.txt

a       b       1
a       b       2
a       b       3
c       d       4
c       d       5
c       d       6
```
2.3 解决方法：
```
drop table tmp_jiangzl_test;

建表：

create table tmp_jiangzl_test
(
col1 string,
col2 string,
col3 string
)
row format delimited fields terminated by '\t'
stored as textfile;
加载数据：
load data local inpath '/home/jiangzl/shell/test.txt' into table tmp_jiangzl_test;

处理：

select col1,col2,concat_ws(',',collect_set(col3))
from tmp_jiangzl_test  
group by col1,col2;
```




----
## 常用语法&案例
### 1.where 1=1 和 where 1=0
what:1=1 永真， 1<>1 永假
where:
1.where 1=1：用于动态SQL;仅仅只是为了满足多条件查询页面中不确定的各种因素而采用的一种构造一条正确能运行的动态SQL语句的一种方法。

2.where 1=0：用于只取结构不取数据的场合 ;这个条件始终为false，结果不会返回任何数据，只有表结构，可用于快速建表
```
SELECT * FROM strName WHERE 1 = 0;
该select语句主要用于读取表的结构而不考虑表中的数据，这样节省了内存，因为可以不用保存结果集。  

create table newtable as select * from oldtable where 1=0;  
创建一个新表，而新表的结构与查询的表的结构是一样的。
```
