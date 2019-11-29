# MySQL-基本使用
configure配置 conf cnf sudo rm ~/.navicat64/ -rf
## 1.数据库简介
    优点：存储数据，优化读写
    数据库解决的问题：持久化存储，优化读写，保证数据的准确性
    数据的操作：crdu（creat retrieve update delete）

## 2.E-R模型（entry-relationship）
    一个实体转换为数据库中的一个表
    关系描述两个实体之间的对应规则：一对一；一对多；多对多
    关系转换为数据库中的一个列，在关系型数据库中一行就是一个对象

## 3.三范式
    经过研究和对使用中问题的总结，对于设计数据库提出了一些规范，这些规范被称为范式
    第一范式（1NF）：列不可拆分
    第二范式（2NF）：唯一标识
    第三范式（3NF）：引用主键
    说明：后一个范式，都是在前一个范式的基础上建立的

## 4.数据完整性
## 5.字段类型：
    数字int，decimal
    字符串：char，varchar，text
    日期：datetime
    布尔：bit
    枚举类型：enum

## 6.约束
    主键primary key；非空not null；唯一unique；默认default；外键foreign key

## 7.Navicar for MySQL图形界面操作
### 7.1新建数据库
       数据库名：
        字符集：utf—UTF-8 Unicode
        排序规则：utf8_general_ci
    
### 7.2新建表
    
### 7.3.逻辑删除
        对于重要数据，并不希望物理删除，一旦删除删除，数据无法找回。
        设置isDelete/DeleteFlag改为1，类型为bit,1为删除

## 9.命令脚本操作
    mysql  uroot -p
### 9.1数据库操作
        创建数据库：create database 数据库名 charset=utf8；
        删除数据库：drop database 数据库名；
        切换数据库：use 数据库名；
        查看当前选择的数据库：select databases()；
        查看当前所有的数据库：show databases;
    
### 9.2表操作
        查看当前所有的表：show tables;
        create table 表名(列即类型)；
        auto_increment表示自动增长
        create table students(
        id int auto_increment primary key not null，
        name varchar(10) not null,
        gender bit default 1，
        birthday datetime
        isDelete bit default 0
        );
        
        修改表：alter table 表名 add|change|drop 列名 类型；列的名字不能改，只能修改列的名字
        删除表：drop table 表名；
        查看表结构：desc 表名;
        更改表名称：rename table 原表名 to 新表名；
        show create table ’students’；

### 9.3数据操作
        查询select * from 表名；
        增加
        全列插入：insert into 表名 values(……)；
        缺省插入：insert into 表名(列1，……) values(值1，……)；
        mysql特有功能-同时插入多条数据：insert into 表名 values(……),values(……)；
        修改：update 表名 set 列1=值1，…… where 条件
        删除：delete from 表名 where 条件
        逻辑删除，本质就是修改操作alter table students add isDelete bit default0；
        
### 9.4备份与恢复
        数据备份：sudo-s cd /var/lib/mysql mysqldump -uroot -p 数据库名 > ~/Destop/备份文件.sql
        数据恢复：mysql -uroot -p 数据库名 <表名.sql

