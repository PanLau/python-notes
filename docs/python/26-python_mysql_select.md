# MySQL-查询
消除重复行：select distinct 字段 from 表
## 1.条件
    比较运算符：>，<，=，!=，>=，<=；
    逻辑运算符：and，or，not；
    模糊查询：like，%（任意多个字符），_（任意一个字符）；
    范围查询：in()表示在一个非连续的范围内；between……and……表示在一个连续的范围内，包含两端
    空判断：is null，is not null;
    优先级：小括号—>not—>比较运算符—>逻辑运算符；and比or先运算，如果同时出现并希望先算or，需要结果（）使用

## 2.聚合
    count(*)，max(列)，min(列)，sum(列)，avg(列)—>只能得到聚合结果

## 3.分组
    非聚合字段不能出现在结果中；原始集->where->结果集->分组->结果集
    select 列1,列2,聚合…… from 表名
    group by 列1,列2,列3
    having 列1,列2,列3
    
    分组一般配合聚合使用

## 4.排序
    select * from 表名
    order by 列1 asc|desc, 列2 asc|desc
    默认按照列值从小到大排序

## 5.分页
    select * from 表名
    limit start,count
    
    select * from students
    where isdelete=0
    limit (n-1)*m,m
    
    select distinct * 
    from 表名
    where 条件
    group by 条件 having 条件
    order by 条件
    limit start,count
    
**执行顺序为：from 表名—>where—>group by—>select distinct * —>having—>order by—>limit start,count**

## 6.连接查询
    inner join
    left join
    right join